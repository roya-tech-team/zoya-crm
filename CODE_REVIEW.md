# Zoya CRM - Comprehensive Code Review

## Table of Contents
1. [Technologies Used](#1-technologies-used)
2. [Structure Quality Assessment](#2-structure-quality-assessment)
3. [Maintainability & Customization](#3-maintainability--customization)
4. [Deployment & Release](#4-deployment--release)
5. [Database & Caching](#5-database--caching)
6. [List of Modules](#6-list-of-modules)
7. [Architecture Diagram](#7-architecture-diagram)
8. [Flow Diagram](#8-flow-diagram)
9. [Recommendations](#9-recommendations)
10. [Developer Setup Guide](#10-developer-setup-guide)
11. [Building a Full Module - Contract Example](#11-building-a-full-module---contract-example)
12. [Conclusion & Rating](#12-conclusion--rating)

---

## 1. Technologies Used

### Backend Stack
| Technology | Version | Purpose |
|------------|---------|---------|
| **Python** | ≥3.10 | Primary backend language |
| **Frappe Framework** | ~15.0.0 | Full-stack web framework (ORM, REST API, Auth, Jobs) |
| **MariaDB** | 10.8+ | Primary database |
| **Redis** | Alpine | Caching, session management, real-time updates |
| **Socket.IO** | - | Real-time communication |
| **Twilio SDK** | 8.5.0 | Voice/SMS integration |

### Frontend Stack
| Technology | Version | Purpose |
|------------|---------|---------|
| **Vue.js** | 3.5.13 | Reactive UI framework |
| **Vue Router** | 4.2.2 | Client-side routing |
| **Pinia** | 2.0.33 | State management |
| **Frappe UI** | 0.1.248 | Component library (Frappe ecosystem) |
| **Vite** | 4.4.9 | Build tool & dev server |
| **TailwindCSS** | 3.4.15 | Utility-first CSS framework |
| **Tiptap** | 2.12.0 | Rich text editor |
| **Socket.IO Client** | 4.7.2 | Real-time updates |
| **Twilio Voice SDK** | 2.10.2 | Browser-based calling |

### DevOps & Tooling
| Technology | Purpose |
|------------|---------|
| **Docker Compose** | Containerized development |
| **Flit** | Python package building |
| **Ruff** | Python linting & formatting |
| **PostCSS/Autoprefixer** | CSS processing |
| **PWA (Vite Plugin)** | Progressive Web App support |

---

## 2. Structure Quality Assessment

### ✅ Strengths

```
zoya-crm/
├── crm/                          # Backend (Frappe App)
│   ├── api/                      # ✅ Clean REST API layer
│   ├── fcrm/doctype/            # ✅ Well-organized doctypes
│   ├── integrations/            # ✅ Separated integration logic
│   ├── lead_syncing/            # ✅ Modular feature organization
│   ├── overrides/               # ✅ Proper extension pattern
│   ├── patches/                 # ✅ Version migration scripts
│   └── utils/                   # ✅ Shared utilities
├── frontend/                     # Frontend (Vue SPA)
│   └── src/
│       ├── components/          # ✅ Rich component library (298 files)
│       ├── composables/         # ✅ Reusable Vue composables
│       ├── stores/              # ✅ Centralized state management
│       ├── pages/               # ✅ Clear page organization
│       └── utils/               # ✅ Frontend utilities
└── docker/                      # ✅ Containerization support
```

### ⚠️ Areas of Concern

| Aspect | Status | Notes |
|--------|--------|-------|
| **Separation of Concerns** | ⚠️ Moderate | Backend couples ORM with business logic in doctypes |
| **Test Coverage** | ⚠️ Basic | Test files exist but appear minimal |
| **Component Granularity** | ⚠️ High | 298 components may indicate some redundancy |
| **Error Handling** | ⚠️ Inconsistent | Some APIs lack proper error handling |

### Structure Quality Score: **7.5/10**

---

## 3. Maintainability & Customization

### ✅ Easy to Customize

| Feature | Implementation |
|---------|---------------|
| **Custom Fields** | Frappe's native custom field support |
| **Form Scripts** | `crm_form_script` doctype for custom behaviors |
| **Field Layouts** | `crm_fields_layout` - configurable form layouts |
| **View Settings** | `crm_view_settings` - customizable list/kanban views |
| **Assignment Rules** | Flexible lead/deal assignment automation |
| **SLA Configuration** | Service Level Agreements with priorities |

### ✅ Extensibility Points

```python
# hooks.py - Event-driven extensibility
doc_events = {
    "Contact": {"validate": ["crm.api.contact.validate"]},
    "CRM Deal": {"on_update": ["crm.fcrm.doctype..."]},
}

# Scheduled Tasks
scheduler_events = {
    "daily": ["crm.api.event.trigger_daily_event_notifications"],
    "cron": {"*/5 * * * *": ["crm.lead_syncing..."]},
}
```

### ⚠️ Maintenance Challenges

| Challenge | Impact |
|-----------|--------|
| **Frappe Dependency** | Tightly coupled to Frappe ecosystem |
| **Version Migrations** | Complex patch system needed |
| **Learning Curve** | Requires Frappe knowledge |
| **TypeScript Adoption** | Mostly JS, limited TS (only 3 `.ts` files) |

### Maintainability Score: **7/10**

---

## 4. Deployment & Release

### ✅ Deployment Options

```yaml
# docker/docker-compose.yml - Ready for containerization
services:
  mariadb:
    image: mariadb:10.8
  redis:
    image: redis:alpine
  frappe:
    image: frappe/bench:latest
    ports:
      - 8000:8000
      - 9000:9000
```

### Deployment Methods

| Method | Complexity | Production Ready |
|--------|------------|-----------------|
| **Docker Compose** | Low | ⚠️ Dev only |
| **Frappe Bench** | Medium | ✅ Yes |
| **Frappe Cloud** | Low | ✅ Yes (managed) |
| **Manual Setup** | High | ✅ Yes |

### Build Process

```bash
# Frontend Build
yarn build  # Vite build → /assets/crm/frontend/

# Python Package
flit build  # Creates wheel/sdist
```

### Release Considerations

| Aspect | Status |
|--------|--------|
| **CI/CD Pipeline** | ❌ Not included |
| **Migration Scripts** | ✅ Patch system present |
| **Version Management** | ⚠️ Dynamic versioning |
| **Rollback Strategy** | ⚠️ Depends on Frappe bench |

### Deployment Score: **6.5/10**

---

## 5. Database & Caching

### Database: MariaDB

```yaml
# Configuration
mariadb:
  image: mariadb:10.8
  command:
    - --character-set-server=utf8mb4
    - --collation-server=utf8mb4_unicode_ci
```

#### ORM Features (Frappe)
- **Query Builder**: PyPika integration
- **Caching**: Document caching (`frappe.get_cached_doc`)
- **Indexing**: Automatic index management
- **Migrations**: DocType-driven schema changes

### Caching: Redis

```python
# Document Caching Example
deal = frappe.get_cached_doc("CRM Deal", deal_name)

# Resource Caching (Frontend)
getCachedResource(data.cache_key)
getCachedListResource(data.cache_key)
```

| Cache Type | Technology | Use Case |
|------------|------------|----------|
| **Session** | Redis | User sessions |
| **Document** | Redis | Frequent doc access |
| **Queue** | Redis + RQ | Background jobs |
| **Real-time** | Socket.IO + Redis | Live updates |

### Database Schema Highlights

| DocType | Purpose | Key Fields |
|---------|---------|------------|
| `CRM Lead` | Lead management | status, lead_owner, sla |
| `CRM Deal` | Deal pipeline | status, deal_value, probability |
| `CRM Organization` | Company records | annual_revenue, territory |
| `Contact` | Contact info | email_ids, phone_nos |
| `CRM Call Log` | Call tracking | duration, recording_url |

---

## 6. List of Modules

### Core CRM Modules (FCRM)

| Module | DocType | Description |
|--------|---------|-------------|
| **Leads** | `CRM Lead` | Lead capture and qualification |
| **Deals** | `CRM Deal` | Sales pipeline management |
| **Organizations** | `CRM Organization` | Company/account management |
| **Contacts** | Contact | People management |
| **Tasks** | `CRM Task` | Task tracking |
| **Notes** | `FCRM Note` | Notes and documentation |
| **Call Logs** | `CRM Call Log` | Call history tracking |
| **Dashboard** | `CRM Dashboard` | Analytics dashboard |
| **Notifications** | `CRM Notification` | In-app notifications |

### Configuration Modules

| Module | Purpose |
|--------|---------|
| `CRM Lead Status` | Lead workflow stages |
| `CRM Deal Status` | Deal pipeline stages |
| `CRM Lead Source` | Lead origin tracking |
| `CRM Industry` | Industry categorization |
| `CRM Territory` | Geographic territories |
| `CRM Lost Reason` | Deal loss tracking |
| `CRM Fields Layout` | Form customization |
| `CRM View Settings` | View preferences |
| `CRM Form Script` | Custom behaviors |

### Integration Modules

| Module | Purpose |
|--------|---------|
| `CRM Twilio Settings` | Twilio voice integration |
| `CRM Exotel Settings` | Exotel integration |
| `CRM Telephony Agent` | Agent phone mapping |
| `ERPNext CRM Settings` | ERPNext sync |
| `Helpdesk CRM Settings` | Helpdesk integration |

### Lead Syncing Modules

| Module | Purpose |
|--------|---------|
| `Lead Sync Source` | External lead sources |
| `Facebook Page` | Facebook integration |
| `Facebook Lead Form` | FB form mapping |
| `Failed Lead Sync Log` | Error tracking |

### Service Level Modules

| Module | Purpose |
|--------|---------|
| `CRM Service Level Agreement` | SLA definitions |
| `CRM Service Level Priority` | Priority levels |
| `CRM Holiday List` | Business hours |
| `CRM Status Change Log` | Audit trail |

---

## 7. Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              CLIENT LAYER                                │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐  │
│  │   Vue SPA    │  │   PWA App    │  │  Mobile Web  │  │ Twilio SDK  │  │
│  │  (Vite+Vue3) │  │              │  │              │  │             │  │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬──────┘  │
│         │                 │                 │                 │         │
│         └─────────────────┴─────────────────┴─────────────────┘         │
│                                    │                                     │
│                          ┌─────────▼─────────┐                          │
│                          │    Frappe UI      │                          │
│                          │  Component Lib    │                          │
│                          └─────────┬─────────┘                          │
└────────────────────────────────────┼────────────────────────────────────┘
                                     │
                          ┌──────────▼──────────┐
                          │    Socket.IO        │
                          │   (Real-time)       │
                          └──────────┬──────────┘
                                     │
┌────────────────────────────────────┼────────────────────────────────────┐
│                          APPLICATION LAYER                               │
├────────────────────────────────────┼────────────────────────────────────┤
│                          ┌─────────▼─────────┐                          │
│                          │   Frappe Server   │                          │
│                          │   (Python/WSGI)   │                          │
│                          └─────────┬─────────┘                          │
│                                    │                                     │
│    ┌───────────────────────────────┼───────────────────────────────┐    │
│    │                               │                               │    │
│  ┌─▼──────────┐  ┌─────────────────▼─────────────────┐  ┌─────────▼─┐  │
│  │  REST API  │  │           CRM Module              │  │  Hooks    │  │
│  │  Layer     │  │  ┌───────────────────────────┐   │  │  System   │  │
│  │            │  │  │      DocTypes (ORM)       │   │  │           │  │
│  │ - doc.py   │  │  │  - CRM Lead               │   │  │ doc_events│  │
│  │ - views.py │  │  │  - CRM Deal               │   │  │ scheduler │  │
│  │ - auth.py  │  │  │  - CRM Organization       │   │  │ webhooks  │  │
│  │ - event.py │  │  │  - Contact                │   │  │           │  │
│  └────────────┘  │  │  - CRM Task               │   │  └───────────┘  │
│                  │  └───────────────────────────┘   │                  │
│                  │                                   │                  │
│                  │  ┌───────────────────────────┐   │                  │
│                  │  │      Integrations         │   │                  │
│                  │  │  - Twilio (Voice)         │   │                  │
│                  │  │  - Exotel                 │   │                  │
│                  │  │  - Facebook Leads         │   │                  │
│                  │  │  - ERPNext               │   │                  │
│                  │  └───────────────────────────┘   │                  │
│                  └───────────────────────────────────┘                  │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
┌────────────────────────────────────┼────────────────────────────────────┐
│                            DATA LAYER                                    │
├────────────────────────────────────┼────────────────────────────────────┤
│    ┌───────────────────────────────┼───────────────────────────────┐    │
│    │                               │                               │    │
│  ┌─▼──────────┐  ┌─────────────────▼─────────────────┐  ┌─────────▼─┐  │
│  │  MariaDB   │  │             Redis                 │  │   File    │  │
│  │  10.8      │  │  ┌─────────┐  ┌──────────────┐   │  │  Storage  │  │
│  │            │  │  │ Session │  │    Cache     │   │  │           │  │
│  │ - DocTypes │  │  │         │  │              │   │  │ - Uploads │  │
│  │ - Custom   │  │  └─────────┘  └──────────────┘   │  │ - Assets  │  │
│  │   Fields   │  │  ┌─────────┐  ┌──────────────┐   │  │           │  │
│  │ - Indexes  │  │  │  Queue  │  │   Pub/Sub    │   │  │           │  │
│  │            │  │  │  (RQ)   │  │ (Socket.IO)  │   │  │           │  │
│  └────────────┘  │  └─────────┘  └──────────────┘   │  └───────────┘  │
│                  └───────────────────────────────────┘                  │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         EXTERNAL SERVICES                                │
├─────────────────────────────────────────────────────────────────────────┤
│   ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────────────┐   │
│   │  Twilio   │  │  Exotel   │  │ Facebook  │  │     ERPNext       │   │
│   │  Voice    │  │  Voice    │  │  Lead Ads │  │  (Optional)       │   │
│   └───────────┘  └───────────┘  └───────────┘  └───────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 8. Flow Diagram

### Lead to Deal Conversion Flow

```
┌──────────────────────────────────────────────────────────────────────────┐
│                        LEAD ACQUISITION FLOW                              │
└──────────────────────────────────────────────────────────────────────────┘

    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │   Manual    │     │  Facebook   │     │    API      │
    │   Entry     │     │   Lead Ads  │     │   Import    │
    └──────┬──────┘     └──────┬──────┘     └──────┬──────┘
           │                   │                   │
           └───────────────────┼───────────────────┘
                               │
                               ▼
                    ┌──────────────────┐
                    │   Create Lead    │
                    │   (CRM Lead)     │
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              │      before_validate        │
              │    - Apply SLA             │
              └──────────────┬──────────────┘
                             │
              ┌──────────────┴──────────────┐
              │        validate             │
              │  - Set full name            │
              │  - Validate email           │
              │  - Assign agent             │
              │  - Log status change        │
              └──────────────┬──────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Lead Status:   │
                    │      "New"       │
                    └────────┬─────────┘
                             │
           ┌─────────────────┼─────────────────┐
           ▼                 ▼                 ▼
    ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
    │  Contacted  │   │   Nurture   │   │  Qualified  │
    └──────┬──────┘   └──────┬──────┘   └──────┬──────┘
           │                 │                 │
           └─────────────────┴─────────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Convert to Deal  │
                    │ convert_to_deal()│
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              │  1. Create/Find Contact     │
              │  2. Create/Find Org         │
              │  3. Create Deal             │
              │  4. Mark Lead "Qualified"   │
              └──────────────┬──────────────┘
                             │
                             ▼

┌──────────────────────────────────────────────────────────────────────────┐
│                          DEAL PIPELINE FLOW                               │
└──────────────────────────────────────────────────────────────────────────┘

                    ┌──────────────────┐
                    │   Deal Created   │
                    │  "Qualification" │
                    │   Prob: 10%      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Demo/Making    │
                    │   Prob: 25%      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Proposal/Quote   │
                    │   Prob: 50%      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Negotiation    │
                    │   Prob: 70%      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  Ready to Close  │
                    │   Prob: 90%      │
                    └────────┬─────────┘
                             │
              ┌──────────────┴──────────────┐
              ▼                             ▼
    ┌──────────────────┐          ┌──────────────────┐
    │       WON        │          │       LOST       │
    │   Prob: 100%     │          │   Prob: 0%       │
    │ - Set close date │          │ - Require reason │
    │ - Sync to ERPNext│          │ - Log notes      │
    └──────────────────┘          └──────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│                       REAL-TIME UPDATE FLOW                               │
└──────────────────────────────────────────────────────────────────────────┘

    ┌─────────────┐
    │ User Action │
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │   Frappe    │────▶│   Redis     │────▶│ Socket.IO   │
    │   Server    │     │   Pub/Sub   │     │   Server    │
    └─────────────┘     └─────────────┘     └──────┬──────┘
                                                   │
           ┌───────────────────────────────────────┘
           │
           ▼
    ┌─────────────┐
    │  Event:     │
    │refetch_     │
    │resource     │
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐     ┌─────────────┐
    │   Vue App   │────▶│   Resource  │
    │  Listener   │     │   Reload    │
    └─────────────┘     └─────────────┘
```

---

## 9. Recommendations

### Should You Use This CRM or Build From Scratch?

#### ✅ Use This CRM If:

| Scenario | Reason |
|----------|--------|
| **Time-to-market is critical** | Ready-to-use with < 1 week setup |
| **Already using Frappe/ERPNext** | Native integration, shared infrastructure |
| **Standard CRM needs** | Covers 80%+ of typical CRM requirements |
| **Small-to-medium team** | Scales well for 1-100 sales users |
| **Budget constraints** | Open-source, no licensing costs |
| **Need customization** | Extensible through doctypes and scripts |

#### ❌ Build From Scratch If:

| Scenario | Reason |
|----------|--------|
| **Unique business logic** | Non-standard workflows that conflict with Frappe patterns |
| **Massive scale** | >10M records, >1000 concurrent users |
| **Multi-tenant SaaS** | Frappe multi-tenancy has limitations |
| **Full stack control** | Team prefers different tech stack |
| **Microservices architecture** | Frappe is monolithic by design |

### Recommended Improvements

#### High Priority

1. **Add TypeScript** - Convert frontend JS to TypeScript for better maintainability
2. **Improve Test Coverage** - Add unit and integration tests
3. **Add CI/CD Pipeline** - Automate testing and deployment
4. **Error Handling** - Standardize error responses across APIs

#### Medium Priority

5. **API Documentation** - Add OpenAPI/Swagger documentation
6. **Performance Monitoring** - Integrate APM tools
7. **Component Refactoring** - Reduce component count through composition
8. **Logging Improvement** - Structured logging for better debugging

#### Low Priority

9. **Offline Support** - Enhance PWA for offline capability
10. **Accessibility** - WCAG compliance audit

---

## 10. Developer Setup Guide

### Prerequisites

Before starting, ensure you have the following installed:

| Software | Version | Purpose |
|----------|---------|---------|
| **Python** | 3.10+ | Backend runtime |
| **Node.js** | 18+ | Frontend tooling |
| **Yarn** | 1.22+ | Package manager |
| **MariaDB** | 10.8+ | Database |
| **Redis** | 6+ | Caching & queues |
| **Git** | 2.x | Version control |

---

### Option A: Docker Setup (Recommended for Quick Start)

#### Step 1: Clone the Repository

```bash
git clone https://github.com/your-org/zoya-crm.git
cd zoya-crm
```

#### Step 2: Start Docker Services

```bash
cd docker
docker-compose up -d
```

This starts:
- **MariaDB** on port 3306
- **Redis** on port 6379
- **Frappe** on ports 8000 (web) and 9000 (socketio)

#### Step 3: Initialize the Site

```bash
# Enter the frappe container
docker exec -it crm-frappe-1 bash

# Inside container:
cd frappe-bench
bench new-site crm.localhost --mariadb-root-password 123 --admin-password admin
bench --site crm.localhost install-app crm
bench --site crm.localhost set-config developer_mode 1
bench start
```

#### Step 4: Access the Application

- **Backend**: http://localhost:8000
- **Login**: Administrator / admin

---

### Option B: Manual Setup (Full Development Environment)

#### Step 1: Install System Dependencies

**Ubuntu/Debian:**
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install MariaDB
sudo apt install mariadb-server mariadb-client -y
sudo mysql_secure_installation

# Install Redis
sudo apt install redis-server -y
sudo systemctl enable redis-server

# Install Python dependencies
sudo apt install python3.10 python3.10-venv python3-pip -y

# Install Node.js (using nvm)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 18
nvm use 18

# Install Yarn
npm install -g yarn

# Install wkhtmltopdf (for PDF generation)
sudo apt install wkhtmltopdf -y
```

**macOS:**
```bash
# Install Homebrew if not installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install MariaDB
brew install mariadb
brew services start mariadb

# Install Redis
brew install redis
brew services start redis

# Install Python
brew install python@3.10

# Install Node.js
brew install node@18

# Install Yarn
npm install -g yarn
```

#### Step 2: Configure MariaDB

```bash
# Login to MariaDB
sudo mysql -u root -p

# Run these SQL commands:
```

```sql
-- Create database user for Frappe
CREATE USER 'frappe'@'localhost' IDENTIFIED BY 'frappe_password';
GRANT ALL PRIVILEGES ON *.* TO 'frappe'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

-- Set character encoding
SET GLOBAL character_set_server = 'utf8mb4';
SET GLOBAL collation_server = 'utf8mb4_unicode_ci';
EXIT;
```

#### Step 3: Configure Redis

```bash
# Edit Redis config (optional - for persistence)
sudo nano /etc/redis/redis.conf

# Ensure these settings:
# bind 127.0.0.1
# port 6379
# daemonize yes

# Restart Redis
sudo systemctl restart redis-server

# Verify Redis is running
redis-cli ping
# Should return: PONG
```

#### Step 4: Install Frappe Bench

```bash
# Install bench CLI
pip3 install frappe-bench

# Initialize bench directory
bench init frappe-bench --frappe-branch version-15
cd frappe-bench

# Create a new site
bench new-site crm.localhost \
    --mariadb-root-password your_mysql_root_password \
    --admin-password admin

# Set site as default
bench use crm.localhost
```

#### Step 5: Clone and Install the CRM App

```bash
# Navigate to bench directory
cd frappe-bench

# Clone the CRM app
bench get-app https://github.com/your-org/zoya-crm.git --branch main

# Install CRM app on the site
bench --site crm.localhost install-app crm

# Enable developer mode
bench --site crm.localhost set-config developer_mode 1
```

#### Step 6: Start Backend Services

```bash
# Start all Frappe services
bench start
```

This starts:
- **Web server** on http://localhost:8000
- **Socket.IO** on http://localhost:9000
- **Redis Queue** workers
- **Scheduler** for background jobs

**Terminal Output:**
```
14:30:00 web.1       | started with pid 12345
14:30:00 socketio.1  | started with pid 12346
14:30:00 watch.1     | started with pid 12347
14:30:00 schedule.1  | started with pid 12348
14:30:00 worker.1    | started with pid 12349
```

---

### Frontend Development Setup

#### Step 1: Navigate to Frontend Directory

```bash
cd frappe-bench/apps/crm/frontend
```

#### Step 2: Install Dependencies

```bash
yarn install
```

#### Step 3: Configure Development Proxy

The frontend connects to the Frappe backend. Ensure `vite.config.js` has the correct proxy:

```javascript
// vite.config.js already configured with frappeProxy: true
```

#### Step 4: Start Frontend Dev Server

```bash
yarn dev
```

**Frontend runs on:** http://localhost:8080

The dev server proxies API requests to the Frappe backend at localhost:8000.

---

### Development Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    DEVELOPMENT ENVIRONMENT                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Terminal 1: Backend                Terminal 2: Frontend        │
│   ┌─────────────────────┐            ┌─────────────────────┐    │
│   │ cd frappe-bench     │            │ cd frontend         │    │
│   │ bench start         │            │ yarn dev            │    │
│   │                     │            │                     │    │
│   │ Port 8000 (web)     │◄──────────►│ Port 8080 (vite)    │    │
│   │ Port 9000 (socket)  │            │                     │    │
│   └─────────────────────┘            └─────────────────────┘    │
│              │                                │                  │
│              ▼                                ▼                  │
│   ┌─────────────────────┐            ┌─────────────────────┐    │
│   │     MariaDB         │            │      Browser        │    │
│   │   Port 3306         │            │                     │    │
│   └─────────────────────┘            │ http://localhost:   │    │
│              │                       │   8080/crm          │    │
│   ┌─────────────────────┐            └─────────────────────┘    │
│   │      Redis          │                                       │
│   │   Port 6379         │                                       │
│   └─────────────────────┘                                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

### Useful Commands

| Command | Description |
|---------|-------------|
| `bench start` | Start all backend services |
| `bench --site crm.localhost console` | Open Python shell with Frappe context |
| `bench --site crm.localhost mariadb` | Open MariaDB shell |
| `bench migrate` | Run database migrations |
| `bench build` | Build frontend assets |
| `bench clear-cache` | Clear Redis cache |
| `bench restart` | Restart all services |
| `yarn dev` | Start frontend dev server |
| `yarn build` | Build frontend for production |

---

### Troubleshooting

| Issue | Solution |
|-------|----------|
| **MariaDB connection refused** | `sudo systemctl start mariadb` |
| **Redis connection failed** | `sudo systemctl start redis-server` |
| **Port 8000 in use** | `lsof -i :8000` then `kill -9 <PID>` |
| **Module not found** | `bench setup requirements` |
| **Frontend 404 errors** | Ensure backend is running on port 8000 |
| **Permission denied** | `bench setup production` or fix file permissions |

---

## 11. Building a Full Module - Contract Example

This guide walks through creating a complete **Contract Management** module from database to UI.

### Module Overview

We'll build a Contract module with:
- Contract management (CRUD)
- Status workflow (Draft → Active → Expired)
- Link to Deals and Organizations
- List view, Detail page, and Create modal

```
┌──────────────────────────────────────────────────────────────────┐
│                    CONTRACT MODULE STRUCTURE                      │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│   Backend (crm/fcrm/doctype/crm_contract/)                       │
│   ├── __init__.py                                                 │
│   ├── crm_contract.json        ← DocType Definition (Schema)     │
│   ├── crm_contract.py          ← Business Logic (Model)          │
│   ├── crm_contract.js          ← Desk Form Script                │
│   └── test_crm_contract.py     ← Unit Tests                      │
│                                                                   │
│   API Layer (crm/api/)                                           │
│   └── contract.py              ← REST API Endpoints              │
│                                                                   │
│   Frontend (frontend/src/)                                        │
│   ├── pages/                                                      │
│   │   ├── Contracts.vue        ← List Page                       │
│   │   └── Contract.vue         ← Detail Page                     │
│   ├── components/                                                 │
│   │   ├── Modals/                                                │
│   │   │   └── ContractModal.vue  ← Create/Edit Modal            │
│   │   └── ListViews/                                             │
│   │       └── ContractsListView.vue  ← List Component           │
│   └── stores/                                                    │
│       └── contracts.js         ← State Management                │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

---

### Step 1: Create the DocType (Database Table)

#### 1.1 Create the Directory Structure

```bash
cd frappe-bench/apps/crm/crm/fcrm/doctype
mkdir crm_contract
cd crm_contract
touch __init__.py crm_contract.py crm_contract.js test_crm_contract.py
```

#### 1.2 Create the DocType JSON Schema

Create `crm_contract.json`:

```json
{
  "actions": [],
  "autoname": "naming_series:",
  "creation": "2026-01-11 10:00:00.000000",
  "doctype": "DocType",
  "engine": "InnoDB",
  "field_order": [
    "naming_series",
    "title",
    "status",
    "column_break_1",
    "contract_owner",
    "organization",
    "section_break_details",
    "deal",
    "contact",
    "column_break_2",
    "start_date",
    "end_date",
    "section_break_value",
    "contract_value",
    "currency",
    "column_break_3",
    "payment_terms",
    "renewal_type",
    "section_break_content",
    "description",
    "terms_and_conditions",
    "section_break_attachments",
    "contract_document"
  ],
  "fields": [
    {
      "fieldname": "naming_series",
      "fieldtype": "Select",
      "label": "Naming Series",
      "options": "CRM-CONTRACT-.YYYY.-",
      "reqd": 1
    },
    {
      "fieldname": "title",
      "fieldtype": "Data",
      "in_list_view": 1,
      "in_standard_filter": 1,
      "label": "Title",
      "reqd": 1
    },
    {
      "default": "Draft",
      "fieldname": "status",
      "fieldtype": "Select",
      "in_list_view": 1,
      "in_standard_filter": 1,
      "label": "Status",
      "options": "Draft\nActive\nExpired\nCancelled\nRenewed"
    },
    {
      "fieldname": "column_break_1",
      "fieldtype": "Column Break"
    },
    {
      "fieldname": "contract_owner",
      "fieldtype": "Link",
      "in_standard_filter": 1,
      "label": "Contract Owner",
      "options": "User"
    },
    {
      "fieldname": "organization",
      "fieldtype": "Link",
      "in_list_view": 1,
      "in_standard_filter": 1,
      "label": "Organization",
      "options": "CRM Organization"
    },
    {
      "fieldname": "section_break_details",
      "fieldtype": "Section Break",
      "label": "Contract Details"
    },
    {
      "fieldname": "deal",
      "fieldtype": "Link",
      "label": "Related Deal",
      "options": "CRM Deal"
    },
    {
      "fieldname": "contact",
      "fieldtype": "Link",
      "label": "Contact Person",
      "options": "Contact"
    },
    {
      "fieldname": "column_break_2",
      "fieldtype": "Column Break"
    },
    {
      "fieldname": "start_date",
      "fieldtype": "Date",
      "in_list_view": 1,
      "label": "Start Date",
      "reqd": 1
    },
    {
      "fieldname": "end_date",
      "fieldtype": "Date",
      "in_list_view": 1,
      "label": "End Date",
      "reqd": 1
    },
    {
      "fieldname": "section_break_value",
      "fieldtype": "Section Break",
      "label": "Contract Value"
    },
    {
      "fieldname": "contract_value",
      "fieldtype": "Currency",
      "in_list_view": 1,
      "label": "Contract Value"
    },
    {
      "default": "USD",
      "fieldname": "currency",
      "fieldtype": "Link",
      "label": "Currency",
      "options": "Currency"
    },
    {
      "fieldname": "column_break_3",
      "fieldtype": "Column Break"
    },
    {
      "fieldname": "payment_terms",
      "fieldtype": "Select",
      "label": "Payment Terms",
      "options": "\nMonthly\nQuarterly\nAnnually\nOne-time"
    },
    {
      "fieldname": "renewal_type",
      "fieldtype": "Select",
      "label": "Renewal Type",
      "options": "\nAuto-renew\nManual\nNone"
    },
    {
      "fieldname": "section_break_content",
      "fieldtype": "Section Break",
      "label": "Content"
    },
    {
      "fieldname": "description",
      "fieldtype": "Text Editor",
      "label": "Description"
    },
    {
      "fieldname": "terms_and_conditions",
      "fieldtype": "Text Editor",
      "label": "Terms and Conditions"
    },
    {
      "fieldname": "section_break_attachments",
      "fieldtype": "Section Break",
      "label": "Attachments"
    },
    {
      "fieldname": "contract_document",
      "fieldtype": "Attach",
      "label": "Contract Document"
    }
  ],
  "index_web_pages_for_search": 1,
  "links": [],
  "modified": "2026-01-11 10:00:00.000000",
  "modified_by": "Administrator",
  "module": "FCRM",
  "name": "CRM Contract",
  "naming_rule": "By \"Naming Series\" field",
  "owner": "Administrator",
  "permissions": [
    {
      "create": 1,
      "delete": 1,
      "email": 1,
      "export": 1,
      "print": 1,
      "read": 1,
      "report": 1,
      "role": "System Manager",
      "share": 1,
      "write": 1
    },
    {
      "create": 1,
      "delete": 1,
      "email": 1,
      "export": 1,
      "print": 1,
      "read": 1,
      "report": 1,
      "role": "Sales Manager",
      "share": 1,
      "write": 1
    },
    {
      "create": 1,
      "email": 1,
      "export": 1,
      "print": 1,
      "read": 1,
      "report": 1,
      "role": "Sales User",
      "share": 1,
      "write": 1
    }
  ],
  "sort_field": "modified",
  "sort_order": "DESC",
  "states": [],
  "title_field": "title",
  "track_changes": 1
}
```

---

### Step 2: Create the Python Model (Business Logic)

Create `crm_contract.py`:

```python
# Copyright (c) 2026, Your Company and contributors
# For license information, please see license.txt

import frappe
from frappe import _
from frappe.model.document import Document
from frappe.utils import getdate, nowdate, add_days


class CRMContract(Document):
    # begin: auto-generated types
    from typing import TYPE_CHECKING

    if TYPE_CHECKING:
        from frappe.types import DF

        title: DF.Data
        status: DF.Literal["Draft", "Active", "Expired", "Cancelled", "Renewed"]
        contract_owner: DF.Link | None
        organization: DF.Link | None
        deal: DF.Link | None
        contact: DF.Link | None
        start_date: DF.Date
        end_date: DF.Date
        contract_value: DF.Currency
        currency: DF.Link | None
        payment_terms: DF.Literal["", "Monthly", "Quarterly", "Annually", "One-time"]
        renewal_type: DF.Literal["", "Auto-renew", "Manual", "None"]
        description: DF.TextEditor | None
        terms_and_conditions: DF.TextEditor | None
        contract_document: DF.Attach | None
    # end: auto-generated types

    def validate(self):
        self.validate_dates()
        self.set_status_based_on_dates()
        if self.has_value_changed("contract_owner") and self.contract_owner:
            self.share_with_owner()

    def validate_dates(self):
        """Ensure end date is after start date"""
        if self.end_date and self.start_date:
            if getdate(self.end_date) < getdate(self.start_date):
                frappe.throw(_("End Date cannot be before Start Date"))

    def set_status_based_on_dates(self):
        """Auto-update status based on dates"""
        if self.status == "Draft":
            return
        
        today = getdate(nowdate())
        start = getdate(self.start_date)
        end = getdate(self.end_date)

        if today < start:
            self.status = "Draft"
        elif start <= today <= end:
            if self.status not in ["Cancelled", "Renewed"]:
                self.status = "Active"
        elif today > end:
            if self.status not in ["Cancelled", "Renewed"]:
                self.status = "Expired"

    def share_with_owner(self):
        """Share contract with the owner"""
        if not self.contract_owner:
            return
        
        if not frappe.db.exists(
            "DocShare",
            {"user": self.contract_owner, "share_name": self.name, "share_doctype": self.doctype}
        ):
            frappe.share.add_docshare(
                self.doctype,
                self.name,
                self.contract_owner,
                write=1,
                flags={"ignore_share_permission": True}
            )

    def before_save(self):
        """Set contract owner if not set"""
        if not self.contract_owner:
            self.contract_owner = frappe.session.user

    def after_insert(self):
        """Actions after contract creation"""
        self.notify_owner()

    def notify_owner(self):
        """Send notification to contract owner"""
        if self.contract_owner and self.contract_owner != frappe.session.user:
            frappe.get_doc({
                "doctype": "CRM Notification",
                "from_user": frappe.session.user,
                "to_user": self.contract_owner,
                "notification_type": "Assignment",
                "message": f"You have been assigned to contract: {self.title}",
                "reference_doctype": self.doctype,
                "reference_name": self.name,
            }).insert(ignore_permissions=True)

    @staticmethod
    def default_list_data():
        """Default columns for list view"""
        columns = [
            {
                "label": "Title",
                "type": "Data",
                "key": "title",
                "width": "15rem",
            },
            {
                "label": "Organization",
                "type": "Link",
                "key": "organization",
                "options": "CRM Organization",
                "width": "12rem",
            },
            {
                "label": "Status",
                "type": "Select",
                "key": "status",
                "width": "8rem",
            },
            {
                "label": "Contract Value",
                "type": "Currency",
                "key": "contract_value",
                "width": "10rem",
            },
            {
                "label": "Start Date",
                "type": "Date",
                "key": "start_date",
                "width": "8rem",
            },
            {
                "label": "End Date",
                "type": "Date",
                "key": "end_date",
                "width": "8rem",
            },
            {
                "label": "Owner",
                "type": "Link",
                "key": "contract_owner",
                "width": "10rem",
            },
        ]
        rows = [
            "name",
            "title",
            "organization",
            "status",
            "contract_value",
            "currency",
            "start_date",
            "end_date",
            "contract_owner",
            "modified",
        ]
        return {"columns": columns, "rows": rows}

    @staticmethod
    def default_kanban_settings():
        """Default Kanban view settings"""
        return {
            "column_field": "status",
            "title_field": "title",
            "kanban_fields": '["organization", "contract_value", "end_date", "contract_owner"]',
        }


# API Functions
@frappe.whitelist()
def get_contracts_by_organization(organization):
    """Get all contracts for an organization"""
    if not frappe.has_permission("CRM Contract", "read"):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    contracts = frappe.get_all(
        "CRM Contract",
        filters={"organization": organization},
        fields=["name", "title", "status", "contract_value", "start_date", "end_date"],
        order_by="start_date desc"
    )
    return contracts


@frappe.whitelist()
def get_contracts_by_deal(deal):
    """Get all contracts linked to a deal"""
    if not frappe.has_permission("CRM Contract", "read"):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    contracts = frappe.get_all(
        "CRM Contract",
        filters={"deal": deal},
        fields=["name", "title", "status", "contract_value", "start_date", "end_date"],
        order_by="start_date desc"
    )
    return contracts


@frappe.whitelist()
def renew_contract(contract_name, new_end_date):
    """Renew an existing contract"""
    if not frappe.has_permission("CRM Contract", "write", contract_name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    old_contract = frappe.get_doc("CRM Contract", contract_name)
    
    # Mark old contract as renewed
    old_contract.status = "Renewed"
    old_contract.save()

    # Create new contract
    new_contract = frappe.copy_doc(old_contract)
    new_contract.status = "Draft"
    new_contract.start_date = add_days(old_contract.end_date, 1)
    new_contract.end_date = new_end_date
    new_contract.insert()

    return new_contract.name


@frappe.whitelist()
def get_expiring_contracts(days=30):
    """Get contracts expiring within specified days"""
    from frappe.utils import add_days, nowdate

    if not frappe.has_permission("CRM Contract", "read"):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    future_date = add_days(nowdate(), days)
    
    contracts = frappe.get_all(
        "CRM Contract",
        filters={
            "status": "Active",
            "end_date": ["<=", future_date],
            "end_date": [">=", nowdate()]
        },
        fields=["name", "title", "organization", "end_date", "contract_value", "contract_owner"]
    )
    return contracts
```

---

### Step 3: Create the API Layer

Create `crm/api/contract.py`:

```python
import frappe
from frappe import _


@frappe.whitelist()
def get_contract(name):
    """Get a single contract with full details"""
    if not frappe.has_permission("CRM Contract", "read", name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    contract = frappe.get_doc("CRM Contract", name)
    
    # Add related data
    data = contract.as_dict()
    
    # Get organization details if linked
    if contract.organization:
        data["organization_details"] = frappe.get_cached_doc(
            "CRM Organization", contract.organization
        ).as_dict()

    # Get deal details if linked
    if contract.deal:
        data["deal_details"] = frappe.get_cached_doc(
            "CRM Deal", contract.deal
        ).as_dict()

    return data


@frappe.whitelist()
def create_contract(args):
    """Create a new contract"""
    args = frappe.parse_json(args) if isinstance(args, str) else args
    
    contract = frappe.new_doc("CRM Contract")
    contract.update(args)
    contract.insert()
    
    return contract.name


@frappe.whitelist()
def update_contract(name, args):
    """Update an existing contract"""
    if not frappe.has_permission("CRM Contract", "write", name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    args = frappe.parse_json(args) if isinstance(args, str) else args
    
    contract = frappe.get_doc("CRM Contract", name)
    contract.update(args)
    contract.save()
    
    return contract.name


@frappe.whitelist()
def activate_contract(name):
    """Activate a draft contract"""
    if not frappe.has_permission("CRM Contract", "write", name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    contract = frappe.get_doc("CRM Contract", name)
    if contract.status != "Draft":
        frappe.throw(_("Only draft contracts can be activated"))
    
    contract.status = "Active"
    contract.save()
    
    return {"status": "success", "message": _("Contract activated successfully")}


@frappe.whitelist()
def cancel_contract(name, reason=None):
    """Cancel a contract"""
    if not frappe.has_permission("CRM Contract", "write", name):
        frappe.throw(_("Not permitted"), frappe.PermissionError)

    contract = frappe.get_doc("CRM Contract", name)
    contract.status = "Cancelled"
    if reason:
        contract.add_comment("Info", f"Cancellation reason: {reason}")
    contract.save()
    
    return {"status": "success", "message": _("Contract cancelled successfully")}


@frappe.whitelist()
def get_contract_stats():
    """Get contract statistics for dashboard"""
    stats = {
        "total": frappe.db.count("CRM Contract"),
        "active": frappe.db.count("CRM Contract", {"status": "Active"}),
        "draft": frappe.db.count("CRM Contract", {"status": "Draft"}),
        "expired": frappe.db.count("CRM Contract", {"status": "Expired"}),
        "total_value": frappe.db.sql("""
            SELECT COALESCE(SUM(contract_value), 0) 
            FROM `tabCRM Contract` 
            WHERE status = 'Active'
        """)[0][0] or 0
    }
    return stats
```

---

### Step 4: Register in hooks.py

Add to `crm/hooks.py`:

```python
# Add to doc_events
doc_events = {
    # ... existing events ...
    "CRM Contract": {
        "on_update": ["crm.api.contract.on_contract_update"],  # Optional
    },
}

# Add scheduled task for expiring contracts notification
scheduler_events = {
    # ... existing events ...
    "daily": [
        # ... existing ...
        "crm.api.contract.notify_expiring_contracts"
    ],
}
```

---

### Step 5: Create Frontend Components

#### 5.1 Create the Pinia Store

Create `frontend/src/stores/contracts.js`:

```javascript
import { defineStore } from 'pinia'
import { createResource, createListResource } from 'frappe-ui'
import { ref, computed } from 'vue'

export const contractsStore = defineStore('crm-contracts', () => {
  // List resource for contracts
  const contracts = createListResource({
    doctype: 'CRM Contract',
    fields: [
      'name',
      'title',
      'organization',
      'status',
      'contract_value',
      'currency',
      'start_date',
      'end_date',
      'contract_owner',
      'modified',
    ],
    orderBy: 'modified desc',
    pageLength: 20,
    auto: false,
  })

  // Contract statistics
  const stats = createResource({
    url: 'crm.api.contract.get_contract_stats',
    auto: true,
  })

  // Status options with colors
  const statusOptions = computed(() => [
    { label: 'Draft', value: 'Draft', color: 'gray' },
    { label: 'Active', value: 'Active', color: 'green' },
    { label: 'Expired', value: 'Expired', color: 'red' },
    { label: 'Cancelled', value: 'Cancelled', color: 'orange' },
    { label: 'Renewed', value: 'Renewed', color: 'blue' },
  ])

  // Get status color
  function getStatusColor(status) {
    const option = statusOptions.value.find((o) => o.value === status)
    return option?.color || 'gray'
  }

  // Reload contracts
  function reload() {
    contracts.reload()
    stats.reload()
  }

  return {
    contracts,
    stats,
    statusOptions,
    getStatusColor,
    reload,
  }
})
```

#### 5.2 Create the List Page

Create `frontend/src/pages/Contracts.vue`:

```vue
<template>
  <LayoutHeader>
    <template #left-header>
      <Breadcrumbs :items="[{ label: __('Contracts'), route: '/contracts' }]" />
    </template>
    <template #right-header>
      <Button variant="solid" @click="showCreateModal = true">
        <template #prefix>
          <FeatherIcon name="plus" class="h-4 w-4" />
        </template>
        {{ __('Create Contract') }}
      </Button>
    </template>
  </LayoutHeader>

  <ViewControls
    v-model:filters="filters"
    v-model:sort="sort"
    doctype="CRM Contract"
    @update="reload"
  />

  <ContractsListView
    :contracts="contracts.data || []"
    :loading="contracts.loading"
    @row-click="(contract) => router.push(`/contracts/${contract.name}`)"
  />

  <ContractModal
    v-if="showCreateModal"
    @close="showCreateModal = false"
    @created="onContractCreated"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { Breadcrumbs, Button, FeatherIcon } from 'frappe-ui'
import LayoutHeader from '@/components/LayoutHeader.vue'
import ViewControls from '@/components/ViewControls.vue'
import ContractsListView from '@/components/ListViews/ContractsListView.vue'
import ContractModal from '@/components/Modals/ContractModal.vue'
import { contractsStore } from '@/stores/contracts'

const router = useRouter()
const store = contractsStore()
const { contracts } = store

const showCreateModal = ref(false)
const filters = ref({})
const sort = ref({ field: 'modified', order: 'desc' })

onMounted(() => {
  contracts.reload()
})

function reload() {
  contracts.update({
    filters: filters.value,
    orderBy: `${sort.value.field} ${sort.value.order}`,
  })
  contracts.reload()
}

function onContractCreated(contractName) {
  showCreateModal.value = false
  contracts.reload()
  router.push(`/contracts/${contractName}`)
}
</script>
```

#### 5.3 Create the Detail Page

Create `frontend/src/pages/Contract.vue`:

```vue
<template>
  <div v-if="contract.data" class="flex flex-col h-full">
    <LayoutHeader>
      <template #left-header>
        <Breadcrumbs
          :items="[
            { label: __('Contracts'), route: '/contracts' },
            { label: contract.data.title, route: `/contracts/${contractId}` },
          ]"
        />
      </template>
      <template #right-header>
        <div class="flex gap-2">
          <Badge :variant="getStatusVariant(contract.data.status)">
            {{ contract.data.status }}
          </Badge>
          <Dropdown :options="actionOptions">
            <Button variant="outline">
              {{ __('Actions') }}
              <template #suffix>
                <FeatherIcon name="chevron-down" class="h-4 w-4" />
              </template>
            </Button>
          </Dropdown>
        </div>
      </template>
    </LayoutHeader>

    <div class="flex-1 overflow-auto p-6">
      <div class="grid grid-cols-3 gap-6">
        <!-- Main Content -->
        <div class="col-span-2 space-y-6">
          <!-- Contract Details Card -->
          <Card>
            <template #title>{{ __('Contract Details') }}</template>
            <div class="grid grid-cols-2 gap-4 p-4">
              <div>
                <label class="text-sm text-gray-600">{{ __('Title') }}</label>
                <p class="font-medium">{{ contract.data.title }}</p>
              </div>
              <div>
                <label class="text-sm text-gray-600">{{ __('Organization') }}</label>
                <p class="font-medium">
                  <router-link
                    v-if="contract.data.organization"
                    :to="`/organizations/${contract.data.organization}`"
                    class="text-blue-600 hover:underline"
                  >
                    {{ contract.data.organization }}
                  </router-link>
                  <span v-else class="text-gray-400">-</span>
                </p>
              </div>
              <div>
                <label class="text-sm text-gray-600">{{ __('Start Date') }}</label>
                <p class="font-medium">{{ formatDate(contract.data.start_date) }}</p>
              </div>
              <div>
                <label class="text-sm text-gray-600">{{ __('End Date') }}</label>
                <p class="font-medium">{{ formatDate(contract.data.end_date) }}</p>
              </div>
              <div>
                <label class="text-sm text-gray-600">{{ __('Contract Value') }}</label>
                <p class="font-medium text-lg">
                  {{ formatCurrency(contract.data.contract_value, contract.data.currency) }}
                </p>
              </div>
              <div>
                <label class="text-sm text-gray-600">{{ __('Payment Terms') }}</label>
                <p class="font-medium">{{ contract.data.payment_terms || '-' }}</p>
              </div>
            </div>
          </Card>

          <!-- Description Card -->
          <Card v-if="contract.data.description">
            <template #title>{{ __('Description') }}</template>
            <div class="p-4 prose max-w-none" v-html="contract.data.description" />
          </Card>

          <!-- Terms Card -->
          <Card v-if="contract.data.terms_and_conditions">
            <template #title>{{ __('Terms and Conditions') }}</template>
            <div class="p-4 prose max-w-none" v-html="contract.data.terms_and_conditions" />
          </Card>
        </div>

        <!-- Sidebar -->
        <div class="space-y-6">
          <!-- Owner Card -->
          <Card>
            <template #title>{{ __('Owner') }}</template>
            <div class="p-4">
              <UserAvatar
                v-if="contract.data.contract_owner"
                :user="contract.data.contract_owner"
                size="lg"
              />
              <p v-else class="text-gray-400">{{ __('No owner assigned') }}</p>
            </div>
          </Card>

          <!-- Related Deal Card -->
          <Card v-if="contract.data.deal">
            <template #title>{{ __('Related Deal') }}</template>
            <div class="p-4">
              <router-link
                :to="`/deals/${contract.data.deal}`"
                class="text-blue-600 hover:underline"
              >
                {{ contract.data.deal }}
              </router-link>
            </div>
          </Card>

          <!-- Document Card -->
          <Card v-if="contract.data.contract_document">
            <template #title>{{ __('Document') }}</template>
            <div class="p-4">
              <a
                :href="contract.data.contract_document"
                target="_blank"
                class="flex items-center gap-2 text-blue-600 hover:underline"
              >
                <FeatherIcon name="file" class="h-4 w-4" />
                {{ __('View Document') }}
              </a>
            </div>
          </Card>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { createResource, Badge, Button, Dropdown, FeatherIcon, Card } from 'frappe-ui'
import { useRouter } from 'vue-router'
import LayoutHeader from '@/components/LayoutHeader.vue'
import Breadcrumbs from '@/components/ViewBreadcrumbs.vue'
import UserAvatar from '@/components/UserAvatar.vue'

const props = defineProps({
  contractId: { type: String, required: true },
})

const router = useRouter()

const contract = createResource({
  url: 'crm.api.contract.get_contract',
  params: { name: props.contractId },
  auto: true,
})

const activateContract = createResource({
  url: 'crm.api.contract.activate_contract',
  onSuccess: () => contract.reload(),
})

const cancelContract = createResource({
  url: 'crm.api.contract.cancel_contract',
  onSuccess: () => contract.reload(),
})

const actionOptions = computed(() => {
  const options = []
  
  if (contract.data?.status === 'Draft') {
    options.push({
      label: 'Activate',
      icon: 'check',
      onClick: () => activateContract.submit({ name: props.contractId }),
    })
  }
  
  if (['Draft', 'Active'].includes(contract.data?.status)) {
    options.push({
      label: 'Cancel',
      icon: 'x',
      onClick: () => cancelContract.submit({ name: props.contractId }),
    })
  }
  
  options.push({
    label: 'Edit',
    icon: 'edit',
    onClick: () => {/* Open edit modal */},
  })

  return options
})

function getStatusVariant(status) {
  const variants = {
    Draft: 'subtle',
    Active: 'success',
    Expired: 'danger',
    Cancelled: 'warning',
    Renewed: 'info',
  }
  return variants[status] || 'subtle'
}

function formatDate(date) {
  if (!date) return '-'
  return new Date(date).toLocaleDateString()
}

function formatCurrency(value, currency = 'USD') {
  if (!value) return '-'
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: currency,
  }).format(value)
}
</script>
```

#### 5.4 Create the Create/Edit Modal

Create `frontend/src/components/Modals/ContractModal.vue`:

```vue
<template>
  <Dialog v-model="show" :options="{ title: __('Create Contract'), size: 'xl' }">
    <template #body-content>
      <div class="grid grid-cols-2 gap-4">
        <FormControl
          v-model="form.title"
          :label="__('Title')"
          type="text"
          required
        />
        <FormControl
          v-model="form.organization"
          :label="__('Organization')"
          type="link"
          doctype="CRM Organization"
        />
        <FormControl
          v-model="form.start_date"
          :label="__('Start Date')"
          type="date"
          required
        />
        <FormControl
          v-model="form.end_date"
          :label="__('End Date')"
          type="date"
          required
        />
        <FormControl
          v-model="form.contract_value"
          :label="__('Contract Value')"
          type="number"
        />
        <FormControl
          v-model="form.currency"
          :label="__('Currency')"
          type="link"
          doctype="Currency"
        />
        <FormControl
          v-model="form.payment_terms"
          :label="__('Payment Terms')"
          type="select"
          :options="['Monthly', 'Quarterly', 'Annually', 'One-time']"
        />
        <FormControl
          v-model="form.renewal_type"
          :label="__('Renewal Type')"
          type="select"
          :options="['Auto-renew', 'Manual', 'None']"
        />
        <FormControl
          v-model="form.deal"
          :label="__('Related Deal')"
          type="link"
          doctype="CRM Deal"
        />
        <FormControl
          v-model="form.contact"
          :label="__('Contact Person')"
          type="link"
          doctype="Contact"
        />
      </div>
      <div class="mt-4">
        <FormControl
          v-model="form.description"
          :label="__('Description')"
          type="textarea"
          :rows="4"
        />
      </div>
    </template>
    <template #actions>
      <Button variant="subtle" @click="show = false">
        {{ __('Cancel') }}
      </Button>
      <Button
        variant="solid"
        :loading="createContract.loading"
        @click="submit"
      >
        {{ __('Create') }}
      </Button>
    </template>
  </Dialog>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { Dialog, FormControl, Button, createResource } from 'frappe-ui'

const emit = defineEmits(['close', 'created'])

const show = ref(true)

const form = reactive({
  title: '',
  organization: '',
  start_date: '',
  end_date: '',
  contract_value: 0,
  currency: 'USD',
  payment_terms: '',
  renewal_type: '',
  deal: '',
  contact: '',
  description: '',
})

const createContract = createResource({
  url: 'crm.api.contract.create_contract',
  onSuccess: (contractName) => {
    emit('created', contractName)
    show.value = false
  },
})

function submit() {
  createContract.submit({ args: form })
}

// Watch for dialog close
watch(show, (value) => {
  if (!value) emit('close')
})
</script>
```

---

### Step 6: Add Routes

Update `frontend/src/router.js`:

```javascript
const routes = [
  // ... existing routes ...
  
  {
    alias: '/contracts',
    path: '/contracts/view/:viewType?',
    name: 'Contracts',
    component: () => import('@/pages/Contracts.vue'),
  },
  {
    path: '/contracts/:contractId',
    name: 'Contract',
    component: () => import('@/pages/Contract.vue'),
    props: true,
  },
]
```

---

### Step 7: Add to Sidebar Navigation

Update `frontend/src/components/Layouts/AppSidebar.vue` to include:

```javascript
const sidebarLinks = [
  // ... existing links ...
  {
    label: 'Contracts',
    icon: 'file-text',
    to: '/contracts',
  },
]
```

---

### Step 8: Run Migrations

```bash
# Create the database table
cd frappe-bench
bench --site crm.localhost migrate

# Clear cache
bench --site crm.localhost clear-cache

# Restart services
bench restart
```

---

### Step 9: Test the Module

```bash
# Backend tests
bench --site crm.localhost run-tests --app crm --module crm_contract

# Manual testing
# 1. Open browser: http://localhost:8080/crm/contracts
# 2. Click "Create Contract"
# 3. Fill form and save
# 4. Verify in list view
# 5. Click on contract to view details
```

---

### Module Development Checklist

| Step | Task | Status |
|------|------|--------|
| 1 | Create DocType JSON schema | ☐ |
| 2 | Create Python model with business logic | ☐ |
| 3 | Create API endpoints | ☐ |
| 4 | Register in hooks.py | ☐ |
| 5 | Create Pinia store | ☐ |
| 6 | Create list page component | ☐ |
| 7 | Create detail page component | ☐ |
| 8 | Create create/edit modal | ☐ |
| 9 | Add routes | ☐ |
| 10 | Add sidebar navigation | ☐ |
| 11 | Run migrations | ☐ |
| 12 | Test functionality | ☐ |

---

## 12. Conclusion & Rating

### Overall Assessment

| Category | Score | Notes |
|----------|-------|-------|
| **Code Quality** | 7/10 | Clean but some inconsistencies |
| **Structure** | 7.5/10 | Well-organized, follows Frappe patterns |
| **Maintainability** | 7/10 | Extensible but requires Frappe knowledge |
| **Documentation** | 5/10 | Limited inline and external docs |
| **Testing** | 4/10 | Test files exist but coverage is low |
| **Deployment** | 6.5/10 | Docker support but no CI/CD |
| **Security** | 7/10 | Frappe handles auth well |
| **Performance** | 7/10 | Redis caching, but no profiling |
| **UI/UX** | 8/10 | Modern, responsive, PWA-ready |
| **Features** | 8.5/10 | Comprehensive CRM functionality |

### Final Score: **6.9/10**

### Verdict: **RECOMMENDED FOR USE** ✅

This CRM is a **solid choice** for organizations that:
- Need a modern, open-source CRM solution
- Have standard sales pipeline requirements  
- Want to avoid vendor lock-in
- Have technical resources for customization

**Key Strengths:**
- 🏆 Modern Vue 3 + Frappe stack
- 🏆 Comprehensive feature set (leads, deals, calls, SLA)
- 🏆 Real-time updates via Socket.IO
- 🏆 Telephony integrations (Twilio, Exotel)
- 🏆 Active development by Frappe

**Key Weaknesses:**
- ⚠️ Tightly coupled to Frappe ecosystem
- ⚠️ Limited test coverage
- ⚠️ No built-in CI/CD
- ⚠️ Learning curve for non-Frappe developers

### Building from scratch is recommended only if:
- You need fundamentally different architecture
- Your scale requirements exceed Frappe's capabilities
- Your team has strong preferences for a different stack

---

### Quick Reference Card

```
┌─────────────────────────────────────────────────────────────────┐
│                    DEVELOPER QUICK REFERENCE                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  START DEVELOPMENT                                               │
│  ─────────────────                                               │
│  Terminal 1: cd frappe-bench && bench start                     │
│  Terminal 2: cd frontend && yarn dev                            │
│                                                                  │
│  ACCESS POINTS                                                   │
│  ─────────────                                                   │
│  Backend API:    http://localhost:8000                          │
│  Frontend Dev:   http://localhost:8080                          │
│  Socket.IO:      http://localhost:9000                          │
│                                                                  │
│  CREATE NEW MODULE                                               │
│  ─────────────────                                               │
│  1. DocType JSON  → crm/fcrm/doctype/{name}/{name}.json        │
│  2. Python Model  → crm/fcrm/doctype/{name}/{name}.py          │
│  3. API Layer     → crm/api/{name}.py                          │
│  4. Vue Store     → frontend/src/stores/{name}.js              │
│  5. List Page     → frontend/src/pages/{Names}.vue             │
│  6. Detail Page   → frontend/src/pages/{Name}.vue              │
│  7. Modal         → frontend/src/components/Modals/{Name}.vue  │
│  8. Routes        → frontend/src/router.js                     │
│  9. Migrate       → bench --site xxx migrate                   │
│                                                                  │
│  USEFUL COMMANDS                                                 │
│  ───────────────                                                 │
│  bench console           → Python shell with Frappe context     │
│  bench mariadb           → Database shell                       │
│  bench migrate           → Run migrations                       │
│  bench clear-cache       → Clear Redis cache                    │
│  bench restart           → Restart all services                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Review Date: January 2026*  
*Codebase: zoya-crm (Frappe CRM Fork)*

