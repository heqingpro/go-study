## api

### notifications:

/notifications/count/read

/notifications/get?limit=5

/notifications/get?limit=5&read=false

### roles

/privileges

```
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
                "id": "exchange_rate",
                "name": "Exchange Rate",
                "resource": "exchange_rate",
                "path": "/finance/exchange-rate",
                "icon": "fiber_manual_record",
                "actions": 1
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
                "name": "To-do Instruction List",
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
                "actions": 15
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
        "icon": "pie_chart",
        "actions": 15,
        "children": [
            {
                "id": "dj1_report",
                "name": "DJ1 Report",
                "resource": "dj1_report",
                "path": "/dj1/report",
                "icon": "fiber_manual_record",
                "actions": 15
            },
            {
                "id": "dj1_upload",
                "name": "DJ1 Upload",
                "resource": "dj1_upload",
                "path": "/dj1/upload",
                "icon": "description",
                "actions": 15
            },
            {
                "id": "dj1_config",
                "name": "DJ1 Config",
                "resource": "dj1_config",
                "path": "/dj1/config",
                "icon": "settings",
                "actions": 15
            }
        ]
    }
]
```

/roles/operation_entry

```
{
    "roleId": "operation_entry",
    "roleName": "Operation Entry",
    "status": "A",
    "remark": "Operation Entry",
    "createdBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
    "createdAt": "2025-08-19T10:51:05.404Z",
    "updatedBy": "1c6941ea-1dee-4311-9f38-9c64876d74cb",
    "updatedAt": "2025-08-19T10:55:05.300946Z",
    "privileges": [
        "application_versions 1",
        "user 1",
        "role 1",
        "admin",
        "tokens_network 1",
        "legals 1",
        "downtimes 1",
        "marketings 1",
        "content",
        "retail_fees 1",
        "fee_management",
        "coins 1",
        "currencies 1",
        "time_managements 1",
        "invoice 1",
        "tax_invoice 1",
        "finance",
        "auc_fees 1",
        "corporate_subscriptions 1",
        "off_chain_fees 1",
        "on_chain_fees 1",
        "corporate_fees",
        "todo_instructions 1",
        "instruction_list 1",
        "instruction",
        "no_issues 1",
        "with_issues 1",
        "todo_check_fraud",
        "faq_details 1",
        "topic_managements 1",
        "faq",
        "service_agreements 1",
        "client_policy_inquiries 1",
        "global_policy_inquiries 1",
        "policies",
        "report_generations 1",
        "report_versions 1",
        "report_configuration 1",
        "reporting",
        "customer_managements 1",
        "templates 1",
        "schedule_message 1",
        "messaging",
        "setting 1",
        "msmp 1",
        "generate_msmp 1",
        "msmp_approve 1",
        "special_transfer 1",
        "ck_approve 1",
        "client_authority_key 1",
        "ek_activation_transfer 1",
        "core_system_function",
        "generate_summarrize_fee_report 1",
        "generate_report 1"
    ],
    "isUsed": false
}
```
