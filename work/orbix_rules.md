## 102/103 report rules

- Rule102 Transactions of any kind that is more than or equal to 5m THB
  
  ```sql
  const Query102 = `
  select
      t.tx_id,
      t.from_identity_id,
      t.to_identity_id,
      t.from_customer_id,
      t.to_customer_id,
      t.from_address,
      t.to_address,
      t.from_wallet_id,
      t.to_wallet_id,    
      t.from_customer_type,
      t.to_customer_type, 
      t.from_customer_name,
      t.to_customer_name,   
      t.last_approver_id,
      t.transaction_type,
      t.tx_created_at,
      t.offchain,
      t.network_id,
      t.network_name,
      t.asset_id,
      t.asset_name,
      t.asset_type,
      t.amount,
      t.thb_amount
  from
      success_tx_reports_view t where t.tx_created_at >= $1 and t.tx_created_at < $2 and t.transaction_status = 'completed' and t.thb_amount >= $3  
  `
  ```

- Rule1031 Deposit more than 5 times/day/asset
  
  ```sql
  const Query1031 = `
  select *
      from (
  
      select ld.tx_id,
      ld.from_customer_id,
      ld.from_identity_id,
      ld.to_identity_id,
      ld.to_customer_id,
      ld.from_address,
      ld.to_address,
      ld.from_wallet_id,
      ld.to_wallet_id,  
      ld.from_customer_type,
      ld.to_customer_type,  
      ld.from_customer_name,
      ld.to_customer_name, 
      ld.last_approver_id,
      ld.transaction_type,
      ld.tx_created_at,
      ld.offchain,
      ld.network_id,
      ld.network_name,
      ld.asset_id,
      ld.asset_name,
      ld.asset_type,
      ld.amount,
      ld.thb_amount,
      ROW_NUMBER() OVER w as rank,
      count(*) OVER w as count1
      from success_tx_reports_view ld where ld.transaction_status = 'completed' and ld.transaction_type = 'Received' and ld.tx_created_at >= $1 and ld.tx_created_at < $2
      WINDOW w as (
          PARTITION BY ld.to_customer_id, ld.asset_id
          ORDER BY ld.tx_created_at desc
          RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
        )
      ) t where
  
      t.count1 > $3
  `
  ```

- Rule1032 Withdraw more than 5 times/24h/asset
  
  ```sql
  const Query1032 = `
  -- Return all unique transactions within threshold-exceeding windows,
  -- excluding those flagged with reason_id '103-2'
  SELECT DISTINCT
      -- all_tx.*
      all_tx.tx_id,
      all_tx.from_customer_id,
      all_tx.from_identity_id,
      all_tx.to_identity_id,
      all_tx.to_customer_id,
      all_tx.from_address,
      all_tx.to_address,  
      all_tx.from_wallet_id,
      all_tx.to_wallet_id,  
      all_tx.from_customer_type,
      all_tx.to_customer_type,  
      all_tx.from_customer_name,
      all_tx.to_customer_name, 
      all_tx.last_approver_id,
      all_tx.transaction_type,
      all_tx.tx_created_at,
      all_tx.offchain,
      all_tx.network_id,
      all_tx.network_name,
      all_tx.asset_id,
      all_tx.asset_name,
      all_tx.asset_type,
      all_tx.amount,
      all_tx.thb_amount
  FROM (
      -- Subquery 1: Get unique windows where transaction count exceeds threshold
      SELECT DISTINCT
          t.from_customer_id,
          t.asset_id,
          -- Calculate window time range: 24 hours before to current transaction time
          t.tx_created_at - INTERVAL '24 hours' AS window_start,
          t.tx_created_at AS window_end
      FROM (
          -- Subquery 2: Calculate transaction count within 24-hour window for each transaction
          SELECT 
              ld.tx_id,
              ld.from_customer_id,
              ld.asset_id,
              ld.tx_created_at,
              COUNT(*) OVER w AS count_within_24h
          FROM success_tx_reports_view ld 
          WHERE ld.transaction_status = 'completed' 
            AND ld.transaction_type = 'Sent'
          WINDOW w AS (
              PARTITION BY ld.from_customer_id, ld.asset_id
              ORDER BY ld.tx_created_at
              RANGE BETWEEN INTERVAL '24 hours' PRECEDING AND CURRENT ROW
          )
      ) t 
      -- Filter: windows with count >= threshold and within target time range
      WHERE t.count_within_24h >= $3
          AND t.tx_created_at >= $1 AND t.tx_created_at < $2
  ) threshold_windows
  -- Join to get all transactions within the threshold-exceeding windows
  JOIN success_tx_reports_view all_tx
      ON all_tx.from_customer_id = threshold_windows.from_customer_id
      AND all_tx.asset_id = threshold_windows.asset_id
      AND all_tx.transaction_status = 'completed'
      AND all_tx.transaction_type = 'Sent'
      AND all_tx.tx_created_at BETWEEN threshold_windows.window_start 
                                   AND threshold_windows.window_end
  -- Left join with flag table to identify transactions to exclude
  LEFT JOIN flag_transactions ft
      ON all_tx.tx_id = ft.transaction_id
      AND ft.reason_id = '103-2'
  -- Exclude transactions that exist in flag table with reason_id '103-2'
  WHERE ft.transaction_id IS NULL
  -- Sort by transaction creation time (newest first)
  ORDER BY all_tx.tx_created_at DESC
  `
  ```

- Rule1034 Repeated transaction 3 times of the same: Token/Amount/Token/Destination Wallet Address
  
  ```sql
  const Query1034 = `
  select *
      from (
          select ld.tx_id,
          ld.from_identity_id,
          ld.to_identity_id,
          ld.from_customer_id,
          ld.to_customer_id,
          ld.from_address,
          ld.to_address,  
          ld.from_wallet_id,
          ld.to_wallet_id,
          ld.from_customer_type,
          ld.to_customer_type,
          ld.from_customer_name,
          ld.to_customer_name, 
          ld.last_approver_id,  
          ld.transaction_type,
          ld.tx_created_at,
          ld.offchain,
          ld.network_id,
          ld.network_name,
          ld.asset_id,
          ld.asset_name,
          ld.asset_type,
          ld.amount,
          ld.thb_amount,
      ROW_NUMBER() OVER w as rank,
      count(*) OVER w as count1
  
      from success_tx_reports_view ld where ld.transaction_status = 'completed' and ld.tx_created_at >= $1 and ld.tx_created_at < $2
      WINDOW w as (
          PARTITION BY ld.amount, ld.asset_id, ld.to_address
          ORDER BY ld.tx_created_at desc
          RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
        )
      ) t where 
  
      t.count1 >= 3 and t.thb_amount >= $3 and t.thb_amount <= $4
  `
  ```

- Rule1035 Accumulative Transaction value of more than or equal to 10m THB in the span of 3 days
  
  ```sql
  const Query1035_1 = `
  select
      from_customer_id, to_customer_id
  from
      (
      select
          t3.from_customer_id,
          '' as to_customer_id,
          sum(t3.count1) as total
      from
          (
          select
              distinct("from_customer_id"),
              '' as to_customer_id,
              count1
          from
              (
              select
                  ld.from_customer_id,
                  ld.tx_id,
                  ld.to_customer_id,
                  ld.from_address,
                  ld.to_address,
                  ld.from_wallet_id,
                  ld.to_wallet_id,  
                  ld.from_customer_type,
                  ld.to_customer_type,
                  ld.from_customer_name,
                  ld.to_customer_name,
                  ld.last_approver_id,
                  ld.transaction_type,
                  ld.tx_created_at,
                  ld.offchain,
                  ld.network_id,
                  ld.network_name,
                  ld.asset_id,
                  ld.asset_name,
                  ld.asset_type,
                  ld.amount,
                  ld.thb_amount,
                  sum(ld.thb_amount) over w as count1
              from
                  success_tx_reports_view ld
              where
                  ld.transaction_status = 'completed' and
                  ld.transaction_type = 'Sent' and ld.from_customer_type = 'retail'  and ld.tx_created_at >= $1 and ld.tx_created_at < $2
                    window w as ( partition by ld.from_customer_id range between unbounded preceding and unbounded
                            following )) t1
      union all
          select
              distinct("to_customer_id") as  to_customer_id,
              '' as from_customer_id,
              count1
          from
              (
              select
                  ld.from_customer_id,
                  ld.tx_id,
                  ld.to_customer_id,
                  ld.from_address,
                  ld.to_address,
                  ld.from_wallet_id,
                  ld.to_wallet_id,  
                  ld.from_customer_type,
                  ld.to_customer_type,
                  ld.from_customer_name,
                  ld.to_customer_name,
                  ld.last_approver_id,
                  ld.transaction_type,
                  ld.tx_created_at,
                  ld.offchain,
                  ld.network_id,
                  ld.network_name,
                  ld.asset_id,
                  ld.asset_name,
                  ld.asset_type,
                  ld.amount,
                  ld.thb_amount,
                  sum(ld.thb_amount) over w as count1
              from
                  success_tx_reports_view ld
              where
                  ld.transaction_type = 'Received' and ld.to_customer_type = 'retail' and ld.tx_created_at >= $3 and ld.tx_created_at < $4
                    window w as ( partition by ld.to_customer_id range between unbounded preceding and unbounded following
                                )) t2) t3
      group by
          from_customer_id) t
  where
      total >= $5
  `
  const Query1035_2 = `
  select *
      from (
          select ld.tx_id,
          ld.from_identity_id,
          ld.to_identity_id,
          ld.from_customer_id,
          ld.to_customer_id,
          ld.from_address,
          ld.to_address,
          ld.from_wallet_id,
          ld.to_wallet_id,  
          ld.from_customer_type,
          ld.to_customer_type,
          ld.from_customer_name,
          ld.to_customer_name, 
          ld.last_approver_id,    
          ld.transaction_type,
          ld.tx_created_at,
          ld.offchain,
          ld.network_id,
          ld.network_name,
          ld.asset_id,
          ld.asset_name,
          ld.asset_type,
          ld.amount,
          ld.thb_amount
  
      from success_tx_reports_view ld where ld.transaction_status = 'completed' and ld.tx_created_at >= $1 and ld.tx_created_at < $2
      order by ld.tx_created_at desc
      ) t where 1=1
  
  `
  ```

- Rule1037 Customer withdraw {amount} {token} eq to {priceThb} THB more than 10x of their income {incomeThb} THB corporate/retail
  
  ```sql
  const Query1037 = `
  select *
      from (
          select ld.tx_id,
          ld.from_identity_id,
          ld.to_identity_id,
          ld.from_customer_id,
          ld.to_customer_id,
          ld.from_address,
          ld.to_address,
          ld.from_wallet_id,
          ld.to_wallet_id,  
          ld.from_customer_type,
          ld.to_customer_type,
          ld.from_customer_name,
          ld.to_customer_name, 
          ld.last_approver_id,    
          ld.transaction_type,
          ld.tx_created_at,
          ld.offchain,
          ld.network_id,
          ld.network_name,
          ld.asset_id,
          ld.asset_name,
          ld.asset_type,
          ld.amount,
          ld.thb_amount,
          sum(ld.thb_amount) over w as total_thb_amount
      from success_tx_reports_view ld where ld.transaction_status = 'completed' and ld.tx_created_at >= $1 and ld.tx_created_at < $2 and ld.transaction_type = $3
       window w as (
                partition by ld.to_customer_id
                range between unbounded preceding and unbounded following )
      ) t where total_thb_amount > $4
  `
  ```

- Rule1038 Transaction comes from suspicious location Yala/Pattani/Narathiwat
  
  ```sql
  const Query103_8 = `
  select *
      from (
          select ld.tx_id,
          ld.from_identity_id,
          ld.to_identity_id,
          ld.from_customer_id,
          ld.to_customer_id,
          ld.from_address,
          ld.to_address,
          ld.from_wallet_id,
          ld.to_wallet_id,  
          ld.from_customer_type,
          ld.to_customer_type,
          ld.from_customer_name,
          ld.to_customer_name, 
          ld.last_approver_id,    
          ld.transaction_type,
          ld.tx_created_at,
          ld.offchain,
          ld.network_id,
          ld.network_name,
          ld.asset_id,
          ld.asset_name,
          ld.asset_type,
          ld.amount,
          ld.thb_amount
      from success_tx_reports_view ld
      inner join blacklisted_ip_transactions bitxn on 
          (bitxn.city = ld.city_maker and bitxn.country_code = ld.country_code_maker) 
       or (bitxn.city = ld.city_approver and bitxn.city = ld.country_code_maker)
      where ld.transaction_status = 'completed' and ld.tx_created_at >= $1 and ld.tx_created_at < $2
      order by ld.tx_created_at desc
      ) t where 1=1
  `
  ```

- Rule10310 Customer withdraw accumulated >= 90% of total value(after transaction) per coin within 1 hour
  
  ```sql
  const Query103_10 = `
  -- Return all transactions in 1-hour windows where total sent ≥ 90% of asset balance,
  -- excluding flagged transactions with reason_id '103-10'
  SELECT DISTINCT
      -- all_tx.*
      all_tx.tx_id,
      all_tx.from_identity_id,
      all_tx.to_identity_id,
      all_tx.from_customer_id,
      all_tx.to_customer_id,
      all_tx.from_address,
      all_tx.to_address,
      all_tx.from_wallet_id,
      all_tx.to_wallet_id,  
      all_tx.from_customer_type,
      all_tx.to_customer_type,
      all_tx.from_customer_name,
      all_tx.to_customer_name, 
      all_tx.last_approver_id,
      all_tx.transaction_type,
      all_tx.tx_created_at,
      all_tx.offchain,
      all_tx.network_id,
      all_tx.network_name,
      all_tx.asset_id,
      all_tx.asset_name,
      all_tx.asset_type,
      all_tx.amount,
      all_tx.thb_amount,
      all_tx.asset_total_balance
  FROM (
      -- Step 1: Identify all windows where total_sent_1h meets the threshold
      SELECT DISTINCT
          t.from_customer_id,
          t.asset_id,
          -- Calculate 1-hour window boundaries for each triggering transaction
          t.tx_created_at - INTERVAL '1 hour' AS window_start,
          t.tx_created_at AS window_end,
          t.asset_total_balance  -- Need balance to verify threshold for window transactions
      FROM (
          -- Subquery: Calculate 1-hour rolling sum for each transaction
          SELECT 
              *,
              SUM(amount) OVER (
                  PARTITION BY from_customer_id, asset_id
                  ORDER BY tx_created_at 
                  RANGE BETWEEN INTERVAL '1 hour' PRECEDING AND CURRENT ROW
              ) as total_sent_1h
          FROM success_tx_reports_view
          WHERE transaction_status = 'completed' 
            AND transaction_type = 'Sent'
      ) t
      -- Filter: Only windows where total meets 90% balance threshold and time range
      WHERE total_sent_1h >= (t.asset_total_balance * 0.9)
        AND t.tx_created_at >= $1 
        AND t.tx_created_at < $2
  ) threshold_windows
  -- Step 2: Get all transactions within these qualifying windows
  JOIN success_tx_reports_view all_tx
      ON all_tx.from_customer_id = threshold_windows.from_customer_id
      AND all_tx.asset_id = threshold_windows.asset_id
      AND all_tx.transaction_status = 'completed'
      AND all_tx.transaction_type = 'Sent'
      -- Ensure transaction falls within the 1-hour window
      AND all_tx.tx_created_at BETWEEN threshold_windows.window_start 
                                   AND threshold_windows.window_end
  -- Step 3: Exclude transactions flagged with the same reason_id '103-10'
  LEFT JOIN flag_transactions ft
      ON all_tx.tx_id = ft.transaction_id
      AND ft.reason_id = '103-10'
  WHERE ft.transaction_id IS NULL  -- Keep only unflagged transactions
  ORDER BY all_tx.tx_created_at DESC
  `
  ```

- Rule10311 Withdraw same network >= 10 times per day(00:00-23:59) and withdraw to all different wallet addresses (need to be 10 different wallet)
  
  ```sql
  const Query103_11 = `
  SELECT *
  FROM (
      SELECT 
          ld.tx_id,
          ld.from_customer_id,
          ld.from_identity_id,
          ld.to_identity_id,
          ld.to_customer_id,
          ld.from_address,
          ld.to_address,
          ld.from_wallet_id,
          ld.to_wallet_id,  
          ld.from_customer_type,
          ld.to_customer_type,  
          ld.from_customer_name,
          ld.to_customer_name, 
          ld.last_approver_id,
          ld.transaction_type,
          ld.tx_created_at,
          ld.offchain,
          ld.network_id,
          ld.network_name,
          ld.asset_id,
          ld.asset_name,
          ld.asset_type,
          ld.amount,
          ld.thb_amount,
          (
              SELECT COUNT(DISTINCT sub.to_address)
              FROM success_tx_reports_view sub
              WHERE sub.from_customer_id = ld.from_customer_id
                AND sub.network_id = ld.network_id
                AND sub.transaction_status = 'completed'
                AND sub.transaction_type = 'Sent'
                AND sub.tx_created_at >= $1
                AND sub.tx_created_at < $2
          ) as unique_wallet_count_per_network
      FROM success_tx_reports_view ld 
      WHERE ld.transaction_status = 'completed' 
        AND ld.transaction_type = 'Sent' 
        AND ld.tx_created_at >= $1 
        AND ld.tx_created_at < $2
  ) t 
  WHERE t.unique_wallet_count_per_network >= 10
  ORDER BY t.from_customer_id, t.tx_created_at
  `
  ```

**Requirement**

we need to update current STR rule for 1-03 report:  
STR = 1-02, 1-03 reports.

With current STR: 1-03 job, it run a job to generate at night (we can use the same schedule).

1.Withdraw accumulated >= 90% of total balance(after transaction finished) per coin within 1 hour,

percentage = accumulate amount / remaining balance.

**New rule:**

In Wallet level,

- If has at least 1 received transaction, and withdraw accumulated >= 90% of (total balance before 1 hr + Accumulated Received assets) per coin within 1 hour, then 1-03 report with reason "มีรายการฝากและทำการถอนออกจากบัญชีมูลค่าตั้งแต่ 90% ของเหรียญทันทีภายใน 1 ชั่วโมง".
- If no received transaction, then no need generate report.

Calculation of accumulated pecentage:

**percentage** = (Accumulate Withdraw amount /  (total balance before 1 hr + Accumulate Receive)) per coin per wallet per hour

**Explaination:**

to gen STR report with this rule, 

1. Wallet level

2. Per coin

3. Within 1h

4. If it’s received tx, no need generate report

5. If it's accumulated withdraw tx >= 90% of before 1 hr balance + accumulated received amount,
- look back 1h, if no received tx, no need generate report.

- look back 1h, if has the received tx, generate report.

**Acceptance Criteria:**

1. If has received transations and withdraw accumulated >= 90% of (total balance before 1 hr + Accumulated Received assets) per coin within 1 hour, system should generate 1-03 report with reason "มีรายการฝากและทำการถอนออกจากบัญชีมูลค่าตั้งแต่ 90% ของเหรียญทันทีภายใน 1 ชั่วโมง".
2. If no received transaction in the time window(1h), no need generate the 1-03 report with this rule above.

=============

**Examples:**

Case 1

coin balance in the wallet: 6  
within 1h:

#1 transfer 2, remain: 4

#2 received 6, remain: 10

#3 transfer 10, remain 0

percentage = 12/(6+6) -> 100% -> generate report

Case 2

coin balance in the wallet: 8  
within 1h:

#1 received 6, remain: 14  
#2 transfer 2, remain: 12

#3 transfer 10, remain 2

percentage = 12/(6+8) -> 85% -> not generate

Case 3

coin balance in the wallet: 8  
within 1h:

#1 received 6, remain: 14  
#2 transfer 2, remain: 12

#3 transfer 11, remain 2

percentage = 13/(8+6) -> 92% generate report

Case 4  
coin balance in the wallet: 0  
within 1h:

#1 received 10, remain: 10  
#2 transfer 8, remain: 2

#3 transfer 2, remain 0

% = 10/10 -> 100% generate report

Case 5  
coin balance in the wallet: 0  
within 1h:

#1 received 10, remain: 10  
#2 transfer 8, remain: 2

#3 transfer 1, remain 0

% = 9/10 = 90% generate report

Case 6

coin balance in the wallet: 0  
within 1h:  
#1 received 10, remain: 10  
#2 transfer 8, remain: 2

#3 received 7, remain: 9

#4 transfer 8, remain 1

% = 16/17 = 94% generate report

Case 7  
coin balance in the wallet: 10  
within 1h:

#1 transfer 8, remain: 2  
#2 received 10, remain: 12  
#3 transfer 4, remain 8

% = 12/20 = 60% not generate report

Case 8  
coin balance in the wallet: 10  
within 1h:

#1 transfer 10, remain: 0

% = 10/10 = 100% not generate report, cuz no received tx

现在这个告警规则需要更新，详细如下：

- asset_total_balance 的值存储为交易前的当前wallet 当前asset的balance

- transaction_type 需要同时考虑“Sent”和"Received"，两种情况，同一个tx_id 可能有2个纪录（Sent 对应from_customer_id/Received对应to_customer_id），所以累加前要过滤相同的tx_id

- 规则改为如果当前是 “Sent” 交易时，统计前一小时的“Sent”累加值amount为转出额，前一小时内第一条交易的asset_total_balance 为前总额，前一小时内转入总amount为转入额（转入交易包含received交易和Sent交易的to_customer_id等于当前交易的from_customer_id两者对相同tx_id去重得到的所有交易），如果 转出额 >= （前总额+转入额）的90，就返回所有交易（tx_id 唯一），且如果前一小时转入额是0的话，就不不返回任何交易
