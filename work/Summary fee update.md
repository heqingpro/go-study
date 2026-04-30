## Summary fee update

- POST /report-generations/search
  
  ```json
  // monthly
  {
    "limit": 10,
    "reportName": "Summarize Fee Report",
    "schedule": "M",
    "status": [
      "PS",
      "F",
      "S"
    ]
  }
  // weekly
  {
    "limit": 10,
    "reportName": "Summarize Fee Report",
    "schedule": "W",
    "status": [
      "PS",
      "F",
      "S"
    ]
  }
  ```

```json
{
  "list": [
    {
      "id": "monthly_summarize_fee_05_2025",
      "reportName": "Summarize Fee Report",
      "schedule": "M",
      "reportDate": "2025-05-31T16:59:59Z",
      "generatedAt": "2025-05-31T18:00:00.517746Z",
      "generatedStatus": "S",
      "numReports": 1,
      "attachments": [
        {
          "fileName": "summarize-fee-2025-05.csv",
          "url": "https://internal-api-uat.test.internal.orbixcustodian.com/files/monthly_summarize_fee/summarize-fee-2025-05.csv"
        }
      ]
    },
    {
      "id": "monthly_summarize_fee_03_2025",
      "reportName": "Summarize Fee Report",
      "schedule": "M",
      "reportDate": "2025-03-31T16:59:59Z",
      "generatedAt": "2025-03-31T18:00:00.651589Z",
      "generatedStatus": "S",
      "numReports": 1,
      "attachments": [
        {
          "fileName": "summarize-fee-2025-03.csv",
          "url": "https://internal-api-uat.test.internal.orbixcustodian.com/files/monthly_summarize_fee/summarize-fee-2025-03.csv"
        }
      ]
    },
    {
      "id": "monthly_summarize_fee_11_2024",
      "reportName": "Summarize Fee Report",
      "schedule": "M",
      "reportDate": "2024-11-30T16:59:00Z",
      "generatedAt": "2024-11-30T16:59:00.288957Z",
      "generatedStatus": "S",
      "numReports": 1,
      "attachments": [
        {
          "fileName": "summarize-fee-2024-11.csv",
          "url": "https://internal-api-uat.test.internal.orbixcustodian.com/files/monthly_summarize_fee/summarize-fee-2024-11.csv"
        }
      ]
    }
  ],
  "total": 3
}
```

- GET /files/monthly_summarize_fee/summarize-fee-2025-05.csv downloads report

- POST /generate-summarrize-fee-report : generate summary report manualy
  
  ```json
  {
    "startDate": "2026-01-01T00:00:00.000Z",
    "endDate": "2026-01-03T00:00:00.000Z"
  }
  ```
  
  ```json
  {"id":"manual_summarize_fee_01012026_03012026_Uj3UsNtcM"}
  ```
