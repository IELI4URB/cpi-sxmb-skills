# SXMB Transaction Code Reference

All 14 SXMB transactions confirmed in the SAP S/4HANA Platform Content Model (PCM).

SAP Help Portal — Integration Engine documentation:
https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Administration Transactions

### SXMB_ADM — Integration Engine Administration

The primary administration transaction for the ABAP-side Integration Engine. Use this to:
- Set the **system role** (Application System, Integration Server, or both)
- Configure **inbound services**: define which qRFC inbound queue processes incoming XI/CPI messages
- Configure **outbound services**: define which qRFC outbound queue dispatches messages to CPI/PI
- Register **CPI/PI endpoint URLs** and authentication credentials
- Enable or disable **pipeline processing** services
- Set **log level** (none, headers, full payload) and **retention policy**
- Configure **message acknowledgement** behaviour

Key sub-nodes in SXMB_ADM:
- Integration Engine Configuration: sets role and pipeline flags
- Inbound Queue: qRFC queue name for inbound processing
- Outbound Queue: qRFC queue name for outbound dispatching
- Exchange Profile: transport and runtime parameters

SAP Help Reference: https://help.sap.com/docs/CONNECTOR_FOR_SAP_MULTI-BANK_CONNECTIVITY/1bfdce7622f446e2af40dc1188891544/3a90ab37fc8a4af092f3a60c8c07a2d7.html

---

### SXMB_ADMIN — Integration Engine: All Functions

Consolidated view of all Integration Engine administrative functions in a single transaction. Useful when doing a full engine health-check or initial setup review.

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

### SXMB_INITIAL_SETUP — XI Runtime: Initial Setup

First-time activation of the XI runtime on the ABAP system. Performs:
- Activation of the Integration Engine service
- Generation of the System Landscape Directory (SLD) connection
- Registration of the local integration engine in the XI registry
- Initial queue setup

Must be run before any other SXMB transaction will function.

SAP Help Reference: https://help.sap.com/docs/SAP_NETWEAVER_PI

---

### SXMB_ADM_BPE — Process Engine Administration

Administers the Business Process Engine (BPE) — used for multi-step, stateful integration scenarios (cross-system workflows, correlation-based message handling). Configure BPE settings here when your CPI flows include asynchronous multi-step patterns.

SAP Help Reference: https://help.sap.com/docs/SAP_NETWEAVER_PI

---

## Monitoring Transactions

### SXMB_MONI — Integration Engine: Monitoring

The primary monitoring transaction. Shows all messages in or recently processed by the integration engine with inbound/outbound separation. Features:
- Filter by direction (inbound/outbound), status (error, processing, done), interface, and time range
- Drill into individual message payloads (headers + body if logging is enabled)
- Trigger manual reprocessing of failed messages
- Cancel stuck/stalled messages

SAP Help Reference: https://help.sap.com/docs/SAP_ERP_SPV/2aed1d21d1c244d887be7fc349520076/1d83933f1bcf4d6eb703d0e6b20a0c2e.html

---

### SXMB_MONI_BPE — Process Engine: Monitoring

Monitors Business Process Engine workflow instances. Use when multi-step BPM flows are involved (correlation, aggregation, or fork/join patterns) and you need to track instance state.

SAP Help Reference: https://help.sap.com/docs/SAP_NETWEAVER_PI

---

### SXMB_EP_MONI — Exchange Profile Monitor

Displays and validates the active exchange profile configuration. The exchange profile holds runtime transport parameters (timeout values, retry counts, security settings). Use after changes in SXMB_ADM to confirm profile entries are correctly applied.

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Application and Adapter Transactions

### SXMB_APPL — Integration Engine Application Suite

Manages registration of ABAP application components that send or receive XI/CPI messages. Each registered application has:
- An application component name (matches the XI Design component)
- Inbound service bindings (which function modules/BAPIs receive incoming messages)
- Outbound service bindings (which function modules trigger outbound dispatching)

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

### SXMB_IFR — Start Integration Builder

Launches the Integration Builder — the XI/PI design-time tool for:
- Defining message interfaces (inbound/outbound)
- Mapping source/target structures
- Creating integration scenarios and collaboration profiles

In a CPI-connected landscape, the Integration Builder is superseded by SAP Integration Suite's graphical editor, but SXMB_IFR is still used on the ABAP backend to manage legacy PI objects.

SAP Help Reference: https://help.sap.com/docs/SAP_NETWEAVER_PI

---

### SXMB_IDOC — IDoc Adapter Suite

Configures the IDoc Adapter for inbound and outbound IDoc processing via the integration engine. Use when:
- CPI flows consume or produce IDocs on the ABAP side
- You need to configure IDoc-to-XI message conversion (inbound: IDoc -> SXMB; outbound: SXMB -> IDoc)
- Troubleshooting IDoc adapter errors in the inbound/outbound pipeline

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Maintenance and Diagnostic Transactions

### SXMB_CHECK — Integration Engine Test Suite

Runs diagnostic and health-check tests against the integration engine:
- Connectivity tests to registered integration servers
- Pipeline component availability checks
- Queue accessibility tests
- Configuration consistency validation

Run this after any system copy, transport, or major configuration change.

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

### SXMB_CHK_ENCKEY — Check Use of Key for DB Encryption

Verifies that the correct encryption key is in use for the integration engine message database. Run when:
- The system encryption key has been rotated
- You encounter "decryption error" messages in SXMB_MONI
- During security audits of the PI/CPI configuration

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

### SXMB_ITFACTION — Interfaces for Archiving

Manages archiving interface actions for message data. Use to:
- Define which inbound/outbound interfaces are eligible for archiving
- Configure archiving objects
- Trigger archiving runs for aged message data

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

### SXMB_RETENTION — Define Retention Periods

Sets how long message data (headers, payload, status) is retained in the integration engine database before automatic deletion:
- Retention period in days (separate for successful vs. failed messages)
- Maximum database size threshold
- Whether to retain only headers or full payloads

Best practice: keep failed message retention longer (e.g. 60 days) than successful (e.g. 14 days) to allow post-incident investigation.

SAP Help Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Summary Table

| Transaction | Description | Primary Use | SAP Help |
|---|---|---|---|
| SXMB_ADM | Integration Engine - Administration | Inbound/outbound config, CPI endpoint, role | https://help.sap.com/docs/CONNECTOR_FOR_SAP_MULTI-BANK_CONNECTIVITY/1bfdce7622f446e2af40dc1188891544/3a90ab37fc8a4af092f3a60c8c07a2d7.html |
| SXMB_ADMIN | Integration Engine - All Functions | Consolidated administration view | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_INITIAL_SETUP | XI Runtime: Initial Setup | First-time engine activation | https://help.sap.com/docs/SAP_NETWEAVER_PI |
| SXMB_ADM_BPE | Process Engine - Administration | BPE/BPM flow administration | https://help.sap.com/docs/SAP_NETWEAVER_PI |
| SXMB_MONI | Integration Engine - Monitoring | Inbound/outbound message monitoring | https://help.sap.com/docs/SAP_ERP_SPV/2aed1d21d1c244d887be7fc349520076/1d83933f1bcf4d6eb703d0e6b20a0c2e.html |
| SXMB_MONI_BPE | Process Engine - Monitoring | BPE workflow instance monitoring | https://help.sap.com/docs/SAP_NETWEAVER_PI |
| SXMB_EP_MONI | Exchange Profile Monitor | Exchange profile validation | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_APPL | Integration Engine Application Suite | Application component registration | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_IFR | Start Integration Builder | XI/PI design-time tool | https://help.sap.com/docs/SAP_NETWEAVER_PI |
| SXMB_IDOC | IDoc Adapter Suite | IDoc inbound/outbound adapter config | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_CHECK | Integration Engine - Test Suite | Health and connectivity checks | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_CHK_ENCKEY | Check Use of Key for DB Encryption | Encryption key verification | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_ITFACTION | Interfaces for Archiving | Archiving interface management | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| SXMB_RETENTION | Define Retention Periods | Message log retention config | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
