# 🏥 Hospital Patient Management System

> A comprehensive full-stack web application for managing hospital patient visit records with real-time queue management, Aadhar-based deduplication, and live queue board display.

[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)
[![Node.js Version](https://img.shields.io/badge/node-v16+-green.svg)](https://nodejs.org/)
[![React Version](https://img.shields.io/badge/React-19-blue.svg)](https://react.dev/)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)
- [Configuration](#configuration)
- [File Upload Format](#file-upload-format)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## 📖 Overview

This Hospital Patient Management System is a robust full-stack application designed to:
- Efficiently manage patient visit records in a hospital environment
- Prevent duplicate patient records using Aadhar number-based deduplication
- Provide real-time queue management with live display boards
- Enable bulk import of patient data through CSV/TXT files
- Track patient department visit history

## 🎯 Features

### Patient Management
- ✅ **Bulk Upload**: Import CSV/TXT files containing daily patient visit records
- ✅ **Smart Deduplication**: Automatically detects existing patients using Aadhar number
- ✅ **Department History Tracking**: Maintains comma-separated visit history for each patient
- ✅ **Patient Search**: Quick lookup by Patient ID or Aadhar number
- ✅ **Patient Information Display**: View complete patient details and history

### Queue Management System
- ✅ **Real-Time Queue Updates**: WebSocket-based live queue board
- ✅ **Department-Based Queues**: Separate queue management for each department
- ✅ **Queue Control**: Doctors can call next patient and mark as complete
- ✅ **Live Display Board**: Public-facing board perfect for waiting rooms
- ✅ **Queue Analytics**: Track queue performance metrics

### User Interface
- ✅ **Responsive Design**: Mobile-friendly interface with Tailwind CSS
- ✅ **Multiple Pages**: 
  - Upload Patient Records
  - Manual Entry
  - Patient Search
  - Patient List
  - Queue Management
  - Queue Board Display
- ✅ **Real-time Notifications**: Instant updates across all connected clients
- ✅ **Modern UI Components**: Built with Lucide React icons

## 🏗️ Architecture

### Technology Stack

**Backend:**
- **Runtime**: Node.js (v16+)
- **Framework**: Express.js 5.x
- **Database**: IBM DB2
- **Real-time**: Socket.io 4.x
- **File Processing**: Multer, csv-parser
- **Environment**: dotenv

**Frontend:**
- **Framework**: React 19 with Vite
- **Routing**: React Router 7.x
- **HTTP Client**: Axios
- **Styling**: Tailwind CSS 4.x
- **Icons**: Lucide React
- **Real-time**: Socket.io-client 4.x
- **Charts**: Chart.js with react-chartjs-2
- **PDF Export**: jsPDF with autotable

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend (React + Vite)                │
│  Upload │ Search │ Queue Board │ Analytics │ Management    │
└──────────────────────┬──────────────────────────────────────┘
                       │ (HTTP/WebSocket)
                       ▼
┌──────────────────────────────────────────────────────────────┐
│            Backend API (Express.js + Socket.io)              │
│  Routes │ Controllers │ Services │ Middlewares               │
└──────────────────────┬───────────────────────────────────────┘
                       │
                       ▼
            ┌──────────────────────┐
            │   IBM DB2 Database   │
            │  PATIENT_MASTER      │
            │  QUEUE_MASTER        │
            │  ANALYTICS           │
            └──────────────────────┘
```

## 📋 Prerequisites

- **Node.js**: v16 or higher
- **npm**: v7 or higher
- **IBM DB2**: Database access with credentials
- **DB2 CLI Tools**: For database initialization (optional)
- **Git**: For version control

## 🚀 Quick Start

### 1. Clone and Install

```bash
# Clone repository
git clone <repository-url>
cd HospitalMangement-main

# Install backend dependencies
cd backend
npm install

# Install frontend dependencies
cd ../frontend
npm install
```

### 2. Database Setup

**Option A: Using DB2 Command Line (Recommended)**

```bash
# Connect to DB2
db2 connect to HOSPDB user db2admin using Atharva@123

# Create tables
db2 -tvf backend/db_scripts/create_table.sql
db2 -tvf backend/db_scripts/create_queue_table.sql

# Disconnect
db2 disconnect HOSPDB

# Test connection
cd backend
node test-db.js
```

**Option B: Using DB2 GUI Tool**

1. Open DB2 Control Center or DBeaver
2. Connect to HOSPDB database
3. Execute SQL files from `backend/db_scripts/`
   - `create_table.sql` - Patient master table
   - `create_queue_table.sql` - Queue management table

### 3. Environment Configuration

Create `.env` file in backend directory:

```env
# Database Configuration
DB2_CONN_STRING=DATABASE=HOSPDB;HOSTNAME=127.0.0.1;PORT=25000;PROTOCOL=TCPIP;UID=db2admin;PWD=Atharva@123;

# Server Configuration
PORT=4000
NODE_ENV=development

# Frontend URL (for CORS)
FRONTEND_URL=http://localhost:5173
```

### 4. Start the Application

**Terminal 1 - Backend Server:**

```bash
cd backend
npm start
# Backend running on http://localhost:4000
```

**Terminal 2 - Frontend Server:**

```bash
cd frontend
npm run dev
# Frontend running on http://localhost:5173
```

### 5. Access the Application

Open your browser and navigate to:

```
http://localhost:5173
```

## 📁 Project Structure

```
HospitalMangement-main/
├── backend/                          # Backend server (Node.js/Express)
│   ├── src/
│   │   ├── app.js                    # Express app configuration
│   │   ├── index.js                  # Server entry point
│   │   ├── socket.js                 # Socket.io configuration
│   │   ├── config/
│   │   │   └── db2.js                # DB2 connection config
│   │   ├── controllers/
│   │   │   ├── patientController.js  # Patient operations
│   │   │   ├── queueController.js    # Queue operations
│   │   │   └── analyticsController.js # Analytics operations
│   │   ├── models/
│   │   │   ├── Patient.model.js      # Patient model
│   │   │   ├── Queue.model.js        # Queue model
│   │   │   └── Analytics.model.js    # Analytics model
│   │   ├── routes/
│   │   │   ├── patient.routes.js     # Patient endpoints
│   │   │   ├── queue.routes.js       # Queue endpoints
│   │   │   └── analytics.routes.js   # Analytics endpoints
│   │   ├── middlewares/
│   │   │   ├── errorHandler.js       # Error handling
│   │   │   └── multer.middleware.js  # File upload
│   │   ├── services/
│   │   │   └── fileParser.js         # CSV parsing
│   │   ├── utils/
│   │   │   └── validators.js         # Data validation
│   │   └── db/
│   │       └── index.js              # Database connection
│   ├── db_scripts/
│   │   ├── create_table.sql          # Patient table schema
│   │   ├── create_queue_table.sql    # Queue table schema
│   │   ├── add_visit_count.sql       # Visit count column
│   │   └── setup-queue.bat           # Windows setup script
│   ├── sample_data.csv               # Sample patient data
│   ├── package.json
│   └── test-db.js                    # Database connection test
├── frontend/                         # Frontend app (React/Vite)
│   ├── src/
│   │   ├── main.jsx                  # React entry point
│   │   ├── App.jsx                   # Main App component
│   │   ├── App.css                   # Global styles
│   │   ├── index.css                 # Base styles
│   │   ├── components/
│   │   │   ├── FileUpload.jsx        # File upload component
│   │   │   ├── PatientInfo.jsx       # Patient info display
│   │   │   ├── QueueControl.jsx      # Queue control panel
│   │   │   └── SearchPatient.jsx     # Patient search
│   │   ├── pages/
│   │   │   ├── Home.jsx              # Home page
│   │   │   ├── UploadPage.jsx        # CSV upload page
│   │   │   ├── ManualEntryPage.jsx   # Manual patient entry
│   │   │   ├── SearchPage.jsx        # Patient search page
│   │   │   ├── PatientsListPage.jsx  # Patient listing
│   │   │   ├── QueueBoard.jsx        # Live queue display
│   │   │   └── QueueManagement.jsx   # Queue management
│   │   ├── services/
│   │   │   └── api.js                # API service layer
│   │   └── assets/                   # Images/resources
│   ├── public/                       # Static files
│   ├── vite.config.js                # Vite configuration
│   ├── tailwind.config.js            # Tailwind CSS config
│   ├── eslint.config.js              # ESLint configuration
│   ├── index.html                    # HTML template
│   └── package.json
├── README.md                         # This file
├── SETUP_GUIDE.md                    # Detailed setup guide
├── QUEUE_SYSTEM_README.md            # Queue system documentation
└── .env.example                      # Environment variables template
```

## 🔌 API Documentation

### Patient Endpoints

#### Get All Patients
```
GET /api/patients
Response: [{ AADHAR_NO, NAME, AGE, GENDER, ADDRESS, PHONE, DEPARTMENT_VISITED, CREATED_AT }]
```

#### Get Patient by ID
```
GET /api/patient/:id
Response: { AADHAR_NO, NAME, AGE, ... }
```

#### Add Single Patient
```
POST /api/patient/add
Body: { aadhar_no, name, age, gender, address, phone, department }
Response: { success, message, patient }
```

#### Bulk Upload Patients
```
POST /api/patient/upload
Content-Type: multipart/form-data
File: CSV with columns (Aadhar, Name, Age, Gender, Address, Phone, Department)
Response: { success, summary: { newCount, updateCount }, patients }
```

#### Search Patient
```
GET /api/patient/search?aadhar=123456789012
Response: { found, patient }
```

### Queue Endpoints

#### Add to Queue
```
POST /api/queue/add
Body: { patient_id, aadhar_no, patient_name, department }
Response: { success, queue_id, queue_number }
```

#### Get Active Queue
```
GET /api/queue/active
Response: [{ QUEUE_ID, PATIENT_NAME, DEPARTMENT, STATUS, QUEUE_NUMBER }]
```

#### Get Queue by Department
```
GET /api/queue/department/:department
Response: [{ QUEUE_ID, PATIENT_NAME, STATUS, QUEUE_NUMBER }]
```

#### Call Next Patient
```
PUT /api/queue/call-next/:department
Response: { success, queue_id, patient_name }
```

#### Complete Patient Service
```
PUT /api/queue/complete/:queue_id
Response: { success, message }
```

### Analytics Endpoints

#### Get Queue Analytics
```
GET /api/analytics/queue-stats
Response: { totalWaiting, totalInProgress, averageWaitTime }
```

#### Get Department Stats
```
GET /api/analytics/department-stats
Response: [{ department, patientCount, visitCount }]
```

## 💾 Database Schema

### PATIENT_MASTER Table

```sql
CREATE TABLE PATIENT_MASTER (
    AADHAR_NO CHAR(12) NOT NULL PRIMARY KEY,
    NAME VARCHAR(50) NOT NULL,
    AGE INTEGER,
    GENDER CHAR(1),
    ADDRESS VARCHAR(100),
    PHONE VARCHAR(15),
    DEPARTMENT_VISITED VARCHAR(500),
    CREATED_AT TIMESTAMP DEFAULT CURRENT TIMESTAMP,
    UPDATED_AT TIMESTAMP
);
```

### QUEUE_MASTER Table

```sql
CREATE TABLE QUEUE_MASTER (
    QUEUE_ID INTEGER PRIMARY KEY AUTO_INCREMENT,
    PATIENT_ID VARCHAR(20),
    AADHAR_NO CHAR(12),
    PATIENT_NAME VARCHAR(50),
    DEPARTMENT VARCHAR(50),
    STATUS VARCHAR(20) DEFAULT 'WAITING',
    QUEUE_NUMBER INTEGER,
    CREATED_AT TIMESTAMP DEFAULT CURRENT TIMESTAMP,
    UPDATED_AT TIMESTAMP
);
```

## ⚙️ Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `DB2_CONN_STRING` | DB2 database connection string | Required |
| `PORT` | Backend server port | 4000 |
| `NODE_ENV` | Environment mode | development |
| `FRONTEND_URL` | Frontend URL for CORS | http://localhost:5173 |

### Database Connection

Database connection is configured in `backend/src/config/db2.js`

```javascript
const connString = process.env.DB2_CONN_STRING;
const db2 = require('ibm_db');
// Connection established automatically on server start
```

## 📤 File Upload Format

### CSV/TXT File Requirements

**Required Columns (in order):**
1. **Aadhar** - 12-digit Aadhar number (PRIMARY KEY)
2. **Name** - Patient full name
3. **Age** - Patient age (integer)
4. **Gender** - M/F/O
5. **Address** - Patient address
6. **Phone** - 10-digit phone number
7. **Department** - Department visited

**Example:**
```csv
Aadhar,Name,Age,Gender,Address,Phone,Department
123456789012,John Doe,35,M,123 Main St,9876543210,Cardiology
234567890123,Jane Smith,28,F,456 Oak Ave,9876543211,Neurology
345678901234,Bob Johnson,45,M,789 Pine Rd,9876543212,Orthopedics
```

**Supported Formats:**
- `.csv` - Comma-separated values
- `.txt` - Tab or comma-separated
- Line endings: Windows (CRLF) or Unix (LF)

See `backend/sample_data.csv` for example file.

## 🐛 Troubleshooting

### Database Connection Issues

**Problem**: "Connection failed" error

**Solutions:**
1. Verify DB2 is running: `db2 list db directory`
2. Check connection string in `.env`
3. Verify credentials are correct
4. Ensure DB2 firewall/port is accessible

```bash
# Test connection
cd backend
node test-db.js
```

### Frontend-Backend Communication

**Problem**: "Cannot connect to server" or CORS errors

**Solutions:**
1. Ensure backend is running on correct port (4000)
2. Check `FRONTEND_URL` in backend `.env`
3. Verify CORS configuration in `backend/src/app.js`
4. Check browser console for specific error

### WebSocket/Queue Issues

**Problem**: Queue updates not showing in real-time

**Solutions:**
1. Restart both servers (backend and frontend)
2. Check Socket.io is initialized: Look for "WebSocket server is ready" in console
3. Verify client can connect to WebSocket
4. Check browser DevTools Network tab for WebSocket connection

### File Upload Issues

**Problem**: "File upload failed" or data not inserted

**Solutions:**
1. Verify CSV format matches requirements
2. Check file size (max 16MB)
3. Ensure Aadhar numbers are 12 digits
4. Check database table exists with correct schema
5. Review backend logs for detailed error message

## 📊 Performance Considerations

- **Database Indexing**: Indexes created on AADHAR_NO and NAME for fast searches
- **Connection Pooling**: DB2 manages connection pool automatically
- **Real-time Updates**: Socket.io broadcasts to all connected clients
- **File Upload**: Large files processed in chunks by Multer

## 🔒 Security Features

- **Input Validation**: All patient data validated before database insertion
- **Error Handling**: Global error handler prevents stack traces in production
- **CORS Configuration**: Restricted to frontend origin
- **Environment Variables**: Sensitive data not hardcoded

## 🚀 Deployment Guide

### Production Build

```bash
# Frontend production build
cd frontend
npm run build

# Output in: frontend/dist/
```

### Deployment Considerations

1. Update `.env` with production database credentials
2. Set `NODE_ENV=production`
3. Use production DB2 database instance
4. Configure proper CORS origin
5. Set up reverse proxy (nginx/Apache)
6. Enable HTTPS
7. Configure database backups

## 📝 License

This project is licensed under the ISC License - see LICENSE file for details.

## 👥 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📧 Support

For issues, questions, or suggestions:

1. Check existing [Issues](../../issues)
2. Create a new issue with detailed description
3. Include error messages and logs
4. Mention your OS and Node.js version

## 🙏 Acknowledgments

- IBM DB2 documentation and community
- React documentation
- Express.js framework
- Socket.io real-time library

---

**Last Updated**: December 30, 2024

**Version**: 1.0.0

Application will run on: `http://localhost:5173`

## 🔌 API Endpoints

### POST /api/uploadFile
Upload CSV/TXT file with patient records
- **Input**: FormData with 'file' field
- **Response**: Summary of new/updated patients

### POST /api/addVisit
Add single patient visit
- **Input**: JSON with patient data
- **Response**: Created/updated patient record

### GET /api/patient/:aadhar
Get patient by Aadhar number
- **Input**: 12-digit Aadhar in URL
- **Response**: Patient details with visit history

### GET /api/allPatients
Get all patients
- **Response**: Array of all patient records

### GET /api/stats
Get system statistics
- **Response**: Total patient count

### GET /health
Health check endpoint
- **Response**: System status

## 📁 File Format

### CSV Format
```csv
AADHAR_NO,NAME,AGE,GENDER,ADDRESS,PHONE,DEPARTMENT_VISITED
123456789012,Rajesh Kumar,45,M,123 MG Road Mumbai,9876543210,Heart
234567890123,Priya Sharma,32,F,456 Park Street Kolkata,9876543211,Fracture
```

### Required Fields
- **AADHAR_NO**: 12-digit number (required)
- **NAME**: Patient name (required)
- **DEPARTMENT_VISITED**: Department name (required)

### Optional Fields
- **AGE**: Integer
- **GENDER**: M/F/O
- **ADDRESS**: Text
- **PHONE**: 10-15 digits

## 🔄 Deduplication Logic

1. When a record is uploaded/added:
   - System checks if Aadhar exists in database
   - **If NEW**: Creates new patient record
   - **If EXISTS**: Appends new department to DEPARTMENT_VISITED field
   
2. Department concatenation example:
   - First visit: "Heart"
   - Second visit: "Heart, Fracture"
   - Third visit: "Heart, Fracture, Skin"

## 🧪 Testing

### Test with Sample Data

A sample CSV file is provided at `backend/sample_data.csv`

1. Open the application: `http://localhost:5173`
2. Navigate to Upload page
3. Upload `sample_data.csv`
4. Verify the summary shows correct counts
5. Upload the same file again to test deduplication
6. Search for patient "123456789012" to verify department concatenation

### Manual Testing Checklist

- [ ] Upload CSV file successfully
- [ ] Verify new patients are created
- [ ] Upload same file to test deduplication
- [ ] Verify departments are concatenated correctly
- [ ] Search patient by Aadhar
- [ ] View patient details and visit history
- [ ] Test with invalid Aadhar (should show error)
- [ ] Test file upload with invalid format

## 📂 Project Structure

```
Project/
├── backend/
│   ├── db_scripts/
│   │   └── create_table.sql
│   ├── public/
│   │   └── temp/              # Temporary file uploads
│   ├── src/
│   │   ├── config/
│   │   │   └── db2.js
│   │   ├── controllers/
│   │   │   └── patientController.js
│   │   ├── db/
│   │   │   └── index.js
│   │   ├── middlewares/
│   │   │   ├── errorHandler.js
│   │   │   └── multer.middleware.js
│   │   ├── models/
│   │   │   └── Patient.model.js
│   │   ├── routes/
│   │   │   └── patient.routes.js
│   │   ├── services/
│   │   │   └── fileParser.js
│   │   ├── utils/
│   │   │   └── validators.js
│   │   ├── app.js
│   │   └── index.js
│   ├── .env
│   ├── package.json
│   └── sample_data.csv
│
└── frontend/
    ├── src/
    │   ├── components/
    │   │   ├── FileUpload.jsx
    │   │   ├── PatientInfo.jsx
    │   │   └── SearchPatient.jsx
    │   ├── pages/
    │   │   ├── Home.jsx
    │   │   ├── SearchPage.jsx
    │   │   └── UploadPage.jsx
    │   ├── services/
    │   │   └── api.js
    │   ├── App.css
    │   ├── App.jsx
    │   ├── index.css
    │   └── main.jsx
    ├── index.html
    ├── package.json
    └── vite.config.js
```

## 🛠️ Technologies Used

### Backend
- Express.js 5.1
- IBM DB2 (ibm_db 3.3.4)
- Multer 2.0.2 (file uploads)
- CSV-parser 3.2.0
- CORS
- Cookie-parser
- Dotenv

### Frontend
- React 19.2
- React Router DOM
- Axios
- Vite 7.2
- Tailwind CSS 4.1

## 🐛 Troubleshooting

### Backend won't start
- Check if DB2 connection string is correct in `.env`
- Verify DB2 service is running
- Check if port 4000 is available

### Frontend won't start
- Run `npm install` to ensure dependencies are installed
- Check if port 5173 is available
- Clear browser cache

### File upload fails
- Ensure `backend/public/temp/` directory exists
- Check file size (max 5MB)
- Verify file format (CSV or TXT only)

### Database connection error
- Verify DB2 service is running on port 25000
- Check credentials in `.env`
- Ensure PATIENT_MASTER table exists

## 📝 Notes

- Department visit history is stored as comma-separated values (max 500 characters)
- Uploaded files are automatically deleted after processing
- System prevents duplicate departments in visit history
- All API responses follow consistent JSON format

## 👥 Support

For issues or questions, please check:
1. Console logs (browser and terminal)
2. Network tab in browser DevTools
3. DB2 error logs

## 📄 License

This project is for educational/internal use.
# HospitalMangement
