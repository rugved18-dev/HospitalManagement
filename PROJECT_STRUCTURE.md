# Project Structure Guide

This document provides a detailed explanation of the project's file structure and organization.

## Directory Tree

```
HospitalMangement-main/
│
├── 📄 README.md                          # Main project documentation
├── 📄 CHANGELOG.md                       # Version history and changes
├── 📄 CONTRIBUTING.md                    # Contributing guidelines
├── 📄 SETUP_GUIDE.md                     # Detailed setup instructions
├── 📄 QUEUE_SYSTEM_README.md             # Queue system documentation
├── 📄 LICENSE                            # ISC License
├── 📄 .gitignore                         # Git ignore rules
├── 📄 .env.example                       # Environment variables template
├── 📄 zapp.yaml                          # Z Open Editor configuration
│
├── 📁 backend/                           # Backend Server (Node.js/Express)
│   │
│   ├── 📄 package.json                   # Backend dependencies and scripts
│   ├── 📄 sample_data.csv                # Sample CSV for testing
│   ├── 📄 test-db.js                     # Database connection test
│   ├── 📄 setup-database.bat             # Windows database setup script
│   │
│   ├── 📁 src/                           # Source code
│   │   ├── 📄 app.js                     # Express app configuration
│   │   ├── 📄 index.js                   # Server entry point & HTTP server
│   │   ├── 📄 socket.js                  # Socket.io initialization
│   │   │
│   │   ├── 📁 config/                    # Configuration files
│   │   │   └── 📄 db2.js                 # DB2 connection configuration
│   │   │
│   │   ├── 📁 db/                        # Database connection
│   │   │   └── 📄 index.js               # Database initialization
│   │   │
│   │   ├── 📁 models/                    # Database models/operations
│   │   │   ├── 📄 Patient.model.js       # Patient CRUD operations
│   │   │   ├── 📄 Queue.model.js         # Queue CRUD operations
│   │   │   └── 📄 Analytics.model.js     # Analytics operations
│   │   │
│   │   ├── 📁 controllers/               # Route controllers
│   │   │   ├── 📄 patientController.js   # Patient endpoint handlers
│   │   │   ├── 📄 queueController.js     # Queue endpoint handlers
│   │   │   └── 📄 analyticsController.js # Analytics endpoint handlers
│   │   │
│   │   ├── 📁 routes/                    # API routes
│   │   │   ├── 📄 patient.routes.js      # Patient endpoints
│   │   │   ├── 📄 queue.routes.js        # Queue endpoints
│   │   │   └── 📄 analytics.routes.js    # Analytics endpoints
│   │   │
│   │   ├── 📁 middlewares/               # Express middlewares
│   │   │   ├── 📄 errorHandler.js        # Global error handling
│   │   │   └── 📄 multer.middleware.js   # File upload configuration
│   │   │
│   │   ├── 📁 services/                  # Business logic services
│   │   │   └── 📄 fileParser.js          # CSV file parsing service
│   │   │
│   │   └── 📁 utils/                     # Utility functions
│   │       └── 📄 validators.js          # Input validation functions
│   │
│   └── 📁 db_scripts/                    # Database scripts
│       ├── 📄 create_table.sql           # Patient table schema
│       ├── 📄 create_queue_table.sql     # Queue table schema
│       ├── 📄 create-queue-table.js      # Queue table creation (Node)
│       ├── 📄 add_visit_count.sql        # Add visit count column
│       ├── 📄 migrate_to_custid.sql      # Customer ID migration
│       ├── 📄 fix-queue-table.js         # Queue table fixes
│       ├── 📄 setup-queue.bat            # Windows queue setup
│       └── 📄 add-visit-count-column.js  # Add visit count (Node)
│
├── 📁 frontend/                          # Frontend App (React/Vite)
│   │
│   ├── 📄 package.json                   # Frontend dependencies
│   ├── 📄 vite.config.js                 # Vite configuration
│   ├── 📄 tailwind.config.js             # Tailwind CSS configuration
│   ├── 📄 eslint.config.js               # ESLint configuration
│   ├── 📄 index.html                     # HTML entry point
│   ├── 📄 README.md                      # Frontend-specific docs
│   │
│   ├── 📁 src/                           # React source code
│   │   ├── 📄 main.jsx                   # React app entry point
│   │   ├── 📄 App.jsx                    # Main App component
│   │   ├── 📄 App.css                    # Global app styles
│   │   ├── 📄 index.css                  # Base CSS styles
│   │   │
│   │   ├── 📁 pages/                     # Page components
│   │   │   ├── 📄 Home.jsx               # Homepage
│   │   │   ├── 📄 UploadPage.jsx         # CSV upload page
│   │   │   ├── 📄 ManualEntryPage.jsx    # Manual patient entry
│   │   │   ├── 📄 SearchPage.jsx         # Patient search page
│   │   │   ├── 📄 PatientsListPage.jsx   # Patient list view
│   │   │   ├── 📄 QueueBoard.jsx         # Live queue display
│   │   │   └── 📄 QueueManagement.jsx    # Queue management page
│   │   │
│   │   ├── 📁 components/                # Reusable components
│   │   │   ├── 📄 FileUpload.jsx         # File upload component
│   │   │   ├── 📄 PatientInfo.jsx        # Patient info display
│   │   │   ├── 📄 QueueControl.jsx       # Queue control panel
│   │   │   └── 📄 SearchPatient.jsx      # Patient search component
│   │   │
│   │   ├── 📁 services/                  # API services
│   │   │   └── 📄 api.js                 # API calls & axios config
│   │   │
│   │   └── 📁 assets/                    # Images, icons, fonts
│   │
│   ├── 📁 public/                        # Static files
│   │   └── [favicon, images, etc.]
│   │
│   └── 📁 dist/                          # Production build (generated)

```

## File Descriptions

### Root Level Files

| File | Purpose |
|------|---------|
| `README.md` | Main documentation with complete feature list and setup |
| `CHANGELOG.md` | Version history and release notes |
| `CONTRIBUTING.md` | Guidelines for contributing to the project |
| `SETUP_GUIDE.md` | Detailed step-by-step setup instructions |
| `QUEUE_SYSTEM_README.md` | Queue management system documentation |
| `LICENSE` | ISC License text |
| `.gitignore` | Files to exclude from Git |
| `.env.example` | Template for environment variables |
| `zapp.yaml` | Z Open Editor configuration |

### Backend Structure

#### `/backend/src/`

**Core Files:**
- `app.js` - Express application setup with middleware configuration
- `index.js` - Server initialization with Socket.io and HTTP server
- `socket.js` - Real-time communication setup with Socket.io

**Subdirectories:**

**`config/`** - Configuration management
- `db2.js` - IBM DB2 database connection settings

**`db/`** - Database operations
- `index.js` - Database connection and pool management

**`models/`** - Data access layer
- `Patient.model.js` - All patient-related database operations
- `Queue.model.js` - Queue management database operations
- `Analytics.model.js` - Analytics data retrieval

**`controllers/`** - Request handlers
- `patientController.js` - Patient endpoints handler
- `queueController.js` - Queue endpoints handler
- `analyticsController.js` - Analytics endpoints handler

**`routes/`** - API endpoint definitions
- `patient.routes.js` - Patient API routes
- `queue.routes.js` - Queue API routes
- `analytics.routes.js` - Analytics API routes

**`middlewares/`** - Express middleware
- `errorHandler.js` - Global error handling and 404 handler
- `multer.middleware.js` - File upload configuration and validation

**`services/`** - Business logic layer
- `fileParser.js` - CSV/TXT file parsing and validation

**`utils/`** - Helper functions
- `validators.js` - Input validation utilities

#### `/backend/db_scripts/`

Database initialization and migration scripts:
- `create_table.sql` - PATIENT_MASTER table schema
- `create_queue_table.sql` - QUEUE_MASTER table schema
- SQL and Node.js migration/setup scripts

### Frontend Structure

#### `/frontend/src/`

**Core Files:**
- `main.jsx` - React DOM render entry point
- `App.jsx` - Main App component with routing
- `App.css` - Application styles
- `index.css` - Global base styles

**Subdirectories:**

**`pages/`** - Full page components
- `Home.jsx` - Homepage dashboard
- `UploadPage.jsx` - CSV upload interface
- `ManualEntryPage.jsx` - Manual patient entry form
- `SearchPage.jsx` - Patient search interface
- `PatientsListPage.jsx` - Paginated patient listing
- `QueueBoard.jsx` - Public queue display board
- `QueueManagement.jsx` - Admin queue management

**`components/`** - Reusable components
- `FileUpload.jsx` - Drag-and-drop file upload
- `PatientInfo.jsx` - Patient details display
- `QueueControl.jsx` - Queue action buttons
- `SearchPatient.jsx` - Search input component

**`services/`** - API integration
- `api.js` - Axios instance with base configuration and API calls

**`assets/`** - Static resources
- Images, icons, and other media files

#### `/frontend/public/`

Static files served directly:
- Favicon
- Images
- Other static assets

## Architecture Patterns

### Backend Architecture

```
Routes → Controllers → Services → Models → Database
  ↓          ↓           ↓        ↓         ↓
 API      Handlers    Business   DB      DB2
Paths    Request      Logic    Queries
        Validation
```

### Frontend Architecture

```
Pages → Components → Services → API
  ↓         ↓           ↓       ↓
Routes   UI Logic    API Calls Backend
         State       Config
         Props
```

## Data Flow

### Patient Upload Flow

```
1. FileUpload.jsx → File selected
   ↓
2. API call → POST /api/patient/upload
   ↓
3. patientController.js → Handle upload request
   ↓
4. fileParser.js → Parse CSV file
   ↓
5. Patient.model.js → Insert/update database
   ↓
6. Response → UI update with summary
```

### Real-time Queue Update Flow

```
1. QueueControl.jsx → User adds to queue
   ↓
2. API call → POST /api/queue/add
   ↓
3. queueController.js → Process request
   ↓
4. Queue.model.js → Database insert
   ↓
5. Socket.io → Emit 'queue_updated'
   ↓
6. QueueBoard.jsx → Real-time update via WebSocket
```

## Database Tables Structure

### PATIENT_MASTER
```sql
AADHAR_NO (PK)        → Patient identifier
NAME                  → Full name
AGE                   → Age
GENDER                → M/F/O
ADDRESS               → Residential address
PHONE                 → Contact number
DEPARTMENT_VISITED    → Comma-separated departments
CREATED_AT            → Record creation timestamp
UPDATED_AT            → Last update timestamp
```

### QUEUE_MASTER
```sql
QUEUE_ID (PK)         → Auto-increment
PATIENT_ID            → Reference to patient
AADHAR_NO             → Patient's Aadhar
PATIENT_NAME          → Patient name
DEPARTMENT            → Current department
STATUS                → WAITING/IN_PROGRESS/DONE
QUEUE_NUMBER          → Position in queue
CREATED_AT            → Entry timestamp
UPDATED_AT            → Status update timestamp
```

## Configuration Files

### Environment Configuration (.env)
```
DB2_CONN_STRING       → Database connection
PORT                  → Server port
NODE_ENV              → Environment mode
FRONTEND_URL          → CORS origin
```

### Vite Configuration (frontend)
- Asset optimization
- Development server settings
- Build output configuration

### Tailwind Configuration (frontend)
- Custom color schemes
- Typography settings
- Plugin configurations

## Naming Conventions

### Files
- Components: PascalCase (e.g., `PatientCard.jsx`)
- Utilities: camelCase (e.g., `validators.js`)
- Routes: camelCase (e.g., `patient.routes.js`)

### Variables & Functions
- Constants: UPPER_SNAKE_CASE
- Functions: camelCase
- React Components: PascalCase
- CSS Classes: kebab-case

### Database
- Table names: UPPER_SNAKE_CASE
- Column names: UPPER_SNAKE_CASE
- Indexes: IDX_TABLE_COLUMN

## Dependencies Overview

### Backend Key Dependencies
- `express`: Web framework
- `ibm_db`: DB2 driver
- `socket.io`: Real-time communication
- `multer`: File upload handling
- `csv-parser`: CSV parsing
- `cors`: Cross-origin requests
- `dotenv`: Environment variables

### Frontend Key Dependencies
- `react`: UI library
- `react-router-dom`: Routing
- `axios`: HTTP client
- `socket.io-client`: Real-time client
- `tailwindcss`: Styling
- `chart.js`: Data visualization
- `jspdf`: PDF generation

## Best Practices in This Project

1. **Separation of Concerns**: Controllers, services, models separated
2. **Error Handling**: Global error middleware
3. **Real-time Updates**: Socket.io for live features
4. **Responsive Design**: Tailwind CSS for all devices
5. **API Consistency**: RESTful endpoints with JSON responses
6. **Database**: Parameterized queries to prevent SQL injection
7. **Validation**: Input validation at API level
8. **Documentation**: Comprehensive comments and docs

---

For more details on specific modules, refer to inline comments in respective files.
