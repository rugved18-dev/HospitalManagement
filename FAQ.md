# Frequently Asked Questions (FAQ)

## General Questions

### Q: What is this project about?
**A:** This is a comprehensive Hospital Patient Management System that handles:
- Patient record management with Aadhar-based deduplication
- Real-time queue management for hospital departments
- Bulk import of patient data from CSV files
- Live queue board display (similar to airport displays)

### Q: Who should use this system?
**A:** This system is designed for:
- Hospital administrators managing patient records
- Receptionists handling patient check-ins
- Doctors and medical staff managing patient queues
- Hospital IT teams deploying healthcare solutions

### Q: Is this system production-ready?
**A:** Version 1.0.0 is production-ready for basic hospital queue management. For enterprise deployments, consider adding:
- User authentication and role-based access
- Advanced security features
- Database backup and recovery
- Load balancing for multiple servers

### Q: What is the license?
**A:** This project is licensed under the ISC License - free to use, modify, and distribute.

---

## Installation & Setup

### Q: What are the system requirements?
**A:** 
- Node.js v16 or higher
- npm v7 or higher
- IBM DB2 database
- 100MB free disk space
- Modern web browser (Chrome, Firefox, Safari, Edge)

### Q: Can I run this on Windows/Mac/Linux?
**A:** 
- **Backend**: Works on all operating systems
- **Frontend**: Works on all operating systems
- **Database**: DB2 runs on all platforms
- Note: Some batch scripts are Windows-specific; Linux users should adapt them to bash

### Q: How do I set up the database?
**A:** Three options:
1. **DB2 Command Line**: Use `db2 -tvf` command
2. **GUI Tool**: Use DB2 Control Center or DBeaver
3. **Manual**: Copy-paste SQL from setup guide

See [SETUP_GUIDE.md](SETUP_GUIDE.md) for detailed instructions.

### Q: What if I don't have DB2 installed?
**A:** You'll need DB2 database access:
- Install DB2 Server locally, OR
- Use DB2 Cloud service, OR
- Get access to existing DB2 instance

### Q: Can I change the database credentials?
**A:** Yes, update the `.env` file with your credentials:
```env
DB2_CONN_STRING=DATABASE=HOSPDB;HOSTNAME=...;UID=user;PWD=password;
```

### Q: How do I test the database connection?
**A:**
```bash
cd backend
node test-db.js
```
This runs comprehensive database tests.

---

## Features & Functionality

### Q: How does patient deduplication work?
**A:** 
- When uploading patient records, the system checks if Aadhar number exists
- If found: Updates existing record with new department visit
- If not found: Creates new patient record
- This prevents duplicate patient entries

### Q: Can I manually add patients without uploading CSV?
**A:** Yes, use the **Manual Entry** page in the application to add individual patients.

### Q: What CSV format should I use?
**A:** Required columns (in order):
```
Aadhar, Name, Age, Gender, Address, Phone, Department
```
Example: `123456789012, John Doe, 35, M, 123 Main St, 9876543210, Cardiology`

See `backend/sample_data.csv` for example.

### Q: How does the real-time queue work?
**A:** 
- Uses Socket.io WebSocket connection
- When patient is added to queue, all connected clients update instantly
- Doctors can call next patient or mark complete
- Public display board shows current status in real-time

### Q: Can I view queue history?
**A:** Currently, completed queue entries are marked "DONE". For historical analysis, use the Analytics page.

### Q: How many patients can the system handle?
**A:** 
- No hard limit in application
- Performance depends on database size
- Tested with 10,000+ patient records
- For 100,000+ records, implement pagination (planned for v1.1)

---

## Technical Questions

### Q: How do I start both servers?
**A:** Open two terminals:
```bash
# Terminal 1
cd backend
npm start

# Terminal 2
cd frontend
npm run dev
```

### Q: What port does the application use?
**A:**
- Backend API: `http://localhost:4000`
- Frontend: `http://localhost:5173`
- Database: Port specified in connection string (usually 25000)

### Q: Can I change the port numbers?
**A:** 
- **Backend**: Update `PORT` in `.env` and restart
- **Frontend**: Vite will automatically use next available port
- Ensure CORS is configured correctly for new ports

### Q: How do I deploy to production?
**A:** See [Deployment Guide](README.md#-deployment-guide) in README for steps.

### Q: Can I run both servers on the same machine?
**A:** Yes, use different ports and ensure sufficient resources (RAM, CPU).

### Q: What is Socket.io and why is it used?
**A:** Socket.io enables real-time bidirectional communication:
- Queue board updates instantly
- No page refresh needed
- Persistent connection between client and server
- Perfect for queue status updates

### Q: How do I check if Socket.io is working?
**A:** 
1. Check browser DevTools Network tab
2. Look for WebSocket connection to `localhost:4000/socket.io`
3. Check backend console for "WebSocket server is ready"

---

## File Upload & Data

### Q: What file formats are supported?
**A:** 
- `.csv` - Comma-separated values
- `.txt` - Tab or comma-separated
- Maximum file size: 16MB
- Line endings: Windows (CRLF) or Unix (LF)

### Q: What happens if I upload invalid data?
**A:** 
- Invalid records are skipped
- Summary shows successful and failed counts
- Check backend logs for specific error messages
- Ensure Aadhar is 12 digits and unique

### Q: Can I undo an upload?
**A:** Currently, there's no undo feature. Delete records manually from database if needed. (Planned for v1.1)

### Q: Where are uploaded files stored?
**A:** Files are processed and not stored. Only parsed data goes to database.

### Q: Can I export patient data?
**A:** 
- Use Analytics page for reports
- Plans to add PDF/Excel export in v1.1

---

## Troubleshooting

### Q: Backend won't start
**A:** Check:
1. Node.js installed: `node --version`
2. Dependencies installed: `npm install`
3. `.env` file exists with valid connection string
4. DB2 database is running and accessible
5. Port 4000 is not in use

### Q: Frontend won't start
**A:** Check:
1. Node.js installed
2. Dependencies installed: `npm install`
3. No other service on port 5173
4. Backend is running

### Q: "Cannot connect to database" error
**A:** 
1. Verify DB2 is running
2. Check connection string in `.env`
3. Verify username/password
4. Ensure firewall allows DB2 port
5. Test with: `node test-db.js`

### Q: CORS error in browser
**A:** 
1. Check `FRONTEND_URL` in `.env` matches actual URL
2. Restart backend server
3. Clear browser cache
4. Check browser console for specific error

### Q: Queue not updating in real-time
**A:** 
1. Restart both servers
2. Hard refresh browser (Ctrl+Shift+R)
3. Check WebSocket connection in DevTools
4. Ensure Socket.io is initialized (check console logs)

### Q: File upload fails
**A:** 
1. Check file format is CSV/TXT
2. Verify file size < 16MB
3. Ensure columns match required format
4. Check Aadhar numbers are 12 digits
5. Check database table exists

### Q: Can't find a patient I just added
**A:** 
1. Refresh page
2. Check if patient was added to correct database
3. Verify search criteria (Aadhar or Name)
4. Check for special characters in name

### Q: Database table not created
**A:** 
1. Ensure connected to DB2: `db2 connect to HOSPDB`
2. Run create script: `db2 -tvf create_table.sql`
3. Verify with: `db2 "SELECT TABNAME FROM SYSCAT.TABLES WHERE TABNAME = 'PATIENT_MASTER'"`

---

## Performance & Optimization

### Q: Is the application fast enough?
**A:** 
- Queries optimized with indexes
- Database connection pooling enabled
- Frontend optimized with Vite bundling
- Real-time updates efficient with Socket.io

### Q: How can I improve performance?
**A:** 
1. Add database indexes on frequently searched columns
2. Implement pagination for large datasets
3. Use caching for static data
4. Optimize frontend bundle size
5. Use CDN for static files (production)

### Q: What's the maximum queue size?
**A:** No limit. Performance depends on:
- Browser capabilities
- Network speed
- Database size
- Server resources

---

## Security

### Q: Is patient data secure?
**A:** Current security features:
- Input validation and sanitization
- Error handling prevents data leaks
- No credentials in code
- Environment variables for sensitive data

For production, add:
- HTTPS encryption
- Authentication and authorization
- Role-based access control
- Audit logging
- Database encryption

### Q: Can I hide patient data from certain users?
**A:** Not in v1.0. User-based access control planned for v1.1.

### Q: Are database connections encrypted?
**A:** Configure in DB2 connection string. See DB2 documentation for SSL configuration.

### Q: How do I secure the application for production?
**A:** 
1. Use HTTPS
2. Add authentication
3. Implement role-based access
4. Use environment variables for secrets
5. Enable database encryption
6. Set up regular backups
7. Configure firewall rules
8. Monitor access logs

---

## API & Integration

### Q: Can I integrate with other systems?
**A:** Yes, REST API is available. See [API Documentation](README.md#-api-documentation) for endpoints.

### Q: Can I call the API from external applications?
**A:** Yes, configure CORS to allow external origins in `backend/src/app.js`.

### Q: Are there API rate limits?
**A:** Not implemented in v1.0. Planned for v1.1 for production deployments.

### Q: How do I authenticate API calls?
**A:** Currently no authentication. Planned for v1.1. For now, use in trusted network only.

---

## Contributing & Development

### Q: How do I contribute to this project?
**A:** See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

### Q: How do I report bugs?
**A:** Open an issue with:
1. Clear description
2. Steps to reproduce
3. Expected vs actual behavior
4. Screenshots if applicable
5. System information

### Q: Can I request new features?
**A:** Yes, open a feature request issue with:
1. Use case description
2. Proposed solution
3. Why it's needed
4. Any alternatives considered

### Q: How can I set up development environment?
**A:** 
1. Fork the repository
2. Clone to local machine
3. Install dependencies: `npm install`
4. Configure `.env`
5. Run tests: `npm test`
6. Create feature branch
7. Make changes and commit
8. Push and create pull request

---

## Updates & Versioning

### Q: How do I check which version I'm running?
**A:** Check `package.json` or `README.md` for version number.

### Q: How do I update to the latest version?
**A:**
```bash
git pull origin main
npm install
npm start
```

### Q: What's new in the latest version?
**A:** See [CHANGELOG.md](CHANGELOG.md) for version history.

### Q: When will new features be released?
**A:** See roadmap in [CHANGELOG.md](CHANGELOG.md) for planned releases.

---

## Other Questions

### Q: Is there a mobile app?
**A:** Not yet. Mobile app planned for v1.1.

### Q: Can I use a different database?
**A:** Currently designed for IBM DB2. Porting to other databases requires code changes.

### Q: Can I customize the UI?
**A:** Yes, modify React components in `frontend/src/`. Styling with Tailwind CSS.

### Q: Are there any known issues?
**A:** See [CHANGELOG.md - Known Issues](CHANGELOG.md#known-issues) section.

### Q: How do I get support?
**A:** 
1. Check this FAQ
2. Read documentation files
3. Search existing GitHub issues
4. Open a new issue for help
5. Check troubleshooting section

---

## Contact & Support

**Questions?** Please open an [issue](../../issues) on GitHub.

**Found a bug?** Submit a detailed bug report with reproduction steps.

**Want to contribute?** Check [CONTRIBUTING.md](CONTRIBUTING.md) guidelines.

---

**Last Updated**: December 30, 2024

**Version**: 1.0.0
