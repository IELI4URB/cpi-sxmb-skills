# CPI SXMB Skills

Expert skill for configuring SAP ABAP Integration Engine (SXMB) inbound and outbound connectivity with SAP CPI (Cloud Integration / Integration Suite).

## Contents

```
skill/
├── SKILL.md                          # Skill entry point — scope, quick answers, SAP Help references
└── references/
    ├── transaction-reference.md      # All 14 SXMB transaction codes with descriptions and use cases
    └── configuration-guide.md       # Step-by-step inbound/outbound setup, CPI tenant (3J2), queues, monitoring, error remediation
```

## Coverage

- All 14 SXMB transaction codes (`SXMB_ADM`, `SXMB_MONI`, `SXMB_CHECK`, `SXMB_IDOC`, etc.)
- Step-by-step `SXMB_ADM` configuration (system role, inbound/outbound queues, exchange profile, log settings)
- CPI tenant scope item **3J2** setup
- IDoc adapter configuration (`SXMB_IDOC`)
- Retention periods (`SXMB_RETENTION`) and encryption key check (`SXMB_CHK_ENCKEY`)
- Common error patterns and resolutions (HTTP 401, queue pile-up, decryption errors)

## SAP Help References

| Topic | URL |
|---|---|
| Integration Services (ABAP Platform) | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_ADM Engine Configuration | https://help.sap.com/docs/CONNECTOR_FOR_SAP_MULTI-BANK_CONNECTIVITY/1bfdce7622f446e2af40dc1188891544/3a90ab37fc8a4af092f3a60c8c07a2d7.html |
| Monitoring the Integration Engine | https://help.sap.com/docs/SAP_ERP_SPV/2aed1d21d1c244d887be7fc349520076/1d83933f1bcf4d6eb703d0e6b20a0c2e.html |
| SAP Integration Suite (Cloud Integration) | https://help.sap.com/docs/cloud-integration |
| SAP Process Integration / Process Orchestration | https://help.sap.com/docs/SAP_NETWEAVER_PI |
