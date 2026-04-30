## Talos Doc

**<u>Username</u>**: orbix

**<u>Password</u>**: mBVUqfulKVHwbV0c

**<u>Link: </u>** [<u>https://docs.talostrading.com/</u>](https://docs.talostrading.com/ "https://docs.talostrading.com/")

### testneet

TALOS_HOST=tal-324.sandbox.talostrading.com

TALOS_API_KEY=ORBRLRKDLNM4

TALOS_API_SECRET=iw5vpwoj6mxlxi9sidjph8iad0hhteu8

wget doc

:

wget --mirror --convert-links --adjust-extension --page-requisites --no-parent --user=orbix --password=mBVUqfulKVHwbV0c  https://docs.talostrading.com/

python3 -m http.server 9000

### config

##### event private

```env
priv: c217ad23e5b8d400cfc8d2c3a857a9dbf876f03f88dd9f1115180917862b8414
pub: 03d6b429e9c274de309861a5397e0c045dfd17cd68be22992b12be451ab0e11f4c
```

##### config

```env
BROKER_DB_ADDR=postgres.kxchain.co:5432
BROKER_DB_USER=postgres
BROKER_DB_PASSWORD=aa31sqqad
BROKER_DB_NAME=broker_dev
BROKER_DB_APPNAME=broker-core

BROKER_REDIS_HOST=redis.kxchain.co
BROKER_REDIS_PORT=6380
BROKER_REDIS_PASSWORD=eYVX7EwVmmxKPCDmwMtyKVge8oLd2t81
BROKER_REDIS_DB=12
BROKER_REDIS_POOLSIZE=10

BROKER_MQ_HOST=rabbitmq.kxchain.co
BROKER_MQ_PORT=5672
BROKER_MQ_USER=kt-test
BROKER_MQ_PASSWORD=sada1owe34
BROKER_MQ_ENABLE_TLS=false
BROKER_MQ_TLS_CA_CERT_PATHS=
BROKER_MQ_TLS_CERT_PATH=
BROKER_MQ_TLS_KEY_PATH=
BROKER_MQ_TLS_SERVER_NAME=

BROKER_MARKET_MARKETS=coinbase
BROKER_MARKET_SYMBOLS=BTC-USD,ETH-USD
BROKER_MARKET_ENABLE_WS=true

BROKER_ORDER_COUNTERPARTY=Plat tier
BROKER_ORDER_TRANSACTOR_MQ_CREATE_EXCHANGE=brokers
BROKER_ORDER_TRANSACTOR_MQ_CREATE_ROUTING_KEY=brokers.%s.creates
BROKER_ORDER_UPDATER_MQ_BASE_CREATED_ROUTING_KEY=brokers.%s.created
BROKER_ORDER_UPDATER_MQ_BASE_SUCCESS_ROUTING_KEY=brokers.%s.success
BROKER_ORDER_UPDATER_MQ_BASE_FAILED_ROUTING_KEY=brokers.%s.failed
BROKER_ORDER_UPDATER_MQ_BASE_TRADE_CREATED_ROUTING_KEY=brokers.trade.%s.created
BROKER_ORDER_UPDATER_MQ_EXCHANGE=brokers
BROKER_ORDER_UPDATER_MQ_PRIVATE_KEY=c217ad23e5b8d400cfc8d2c3a857a9dbf876f03f88dd9f1115180917862b8414


BROKER_TALOS_URL=https://tal-324.sandbox.talostrading.com
BROKER_TALOS_WEB_URL=wss://tal-324.sandbox.talostrading.com/ws/v1
BROKER_TALOS_MARKET_URL=https://sandbox.talostrading.com
BROKER_TALOS_API_KEY=ORBRLRKDLNM4
BROKER_TALOS_SECRET=iw5vpwoj6mxlxi9sidjph8iad0hhteu8
```

```
bson.M{
        "$and": 
            {"_id": bson.M{"$in": ids}},
            {"_id": bson.M{"$ne": template.ID}},
        },
        "slug": template.Slug,
        "$expr": bson.M{
            "$not": bson.M{
                "$or": []bson.M{
                    {
                        "$lt": []any{maxNumber, "$start_at_number"},
                    },
                    {
                        "gt": []any{template.StartAtNumber, bson.M{"$add": []any{"$start_at_number", "$max_supply"}}},
                    },
                },
            },
        },
    }
```

## custodian

### test account

https://kasikornbankgroup.sharepoint.com/:x:/r/sites/Custodian-API-Integration/_layouts/15/Doc.aspx?sourcedoc=%7B65BBB705-C065-425B-83EF-34E96878AA1D%7D&file=Orbix%20Custodian%20Testing%20Accounts.xlsx&action=default&mobileredirect=true

[Orbix Custodian Testing Accounts.xlsx](https://kasikornbankgroup.sharepoint.com/sites/Custodian-API-Integration/Shared%20Documents/General/Testing/Orbix%20Custodian%20Testing%20Accounts.xlsx)

### orbix: 5875

/customer-managements/ba191751-9a5b-4028-a472-bc26ce13b37f/freeze

#### DC-5898

Root Cause:

- The front-end system automatically appends excessive trailing zeros to the input amount.

- The back-office system processes these values by truncating meaningless trailing zeros (after the decimal point), but preserves any significant digits. Examples:
  
  - "4.000000000000000000" → "4"
  
  - "4.000000000000000001" → "4.000000000000000001"
  
  - "4.001000000000000000" → "4.001"

Solution:

- The front-end should be updated to avoid appending unnecessary trailing zeros to numeric inputs.

### aws

- cloudwatch

```sql
fields @timestamp,log
| filter (kubernetes.container_name == 'go-backoffice' and kubernetes.namespace_name == 'orbixcustodian-internal-dev' )
| sort @timestamp desc
| limit 10000
```

```sql
fields @timestamp, @message, @logStream, @log
| filter objectRef.namespace="orbixcustodian-internal-dev"
# | filter `user.extra.authentication.kubernetes.io/pod-name.0` like "go-backoffice"
| filter requestObject.status.containerStatuses.0.name="go-backoffice"
| sort @timestamp desc
| limit 100



fields @timestamp,log
| filter (kubernetes.container_name == 'go-backoffice' and kubernetes.namespace_name == 'orbixcustodian-internal-sit' and @message like 'update_corporate_status')
| sort @timestamp desc
| limit 10000

fields @timestamp,log
| filter (kubernetes.container_name == 'go-cronjob' and kubernetes.namespace_name == 'orbixcustodian-internal-sit' and log_processed.api_endpoint like 'get_corporate_info' and log_processed.request like '1234567891090')
| sort @timestamp desc
| limit 10000


fields @timestamp,log
| filter (kubernetes.container_name == 'go-cronjob' and kubernetes.namespace_name == 'orbixcustodian-internal-prod' and log_processed.api_endpoint like 'get_corporate_info' and log_processed.request like '9999999999001')
| sort @timestamp desc
| limit 10000


fields @timestamp,log
| filter (kubernetes.container_name == 'go-cronjob' and kubernetes.namespace_name == 'orbixcustodian-internal-sit' and log_processed.job_name=='ReportGeneration')
| sort @timestamp desc
| limit 10000


fields @timestamp,log
| filter (kubernetes.container_name == 'go-cronjob' and kubernetes.namespace_name == 'orbixcustodian-internal-sit' and log like 'get_corporate_info')
| sort @timestamp desc
| limit 10000
```

- EC2-instance:(OrbixCustodian-Internal-Sit-BastionHost->会话管理器)kubectl

`sudo su ec2-user`

OrbixCustodian-Internal-Sit-BastionHost

- postgres price dev
  
  ```shell
    psql --host=orbixcustodian-internal-sit-cluster.cluster-cvtpgc1bvvqu.ap-southeast-1.rds.amazonaws.com --port=5432 --username=ro_price_dev --password --dbname=dev_price
  ```

- postgres backoffice dev:
  
  ```shell
  psql --host=orbixcustodian-internal-sit-cluster.cluster-cvtpgc1bvvqu.ap-southeast-1.rds.amazonaws.com --port=5432 --username=admin_backoffice_dev --password --dbname=dev_backoffice
  ```

- postgres price sit
  
  ```shell
    psql --host=orbixcustodian-internal-sit-instance.cvtpgc1bvvqu.ap-southeast-1.rds.amazonaws.com --port=5432 --username=ro_price_sit --password --dbname=price
  ```

- postgres sit: 

```shell
  psql --host=orbixcustodian-internal-sit-instance.cvtpgc1bvvqu.ap-southeast-1.rds.amazonaws.com --port=5432 --username=internal_admin --password --dbname=backoffice
```

- UAT backoffice:

```plsql
  psql --host=orbixcustodian-internal-uat-cluster.cluster-cvtpgc1bvvqu.ap-southeast-1.rds.amazonaws.com --port=5432 --username=internal_admin_uat --password --dbname=backoffice
```

UPDATE tokens SET status='A' WHERE token_id=46;

select report_detail_id,reason_id,reason_params,tx_created_date from flag_transactions where report_name='1-03' order by tx_created_date desc limit 20;select customer_id,report_detail_id,reason_id,reason_params,tx_created_date from flag_transactions where report_name='1-03' and reason_id='103-6' order by tx_created_date desc limit 20;

select id,customer_id,identity_id,preferred_language,wallet_id,wallet_type,month_of_report,created_at,COALESCE(transaction_report_url, '') as transaction_report_url,COALESCE(wallet_report_url, '') as wallet_report_url,report_generated_at from monthly_reports where email_sent_at is null and month_of_report = '202508';

backoffice=> select * from flag_transactions where reason_id='103-6' order by tx_created_date desc limit 20;
    id     | report_detail_id |   transaction_id    |        tx_created_date        |             customer_id              |      customer_name       |      fraud_category       |                                               reason
                                          | report_name |          created_at           |                                                                  attachment                                                                  |
          from_address                |                 to_address                 | transaction_type | amount | thb_amount |         asset         | reason_id |                                        reason_params
                 |            from_wallet_id            | to_wallet_id
-----------+------------------+---------------------+-------------------------------+--------------------------------------+--------------------------+---------------------------+-----------------------------------------------------------
------------------------------------------+-------------+-------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+------
--------------------------------------+--------------------------------------------+------------------+--------+------------+-----------------------+-----------+-----------------------------------------------------------------------------
-----------------+--------------------------------------+--------------
 ppP2hhrHR | 2EYhh2rNg        | 1963531188354158592 | 2025-09-04 09:14:50.606618+00 | 16aea2c4-be72-47a4-88b8-326fa75e703f | Foundation forr Children | Suspicious Fraud Activity | Customer deposited 6 USDT Ethereum Sepolia eq to 194.52 TH
B more than 10x of their income  0 THB    | 1-03        | 2025-09-05 01:01:44.286319+00 | {"url": "/reports/1963531188354158592_20250905080002_1757034104237.pdf", "fileName": "1963531188354158592_20250905080002_1757034104237.pdf"} | 0x14D
84d972fe2235e57996A601Ef17C97eB8955A9 | 0x900540a706D54837701CA52153A842525897c09a | Received         |      6 |     194.52 | USDT Ethereum Sepolia | 103-6     | {"token": "USDT Ethereum Sepolia", "amount": "6", "priceThb": "194.52", "inc
omeThb": "0"}    | ff831346-736b-485c-a28d-53fbcd5fd5cf |
 ecPh22rHg | 2EYhh2rNg        | 1963508166272487424 | 2025-09-04 07:43:21.714454+00 | 16aea2c4-be72-47a4-88b8-326fa75e703f | Foundation forr Children | Suspicious Fraud Activity | Customer deposited 0.01 ETH Sepolia eq to 1,416.3336 THB m
ore than 10x of their income  0 THB       | 1-03        | 2025-09-05 01:01:44.286319+00 | {"url": "/reports/1963508166272487424_20250905080002_1757034084835.pdf", "fileName": "1963508166272487424_20250905080002_1757034084835.pdf"} | 0x14D
84d972fe2235e57996A601Ef17C97eB8955A9 | 0x900540a706D54837701CA52153A842525897c09a | Received         |   0.01 |  1416.3336 | ETH Sepolia           | 103-6     | {"token": "ETH Sepolia", "amount": "0.01", "priceThb": "1,416.3336", "income
Thb": "0"}       | ff831346-736b-485c-a28d-53fbcd5fd5cf |

### 1036/1037

```sql
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
    from success_transaction_reports_view ld where ld.transaction_status = 'completed' and ld.tx_created_at >= $1 and ld.tx_created_at < $2 and ld.transaction_type = $3
     window w as (
              partition by ld.from_customer_id
              range between unbounded preceding and unbounded following )
    ) t where total_thb_amount > $4
, params: [2025-09-04 00:00:00+07:00 2025-09-05 00:00:00+07:00 Received 0]
```

```sql
select * from flag_transactions where reason_id='103-6' order by tx_created_date desc limit 20;
```

```sql
select * from schema_migrations;
```

backoffice=> select from_customer_id,to_customer_id,transaction_type from success_transactions where tx_id='1963194038899183616';
           from_customer_id           |            to_customer_id            | transaction_type
--------------------------------------+--------------------------------------+------------------
 985e4b6f-b07c-4fe6-92cb-8ea81e459e31 | 985e4b6f-b07c-4fe6-92cb-8ea81e459e31 | Sent
(1 row)

### monthly report regeneration steps:

1. update backoffice db to reset the monthly report data

```sql
UPDATE public.monthly_report_customer_sync_progresses SET finished_at = NULL WHERE month_of_report = '202508';
select * from monthly_report_customer_sync_progresses order by month_of_report desc;

update public.monthly_reports set month_of_report='202508-legacy' where month_of_report='202508';
```

2. update cicd_configs in configuration-go-cronjob to retriger the job

```yaml
  monthly_report_customer_sync: "30 09 11 * *"
  monthly_report_customer_sender: "00 10 11 * *"
```

### go-cronjob api list

| Service Name        | Method | host                                             | API                                                        | Secret Name                   |
| ------------------- | ------ | ------------------------------------------------ | ---------------------------------------------------------- | ----------------------------- |
| price-cronjob       | POST   | https://internal-api.internal.orbixcustodian.com | /price-cron/cron-expression                                | INTERNAL_API_KEY              |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/internal_system/wallet/list                            | ORBIX_CORE_API_KEY            |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/internal_system/wallet_asset/list                      |                               |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/customers/search                                       |                               |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/notify/create                                          |                               |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/internal_system/monthly/customer_wallet/list           |                               |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/customers/admin/update_customer                        |                               |
| core                | POST   | https://core-api.internal.orbixcustodian.com     | /v1/internal_system/wallet/list                            |                               |
| go-price-api        | POST   | https://internal-api.internal.orbixcustodian.com | /price/crypto-price                                        | INTERNAL_API_KEY              |
| go-price-api        | POST   | https://internal-api.internal.orbixcustodian.com | /price/exchange-rate                                       |                               |
| kyc                 | POST   | https://kyc-api.internal.orbixcustodian.com      | /api/additional_investor_info/v1.3/get_additional_info     | KYC_API_KEY                   |
| kyc                 | POST   | https://kyc-api.internal.orbixcustodian.com      | /api/corporate/v1.3/get_corporate_info                     |                               |
| kyc                 | POST   | https://kyc-api.internal.orbixcustodian.com      | /api/corporate/v1.3/get_business_type                      |                               |
| kyc                 | POST   | https://kyc-api.internal.orbixcustodian.com      | /api/business/corporate_list                               |                               |
| cvrs                | POST   | https://cvrs-api.internal.orbixcustodian.com     | /sanction-ws/bulk/checkSanction                            | CVRS_BASIC_AUTH               |
| cvrs                | POST   | https://cvrs-api.internal.orbixcustodian.com     | /kyc-ws/bulk/calculate                                     | CVRS_VALIDATE_RISK_BASIC_AUTH |
| jreporter           | POST   | https://internal-api.internal.orbixcustodian.com | /report-request/group-retry/%groupId?async=true&force=true | JREPORT_API_KEY               |
| go-notification-api | POST   | https://internal-api.internal.orbixcustodian.com | /notification/api/v1/notify?type=email                     | INTERNAL_API_KEY              |
|                     |        |                                                  |                                                            |                               |
|                     |        |                                                  |                                                            |                               |
|                     |        |                                                  |                                                            |                               |

#### How to check api token

- for internal service api(host=[https://internal-api.internal.orbixcustodian.com](https://internal-api.internal.orbixcustodian.com))
  
  request sample:
  
  ```firestore-security-rules
  curl --location --request POST 'https://internal-api-dev.test.internal.orbixcustodian.com/price-cron/cron-expression' \
  --header 'OBX-API-KEY: abcd'
  ```
  
  invalid token response:
  
  ```json
  {
      "message": "Invalid API Key. Must pass a valid API Key header using OBX-API-KEY: $key",
      "errorCode": "forbidden"
  }
  ```

- for core service api(host=[https://core-api.internal.orbixcustodian.com](https://core-api.internal.orbixcustodian.com)):
  
  request sample:
  
  ```firestore-security-rules
  curl --location 'https://core-api-sit.test.internal.orbixcustodian.com/v1/customers/search' \
  --header 'OBX-ACCESS-KEY:xxx' \
  --header 'Content-Type: application/json' \
  --data '{
      "customer_ids": [
          "",
          "985e4b6f-b07c-4fe6-92cb-8ea81e459e31"
      ],
  
      "limit": 2
  }'
  ```
  
  invalid token response:
  
  401 Unauthrized

- for kyc service api
  
  request sample:
  
  ```curl
  curl --location 'https://kyc-api-sit.test.orbixcustodian.com/api/business/corporate_list' \
  --header 'OBX-ACCESS-KEY: abcd' \
  --header 'Content-Type: application/json' \
  --data '{
      "status":"active",
      "registration_number":"1232025082002"
  }
   '
  ```
  
  invalid token response:
  
  ```json
  {
      "status": 400,
      "code": "invalid_api_key",
      "msg": "validateApiKey failed",
      "data": null
  }
  ```

- for cvrs api(host=https://cvrs-api.internal.orbixcustodian.com)
    checkSanction request sample:
  
  ```firestore-security-rules
  curl --location --request POST 'https://cvrs-api-sit.test.internal.orbixcustodian.com/sanction-ws/bulk/checkSanction' \
  --header 'Authorization: Basic CVRS_BASIC_AUTH       
  ```
  
    validate risk request sample:
  
  ```firestore-security-rules
  curl --location --request POST 'https://cvrs-api-sit.test.internal.orbixcustodian.com/kyc-ws/bulk/calculate' \
  --header 'Authorization: Basic CVRS_VALIDATE_RISK_BASIC_AUTH'   
  ```
  
    invalid token response:
     401 Unauthrized

- jreporter api(https://internal-api.internal.orbixcustodian.com/report-request/group-retry/%groupId?async=true&force=true)
  
  request sample:
  
  ```firestore-security-rules
  curl --location --request POST 'https://internal-api-dev.test.internal.orbixcustodian.com/report-request/group-retry/123456?async=true&force=true' \
  --header 'OBX-API-KEY: abcd'
  ```
  
  invalid token response:
   401 Unauthrized

```sql
SELECT *
FROM (
    SELECT 
        t.*,
        -- 对于接收交易：计算该客户作为接收方的一小时内接收总量
        CASE WHEN t.transaction_type = 'Received' THEN
            SUM(t.amount) OVER (
                PARTITION BY t.to_customer_id, t.asset_id 
                ORDER BY t.tx_created_at 
                RANGE BETWEEN INTERVAL '1 hour' PRECEDING AND CURRENT ROW
            )
        END as received_1h,

        -- 对于发送交易：计算该客户作为发送方的一小时内发送总量
        CASE WHEN t.transaction_type = 'Sent' THEN
            SUM(t.amount) OVER (
                PARTITION BY t.from_customer_id, t.asset_id 
                ORDER BY t.tx_created_at 
                RANGE BETWEEN INTERVAL '1 hour' PRECEDING AND CURRENT ROW
            )
        END as sent_1h,

        -- 余额应该根据交易类型选择正确的客户
        CASE WHEN t.transaction_type = 'Received' THEN 
            (SELECT balance FROM wallet_balances WHERE customer_id = t.to_customer_id AND asset_id = t.asset_id)
        ELSE 
            (SELECT balance FROM wallet_balances WHERE customer_id = t.from_customer_id AND asset_id = t.asset_id)
        END as relevant_balance
    FROM success_transaction_reports_view t
    WHERE t.transaction_status = 'completed' 
    AND t.tx_created_at >= $1 
    AND t.tx_created_at < $2
) subquery
WHERE (COALESCE(received_1h, 0) + COALESCE(sent_1h, 0)) > (relevant_balance * 0.9)
```

## Cron Job

| service       | job name                                                                                                   | type                                                        | description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | source                                                                                                                                                                           | others |
| ------------- | ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| price-cronjob | sync_coin_bo                                                                                               | db.orbix_bo_cron_expression<br/>duration<br/>10m            | get orbix token_config/active_fiat_money configuration every 10 minutes and store to local cache                                                                                                                                                                                                                                                                                                                                                                                                                                | https://kscm.kasikornbank.com:8443/10004/backend/go-price-cronjob/-/blob/main/internal/orbix_config/cronjob.go?ref_type=heads#L84                                                |        |
| price-cronjob | cmc_sync_price                                                                                             | db.cmc_cron_expression<br/>duration<br/>1m                  | get token pricing from CoinMarketCap every minute<br/>get latest exchage rate(is_ready=true)  and calculate THB price <br/>store all price  to db.crypto_price                                                                                                                                                                                                                                                                                                                                                                  | https://kscm.kasikornbank.com:8443/10004/backend/go-price-cronjob/-/blob/main/internal/cmc/cronjob.go?ref_type=heads#L48                                                         |        |
| price-cronjob | cmc_sync_price_daily                                                                                       | db.cmc_daily_cron_expression<br/>expression<br/>55 23 * * * | get token pricing from cmc every day and store to db.daily_crypto_price                                                                                                                                                                                                                                                                                                                                                                                                                                                         | https://kscm.kasikornbank.com:8443/10004/backend/go-price-cronjob/-/blob/main/internal/cmc/daily_sync_job.go?ref_type=heads#L62                                                  |        |
| price-cronjob | bot_sync_price                                                                                             | db.bot_cron_expression<br/>expression<br/>0 19 * * *        | get average rate（USD-THB） of the previous 7 days every day and store latest exchange rate/date to db.exchange_rate                                                                                                                                                                                                                                                                                                                                                                                                              | https://kscm.kasikornbank.com:8443/10004/backend/go-price-cronjob/-/blob/main/internal/bot/cronjob.go?ref_type=heads#L60                                                         |        |
| price-cronjob | bot_apply_exchange_rate                                                                                    | db.bot_apply_cron_expression<br/>expression<br/>00 08 * * * | update previous date's exchange_rate and set is_ready = true every day to apply exchange rate                                                                                                                                                                                                                                                                                                                                                                                                                                   | https://kscm.kasikornbank.com:8443/10004/backend/go-price-cronjob/-/blob/main/internal/bot/job_apply_rate_bot.go?ref_type=heads#L64                                              |        |
| cron-job      | Release Customer                                                                                           | config: sa_release_customer<br/>* * * * *                   | update job_release_customer=processing<br/>call core api to release customer<br/>update job_release_customer=succeed                                                                                                                                                                                                                                                                                                                                                                                                            | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/service_agreement/adapter/job/job_release_customer.go?ref_type=heads#L52                        |        |
| cron-job      | TimeManagementCron                                                                                         | 0 * * * *                                                   | call /price-cron/cron-expression udpate cmc bitkup cron time<br/>update token_crawler_conf_approvals synced=true                                                                                                                                                                                                                                                                                                                                                                                                                | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/conf_management/token_crawler_scheduler/service/cron_service.go?ref_type=heads#L33              |        |
| cron-job      | CoinManagementCron                                                                                         | 0 * * * *                                                   | get approved data from draft_tokens<br/>update approved data to tokens<br/>update syncedAt in draft_tokens                                                                                                                                                                                                                                                                                                                                                                                                                      | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/token/service/cron_service.go?ref_type=heads#L31                                                |        |
| cron-job      | RetailFeeCron                                                                                              | 0 * * * *                                                   | get approved data from draft_retail_fees<br/>update approved data to retail_fees<br/>update syncedAt in draft_retail_fees                                                                                                                                                                                                                                                                                                                                                                                                       | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/retail_fee/service/cron_service.go?ref_type=heads#L32                                           |        |
| cron-job      | CurrencyManagementCron                                                                                     | 0 * * * *                                                   | get approved data from draft_currencies<br/>update approved data to currencies<br/>update syncedAt in draft_currencies                                                                                                                                                                                                                                                                                                                                                                                                          | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/currency/service/cron_service.go?ref_type=heads#L31                                             |        |
| cron-job      | ReportGeneration                                                                                           | config: report_generation<br/>0 0 * * *                     | check 102/103 rule and generate report with jreport template                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/report/report_generation/service/cron_service.go?ref_type=heads#L24                             |        |
| cron-job      | MonthlyCustomerFeeSync<br/>FeeCalculationCron                                                              | config: auc_fee_generator<br/>00 05 * * *                   | get daily price from price-api<br/>get latest exchange-rate from price-api<br/>get customer list from core-api<br/>create daily job record fee_calculation_jobs<br/>get customer wallet list from core-api<br/>get/calculate asset list value<br/>get customer fee tiers from corporate_subscription_v2<br/>calculate all fees<br/>insert fees into fee_calculation_auc<br/>update status/generated_at into fee_calculation_jobs                                                                                                | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/fee_calculation/service/auc_fee_cron_service.go?ref_type=heads#L74                              |        |
| cron-job      | NotifyOfDowntimeInTx<br/>ExternalNotification                                                              | * * * * *                                                   | get notify from downtimes/tokens_network/marketings/external_notifications<br/>call core api to create notify<br/>update notify result into external_notifications                                                                                                                                                                                                                                                                                                                                                              | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/external_notification/cron.go?ref_type=heads#L25                                                |        |
| cron-job      | MonthlyReportSenderCron<br/>(depends on MonthlyReportCustomerSync)                                         | config:monthly_report_customer_sender<br/>00 07 05 * *      | get pre month reports in monthly_reports<br/>get customer info from core api<br/>get customer email from kyc api<br/>sent email notify with transaction-report-200601.pdf/wallet-report-200601.pdf<br/>update monthly_reports email_sent_at                                                                                                                                                                                                                                                                                     | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/monthly_report_sender/service/cron_service.go?ref_type=heads#L42                                |        |
| cron-job      | MonthlyReportCustomerSync                                                                                  | config:monthly_report_customer_sync<br/>00 06 05 * *        | create pre month into monthly_report_customer_sync_progresses<br/>get customer list from core api<br/>create pre month into monthly_reports for every customer<br/>update monthly_report_customer_sync_progresses finishedAt<br/>get every pending request from monthly_reports<br/>get monthly tx from core api <br/>get wallet balance from core api/price api(calculate thb total)<br/>load report config from report_configurations<br/>call jreport to generate report and return url<br/>update result to monthly_reports | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/monthly_report_customer_sync/cron.go?ref_type=heads#L38                                         |        |
| cron-job      | Customer Wallet sync for Reconcile Report                                                                  | config:reconcile_report_customer_sync<br/>00 01 * * *       | get wallet list from core api /v1/internal_system/wallet_assets/reconcile<br/>insert wallet balance into reconcile_balance                                                                                                                                                                                                                                                                                                                                                                                                      | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/reconcile_report_customer_sync/cron.go?ref_type=heads#L29                              |        |
| cron-job      | ReconcileReportCron<br/>(depends on "Customer Wallet sync for Reconcile Report"/"SyncReconcileFailReport") | config:reconcile_report_generator<br/>00 02 * * *           | get tx from reconcile_balance/reconile_fail_transactions<br/>get exchange rate from price api<br/>create reconcile-by-balance-%s.csv/reconcile-fail-transaction-%s.csv<br/>upload csv to s3<br/>create Reconcile Report with url into report_details                                                                                                                                                                                                                                                                            | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/reconcile_report_generator/service/reconcile_report_cron_service.go?ref_type=heads#L49 |        |
| cron-job      | SyncReconcileFailReport                                                                                    | config: reconcile_fail_report_sync<br/>00 01 * * *          | get failed transaction from core api<br/>insert into reconile_fail_transactions                                                                                                                                                                                                                                                                                                                                                                                                                                                 | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/reconcile_fail_report_sync/cron.go?ref_type=heads#L29                                  |        |
| cron-job      | Corporate Fee Management                                                                                   | 0 * * * *                                                   | get approved data from draft_corporate_fee_package_details<br/>update fees into corporate_fee_package_details<br/>update draft_corporate_fee_package_details                                                                                                                                                                                                                                                                                                                                                                    | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/corporate_fee/service/cron_service.go?ref_type=heads#L31                               |        |
| cron-job      | Customer sync for Monthly Report<br/>(MonthlyCustomerFeeSync)                                              | config:publish_message_transaction_fee<br/>00 01 * * *      | insert into job_monitor_detail every day<br/>insert into job_monitor with monthly_summarize_fee every day<br/>insert into report_details with "Summarize Fee Report" every day<br/>publish to transaction_sync job id/range everey day                                                                                                                                                                                                                                                                                          | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/publish_transaction_fee/cron.go?ref_type=heads#L49                                     |        |
| cron-job      | CVRS check sanction                                                                                        | config:cvrs_check_sanction<br/>00 01 * * *                  | get customer list from core api<br/>get kyc data from kyc api<br/>check sanctions with cvrs api<br/>upsert customer_process <br/>call core api to frozen customer<br/>send email with PRK-03/04 report                                                                                                                                                                                                                                                                                                                          | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/cvrs/sanction/service/cron_service.go?ref_type=heads#L54                                        |        |
| cron-job      | [RiskReport][HR08]                                                                                         | config:cvrs_validate_risk_08<br/>00 01 * * *                | get customer list from core api<br/>get kyc data from kyc api<br/>check risk by cvrs api(/kyc-ws/bulk/calculate)<br/>call jreport to generate report with 103 template<br/>send email to hr_08.email_receiver                                                                                                                                                                                                                                                                                                                   | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/cvrs/risk_report/service/cron_service.go?ref_type=heads#L78                                     |        |
| cron-job      | [RiskReport][HR02]                                                                                         | config:cvrs_validate_risk_02<br/>00 01 * * *                | get customer list from core api<br/>get kyc data from kyc api<br/>check risk by cvrs api(/kyc-ws/bulk/calculate)<br/>call jreport to generate report with 103 template<br/>send email to hr_02.email_receiver                                                                                                                                                                                                                                                                                                                   | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/main/internal/cvrs/risk_report/service/cron_service.go?ref_type=heads#L78                                     |        |
| cron-job      | CVRS check high risk                                                                                       | config:cvrs_check_high_risk<br/>00 02 * * *                 | get customer list from core api<br/>get kyc data from kyc api<br/>check risk by cvrs api(/kyc-ws/bulk/calculate)<br/>send email to receiver<br/>update high risk to kyc api<br/>freeze wallet to hr_03 black 49                                                                                                                                                                                                                                                                                                                 | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/cvrs/high_risk/service/cron_service.go?ref_type=heads#L53                              |        |
| cron-job      | AzureUserSyncPublisher                                                                                     | config:azure_user_sync<br/>0 * * * *                        | insert job_monitor with AZURE_USER_%s_%s waiting<br/>publish job_id to azure_user_sync                                                                                                                                                                                                                                                                                                                                                                                                                                          | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/azure_user_publisher/cron.go?ref_type=heads#L31                                        |        |

## MQ publish subscription

| service                  | type(pub/sub) | queue/routing_key                                                                | description                                                                                                                                                                                                                                     | code source                                                                                                                                                           |
| ------------------------ | ------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cron-job                 | sub           | k.azureUserSync.default/<br/>k.azureUserSync.default                             | sync users from Azure                                                                                                                                                                                                                           | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/azure_user_consumer/service/azure_user_consumer.go?ref_type=heads#L32       |
| cron-job                 | pub           | k.transactionFeeJob.default                                                      | publish to transaction_sync job id/range everey day                                                                                                                                                                                             | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/publish_transaction_fee/service.go?ref_type=heads#L92                       |
| ~~cron-job~~             | ~~pub~~       | ~~k.transactionFeeGenerate.default~~                                             |                                                                                                                                                                                                                                                 |                                                                                                                                                                       |
| cron-job                 | pub           | k.azureUserSync.default                                                          | pub in AzureUserSyncPublisher                                                                                                                                                                                                                   | https://kscm.kasikornbank.com:8443/10004/backend/go-cronjob/-/blob/release/1.4.0/internal/azure_user_publisher/service.go?ref_type=heads#L49                          |
| backoffice               | pub           | k.azureUserSync.dev.default                                                      | post /sync-users                                                                                                                                                                                                                                | https://kscm.kasikornbank.com:8443/10004/backend/go-backoffice/-/blob/main/internal/user/service.go?ref_type=heads#L274                                               |
| backoffice               | pub           | k.transactionFeeJob.default                                                      | /generate-summarrize-fee-report<br/>/report-generations/{id}/generate                                                                                                                                                                           | https://kscm.kasikornbank.com:8443/10004/backend/go-backoffice/-/blob/main/internal/transaction_fee_report/service/service.go?ref_type=heads#L116                     |
| backoffice               | pub           | k.transactionFeeRetryGenerate.default                                            | /report-generations/{id}/generate                                                                                                                                                                                                               | https://kscm.kasikornbank.com:8443/10004/backend/go-backoffice/-/blob/main/internal/report/monthly_report_customer_fee/service.go?ref_type=heads#L134                 |
| transaction-fee-consumer | sub           | q.transactionFeeJob.default/<br/>k.transactionFeeJob.default                     | get job from job_monitor_detail<br/>get monthly tx fees from core api<br/>get THB rate from price api<br/>insert into transaction_fee/manual_transaction_fee<br/>update job_monitor_detail<br/>publish to k.transactionFeeRetryGenerate.default | https://kscm.kasikornbank.com:8443/10004/backend/go-transaction-fee-consumer/-/blob/main/internal/transaction_fee_sync/handler/transaction_fee.go?ref_type=heads#L14  |
| transaction-fee-consumer | pub           | k.transactionFeeRetryGenerate.default                                            | publish retry after q.transactionFeeJob.default                                                                                                                                                                                                 | https://kscm.kasikornbank.com:8443/10004/backend/go-transaction-fee-consumer/-/blob/main/internal/transaction_fee_sync/service/transaction_fee.go?ref_type=heads#L112 |
| transaction-fee-consumer | sub           | k.transactionFeeGenerate.default/<br/>k.transactionFeeGenerate.default           | get retport name from local/report_details<br/>get auc fee from fee_calculation_auc<br/>get tx fees from transaction_fee/manual_transaction_fee<br/>generate csv report <br/>upload report to s3<br/>update job_monitor                         | https://kscm.kasikornbank.com:8443/10004/backend/go-transaction-fee-consumer/-/blob/main/internal/transaction_generate/handler/transaction_fee.go?ref_type=heads#L36  |
| transaction-fee-consumer | sub           | k.transactionFeeRetryGenerate.default/<br/>k.transactionFeeRetryGenerate.default | get job from job_monitor<br/>get job detail from job_monitor_detail<br/>update job_monitor to processing<br/>GenerateFeeReport                                                                                                                  | https://kscm.kasikornbank.com:8443/10004/backend/go-transaction-fee-consumer/-/blob/main/internal/transaction_generate/handler/transaction_fee.go?ref_type=heads#L62  |

### roles

- https://internal-api-uat.test.internal.orbixcustodian.com/roles/search
  
  ```json
  {
      "list": [
          {
              "roleId": "creator",
              "roleName": "Creator",
              "status": "A",
              "remark": "For Client Service/ Commercial Team",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-09-12T04:17:45.296198Z",
              "isUsed": true
          },
          {
              "roleId": "approver",
              "roleName": "Approver",
              "status": "A",
              "remark": "Approver",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2024-08-05T08:32:47.857908Z",
              "isUsed": true
          },
          {
              "roleId": "finance_maker",
              "roleName": "Finance Maker",
              "status": "A",
              "remark": "ฝ่ายบัญชีและการเงิน",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-11-27T04:29:44.424Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-10-31T04:53:44.572039Z",
              "isUsed": true
          },
          {
              "roleId": "itmaker",
              "roleName": "IT Maker",
              "status": "A",
              "remark": "พนักงานระดับปฏิบัติการฝ่ายเทคโนโลยีและสารสนเทศ",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-11-27T04:19:58.875Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-10-31T04:51:42.223868Z",
              "isUsed": true
          },
          {
              "roleId": "client_service_app",
              "roleName": "Client Service Approver",
              "status": "A",
              "remark": "Client Service Approver",
              "createdBy": "ac063523-d2ec-488d-99ba-6fd1f2dfa2be",
              "createdAt": "2025-04-01T06:17:20.058Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-09-12T04:18:26.583937Z",
              "isUsed": true
          },
          {
              "roleId": "admin",
              "roleName": "Admin",
              "status": "A",
              "remark": "Admin",
              "updatedBy": "4c298e83-2c3c-400c-9244-3a1053e0238c",
              "updatedAt": "2025-10-27T12:28:59.344797Z",
              "isUsed": true
          },
          {
              "roleId": "compliance",
              "roleName": "Compliance",
              "status": "A",
              "remark": "Compliance Team",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-11-28T03:02:56.677Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-10-31T04:52:19.377786Z",
              "isUsed": true
          },
          {
              "roleId": "client_viewer",
              "roleName": "Client Service Viewer",
              "status": "A",
              "remark": "Client Service Viewer",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-02-07T08:09:14.242Z",
              "updatedBy": "ac063523-d2ec-488d-99ba-6fd1f2dfa2be",
              "updatedAt": "2025-04-01T06:11:06.093514Z",
              "isUsed": true
          },
          {
              "roleId": "finance_approver",
              "roleName": "Finance Approver",
              "status": "A",
              "remark": "ฝ่ายบัญชีและการเงิน",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-11-27T04:40:07.217Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-10-31T04:52:51.706411Z",
              "isUsed": true
          },
          {
              "roleId": "it_approver",
              "roleName": "IT Approver",
              "status": "A",
              "remark": "พนักงานอาวุโสฝ่ายเทคโนโลยีและสารสนเทศ",
              "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "createdAt": "2024-11-27T04:57:10.815Z",
              "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
              "updatedAt": "2025-10-31T04:51:57.700846Z",
              "isUsed": true
          }
      ],
      "total": 26
  }
  ```

- https://internal-api-uat.test.internal.orbixcustodian.com/privileges
  
  ```json
  [
      {
          "id": "application_versions",
          "name": "Version Management",
          "resource": "application_version",
          "path": "/application-versions",
          "icon": "description",
          "actions": 15,
          "children": null
      },
      {
          "id": "admin",
          "name": "Admin",
          "resource": "admin",
          "path": "/admin",
          "icon": "description",
          "actions": 7,
          "children": [
              {
                  "id": "user",
                  "name": "User Management",
                  "resource": "user",
                  "path": "/admin/users",
                  "icon": "person",
                  "actions": 3
              },
              {
                  "id": "role",
                  "name": "Role Management",
                  "resource": "role",
                  "path": "/admin/roles",
                  "icon": "credit_card",
                  "actions": 7
              }
          ]
      },
      {
          "id": "content",
          "name": "Content",
          "resource": "content",
          "path": "/content",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "tokens_network",
                  "name": "Tokens Network",
                  "resource": "token_network",
                  "path": "/content/tokens-network",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "legals",
                  "name": "Legal Document Management",
                  "resource": "legals",
                  "path": "/content/legals",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "downtimes",
                  "name": "Downtime",
                  "resource": "downtimes",
                  "path": "/content/downtimes",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "marketings",
                  "name": "Marketing",
                  "resource": "marketings",
                  "path": "/content/marketings",
                  "icon": "fiber_manual_record",
                  "actions": 15
              }
          ]
      },
      {
          "id": "fee_management",
          "name": "Retail Fee Management",
          "resource": "fee_management",
          "path": "/fee-management",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "retail_fees",
                  "name": "Retail",
                  "resource": "retail_fee",
                  "path": "/fee-management/retail-fees",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "retail_subscriptions",
                  "name": "Retail Subscription",
                  "resource": "retail-subscription",
                  "path": "/fee-management/retail-subscriptions",
                  "icon": "zoom_in",
                  "actions": 15
              }
          ]
      },
      {
          "id": "finance",
          "name": "Finance",
          "resource": "finance",
          "path": "/finance",
          "icon": "description",
          "actions": 11,
          "children": [
              {
                  "id": "coins",
                  "name": "Coin Management",
                  "resource": "coins",
                  "path": "/finance/coins",
                  "icon": "fiber_manual_record",
                  "actions": 11
              },
              {
                  "id": "currencies",
                  "name": "Currency Management",
                  "resource": "currencies",
                  "path": "/finance/currencies",
                  "icon": "fiber_manual_record",
                  "actions": 11
              },
              {
                  "id": "time_managements",
                  "name": "Refresh Times",
                  "resource": "time_managements",
                  "path": "/finance/time-managements",
                  "icon": "fiber_manual_record",
                  "actions": 11
              },
              {
                  "id": "invoice",
                  "name": "Invoice",
                  "resource": "invoice",
                  "path": "/finance/invoice",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "tax_invoice",
                  "name": "Tax Invoice",
                  "resource": "tax_invoice",
                  "path": "/finance/tax-invoice",
                  "icon": "fiber_manual_record",
                  "actions": 15
              }
          ]
      },
      {
          "id": "corporate_fees",
          "name": "Corporate Fee Management",
          "resource": "corporate_fee",
          "path": "/corporate-fees",
          "icon": "zoom_in",
          "actions": 15,
          "children": [
              {
                  "id": "auc_fees",
                  "name": "AUC",
                  "resource": "corporate_fee_auc",
                  "path": "/corporate-fees/auc-fees",
                  "icon": "zoom_in",
                  "actions": 15
              },
              {
                  "id": "corporate_subscriptions",
                  "name": "Customer Subscription",
                  "resource": "corporate_subscription",
                  "path": "/corporate-fees/subscriptions",
                  "icon": "zoom_in",
                  "actions": 15
              },
              {
                  "id": "off_chain_fees",
                  "name": "OFF-CHAIN",
                  "resource": "corporate_fee_off",
                  "path": "/corporate-fees/off-chain-fees",
                  "icon": "zoom_in",
                  "actions": 15
              },
              {
                  "id": "on_chain_fees",
                  "name": "ON-CHAIN",
                  "resource": "corporate_fee_on",
                  "path": "/corporate-fees/on-chain-fees",
                  "icon": "zoom_in",
                  "actions": 15
              }
          ]
      },
      {
          "id": "instruction",
          "name": "Instruction",
          "resource": "instruction",
          "path": "/instruction",
          "icon": "description",
          "actions": 1,
          "children": [
              {
                  "id": "todo_instructions",
                  "name": "Todo Instructions",
                  "resource": "todo_instructions",
                  "path": "/instruction/todo-instructions",
                  "icon": "fiber_manual_record",
                  "actions": 11
              },
              {
                  "id": "instruction_list",
                  "name": "Instruction List",
                  "resource": "instruction_list",
                  "path": "/instruction/instructions-list",
                  "icon": "fiber_manual_record",
                  "actions": 7
              }
          ]
      },
      {
          "id": "todo_check_fraud",
          "name": "Transaction Monitoring",
          "resource": "fraud_name",
          "path": "/fraud",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "no_issues",
                  "name": "Transaction Monitoring (No Fraud Issue)",
                  "resource": "fraud_issues",
                  "path": "/fraud/no-issues",
                  "icon": "text_snippet",
                  "actions": 9
              },
              {
                  "id": "with_issues",
                  "name": "Transaction Monitoring (Fraud Issue)",
                  "resource": "fraud_issues",
                  "path": "/fraud/with-issues",
                  "icon": "text_snippet",
                  "actions": 11
              }
          ]
      },
      {
          "id": "payment_management",
          "name": "Payment Management",
          "resource": "payment_management",
          "path": "/payment-management",
          "icon": "fiber_manual_record",
          "actions": 11,
          "children": [
              {
                  "id": "payment_transactions",
                  "name": "Payment Transactions",
                  "resource": "payment_transactions",
                  "path": "/payment-management/payment-transactions",
                  "icon": "fiber_manual_record",
                  "actions": 11
              }
          ]
      },
      {
          "id": "policies",
          "name": "Policy Management",
          "resource": "policy_management",
          "path": "/policies",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "service_agreements",
                  "name": "Service Agreement",
                  "resource": "service_agreements",
                  "path": "/policies/service-agreements",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "client_policy_inquiries",
                  "name": "Client Policy Inquiry",
                  "resource": "client-policy-inquiries",
                  "path": "/policies/client-policy-inquiries",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "global_policy_inquiries",
                  "name": "Global Policy Inquiry",
                  "resource": "global-policy-inquiries",
                  "path": "/policies/global-policy-inquiries",
                  "icon": "fiber_manual_record",
                  "actions": 1
              }
          ]
      },
      {
          "id": "faq",
          "name": "FAQ",
          "resource": "faq",
          "path": "/faq",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "faq_details",
                  "name": "FAQ Detail",
                  "resource": "faq",
                  "path": "/faq/faq-details",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "topic_managements",
                  "name": "Main Topic",
                  "resource": "topic",
                  "path": "/faq/topic-managements",
                  "icon": "fiber_manual_record",
                  "actions": 15
              }
          ]
      },
      {
          "id": "reporting",
          "name": "Report",
          "resource": "faq",
          "path": "/reporting",
          "icon": "zoom_in",
          "actions": 15,
          "children": [
              {
                  "id": "report_generations",
                  "name": "Report Generation",
                  "resource": "report_generations",
                  "path": "/reporting/report-generations",
                  "icon": "text_snippet",
                  "actions": 3
              },
              {
                  "id": "report_versions",
                  "name": "Report Version",
                  "resource": "report_version",
                  "path": "/reporting/report-versions",
                  "icon": "text_snippet",
                  "actions": 15
              },
              {
                  "id": "report_configuration",
                  "name": "Report Configuration",
                  "resource": "report_configuration",
                  "path": "/reporting/report-configuration",
                  "icon": "text_snippet",
                  "actions": 15
              }
          ]
      },
      {
          "id": "customer_managements",
          "name": "Customer Management",
          "resource": "customer_management",
          "path": "/customer-managements",
          "icon": "description",
          "actions": 11,
          "children": null
      },
      {
          "id": "messaging",
          "name": "Messaging",
          "resource": "messaging",
          "path": "/messaging",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "templates",
                  "name": "Template",
                  "resource": "template",
                  "path": "/messaging/templates",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "schedule_message",
                  "name": "Schedule Message",
                  "resource": "schedule_message",
                  "path": "/messaging/schedule-message",
                  "icon": "fiber_manual_record",
                  "actions": 15
              }
          ]
      },
      {
          "id": "setting",
          "name": "System Settings",
          "resource": "setting",
          "path": "/setting",
          "icon": "description",
          "actions": 15,
          "children": null
      },
      {
          "id": "generate_summarrize_fee_report",
          "name": "Generate Summarize Fee Report",
          "resource": "generate_summarrize_fee_report",
          "path": "/generate-sf-report",
          "icon": "settings",
          "actions": 3,
          "children": null
      },
      {
          "id": "core_system_function",
          "name": "Core System Functions",
          "resource": "core_system_function",
          "path": "/core-system",
          "icon": "description",
          "actions": 15,
          "children": [
              {
                  "id": "msmp",
                  "name": "MSMP Dashboard",
                  "resource": "msmb_dashboard",
                  "path": "/core-system/msmp",
                  "icon": "fiber_manual_record",
                  "actions": 1
              },
              {
                  "id": "generate_msmp",
                  "name": "Generate MSMP",
                  "resource": "generate_msmp",
                  "path": "/core-system/generate-msmp",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "msmp_approve",
                  "name": "Approve MSMP Generation",
                  "resource": "msmp_approve",
                  "path": "/core-system/approve-msmp",
                  "icon": "fiber_manual_record",
                  "actions": 9
              },
              {
                  "id": "special_transfer",
                  "name": "Unsolicited",
                  "resource": "special_transfer_name",
                  "path": "/core-system/special-transfer",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "ck_approve",
                  "name": "Approve Client Key Register",
                  "resource": "ck_approve",
                  "path": "/core-system/approve-ck",
                  "icon": "fiber_manual_record",
                  "actions": 9
              },
              {
                  "id": "client_authority_key",
                  "name": "Client Authority Key",
                  "resource": "client_authority_key",
                  "path": "/core-system/client-authority-key",
                  "icon": "fiber_manual_record",
                  "actions": 15
              },
              {
                  "id": "ek_activation_transfer",
                  "name": "Emergency Key Activation and Transfer",
                  "resource": "emergency_key_transfer",
                  "path": "/core-system/ek-activation-transfer",
                  "icon": "fiber_manual_record",
                  "actions": 15
              }
          ]
      },
      {
          "id": "generate_report",
          "name": "Generate SEC Report",
          "resource": "generate_report",
          "path": "/generate-report",
          "icon": "settings",
          "actions": 3,
          "children": null
      },
      {
          "id": "dj1",
          "name": "DJ1",
          "resource": "dj1",
          "path": "/dj1",
          "icon": "fiber_manual_record",
          "actions": 3,
          "children": null
      }
  ]
  ```

- https://internal-api-uat.test.internal.orbixcustodian.com/roles/admin

```json
  {
    "roleId": "admin",
    "roleName": "Admin",
    "status": "A",
    "remark": "Admin",
    "updatedBy": "4c298e83-2c3c-400c-9244-3a1053e0238c",
    "updatedAt": "2025-10-27T12:28:59.344797Z",
    "privileges": [
        "schedule_message 7",
        "messaging",
        "setting 7",
        "application_version 7",
        "approval 7",
        "token 7",
        "user 7",
        "role 7",
        "admin",
        "application_versions 7",
        "tokens_network 7",
        "legals 7",
        "downtimes 7",
        "marketings 7",
        "content",
        "retail_fees 7",
        "retail_subscriptions 7",
        "fee_management",
        "coins 3",
        "currencies 3",
        "time_managements 3",
        "invoice 7",
        "tax_invoice 7",
        "finance",
        "auc_fees 7",
        "corporate_subscriptions 7",
        "off_chain_fees 7",
        "on_chain_fees 7",
        "corporate_fees",
        "todo_instructions 3",
        "instruction_list 7",
        "instruction",
        "payment_transactions 3",
        "payment_management",
        "faq_details 7",
        "topic_managements 7",
        "faq",
        "service_agreements 7",
        "client_policy_inquiries 7",
        "global_policy_inquiries 1",
        "policies",
        "customer_managements 3",
        "no_issues 1",
        "with_issues 3",
        "todo_check_fraud",
        "templates 7",
        "msmp 1",
        "generate_msmp 7",
        "msmp_approve 1",
        "special_transfer 7",
        "ck_approve 1",
        "client_authority_key 7",
        "ek_activation_transfer 7",
        "core_system_function",
        "generate_report 3",
        "report_generations 3",
        "report_versions 3",
        "report_configuration 3",
        "reporting",
        "generate_summarrize_fee_report 3",
        "upload 1",
        "config 1",
        "dj1_report 7",
        "dj1_upload 7",
        "dj1_config 7",
        "dj1 3"
    ],
    "isUsed": false
}
```

## jaspersoft

- add new fonts(ttf) settings->jaspersoft studio -> fonts -> add -> ttf -> **select pdf Encoding(Identity-H (Unicode with horizontal writing))** -> apply -> export jar for project use

https://orbixcustodian-internal-uat-app-bucket.s3.ap-southeast-1.amazonaws.com/public/marketings/580028124_image.png

https://internal-cdn-sit.test.orbixcustodian.com/public/marketings/580028124_image.png

https://internal-cdn-sit.test.orbixcustodian.com/public/marketings/PKR-03_112025.png

https://internal-cdn-sit.test.orbixcustodian.com/public/marketings/PKR-04_112025.png

sit

PKR-03: https://orbixcustodian-internal-sit-app-bucket.s3.ap-southeast-1.amazonaws.com/public/marketings/PKR-03_112025.png

PKR-04: (https://orbixcustodian-internal-sit-app-bucket.s3.ap-southeast-1.amazonaws.com/public/marketings/PKR-04_112025.png

uat

PKR-03: https://orbixcustodian-internal-uat-app-bucket.s3.ap-southeast-1.amazonaws.com/public/marketings/PKR-03_112025.png

PKR-04: https://orbixcustodian-internal-uat-app-bucket.s3.ap-southeast-1.amazonaws.com/public/marketings/PKR-04_112025.png

https://internal-api-uat.test.internal.orbixcustodian.com/files/report_versions/993910599_PKR04-uat.jrxml

https://internal-api-uat.test.internal.orbixcustodian.com/files/report_versions/690659695_PKR03-uat.jrxml

### core-go

golang 版本太低

依赖 github.com/golang-jwt/jwt 存在Highest severity: HIGH 的风险

### notifications:

/notifications/count/read

 /notifications/get?limit=5

/notifications/get?limit=5&read=false

### roles

/privileges

select * from histories where type='customer_managements' and data like '%Auto Freeze Customer Account%' order by updated_at desc;

select from_address,to_address,transaction_type,amount,from_wallet_asset_total_balance,to_wallet_asset_total_balance from success_transactions where tx_id in ('2026504515728969728','2026506993384034304');
