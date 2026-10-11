# Camera Dashboard

**Version:** V161  
**Application file:** `camera_dashboard.py`  
**Project type:** Standalone Python / Flask CCTV & NVR management dashboard  
**Database:** SQLite  
**Primary stream technology:** RTSP + FFmpeg/FFprobe  
**Supported device families:** Hikvision, Dahua, Uniview, generic RTSP/IP cameras and NVR/DVR devices

---

## 1. Project Overview

Camera Dashboard is a self-hosted CCTV/IP-camera management application designed to manage a large inventory of cameras, NVRs, DVRs and RTSP sources from one web dashboard.

The application combines:

- Camera/NVR inventory management
- RTSP stream validation
- Live camera viewing
- Device/vendor identification
- Channel discovery
- Health monitoring
- Diagnostics
- Categories and favorites
- Bulk camera operations
- TXT/CSV/M3U/M3U8/RTSP imports
- Combined TXT/M3U generation
- Camera location metadata
- Device intelligence
- Snapshots
- Telegram alerts
- Database backup and retention management
- User authentication and role-based permissions
- Audit/security controls
- Background workers and validation queues
- Graceful shutdown and recovery mechanisms
- Large-inventory processing and resumable validation

The project is intentionally designed as a **single Python application** so it can be deployed easily without maintaining many application source files.

---

# 2. Main Goals

The project is intended to provide one central dashboard for authorized CCTV infrastructure.

### Main goals

1. Import large camera inventories.
2. Identify cameras and NVR/DVR devices.
3. Detect and validate RTSP streams.
4. Open live feeds in the browser.
5. Organize cameras into categories.
6. Monitor online/offline state.
7. Run diagnostics when needed.
8. Collect useful device information.
9. Keep historical health and operational information.
10. Export camera/device data.
11. Protect credentials and administrative functions.
12. Continue processing large inventories without losing validation progress.
13. Run on common cloud/server platforms with minimal deployment changes.

---

# 3. Architecture

The application is primarily a single-file Flask application:

```text
camera_dashboard.py
│
├── Flask Web Application
├── HTML/CSS/JavaScript dashboard
├── SQLite persistence
├── Camera inventory management
├── RTSP/FFmpeg validation
├── Device intelligence
├── Background workers
├── Health/diagnostic services
├── Authentication/session management
├── Telegram integration
├── Import/export system
└── Backup/maintenance system
```

Typical runtime:

```text
Browser
   │
   ▼
Flask Web Server
   │
   ├── Dashboard/UI
   ├── REST APIs
   ├── Authentication
   ├── Live stream/session handling
   │
   ├── SQLite Database
   │
   ├── FFmpeg / FFprobe
   │
   ├── CCTV/NVR Devices
   │
   └── Telegram API (optional)
```

---

# 4. Supported Devices

## Hikvision

The project includes Hikvision-specific detection and API/ISAPI candidates for device intelligence.

Typical information that may be available depending on model/firmware:

- Device information
- Storage information
- Users
- Channels
- Recording information
- System status
- Health information
- Alerts/events

Not every Hikvision firmware exposes every endpoint.

## Dahua

Dahua-specific CGI/API candidates are supported for device intelligence.

Possible information includes:

- Device/system information
- Storage
- Users
- Channels
- Health information
- Events/alerts

Availability depends on device model and firmware.

## Uniview

Uniview-specific API candidates are included.

Supported information depends on the firmware/API implementation exposed by the device.

## Generic RTSP/IP Cameras

Generic cameras can be managed using:

- IP/hostname
- Port
- Username
- Password
- RTSP URL
- HTTP/HTTPS management URL where applicable

The dashboard does not require every camera to expose a vendor-specific API.

---

# 5. RTSP and FFmpeg

FFmpeg/FFprobe are the preferred media validation tools.

The application does **not** rely on OpenCV `VideoCapture` as the primary production RTSP validation method.

FFmpeg/FFprobe are used for:

- RTSP validation
- Stream probing
- Codec detection
- Resolution detection
- Stream metadata
- Candidate stream testing
- Stream quality observations
- Failure classification

Typical flow:

```text
Camera/NVR
   │
   ▼
RTSP candidate URL
   │
   ▼
FFprobe / FFmpeg
   │
   ├── Reachable
   ├── Authentication failure
   ├── Timeout
   ├── Invalid stream
   ├── Unsupported stream
   └── Valid stream
```

---

# 6. Live View

The dashboard supports multi-camera viewing.

Supported layouts include:

- 1 camera
- 4 cameras
- 8 cameras
- 16 cameras
- 25 cameras
- 36 cameras
- Custom rows/columns

Live-view features include:

- Camera selection
- Tile ordering
- Fullscreen tile
- Per-camera snapshot
- Per-camera stop
- Stop All
- Auto reconnect
- Live/offline/reconnecting state
- FPS/resolution/latency information where available
- Maximum-feed protection
- Live-feed counter
- WebSocket/live session support where configured
- Saved layouts

The system is designed to prevent uncontrolled opening of excessive streams.

---

# 7. Camera Inventory

A camera record can contain information such as:

- Camera ID
- Device ID
- Host/IP
- Management port
- RTSP port
- Username
- Password
- RTSP URL
- Vendor
- Device type
- Channel
- Model
- Firmware
- Status
- Category
- Favorite state
- Description
- Area
- Country
- Region
- City
- Latitude
- Longitude
- Location source/confidence
- Last validation information

---

# 8. Device Intelligence

Device Intelligence is intentionally **on-demand**.

The application does not continuously query every camera for expensive vendor information.

A user can request information such as:

### Storage

- Disk/storage information
- Volume status
- Capacity information when exposed by the device

### Users

- Device user information
- User/account metadata where exposed

Sensitive password/token fields are filtered from displayed results.

### Channels / Live

The system can inspect:

- Configured channels
- Channel information
- Local dashboard channel information
- Live subscriber/session information

Important:

> Dashboard subscriber counts are not automatically equivalent to the NVR's physical live-channel count.

### Playback

The system attempts to obtain recording information from supported vendor APIs.

Exact recording-retention days are **model/firmware dependent**. When the required recording-search information is not available, the dashboard reports the value as unknown rather than inventing a number.

### Performance

Possible device metrics include:

- CPU
- Memory
- Temperature
- Other firmware-exposed resource information

The dashboard may also show the health/resource state of the server running Camera Dashboard.

### Health

Vendor/device health information can be requested where supported.

### Alerts

The system can attempt to retrieve device alerts/events.

Vendor API support varies by model and firmware.

---

# 9. Diagnostics

Diagnostics are designed to be **on-demand** rather than continuously probing every device.

Possible checks include:

- TCP connectivity
- HTTP
- HTTPS
- RTSP
- Authentication
- Device fingerprint
- Device information
- Channel test
- Snapshot test
- Stream test
- Latency
- Failure classification
- Diagnostic report

This approach helps reduce unnecessary network traffic and avoids creating continuous load on CCTV devices.

---

# 10. Device Detection and Classification

The system can classify devices using available evidence.

Possible classifications include:

- Hikvision
- Dahua
- Uniview
- Generic
- IP camera
- NVR
- DVR
- Door camera
- Standalone camera

Detection can use:

- Vendor fingerprints
- HTTP/HTTPS responses
- RTSP evidence
- Device APIs
- Channel information
- Model/firmware evidence
- Service availability

A device should not be considered positively identified merely because a port is open.

---

# 11. Channel Discovery

Channel discovery can be used to identify channels associated with NVR/DVR devices.

The application can retain multiple channels from the same device.

It does not intentionally delete a camera merely because another feed from the same device exists.

Different feeds/links are preserved when they represent distinct inventory records.

---

# 12. Duplicate Handling

The project is designed to avoid destructive duplicate removal.

For inventory imports, the important distinction is:

```text
Same device + different feed/link
        ↓
Keep both records

Same device + same relevant feed/link
        ↓
Candidate duplicate
```

The system also supports device/channel correlation.

The exact duplicate rules should be reviewed before using automated cleanup on a production database.

---

# 13. Import System

Supported inventory/input formats include:

- TXT
- CSV
- M3U
- M3U8
- RTSP URLs

The project can handle common camera inventory representations such as:

```text
rtsp://user:password@192.168.1.10:554/stream
```

and structured TXT/CSV records containing fields such as:

```text
IP
Host
Port
Username
Password
RTSP
Vendor
Channel
Category
```

The parser is designed to tolerate multiple common field aliases.

---

# 14. Combined TXT and M3U

The application supports combining multiple inventory files.

### TXT

Multiple TXT files can be combined into one consolidated TXT inventory.

### M3U/M3U8

Multiple M3U/M3U8 files can be combined into one consolidated playlist.

The goal is to preserve distinct streams while removing true duplicates.

---

# 15. Recheck Imported Files

Imported inventories can be rechecked later.

This is useful when:

- New cameras are added to an input file.
- Existing cameras change.
- RTSP links change.
- A previous validation failed.
- The same source file is updated.

The recheck operation is designed to avoid unnecessary duplication.

---

# 16. Categories

Cameras can be organized into categories.

Examples:

```text
Bangladesh
India
Pakistan
Office
Warehouse
Branch-01
Branch-02
NVR-01
Door Cameras
Favorites
```

Category operations include:

- Create category
- Select all
- Move cameras
- Bulk transfer
- Delete category
- Category metadata
- Category export
- Saved filters

---

# 17. Favorites

Cameras can be marked as favorites for quick access.

Favorites can be used with:

- Dashboard filtering
- Live view selection
- Bulk operations
- Saved filters

---

# 18. Location / Geo Information

Camera records can contain:

- Country
- Region
- City
- Area
- Latitude
- Longitude
- Location source
- Confidence
- Location update time
- Description

## Automatic lookup

Location lookup is user-triggered.

For public IP addresses, approximate geolocation may be obtained from a geolocation service when available.

Private/internal IP addresses are not sent to a public geolocation service merely for lookup.

## Manual location

Administrators can manually enter:

- Country
- Region
- City
- Area
- Description
- Latitude
- Longitude

Manual location is treated as:

```text
source = operator-entered
confidence = operator-verified
```

---

# 19. Description and Area

Camera records support human-readable metadata.

Example:

```text
Area: Reception
Description: Main entrance camera facing front gate
```

This is useful when many cameras share the same NVR or physical location.

---

# 20. Health Monitoring

The project maintains health information for cameras/devices.

Possible states include:

```text
ONLINE
OFFLINE
CHECKING
RECONNECTING
UNKNOWN
```

Health information can include:

- Last check
- Response latency
- Failure reason
- Status change
- Health history
- Recovery history
- Stream observations

---

# 21. Alerts

Alert framework supports concepts such as:

- Offline alert
- Back-online alert
- RTSP failure alert
- Authentication alert
- Device change alert
- Channel change alert
- Cooldown
- Alert deduplication
- Alert history
- Daily report
- Worker/recovery/security alerts

Telegram integration can be used for:

- Startup notification
- Camera snapshot alerts
- Operational alerts

---

# 22. Telegram

Telegram is optional.

The application can send notifications when configured.

Typical configuration uses environment variables rather than hard-coding secrets.

Do not commit:

```text
BOT TOKEN
CHAT ID
PASSWORDS
API KEYS
```

to Git.

---

# 23. Authentication and Security

The dashboard supports role-based access.

Possible roles include:

### Admin

Full administrative access.

### Read-only Viewer

Can view permitted dashboard information without administrative changes.

### Operator

Can receive a controlled subset of operational permissions.

The project includes security concepts such as:

- Password masking
- Secret-safe exports
- Session hardening
- Audit trail
- Active session management
- Individual session revocation
- Logout All
- Lockout management
- Concurrent-session controls
- Password-age/force-change policies where configured
- Configuration/security health reporting

---

# 24. Session Security

The application uses session controls including an authentication epoch mechanism.

The epoch allows existing sessions to be invalidated after security/session changes.

Example concept:

```text
Current server auth epoch = 5

Session created with epoch = 5
        ↓
Valid

Server epoch changes to 6
        ↓
Old session epoch = 5
        ↓
Session becomes invalid
```

This helps support operations such as global logout/session invalidation.

---

# 25. Public-Range Scanning

**Public-range scanning is disabled.**

The application is intended for authorized CCTV infrastructure.

Diagnostics should be performed against:

- Known inventory hosts
- User-supplied/selected device hosts
- Authorized network infrastructure

The project is not intended to be a public Internet camera scanner.

---

# 26. Database

The application uses SQLite.

The database contains operational information such as:

- Cameras
- Devices
- Channels
- Categories
- Users
- Sessions
- Health history
- Diagnostics
- Events
- Audit logs
- Validation state
- Stream observations
- Location metadata
- Application/service state

SQLite WAL mode and retry/recovery mechanisms are used to improve concurrent access behavior.

---

# 27. Database Migration

Database schema changes are versioned.

Current documented application state:

```text
Application version: 161
Schema version: 140
```

The migration system is designed to:

- Preserve existing camera records
- Add missing columns/tables
- Avoid resetting existing data
- Maintain compatibility between application versions

Do not manually delete the SQLite database to solve a migration problem unless a complete backup has been made.

---

# 28. Database Backups

The application contains database backup/maintenance functionality.

Backup features include concepts such as:

- Verified backups
- Rotating backups
- Restore safety backup
- Backup locking/single-flight protection
- Retention maintenance
- Database integrity checks

Only one backup operation should be allowed to run at a time.

---

# 29. Database Contention Protection

Large CCTV inventories can create many concurrent database operations.

The project therefore uses:

- SQLite retry logic
- Write retry gates
- WAL
- Bounded retries
- Background maintenance
- Single-flight operations
- Backup locking
- Shutdown flushing
- Queue backpressure

The purpose is to reduce errors such as:

```text
database is locked
audit log write failed
backup already running
retention write failure
```

---

# 30. Background Services

Background processing can include:

- Camera scanning
- RTSP validation
- Health checks
- Maintenance
- Retention cleanup
- Backup
- Alert processing
- Worker supervision

Persistent service preferences can be stored so that disabled services do not unexpectedly restart after application restart.

---

# 31. Adaptive Workers

The application can adapt worker/queue settings based on server resources.

Relevant factors include:

- CPU count
- Available RAM
- Current load
- Validation batch size
- Queue size

The goal is to process large inventories efficiently without blindly creating unlimited threads.

---

# 32. Validation Resume

Large validation jobs are designed to support resume behavior.

If the application restarts during validation, previously processed inventory should not require unnecessary reprocessing when persistent validation state is available.

This is especially important for inventories containing thousands of streams.

---

# 33. Graceful Shutdown

The application includes a graceful shutdown design.

Shutdown should:

1. Stop accepting new work.
2. Signal background workers to stop.
3. Prevent new scanner/backup jobs.
4. Allow short-running database operations to drain.
5. Flush important state.
6. Close resources.
7. Mark shutdown state where applicable.
8. Exit cleanly.

The goal is to make the application's Stop/Shutdown behavior more reliable than abruptly terminating the Python process.

---

# 34. Scanner Single-Flight Protection

The application protects the scanner against overlapping runs.

Instead of allowing:

```text
Scanner A
Scanner B
Scanner C
Scanner D
```

to run simultaneously, the system should reject or coalesce duplicate scanner requests.

This prevents errors such as:

```text
Camera scan is already running
```

from turning into uncontrolled concurrent work.

---

# 35. Backup Single-Flight Protection

The same principle applies to database backup operations.

If a backup is already active, another request should not start a second full backup simultaneously.

This prevents:

```text
SQLite backup skipped: another backup is already running
```

and reduces database contention.

---

# 36. Performance Features

The project includes performance-oriented features such as:

- Adaptive worker count
- Adaptive queue size
- Per-operation timeouts
- Connection reuse where applicable
- Result caching where applicable
- Background processing
- Non-blocking UI
- Queue backpressure
- Resource-aware validation
- Worker supervision
- Restart cooldown
- Anti-restart-storm protection
- Graceful shutdown

---

# 37. Export System

Supported export concepts include:

- CSV
- JSON
- M3U
- M3U8
- Health reports
- Device reports
- Diagnostic reports
- Category exports
- Selected-camera exports
- Camera list exports

Sensitive credentials should be redacted from safe exports.

---

# 38. Reports

The project can generate or expose operational information such as:

- Camera status
- Device inventory
- Health
- Diagnostics
- Stream quality
- Categories
- Device metadata
- Validation results
- Security/audit information

---

# 39. Stream Quality

The project can collect stream observations such as:

- Resolution
- Codec
- FPS
- Latency
- Connectivity
- Validation result
- Failure classification

A stream-quality score can be used for operational analysis.

---

# 40. Smart Inventory Features

The project contains logic for:

- Automatic device classification
- Duplicate-feed detection
- Same-device/multi-port correlation
- Channel-number pattern detection
- Best RTSP candidate selection
- Main/sub-stream selection
- Change detection
- Evidence history
- Confidence scoring
- Validation priority
- Favorite/new/unknown prioritization
- Recovery history
- Operational metrics

---

# 41. Evidence and Confidence

Device detection should be treated as evidence-based.

Example:

```text
RTSP open
+
Hikvision HTTP fingerprint
+
ISAPI response
+
Model information
=
High-confidence Hikvision identification
```

A single open port should not automatically be treated as proof of a specific vendor.

---

# 42. Door Camera Support

The project includes support for identifying/testing door-camera style feeds where applicable.

Potential checks include:

- Management port
- HTTP port 80
- Authentication
- Stream availability
- Device fingerprint
- RTSP candidate

Actual capabilities depend on the door-camera hardware.

---

# 43. Web Dashboard

The dashboard is browser based.

Typical workflow:

```text
Login
  ↓
Dashboard
  ↓
Categories / Cameras
  ↓
Select camera
  ↓
Watch / Diagnose / Device Intel / Edit
  ↓
Live stream or information panel
```

The interface includes operational controls such as:

- Watch
- Stop
- Stop All
- Fullscreen
- Snapshot
- Device Intel
- Diagnostics
- Location
- Category actions
- Bulk actions

---

# 44. API

The Flask application exposes internal REST endpoints for dashboard operations.

Examples of endpoint groups include:

```text
/api/session-*
/api/category/*
/api/cameras/*
/api/device/*
/api/v42/*
/api/v43/*
```

The exact endpoint set is implementation-dependent and can change between application versions.

A feature-information endpoint is available for exposing the current feature matrix.

---

# 45. Feature Priority Matrix

The project has evolved through a priority-based feature architecture.

## P1 — Live View

- 1/4/8/16/25/36 layouts
- Custom layouts
- Drag/drop ordering
- Fullscreen
- Snapshot
- Stop
- Auto reconnect
- Live status
- FPS/resolution/latency
- Feed protection
- Saved layouts
- WebSocket/live sessions

## P2 — Device Intelligence

- Vendor detection
- Generic detection
- Device classification
- Channel enumeration
- Device fingerprint
- Evidence history
- Confidence score
- Duplicate detection
- Device timeline

## P3 — Stream

- RTSP validation
- Candidate paths
- Main/sub stream candidates
- HTTP/HTTPS media detection
- M3U/M3U8
- Stream observations
- Quality score
- Latency tracking
- Reconnect

## P4 — Health

- Online/offline
- Health history
- Diagnostics
- Device change history
- Response latency
- Quality analytics
- Status alerts
- Health export

## P5 — Database

- SQLite WAL
- Migrations
- Integrity checks
- Verified backups
- Restore safety
- Trash/restore
- Audit log
- Retention
- Shutdown flush

## P6 — Organization

- Categories
- Category metadata
- Favorites
- Bulk select
- Bulk transfer
- Bulk delete
- Tags
- Saved filters
- Saved layouts

## P7 — Export

- CSV
- JSON
- M3U
- Health JSON
- Device reports
- Diagnostic results

## P8 — Automation

- Adaptive workers
- Validation resume
- Scheduled jobs
- Maintenance
- Health checks
- Telegram alert framework

## P9 — Security

- Password masking
- Secret-safe exports
- Read-only diagnostics
- Bounded diagnostics
- Audit trail

The project also contains broader diagnostic, alerting, performance, import/export and smart-feature layers developed through later versions.

---

# 46. Configuration

Configuration should preferably be supplied using environment variables.

Common security-related variables include:

```text
CAMERA_AUTH_ENABLED
CAMERA_ADMIN_USER
CAMERA_ADMIN_PASSWORD
CAMERA_READONLY_USER
CAMERA_READONLY_PASSWORD
CAMERA_SESSION_TIMEOUT_SECONDS
CAMERA_SECURE_COOKIE
CAMERA_LOGIN_MAX_FAILURES
CAMERA_LOGIN_WINDOW_SECONDS
CAMERA_LOGIN_LOCKOUT_SECONDS
```

Telegram configuration should also be provided through environment variables when enabled.

**Never commit real passwords, tokens, API keys or camera credentials to Git.**

---

# 47. Running Locally

Typical command:

```bash
python camera_dashboard.py --port 5001
```

For a larger validation batch:

```bash
python camera_dashboard.py --validation-batch 400 --port 5001
```

The exact CLI options can vary with the application version.

---

# 48. Replit

The project has been developed and tested operationally in a Replit-style environment.

A typical command is:

```bash
python camera_dashboard.py --port 5001
```

For Replit deployment:

- Keep the main source filename as `camera_dashboard.py`.
- Configure the exposed port according to the Replit environment.
- Store secrets in Replit Secrets.
- Do not commit camera credentials.
- Use persistent storage for the SQLite database if the deployment requires persistence.
- Avoid launching multiple independent copies of the application against the same SQLite database unless the deployment architecture is intentionally designed for it.

---

# 49. Deployment Strategy

The project is intended to be deployable to multiple platforms.

Deployment configuration should permanently reference:

```text
camera_dashboard.py
```

The Python application itself is versioned during development:

```text
camera_dashboard_v160.py
camera_dashboard_v161.py
camera_dashboard_v162.py
...
```

After receiving a new version, the deployment file can continue to use:

```text
python camera_dashboard.py
```

The user can manually rename the selected versioned source file to:

```text
camera_dashboard.py
```

This avoids changing Dockerfiles/YAML/TOML/Procfiles for every code update.

---

# 50. Deployment Platforms

The architecture can be adapted for platforms such as:

- Replit
- Render
- Railway-style services
- Fly.io
- VPS
- Docker
- Linux server

The exact deployment configuration should be kept separate from the application logic.

---

# 51. Docker

A typical container workflow is:

```text
Docker image
   │
   ├── Python
   ├── Flask dependencies
   ├── FFmpeg
   ├── FFprobe
   └── camera_dashboard.py
```

FFmpeg and FFprobe must be installed inside the runtime environment if RTSP validation is required.

---

# 52. Important Deployment Requirement: FFmpeg

Before production deployment, verify:

```bash
ffmpeg -version
ffprobe -version
```

If either command is missing, RTSP validation and stream diagnostics may fail.

---

# 53. Python Dependencies

The application requires Python packages according to the version of the source file.

Core runtime dependencies include the Flask web framework and HTTP/networking libraries.

Depending on the enabled features, packages can include:

```text
Flask
flask-sock
requests
```

The production environment should install the project's pinned `requirements.txt` when supplied with the application package.

---

# 54. Security Recommendations

For production:

### Use HTTPS

Do not expose an administrative dashboard over plain HTTP on an untrusted network.

### Strong passwords

Use strong unique passwords for:

- Admin
- Operator
- Viewer
- Camera/NVR accounts

### Secrets

Use environment variables or the platform's secret manager.

### Network restriction

Prefer:

- VPN
- Private network
- Firewall
- Reverse proxy access control

### Backups

Back up:

```text
camera_db.sqlite
configuration
deployment configuration
```

Do not back up credentials into public repositories.

---

# 55. Privacy and Data Protection

Camera inventory may contain sensitive information:

- IP addresses
- Hostnames
- Usernames
- Passwords
- Camera locations
- Internal network information
- Device models
- RTSP URLs

Therefore:

- Keep the repository private when appropriate.
- Redact credentials from logs.
- Do not upload production databases to public Git repositories.
- Do not publish RTSP URLs containing credentials.
- Protect exported reports.
- Use secure transport for remote administration.

---

# 56. Authorized Use

This project should only be used for cameras, NVRs and networks that the operator is authorized to administer or test.

The application is intentionally designed around known inventory and bounded diagnostics rather than public Internet scanning.

---

# 57. Versioning Policy

Application versions are sequential.

Example:

```text
V158
V159
V160
V161
V162
...
```

A modified application should always receive a new version number.

Do not reuse an old version number for a different code revision.

### Application version

Changes whenever application code changes.

Example:

```python
VERSION = "161.0"
```

### Schema version

Changes only when an actual database schema migration is introduced.

Current documented schema:

```text
SCHEMA_VERSION = 140
```

---

# 58. Development Workflow

Recommended workflow:

```text
1. Keep current production file safe
2. Make code change
3. Increment application version
4. Keep schema version unchanged unless DB schema changes
5. Run syntax compilation
6. Run targeted tests
7. Review database migration impact
8. Test startup
9. Test dashboard
10. Test live stream
11. Test authentication
12. Test shutdown
13. Deploy
```

---

# 59. Validation / Testing

At minimum, test:

### Python

```bash
python -m py_compile camera_dashboard.py
```

### FFmpeg

```bash
ffmpeg -version
ffprobe -version
```

### Application startup

```bash
python camera_dashboard.py --port 5001
```

### Browser

Open the configured application URL.

### Authentication

Test:

- Admin login
- Viewer login
- Operator login
- Logout
- Password change
- Session invalidation

### Camera

Test:

- Add/import
- RTSP validation
- Watch
- Stop
- Snapshot
- Device Intel
- Location
- Category transfer

### Database

Test:

- Restart
- Backup
- Restore procedure
- Migration
- Integrity check

### Shutdown

Test:

- UI Stop App
- Ctrl+C
- Restart
- Background worker termination
- Database recovery

---

# 60. Current Known Limitations

Some vendor-specific device intelligence is inherently firmware/model dependent.

### Vendor APIs

A Hikvision, Dahua or Uniview API endpoint may be unavailable on a particular firmware.

The application should report unsupported/unknown rather than inventing data.

### Playback retention

Exact recording-retention days cannot always be calculated from generic device APIs.

When the exact oldest recording timestamp cannot be retrieved, the application reports the retention value as unknown.

### Remote performance

Remote CPU/RAM/temperature information is only available when the device exposes those metrics.

The server's own resource information is separate from the CCTV device's resource information.

### Browser playback

Some browsers do not natively play every RTSP stream format.

Where direct browser playback is not possible, a compatible relay/transcoding architecture may be required.

---

# 61. Troubleshooting

## App starts slowly

Check:

- SQLite database size
- Database locks
- Background maintenance
- Validation workload
- Backup activity
- Number of cameras
- Worker count
- Repeated frontend polling

A very long startup DB check is a performance warning and should be investigated rather than ignored.

## `database is locked`

Check:

- Multiple application processes
- Overlapping background jobs
- Backup operations
- Large concurrent writes
- Long-running transactions

Do not simply increase SQLite timeout indefinitely.

## `Camera scan is already running`

This normally indicates an overlapping scan request.

Use the scanner single-flight protection and inspect whether multiple frontend/background triggers are firing.

## `SQLite backup skipped: another backup is already running`

This indicates a backup operation was already active.

Do not start additional backups simultaneously.

## RTSP failure

Check:

```text
IP/hostname
Port
Username
Password
RTSP path
Network access
Firewall
FFmpeg/FFprobe
Camera stream profile
```

## Authentication failure

Verify:

- Device username
- Device password
- Camera account permissions
- HTTP/HTTPS port
- Device lockout state

---

# 62. Recommended Production Topology

For a serious deployment:

```text
                    Internet / LAN
                          │
                          ▼
                    Reverse Proxy
                  HTTPS / Authentication
                          │
                          ▼
                 Camera Dashboard
                    Flask/Python
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
       SQLite          FFmpeg          Telegram
          │             FFprobe           │
          │               │               │
          ▼               ▼               ▼
      Inventory       CCTV/NVR        Alerts
```

For larger installations, keep the dashboard server close to the camera network to reduce RTSP latency and firewall complexity.

---

# 63. Recommended Backup Policy

For production:

```text
Daily:
    Database backup

Weekly:
    Verified restore test

Before upgrade:
    Full database backup

Before schema migration:
    Full database backup

Before bulk deletion:
    Full database backup
```

A backup is only useful if restoration has been tested.

---

# 64. Git Repository Recommendations

Suggested repository structure:

```text
camera-dashboard/
│
├── camera_dashboard.py
├── requirements.txt
├── README.md
├── Dockerfile
├── render.yaml
├── fly.toml
├── railway configuration
├── .gitignore
└── deployment documentation
```

Do not commit:

```text
camera_db.sqlite
*.sqlite
*.db
.env
camera credentials
Telegram tokens
private RTSP playlists
production exports
snapshots/
backups/
```

unless there is a specific secure reason.

---

# 65. Suggested `.gitignore`

```gitignore
__pycache__/
*.pyc

.env
.env.*
!.env.example

*.sqlite
*.sqlite3
*.db

backups/
snapshots/
logs/

*.m3u
*.m3u8

camera_dashboard_v*.py

.DS_Store
Thumbs.db
```

Keep the actual production `camera_dashboard.py` in the repository if it is intended to be the primary source file.

---

# 66. Operational Principles

The project follows these important principles:

### Do not invent device information

If a device does not expose information, report it as unknown.

### Do not destroy distinct feeds

Different valid streams should remain separate.

### Do not continuously probe everything

Expensive diagnostics and Device Intel operations should be on-demand.

### Do not expose credentials

Passwords/tokens should be filtered from exports and UI responses.

### Do not use public-range scanning

Operate against authorized inventory.

### Do not allow uncontrolled background work

Use queues, limits, cancellation and single-flight protection.

### Do not hide database problems

SQLite contention should be diagnosed and fixed instead of masked with unlimited retries.

---

# 67. Project Status

The project is a large, continuously evolving CCTV management platform.

The architecture has progressed through many versioned releases, with later releases focusing heavily on:

- SQLite reliability
- Startup performance
- Background service controls
- Authentication/session security
- Inventory import compatibility
- RTSP-only inventory
- Device intelligence
- Location metadata
- Graceful shutdown
- Scanner/backup single-flight protection
- Large inventory processing

Current development version documented here:

```text
Camera Dashboard
Schema V140
```

---

# 68. License

No specific open-source license is defined in this README.

If this repository will be published publicly, choose an appropriate license before distribution, for example:

- MIT
- Apache-2.0
- GPL-3.0
- Proprietary / All Rights Reserved

Do not assume an open-source license without explicitly adding a `LICENSE` file.

---

# 69. Final Notes

This application is intended to be a **professional, maintainable CCTV inventory and management platform**, not merely an RTSP URL viewer.

The core design priorities are:

```text
Reliability
Security
Maintainability
Large-inventory support
Accurate device identification
Safe database handling
Controlled background processing
Operational visibility
Easy deployment
```

For production use, always test against the actual camera/NVR models and firmware in the target environment because vendor APIs, RTSP behavior, authentication methods and available telemetry vary significantly between devices.

---

## Quick Start

```bash
# Install dependencies
pip install -r requirements.txt

# Verify FFmpeg
ffmpeg -version
ffprobe -version

# Start application
python camera_dashboard.py --port 5001
```

Then open the configured web address in a browser and log in with the configured administrative account.

---

**Camera Dashboard — centralized CCTV/NVR inventory, RTSP validation, live monitoring, diagnostics, organization, security and operational management.**
