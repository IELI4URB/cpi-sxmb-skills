# CPI Configuration Guide — Inbound and Outbound SXMB Services

This guide covers the end-to-end configuration of inbound and outbound SXMB services when connecting an ABAP system (S/4HANA or ECC) to SAP CPI (SAP Integration Suite - Cloud Integration).

## SAP Help Portal References Used in This Guide

| Document | URL |
|---|---|
| Integration Services (ABAP Platform) | https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html |
| Integrating Engine Configuration in SXMB_ADM | https://help.sap.com/docs/CONNECTOR_FOR_SAP_MULTI-BANK_CONNECTIVITY/1bfdce7622f446e2af40dc1188891544/3a90ab37fc8a4af092f3a60c8c07a2d7.html |
| Monitoring the Integration Engine | https://help.sap.com/docs/SAP_ERP_SPV/2aed1d21d1c244d887be7fc349520076/1d83933f1bcf4d6eb703d0e6b20a0c2e.html |
| SAP Integration Suite (Cloud Integration) | https://help.sap.com/docs/cloud-integration |
| SAP Process Integration / Process Orchestration | https://help.sap.com/docs/SAP_NETWEAVER_PI |

---

## Architecture Overview

```
ABAP System                    SAP CPI / Cloud Integration
+---------------------+        +---------------------------+
|  SXMB_INITIAL_SETUP |        |  CPI Tenant               |
|  SXMB_ADM           |<------>|  Integration Flows        |
|  Integration Engine |        |  Adapters                 |
|  Inbound Queue      |        |  Monitoring               |
|  Outbound Queue     |        +---------------------------+
+---------------------+
```

---

## Phase 1 — Prerequisites

Before configuring SXMB for CPI connectivity, ensure:

1. **CPI Tenant is provisioned** — The CPI tenant must exist on BTP and the Integration Suite must be activated.
   - Reference: https://help.sap.com/docs/cloud-integration

2. **S/4HANA Building Block 3J2 is scoped** — In S/4HANA, scope item "Configuration for Cloud Platform Integration (CPI Tenant)" (ID: 3J2) must be activated. This creates the required communication system and OAuth credentials.

3. **HTTP(S) connectivity** is established between the ABAP system and the CPI tenant (firewall rules, SSL certificates).

4. **RFC destinations** for CPI are created in SM59 (type H — HTTP connection to external server, pointing to the CPI runtime URL).

---

## Phase 2 — Initial Setup (SXMB_INITIAL_SETUP)

Run transaction `SXMB_INITIAL_SETUP` to activate the XI runtime:

1. Open SXMB_INITIAL_SETUP
2. Execute "Activate Integration Engine" — this registers the local engine
3. Confirm SLD (System Landscape Directory) connectivity if prompted
4. Verify that integration engine tables (SXMB*) are populated after activation

**Expected result:** Status "Integration Engine active" displayed without errors.

Reference: https://help.sap.com/docs/SAP_NETWEAVER_PI

---

## Phase 3 — Core Configuration (SXMB_ADM)

Open `SXMB_ADM` (Integration Engine - Administration).

Reference: https://help.sap.com/docs/CONNECTOR_FOR_SAP_MULTI-BANK_CONNECTIVITY/1bfdce7622f446e2af40dc1188891544/3a90ab37fc8a4af092f3a60c8c07a2d7.html

### 3.1 — Set the System Role

Navigate to: Integration Engine Configuration > System Role

| Role | Use when |
|---|---|
| **Application System** | The ABAP system is an end-point and NOT the central Integration Server |
| **Integration Server** | The ABAP system hosts the central routing logic (classic PI/PO on-premise) |
| **Application System + Integration Server** | Hybrid — not recommended for CPI connectivity |

For CPI-connected S/4HANA: **set role to "Application System"**. CPI acts as the Integration Server.

### 3.2 — Configure Inbound Services

Navigate to: Integration Engine Configuration > Inbound

| Parameter | Description | Recommended Value |
|---|---|---|
| **Inbound queue name** | qRFC queue for incoming messages from CPI | XI_INBOUND_Q |
| **Max. parallel processes** | Parallel inbound processing workers | 5-10 |
| **Retry interval** | Seconds between retries for failed inbound messages | 60 |
| **Max. retries** | Maximum retry count before error flag | 5 |
| **Persistence** | Persist message payloads to database | Activate for debugging; off in production |

### 3.3 — Configure Outbound Services

Navigate to: Integration Engine Configuration > Outbound

| Parameter | Description | Recommended Value |
|---|---|---|
| **Outbound queue name** | qRFC queue for messages dispatched to CPI | XI_OUTBOUND_Q |
| **HTTP destination** | SM59 HTTP destination pointing to CPI runtime URL | CPI_RUNTIME |
| **Max. parallel processes** | Parallel outbound workers | 5-10 |
| **Timeout (seconds)** | HTTP timeout for CPI calls | 120 |
| **SSL certificate** | Client certificate for CPI mTLS (if applicable) | As configured by Basis |

### 3.4 — Exchange Profile Parameters

Navigate to: Integration Engine Configuration > Exchange Profile

| Entry | Value |
|---|---|
| `com.sap.aii.connect.runner.adapter.soap.http.version` | `1.1` |
| `com.sap.aii.connect.runner.adapter.soap.http.timeout` | `120000` (ms) |
| `com.sap.aii.util.logging.Severity` | `Information` (Production) / `Debug` (Testing) |

### 3.5 — Log Settings

| Setting | Production | Testing/Debug |
|---|---|---|
| Log mode | Headers only | Full payload |
| Retention | 14 days success / 60 days error | 30 days all |

---

## Phase 4 — Application Registration (SXMB_APPL)

Run `SXMB_APPL` to register ABAP application components:

1. Create a new application component entry matching the name used in the CPI/XI design
2. Assign inbound service: interface namespace, interface name, receiving function module or BAPI
3. Assign outbound service: interface namespace, interface name, sending function module

Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Phase 5 — IDoc Scenarios (SXMB_IDOC)

If the integration scenario involves IDocs:

1. Open `SXMB_IDOC` — IDoc Adapter Suite
2. Configure IDoc-to-XI mapping:
   - **Inbound:** XML payload from CPI is converted to IDoc and posted via standard IDoc inbound processing
   - **Outbound:** IDoc generated by ABAP is converted to XML and dispatched via SXMB outbound queue to CPI

Reference: https://help.sap.com/docs/ABAP_PLATFORM_CROSS/f2afdaf444844c38909aefc7bc792cdb/910326e80b304ec18dffab97f51f8f01.html

---

## Phase 6 — Verification (SXMB_CHECK)

Run `SXMB_CHECK` to validate the configuration:

1. Execute all available check categories:
   - Integration Engine consistency check
   - Connectivity to CPI/PI endpoint
   - Queue registration check
   - Exchange profile consistency
2. Resolve any red/yellow status items before going live

---

## Phase 7 — Set Retention Periods (SXMB_RETENTION)

| Message category | Retention (days) |
|---|---|
| Successful inbound messages | 14 |
| Successful outbound messages | 14 |
| Failed inbound messages | 60 |
| Failed outbound messages | 60 |
| BPE workflow instances | 30 |

---

## Phase 8 — Monitoring (SXMB_MONI)

Reference: https://help.sap.com/docs/SAP_ERP_SPV/2aed1d21d1c244d887be7fc349520076/1d83933f1bcf4d6eb703d0e6b20a0c2e.html

### Inbound Monitoring
1. Open SXMB_MONI, set direction = Inbound
2. Filter by interface and time range
3. Green = processed successfully; Red = error; Yellow = in progress

### Outbound Monitoring
1. Set direction = Outbound
2. Check for messages stuck in the outbound queue
3. Verify HTTP response codes from CPI (200 = success, 4xx/5xx = error)

### Common Error Patterns

| Error | Cause | Resolution |
|---|---|---|
| SYSFAIL in inbound queue | Function module not found | Check SXMB_APPL registration |
| HTTP 401 Outbound | OAuth token expired | Refresh OAuth credentials in SM59 / CPI communication arrangement |
| Queue entries piling up | qRFC scheduler not running | Check SMQR (inbound) and SMQS (outbound) scheduler status |
| EDOC_ADAPTER error | IDoc type mismatch | Check SXMB_IDOC mapping |
| Decryption error | Encryption key mismatch | Run SXMB_CHK_ENCKEY and rotate key if needed |

---

## CPI Tenant Scope Item 3J2

The "Configuration for Cloud Platform Integration (CPI Tenant)" building block (3J2) in S/4HANA automates several of the above steps by providing:

- A pre-built communication system pointing to the CPI tenant
- Pre-configured OAuth 2.0 client credentials
- Communication arrangement linking S/4HANA outbound services to the CPI channel

Steps performed by 3J2:
1. Create communication system (Fiori app: COMM_SYSTEMS)
2. Create communication user or OAuth client
3. Create communication arrangement for the target service
4. Maintain the CPI host URL and credentials in the communication system

Note: SXMB configuration in SXMB_ADM must still be done separately — 3J2 covers the Fiori/communication side only.

Reference: https://help.sap.com/docs/cloud-integration
