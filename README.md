# Valiprop Wholesaling Automation

> A complete Power Platform solution for automating real estate wholesaling. Includes Dataverse tables, Dynamics CRM forms, Power Automate flows, AI enrichment logic, bulk lead import pipelines, and integrations with Twilio, Play.ai, DocuSign, and Rehab Valuator.

[![Power Platform](https://img.shields.io/badge/Power%20Platform-742774?style=flat&logo=microsoft&logoColor=white)](https://powerplatform.microsoft.com)
[![Dynamics 365](https://img.shields.io/badge/Dynamics%20365-002050?style=flat&logo=microsoft&logoColor=white)](https://dynamics.microsoft.com)
[![Twilio](https://img.shields.io/badge/Twilio-F22F46?style=flat&logo=twilio&logoColor=white)](https://twilio.com)
[![DocuSign](https://img.shields.io/badge/DocuSign-FFCC22?style=flat&logo=docusign&logoColor=black)](https://docusign.com)
[![Play.ai](https://img.shields.io/badge/Play.ai-000000?style=flat&logoColor=white)](https://play.ai)
[![Rehab Valuator](https://img.shields.io/badge/Rehab%20Valuator-4CAF50?style=flat&logoColor=white)](https://rehabvaluator.com)

---

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Dataverse Schema Summary](#dataverse-schema-summary)
4. [Dynamics 365 Entity Definitions](#dynamics-365-entity-definitions)
5. [Power Automate Flow List](#power-automate-flow-list)
6. [Integration Notes](#integration-notes)
7. [Setup Instructions](#setup-instructions)
8. [Deployment Steps](#deployment-steps)
9. [Folder Structure](#folder-structure)
10. [FAQ & Troubleshooting](#faq--troubleshooting)
11. [Environment Strategy](#environment-strategy)
12. [Contributing](#contributing)
13. [License](#license)

---

## Overview

**Valiprop Wholesaling Automation** is a production-grade, end-to-end real estate wholesaling system built on Microsoft Power Platform. It replaces every manual touchpoint in the wholesaling pipeline — from raw lead ingestion to signed assignment contracts — with intelligent, event-driven automation.

### What It Automates

| Stage | Capability |
|---|---|
| **Lead Ingestion** | Bulk import motivated seller leads via API and CSV pipelines |
| **AI Enrichment** | Score leads, extract motivation signals, estimate property value |
| **Voice Outreach** | Play.ai AI voice agent calls sellers, handles Q&A, books appointments |
| **SMS Follow-up** | Twilio-powered automated SMS sequences for lead nurturing |
| **ARV & Repair Estimation** | Rehab Valuator integration for MAO calculation inputs |
| **Deal Pipeline** | Dynamics 365 CRM tracks leads from raw inquiry to closed deal |
| **Contract Execution** | DocuSign automates assignment contract generation and e-signature |
| **Reporting** | Power BI dashboards for lead conversion, deal velocity, and ROI |

### Business Model

This system supports a **Valicloud**-branded real estate wholesaling operation. The automation is designed to:

- Run with minimal human intervention on lead intake and outreach stages
- Qualify motivated sellers using AI scoring before any human review
- Present only deal-ready opportunities to the operator for final decision
- Execute contracts and disposition end-to-end without manual document handling

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                          LEAD SOURCES                                    │
│   Bulk CSV Import  ·  Lead API Webhook  ·  Web Form  ·  Inbound Call     │
└───────────────────────────────┬──────────────────────────────────────────┘
                                │
               [Power Automate HTTP Trigger / Scheduled Flow]
                                │
                                ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                  POWER AUTOMATE — ORCHESTRATION LAYER                    │
│                                                                          │
│  ┌──────────────────────┐    ┌─────────────────────────────────────────┐ │
│  │  Lead Intake Flow    │    │  AI Enrichment Flow                     │ │
│  │  (Bulk import /      │───▶│  (Azure AI · scoring · motivation tags) │ │
│  │   API ingest)        │    └─────────────────────┬───────────────────┘ │
│  └──────────────────────┘                          │                     │
│                                                    ▼                     │
│  ┌─────────────────────────────────────────────────────────────────────┐ │
│  │  Qualification & Routing Flow                                       │ │
│  │  · Score ≥ threshold  →  Qualified (Dynamics Opportunity)           │ │
│  │  · Score < threshold  →  Nurture sequence / Disqualify              │ │
│  └──────────┬───────────────────────────┬──────────────────────────────┘ │
│             │                           │                                 │
└─────────────┼───────────────────────────┼─────────────────────────────────┘
              │                           │
    ┌─────────▼──────────┐    ┌───────────▼────────────┐
    │   PLAY.AI AGENT    │    │   TWILIO SMS           │
    │  · Outbound voice  │    │  · Automated sequences │
    │    calls to seller │    │  · Inbound webhook     │
    │  · AI-driven Q&A   │    │  · 2-way conversation  │
    │  · Appointment set │    │  · Opt-out handling    │
    └─────────┬──────────┘    └───────────┬────────────┘
              └─────────────┬─────────────┘
                            ▼
┌──────────────────────────────────────────────────────────────────────────┐
│              DATAVERSE / DYNAMICS 365 — DATA & CRM LAYER                 │
│   Leads · Contacts · Properties · Deals · Offers · Buyers · Activities  │
└──────────┬──────────────────────────────────────────────────────────────┘
     ┌─────┴──────────────────────────────────────┐
     ▼                                            ▼
┌────────────────────┐                ┌───────────────────────────┐
│  REHAB VALUATOR    │                │  DOCUSIGN                 │
│  · ARV lookup      │                │  · Contract auto-gen      │
│  · Repair cost est │                │  · E-signature workflow   │
│  · MAO calculation │                │  · Signed doc → Dataverse │
└────────────────────┘                └───────────────────────────┘
           └──────────────────┬───────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      POWER BI — REPORTING LAYER                          │
│   Deal Pipeline  ·  Lead Funnel  ·  Offer Acceptance  ·  Revenue Trends  │
└──────────────────────────────────────────────────────────────────────────┘
```

### Technology Stack

| Layer | Technology |
|---|---|
| CRM & Pipeline | Microsoft Dynamics 365 Sales |
| Data Platform | Microsoft Dataverse |
| Automation | Power Automate (Cloud Flows) |
| AI Voice Agent | Play.ai |
| SMS / Telephony | Twilio |
| Contract Signing | DocuSign |
| Property Analysis | Rehab Valuator |
| AI Enrichment | Azure AI Language + Azure OpenAI |
| Secrets Management | Azure Key Vault |
| Reporting | Power BI |
| Identity | Microsoft Entra ID |
| ALM | Power Platform Solutions + GitHub |

---

## Dataverse Schema Summary

All custom tables use the `valiprop_` publisher prefix inside the dedicated **Valiprop-Prod** Dataverse environment.

| Display Name | Schema Name | Primary Key | Purpose |
|---|---|---|---|
| Motivated Seller Lead | `valiprop_motivatedsellerlead` | `valiprop_motivatedsellerleadid` | Raw inbound lead — entry point for all ingestion flows |
| Property | `valiprop_property` | `valiprop_propertyid` | Address, type, condition, ARV, repair estimate |
| Deal | `valiprop_deal` | `valiprop_dealid` | Active deal linked to a property and seller contact |
| Offer | `valiprop_offer` | `valiprop_offerid` | Calculated or submitted offer with MAO formula output |
| Buyer | `valiprop_buyer` | `valiprop_buyerid` | Cash buyer / investor in the disposition list |
| Lead Score | `valiprop_leadscore` | `valiprop_leadscoreid` | AI-generated motivation score and enrichment signals |
| Outreach Activity | `valiprop_outreachactivity` | `valiprop_outreachactivityid` | Voice call and SMS log per lead, including Play.ai transcripts |
| DocuSign Envelope | `valiprop_docusignenvelope` | `valiprop_docusignenvelopeid` | Contract envelope status and signed document reference |
| Rehab Estimate | `valiprop_rehabestimate` | `valiprop_rehabestimateid` | Rehab Valuator response: line-item repair costs and ARV |
| Flow Run Log | `valiprop_flowrunlog` | `valiprop_flowrunlogid` | Audit log for all automated flow executions and errors |

### Key Relationships

```
Contact (OOB)                  ──(1:N)──▶  valiprop_motivatedsellerlead
valiprop_motivatedsellerlead   ──(1:1)──▶  valiprop_leadscore
valiprop_motivatedsellerlead   ──(1:N)──▶  valiprop_property
valiprop_property              ──(1:1)──▶  valiprop_rehabestimate
valiprop_property              ──(1:N)──▶  valiprop_deal
valiprop_deal                  ──(1:N)──▶  valiprop_offer
valiprop_deal                  ──(1:N)──▶  valiprop_docusignenvelope
valiprop_deal                  ──(N:N)──▶  valiprop_buyer  [via intersect table]
valiprop_motivatedsellerlead   ──(1:N)──▶  valiprop_outreachactivity
```

---

## Dynamics 365 Entity Definitions

### Out-of-Box (Standard) Entities Used

| Entity | Usage in Valiprop |
|---|---|
| **Contact** | Seller record — name, phone, email; linked to every Lead |
| **Lead** | Raw seller inquiry before AI qualification and conversion |
| **Opportunity** | Qualified deal progressing through CRM pipeline stages |
| **Phone Call / Task / Email** | Manual and automated touchpoints logged per Contact |
| **Queue** | Routes new leads to enrichment or outreach flows |
| **Business Process Flow** | Guides operator through Lead → Qualify → Offer → Contract → Close |

### Custom Field Highlights

#### `valiprop_motivatedsellerlead`

| Field | Schema Name | Type | Description |
|---|---|---|---|
| Full Address | `valiprop_fulladdress` | Single Line of Text | Street address of the property |
| City | `valiprop_city` | Single Line of Text | Property city |
| State | `valiprop_state` | Single Line of Text | 2-character state code |
| Zip Code | `valiprop_zipcode` | Single Line of Text | Property ZIP |
| Seller Asking Price | `valiprop_selleraskinprice` | Currency | Seller's stated price |
| Motivation Score | `valiprop_motivationscore` | Whole Number | 0–100 AI score |
| Lead Source | `valiprop_leadsource` | Choice | Bulk Import, API, Web Form, Inbound Call, Referral |
| Property Type | `valiprop_propertytype` | Choice | SFR, Multi-Family, Land, Commercial |
| AI Status | `valiprop_aistatus` | Choice | Pending, Enriched, Qualified, Disqualified |
| Outreach Status | `valiprop_outreachstatus` | Choice | Not Started, Call Attempted, Reached, Appointment Set |
| Assigned To | `valiprop_assignedto` | Lookup (SystemUser) | Deal owner |

#### `valiprop_deal`

| Field | Schema Name | Type | Description |
|---|---|---|---|
| ARV | `valiprop_arv` | Currency | After-Repair Value |
| Repair Budget | `valiprop_repairbudget` | Currency | Rehab Valuator estimate |
| MAO | `valiprop_mao` | Currency | Computed: `ARV × 0.70 − Repairs` |
| Deal Stage | `valiprop_dealstage` | Choice | Prospecting, Under Contract, Assigned, Closed, Dead |
| Offer Accepted | `valiprop_offeraccepted` | Yes/No | Seller accepted offer flag |
| Contract Sent | `valiprop_contractsent` | Yes/No | DocuSign envelope dispatched |
| Closing Date | `valiprop_closingdate` | Date Only | Target or actual closing date |
| Assignment Fee | `valiprop_assignmentfee` | Currency | Gross assignment profit |

#### `valiprop_offer`

| Field | Schema Name | Type | Description |
|---|---|---|---|
| Offer Amount | `valiprop_offeramount` | Currency | Dollar amount submitted |
| Offer Status | `valiprop_offerstatus` | Choice | Draft, Sent, Accepted, Rejected, Countered |
| Offer Sent Date | `valiprop_offerdatetime` | Date and Time | Timestamp offer was sent |
| Generated By | `valiprop_generatedby` | Choice | AI Auto-Generated, Manual |
| Counter Amount | `valiprop_counteramount` | Currency | Seller counter offer, if any |

#### `valiprop_buyer`

| Field | Schema Name | Type | Description |
|---|---|---|---|
| Buyer Name | `valiprop_buyername` | Single Line of Text | Cash buyer / investor name |
| Email | `valiprop_email` | Email | Primary contact email |
| Phone | `valiprop_phone` | Phone | SMS-capable number |
| Buy Box — City | `valiprop_buycity` | Single Line of Text | Preferred acquisition city |
| Buy Box — Max Price | `valiprop_maxprice` | Currency | Maximum purchase price |
| Property Types | `valiprop_propertytypes` | Multiselect Choice | SFR, Multi-Family, Land |
| Active | `valiprop_isactive` | Yes/No | Include in disposition blasts |

---

## Power Automate Flow List

All flows are packaged inside the **ValipropWholesaling** Power Platform Solution.

| # | Flow Name | Trigger | Description |
|---|---|---|---|
| 1 | **Bulk Lead Import — CSV Ingest** | Manual / Scheduled | Parses uploaded CSV; deduplicates by address + phone; creates `valiprop_motivatedsellerlead` records |
| 2 | **Lead API Ingest — Webhook Receiver** | HTTP POST | Receives inbound lead payloads from external sources; normalizes fields; writes to Dataverse |
| 3 | **Lead Created — AI Enrichment** | Dataverse row created (`valiprop_motivatedsellerlead`) | Calls Azure AI Language + Azure OpenAI; extracts motivation signals; writes `valiprop_leadscore` |
| 4 | **Lead Qualified — Create Opportunity** | Dataverse field changed (`valiprop_aistatus` = Qualified) | Converts lead to Dynamics 365 Opportunity; creates linked Contact and Property records |
| 5 | **Qualified Lead — Trigger Play.ai Voice Call** | Dataverse row created (`valiprop_deal`) | Sends outbound call request to Play.ai API with seller name, phone, and property context |
| 6 | **Play.ai Callback — Process Call Outcome** | HTTP (Play.ai webhook) | Receives call result; updates outreach status; stores transcript in `valiprop_outreachactivity` |
| 7 | **SMS Outreach — Twilio Sequence** | Dataverse field changed (`valiprop_outreachstatus`) | Triggers tiered Twilio SMS sequence (Day 1 / 3 / 7); personalizes with seller and property details |
| 8 | **Inbound SMS — Twilio Receiver** | HTTP (Twilio webhook) | Receives seller SMS reply; matches to lead by phone; updates activity log; routes to Play.ai or human review |
| 9 | **Offer Calculation — Compute MAO** | Dataverse row created (`valiprop_deal`) | Fetches Rehab Valuator estimate; applies `ARV × 0.70 − Repairs`; writes `valiprop_mao` |
| 10 | **Rehab Valuator — Pull Estimate** | Child flow (called by Flow 9) | Calls Rehab Valuator API with property address; writes line-item costs to `valiprop_rehabestimate` |
| 11 | **Offer Accepted — Send DocuSign Contract** | Dataverse field changed (`valiprop_offeraccepted` = Yes) | Generates assignment contract from DocuSign template; populates merge fields; sends envelope to seller |
| 12 | **DocuSign — Envelope Status Sync** | HTTP (DocuSign Connect webhook) | Receives signing events; updates `valiprop_docusignenvelope`; attaches signed PDF to Deal record |
| 13 | **Deal Assigned — Notify Buyer List** | Dataverse field changed (`valiprop_dealstage` = Assigned) | Matches deal to active buyers by buy box; sends deal summary SMS/email blast via Twilio |
| 14 | **Stale Lead — 7-Day Nurture Bump** | Scheduled (daily 6 AM MST) | Finds leads with no activity in 7+ days; triggers new Twilio SMS and Play.ai callback attempt |
| 15 | **Daily Pipeline Report** | Scheduled (daily 7 AM MST) | Aggregates deal stage counts and weekly conversion metrics; emails summary to owner |
| 16 | **Flow Run Logger** | Child flow (called by all parent flows) | Writes execution timestamps, status, and errors to `valiprop_flowrunlog` |

---

## Integration Notes

### Twilio
- **Purpose:** Outbound SMS sequences, inbound 2-way SMS conversation, opt-out management
- **Auth:** Account SID stored as Power Platform Environment Variable (`valiprop_TwilioAccountSID`); Auth Token stored in Azure Key Vault, referenced via Secret Environment Variable (`valiprop_TwilioAuthToken`)
- **Inbound Webhook:** Twilio SMS webhook → Power Automate HTTP trigger (Flow 8)
- **Opt-Out:** STOP keyword handled natively by Twilio; flag synced back to Dataverse via Flow 8
- **From Number:** Provisioned Twilio number in `valiprop_TwilioFromNumber`
- **Rate Limits:** 1 msg/sec default; flows include delay actions for bulk sequences

### Play.ai
- **Purpose:** AI-powered outbound voice calls — initial Q&A, motivation assessment, appointment booking
- **Auth:** API key stored in Azure Key Vault, referenced via Secret Environment Variable (`valiprop_PlayAiApiKey`)
- **Flow:** Power Automate POSTs call request → Play.ai dials seller → AI conversation → webhook callback with transcript + outcome
- **Outcome Codes:** `reached_appointment`, `reached_not_interested`, `voicemail`, `no_answer`, `failed` — mapped to `valiprop_outreachstatus`
- **Transcript:** Full call transcript stored in `valiprop_outreachactivity.valiprop_transcript`

### DocuSign
- **Purpose:** Automated assignment contract generation and e-signature at deal close
- **Auth:** JWT OAuth; RSA private key stored in Azure Key Vault, referenced via Secret Environment Variable (`valiprop_DocuSignPrivateKey`)
- **Template Merge Fields:** `{{SellerName}}`, `{{PropertyAddress}}`, `{{PurchasePrice}}`, `{{ClosingDate}}`, `{{AssignmentFee}}`
- **Webhook (Connect):** Posts `sent`, `delivered`, `completed`, `declined` events → Power Automate Flow 12
- **Signed Doc:** On `completed`, signed PDF fetched and stored as Dataverse file attachment on Deal record
- **Decline Handling:** Sets `valiprop_dealstage` = `Dead`; creates manual review task

### Rehab Valuator
- **Purpose:** Property repair cost estimation and ARV lookup for MAO calculation
- **Auth:** API key stored in Azure Key Vault, referenced via Secret Environment Variable (`valiprop_RehabValApiKey`)
- **MAO Formula:** `MAO = (ARV × 0.70) − Estimated Repair Costs`
- **Fallback:** If address not found, logs warning and sets `valiprop_mao` = `$0` pending manual entry

### Azure AI Services
- **Azure AI Language:** Seller note classification, key phrase extraction, sentiment analysis
- **Azure OpenAI (GPT-4o):** Motivation scoring (0–100), personalized outreach drafts, call script generation
- **Auth:** API keys stored in Azure Key Vault, referenced via Secret Environment Variables (`valiprop_AzureAILanguageKey`, `valiprop_AzureOpenAIKey`)
- **Endpoints:** Stored as Text Environment Variables — never hardcoded in flows

### Power BI
- **Connection:** Direct Query to Dataverse via Power BI Dataflows
- **Workspace:** `Valiprop Analytics`
- **Key Reports:** Lead Funnel, Deal Pipeline, Offer Acceptance Rate, Buyer Engagement, Revenue & ROI

---

## Setup Instructions

### Prerequisites

| Requirement | Details |
|---|---|
| Microsoft 365 tenant | Power Platform must be enabled |
| Power Platform environments | `Valiprop-Dev` (Sandbox) and `Valiprop-Prod` (Production) |
| Dynamics 365 Sales license | Per-user or per-app, assigned to both environments |
| Azure subscription | For Key Vault, Azure AI Language, and Azure OpenAI |
| Twilio account | With a provisioned SMS-capable phone number |
| Play.ai account | API access tier enabled |
| DocuSign account | Production or sandbox with Connect webhooks enabled |
| Rehab Valuator account | API access enabled |
| PAC CLI | [Install guide](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction) |

---

### Step 1 — Provision Azure Resources

All secrets live exclusively in Azure Key Vault. Power Platform Environment Variables reference Key Vault directly — no secret ever touches a local machine, a config file, or a flow definition.

```bash
az login

az group create --name rg-valiprop-prod --location eastus

# Key Vault — single source of truth for all secrets
az keyvault create \
  --name kv-valiprop-prod \
  --resource-group rg-valiprop-prod \
  --location eastus \
  --enable-rbac-authorization true

# Grant your user account permission to manage secrets
az role assignment create \
  --role "Key Vault Secrets Officer" \
  --assignee $(az ad signed-in-user show --query id -o tsv) \
  --scope $(az keyvault show --name kv-valiprop-prod --query id -o tsv)

# Azure AI Language
az cognitiveservices account create \
  --name valiprop-ai-language \
  --resource-group rg-valiprop-prod \
  --kind TextAnalytics --sku S --location eastus

# Azure OpenAI
az cognitiveservices account create \
  --name valiprop-openai \
  --resource-group rg-valiprop-prod \
  --kind OpenAI --sku S0 --location eastus

# Store every secret in Key Vault
az keyvault secret set --vault-name kv-valiprop-prod --name TwilioAuthToken    --value "<from Twilio Console>"
az keyvault secret set --vault-name kv-valiprop-prod --name PlayAiApiKey        --value "<from Play.ai dashboard>"
az keyvault secret set --vault-name kv-valiprop-prod --name DocuSignPrivateKey  --value "<RSA private key PEM>"
az keyvault secret set --vault-name kv-valiprop-prod --name RehabValApiKey      --value "<from Rehab Valuator>"
az keyvault secret set --vault-name kv-valiprop-prod --name AzureAILanguageKey  --value "<from Azure portal>"
az keyvault secret set --vault-name kv-valiprop-prod --name AzureOpenAIKey      --value "<from Azure portal>"
```

Grant the Power Platform service principal read access to Key Vault secrets:

```bash
SP_OBJECT_ID=$(az ad sp show --id "<PP_CLIENT_ID>" --query id -o tsv)

az role assignment create \
  --role "Key Vault Secrets User" \
  --assignee "$SP_OBJECT_ID" \
  --scope $(az keyvault show --name kv-valiprop-prod --query id -o tsv)
```

---

### Step 2 — Configure Power Platform Environments

Repeat these steps for both **Valiprop-Dev** and **Valiprop-Prod**:

1. Go to [Power Platform Admin Center](https://admin.powerplatform.microsoft.com)
2. Create environment — Type: Sandbox (Dev) / Production (Prod), Region: United States
3. Enable Dataverse and add Dynamics 365 Sales
4. Assign security roles:
   - `Valiprop - Admin` — full schema and flow access
   - `Valiprop - Agent` — read/write leads, deals, and activities

---

### Step 3 — Set Environment Variables

Environment Variables are configured in the maker portal and linked directly to Key Vault secrets. **No secret is ever stored in a file, a flow, or hardcoded anywhere.**

#### How the secret flow works

```
Azure Key Vault secret
        ↓
Power Platform Environment Variable (type: Secret, linked to Key Vault)
        ↓
Power Automate flow reads the Environment Variable at runtime
```

#### Configure in the maker portal

1. Go to [make.powerapps.com](https://make.powerapps.com) → select your environment
2. Navigate to **Solutions → ValipropWholesaling → Environment Variables**
3. For each variable below, click it → set its **Current Value**

| Variable Name | Type | Where to get the value |
|---|---|---|
| `valiprop_TwilioAccountSID` | Text | Twilio Console → Account Info |
| `valiprop_TwilioFromNumber` | Text | Twilio Console → Phone Numbers |
| `valiprop_TwilioAuthToken` | Secret → Key Vault | `kv-valiprop-prod` / `TwilioAuthToken` |
| `valiprop_PlayAiAgentId` | Text | Play.ai dashboard → your agent |
| `valiprop_PlayAiApiKey` | Secret → Key Vault | `kv-valiprop-prod` / `PlayAiApiKey` |
| `valiprop_DocuSignIntegrationKey` | Text | DocuSign Admin → Apps & Keys |
| `valiprop_DocuSignAccountId` | Text | DocuSign Admin → Apps & Keys |
| `valiprop_DocuSignTemplateId` | Text | DocuSign → Templates → your template |
| `valiprop_DocuSignPrivateKey` | Secret → Key Vault | `kv-valiprop-prod` / `DocuSignPrivateKey` |
| `valiprop_AzureAILanguageEndpoint` | Text | Azure portal → Language resource → Keys and Endpoint |
| `valiprop_AzureAILanguageKey` | Secret → Key Vault | `kv-valiprop-prod` / `AzureAILanguageKey` |
| `valiprop_AzureOpenAIEndpoint` | Text | Azure portal → OpenAI resource → Keys and Endpoint |
| `valiprop_AzureOpenAIKey` | Secret → Key Vault | `kv-valiprop-prod` / `AzureOpenAIKey` |
| `valiprop_RehabValApiKey` | Secret → Key Vault | `kv-valiprop-prod` / `RehabValApiKey` |
| `valiprop_OwnerEmail` | Text | Your operator email address |

> **Linking a Secret variable to Key Vault:**
> Click the variable → **New value** → toggle to **Azure Key Vault** → paste the secret URI:
> `https://kv-valiprop-prod.vault.azure.net/secrets/<SecretName>`

---

### Step 4 — Register Webhooks

**Twilio (inbound SMS):**
Twilio Console → Phone Numbers → your number → Messaging → set webhook to Flow 8 HTTP trigger URL, method POST

**Play.ai (call callback):**
Play.ai Dashboard → Webhooks → set callback URL to Flow 6 HTTP trigger URL

**DocuSign Connect:**
DocuSign Admin → Connect → Add Configuration → set URL to Flow 12 HTTP trigger URL → subscribe to: `envelope-sent`, `envelope-delivered`, `envelope-completed`, `envelope-declined`

---

### Step 5 — Import the Solution

```bash
pac auth create --environment https://[your-org].crm.dynamics.com

pac solution import \
  --path ./solutions/ValipropWholesaling_managed.zip \
  --activate-plugins true
```

After import, verify all flows are **On** and all connections are authenticated in Power Automate.

---

### Step 6 — Configure Play.ai Agent Script

In the Play.ai dashboard, set the agent system prompt to define:

- **Persona:** Friendly buyer's rep for a real estate investment firm
- **Questions:** Property condition, reason for selling, timeline, mortgage status, occupancy
- **Appointment booking:** Redirect to Calendly or Outlook Calendar link
- **Disqualification:** If not motivated → flag `not_interested` and end call politely

---

## Deployment Steps

### ALM Strategy

```
Dev Environment  →  [Export Unmanaged]  →  GitHub  →  [Pack Managed]  →  Prod
```

> Never develop directly in Prod. All changes flow Dev → GitHub → Prod via managed solution import.

### Export from Dev

```bash
pac solution export \
  --name ValipropWholesaling \
  --path ./solutions/ \
  --managed false

pac solution unpack \
  --zipfile ./solutions/ValipropWholesaling.zip \
  --folder ./src/ValipropWholesaling \
  --processCanvasApps true

git add ./src/ValipropWholesaling
git commit -m "feat: <describe change>"
git push origin main
```

### Deploy to Production

```bash
pac solution pack \
  --zipfile ./solutions/ValipropWholesaling_managed.zip \
  --folder ./src/ValipropWholesaling \
  --managed true

pac solution import \
  --path ./solutions/ValipropWholesaling_managed.zip \
  --activate-plugins true \
  --force-overwrite true \
  --publish-changes true
```

### GitHub Actions CI/CD

`.github/workflows/deploy.yml` triggers automatically on every push to `main`. It runs four jobs in sequence: solution validation → production import → post-deploy verification → failure notification.

**Required GitHub Secrets:**

| Secret | Description |
|---|---|
| `PP_CLIENT_ID` | Entra ID service principal App ID |
| `PP_CLIENT_SECRET` | Service principal secret |
| `PP_TENANT_ID` | Microsoft Entra tenant ID |
| `PP_ENVIRONMENT_URL` | e.g. `https://valiprop.crm.dynamics.com` |

---

## Folder Structure

Once the solution is unpacked via `pac solution unpack`, the repository follows this structure:

```
valiprop-wholesaling-automation/
│
├── .github/
│   └── workflows/
│       └── deploy.yml                    # GitHub Actions CI/CD pipeline
│
├── solutions/
│   ├── ValipropWholesaling.zip           # Unmanaged solution export (dev)
│   └── ValipropWholesaling_managed.zip   # Managed solution for production deploy
│
├── src/
│   └── ValipropWholesaling/              # Unpacked solution source
│       ├── Entities/                     # Dataverse custom table definitions
│       │   ├── valiprop_motivatedsellerlead/
│       │   │   ├── Entity.xml
│       │   │   └── Attributes/
│       │   │       ├── valiprop_fulladdress.xml
│       │   │       ├── valiprop_motivationscore.xml
│       │   │       ├── valiprop_aistatus.xml
│       │   │       └── ...
│       │   ├── valiprop_deal/
│       │   ├── valiprop_offer/
│       │   ├── valiprop_buyer/
│       │   ├── valiprop_property/
│       │   ├── valiprop_leadscore/
│       │   ├── valiprop_outreachactivity/
│       │   ├── valiprop_docusignenvelope/
│       │   ├── valiprop_rehabestimate/
│       │   └── valiprop_flowrunlog/
│       │
│       ├── Workflows/                    # Power Automate cloud flow definitions
│       │   ├── BulkLeadImport_CSV.json
│       │   ├── LeadAPIIngest_Webhook.json
│       │   ├── LeadCreated_AIEnrichment.json
│       │   ├── LeadQualified_CreateOpportunity.json
│       │   ├── QualifiedLead_PlayAiCall.json
│       │   ├── PlayAiCallback_ProcessOutcome.json
│       │   ├── SMSOutreach_TwilioSequence.json
│       │   ├── InboundSMS_TwilioReceiver.json
│       │   ├── OfferCalculation_ComputeMAO.json
│       │   ├── RehabValuator_PullEstimate.json
│       │   ├── OfferAccepted_DocuSignContract.json
│       │   ├── DocuSign_EnvelopeStatusSync.json
│       │   ├── DealAssigned_NotifyBuyerList.json
│       │   ├── StaleLead_NurtureBump.json
│       │   ├── DailyPipelineReport.json
│       │   └── FlowRunLogger_Child.json
│       │
│       ├── Forms/                        # Dynamics 365 CRM form definitions
│       │   ├── valiprop_motivatedsellerlead_MainForm.xml
│       │   ├── valiprop_deal_MainForm.xml
│       │   ├── valiprop_offer_MainForm.xml
│       │   └── valiprop_buyer_MainForm.xml
│       │
│       ├── Views/                        # Dataverse saved views
│       │   ├── valiprop_motivatedsellerlead_ActiveLeads.xml
│       │   ├── valiprop_deal_OpenDeals.xml
│       │   └── valiprop_buyer_ActiveBuyers.xml
│       │
│       ├── EnvironmentVariableDefinitions/
│       │   ├── valiprop_TwilioAccountSID.xml
│       │   ├── valiprop_PlayAiApiKey.xml
│       │   ├── valiprop_DocuSignTemplateId.xml
│       │   └── ...
│       │
│       ├── OptionSets/                   # Global choice definitions
│       │   ├── valiprop_aistatus.xml
│       │   ├── valiprop_dealstage.xml
│       │   ├── valiprop_offerstatus.xml
│       │   └── valiprop_outreachstatus.xml
│       │
│       ├── BusinessProcessFlows/
│       │   └── ValipropDealProcess.xml
│       │
│       ├── SecurityRoles/
│       │   ├── ValipropAdmin.xml
│       │   └── ValipropAgent.xml
│       │
│       ├── [Content_Types].xml
│       └── solution.xml                  # Solution manifest
│
├── docs/
│   ├── architecture-diagram.png          # System architecture visual
│   ├── dataverse-schema.png              # Entity relationship diagram
│   ├── flow-map.md                       # Flow dependency map
│   └── play-ai-script.md                 # Play.ai agent system prompt + Q&A script
│
├── scripts/
│   ├── export-dev.sh                     # One-command Dev export + unpack + git commit
│   └── deploy-prod.sh                    # One-command manual pack + import to production
│
└── README.md
```

### Key Directory Notes

| Directory | Purpose |
|---|---|
| `src/ValipropWholesaling/` | Never edit XML/JSON files directly — always export via `pac solution unpack` |
| `solutions/` | Build artifacts — `.zip` files git-ignored in dev; committed only on release tags |
| `docs/` | Human-readable architecture and design docs; update when schema changes |
| `scripts/` | Convenience wrappers around PAC CLI for common dev operations |
| `.github/workflows/` | CI/CD pipeline — auto-triggers on push to `main` |

---

## FAQ & Troubleshooting

### General

**Q: The repo only has a README — where is the actual solution code?**

The Power Platform solution lives inside your Dataverse environment. To bootstrap source control, run:

```bash
pac solution export --name ValipropWholesaling --path ./solutions/ --managed false
pac solution unpack --zipfile ./solutions/ValipropWholesaling.zip --folder ./src/ValipropWholesaling --processCanvasApps true
git add ./src && git commit -m "chore: initial solution export" && git push
```

This is a one-time step. GitHub Actions handles all subsequent deployments automatically.

---

**Q: How do I find my Power Platform environment URL?**

Power Platform Admin Center → Environments → **Valiprop-Prod** → copy the **Environment URL** (e.g. `https://valiprop.crm.dynamics.com`).

---

**Q: What licenses do I need?**

- **Power Automate Premium** — for Dataverse connector and HTTP triggers
- **Dynamics 365 Sales** — for Opportunity, Lead, and CRM forms
- **Azure subscription** — for Key Vault, Azure AI Language, and Azure OpenAI
- **Twilio** — pay-as-you-go or standard plan
- **Play.ai** — API-enabled plan
- **DocuSign** — Business Pro or above (for Connect webhooks)
- **Rehab Valuator** — API plan

---

### Lead Ingestion

**Q: The CSV bulk import flow is failing — what do I check first?**

1. Verify CSV column headers exactly match field names in the flow's `Parse JSON` action
2. Check for special characters or encoding issues — use UTF-8
3. Confirm the Dataverse connection is authenticated with a licensed user
4. Open `valiprop_flowrunlog` in Dataverse for the exact error logged by the Flow Run Logger

---

**Q: Duplicate leads are being created — why?**

Deduplication in Flows 1 and 2 matches on `valiprop_fulladdress` **and** `valiprop_phonenumber`. Duplicates slip through when:

- Phone numbers use inconsistent formats — normalize to E.164 (`+16025551234`) at source
- Addresses differ slightly (`St` vs `Street`) — add a Dataverse duplicate detection rule or a fuzzy-match step

---

### AI Enrichment

**Q: Enrichment completes but motivation scores are all 0.**

1. Confirm `valiprop_AzureOpenAIEndpoint` and `valiprop_AzureOpenAIKey` Environment Variables are correctly linked to Key Vault
2. Verify the GPT-4o deployment name in Azure OpenAI Studio matches exactly what the flow's HTTP action uses
3. Check flow run history for the Azure OpenAI HTTP call error body — quota or auth issues appear there
4. Ensure `gpt-4o` is deployed in your Azure OpenAI resource, not just `gpt-35-turbo`

---

**Q: Azure AI Language is returning empty key phrases.**

- `valiprop_sellernotes` is likely empty on the lead record — enrichment only fires when seller notes exist
- If notes exist, confirm the Language endpoint URL has no trailing slash and includes the correct API version path

---

### Twilio / SMS

**Q: Inbound SMS is not triggering Flow 8.**

1. Confirm the Twilio webhook URL matches the current Power Automate HTTP trigger URL — these change when flows are deleted and recreated
2. Check Twilio Console → Monitor → Errors for failed delivery attempts
3. Ensure Flow 8 is **turned On** — flows do not auto-enable after solution import

---

**Q: Sellers are receiving duplicate SMS messages.**

- Multiple active triggers on Flow 7 — common after importing the solution twice without deactivating the old version
- Review `valiprop_outreachactivity` records to trace which flow instance sent each message
- Add a guard condition to Flow 7: check `valiprop_outreachstatus` before sending to prevent re-triggering

---

### Play.ai

**Q: Calls are not connecting / always returning `no_answer`.**

1. Verify seller phone is in E.164 format in Dataverse (`+16025551234`)
2. Confirm `valiprop_PlayAiApiKey` Key Vault link is active and the secret has not expired
3. Check Play.ai dashboard for call attempt logs and error codes
4. Confirm your Play.ai plan supports outbound calling to US numbers
5. Test with a known-good number via the Play.ai API directly before re-running the flow

---

**Q: Call transcripts are not appearing in Dataverse.**

- Flow 6 only fires when Play.ai POSTs to its registered webhook URL — confirm that URL is correctly set in Play.ai's dashboard
- Check Flow 6 run history for parse errors — the most common cause is a JSON payload mismatch between what Play.ai sends and what the `Parse JSON` action expects
- Regenerate the `Parse JSON` schema in Flow 6 using a sample payload from Play.ai's docs

---

### DocuSign

**Q: The assignment contract is not being sent after offer acceptance.**

1. Confirm `valiprop_offeraccepted` is set to boolean `true` — not a text string `"Yes"`
2. Verify `valiprop_DocuSignTemplateId` and `valiprop_DocuSignAccountId` are set in Environment Variables
3. Check that the DocuSign JWT RSA key pair in Key Vault is current and the app has "Send on Behalf Of" permission granted
4. Review Flow 11 run history — `401` = auth issue, `400` = bad template field mapping

---

**Q: DocuSign Connect is not sending events to Flow 12.**

1. In DocuSign Admin → Connect → your config → click **Test** to confirm the Power Automate URL is reachable
2. Re-register the webhook URL if the solution was re-imported — HTTP trigger URLs change on flow recreation
3. DocuSign requires HTTP 200 within 10 seconds — add an immediate `Response` action at the top of Flow 12 and process the payload asynchronously to avoid timeouts

---

### Rehab Valuator

**Q: MAO is always $0.**

- Flow 10 likely failed to find the property — check if the address format matches what Rehab Valuator expects
- Review `valiprop_rehabestimate` — if no record exists, the child flow never completed successfully
- Common cause: address not in Rehab Valuator's database (rural or very new properties)
- For unfound properties, manually enter ARV and repair budget directly on the Deal record — `valiprop_mao` accepts manual override

---

### Deployment & ALM

**Q: `pac solution import` fails with a dependency error.**

- All Environment Variables must exist and be configured before import — missing variables block the process
- Run `pac solution check` on the zip to surface issues before importing
- Remove the failing flow from the solution in Dev, import the rest, then add it back separately

---

**Q: Flows are off after a solution import — why?**

Power Platform does not auto-enable flows after a managed solution import. After every Prod import, manually turn on all 16 flows in Power Automate. The post-deploy job in `deploy.yml` will log a reminder checklist in the Actions run summary.

---

**Q: How do I roll back a bad deployment?**

1. You must import the previous managed `.zip` version — partial rollback is not possible for managed solutions
2. Tag each managed release in GitHub (e.g. `v1.2.0`) so prior `.zip` artifacts are always accessible for re-import
3. For flow-only issues, toggle individual flows off while debugging without re-importing the full solution

---

**Q: The GitHub Actions workflow fails at authentication.**

1. Confirm all four secrets are set in GitHub → Settings → Secrets and variables → Actions: `PP_CLIENT_ID`, `PP_CLIENT_SECRET`, `PP_TENANT_ID`, `PP_ENVIRONMENT_URL`
2. The service principal must have **Power Platform Administrator** or **System Administrator** role in the target environment
3. Check Entra ID → App registrations → your app → Certificates & secrets for expiry

---

**Q: A Key Vault secret was rotated — do I need to update anything in Power Platform?**

No. Power Platform Environment Variables linked to Key Vault always fetch the **current** secret value at runtime. Rotate the secret in Key Vault and Power Automate will automatically use the new value on the next flow run — no changes needed in the maker portal or in the solution.

---

## Environment Strategy

| Environment | Name | Purpose | Solution Type |
|---|---|---|---|
| Development | `Valiprop-Dev` | Build, test, and iterate on flows and AI agents | Unmanaged |
| Production | `Valiprop-Prod` | Live automation with real leads and deals | Managed |

**Dev is the right place to:**
- Edit and test Power Automate flows
- Iterate on Play.ai agent scripts and conversation logic
- Add or modify Dataverse tables and fields
- Test new integrations before they touch real sellers

**Never in Prod:**
- Edit flows directly in the cloud portal
- Test with live webhooks pointing at real seller data
- Import unmanaged solutions

---

## Contributing

Internal guidelines for the Valicloud team:

1. Branch from `main` using `feat/`, `fix/`, or `chore/` prefixes
2. All solution changes must be exported and unpacked via `pac solution unpack` before committing
3. **All secrets live in Azure Key Vault only** — never store a key, token, or password in source control, a flow definition, a config file, or any other location
4. Test all flows end-to-end in `Valiprop-Dev` before pushing to `main`
5. Document any new Dataverse table or field in this README
6. Webhook trigger URLs must never be committed — they are environment-specific and registered manually post-deployment

---

## License

Private — © 2026 Valicloud / Johnny. All rights reserved.
Not licensed for public use, redistribution, or commercial adaptation.
