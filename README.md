# EventHub - Event Management System

## 📋 Project Overview

EventHub is a comprehensive web-based Event Management System built with PHP and MySQL. It allows event hosts to create, manage, and track events with features like guest management, photo galleries, vendor tracking, and real-time notifications. The application is deployed on InfinityFree hosting at **https://hostevent.ct.ws**.

---

## 🎯 Software Engineering Concepts Used

### 1. **Architecture & Design Patterns**

#### Model-View-Controller (MVC) Pattern
EventHub follows the MVC architecture:
- **Model**: Database layer (`config/database.php`) - Manages data interactions with MySQL
- **View**: Frontend templates (`pages/*.php`, `e.php`) - HTML/CSS rendering with futuristic dark theme
- **Controller**: Business logic (`includes/functions.php`, individual page handlers) - Processes user requests

```
Request → Router (router.php) → Page Handler (Controller) → Database (Model) → HTML Output (View)
```

#### Single Responsibility Principle (SRP)
- **`config/config.php`**: Configuration management only
- **`config/database.php`**: Database connection only
- **`includes/functions.php`**: Reusable business logic
- **`includes/header.php`**: Common UI components
- Each page handles one primary concern (events, guests, gallery, etc.)

#### DRY (Don't Repeat Yourself)
- Common functions extracted to `includes/functions.php`
- Reusable components (header, footer, sidebar)
- Shared CSS themes (`assets/css/themes.css`)
- Consistent sanitization and validation across all pages

### 2. **Security Implementation**

#### Authentication & Authorization
- **Password Security**: Uses PHP's `password_hash()` and `password_verify()` for secure password storage
- **Session Management**: PHP native sessions (`session_start()`) to track logged-in users
- **Access Control**: Verification that users only access their own events
  ```php
  if (!$event || $event['host_id'] !== $currentUser['id']) {
      redirect('/pages/events.php');
  }
  ```

#### Input Sanitization
- Custom `sanitize()` function removes malicious characters
- Prevents SQL Injection via prepared statements with parameter binding
- All user inputs validated before database operations
  ```php
  $title = sanitize($_POST['title']);
  $stmt = $pdo->prepare("UPDATE events SET title = ? WHERE id = ?");
  $stmt->execute([$title, $eventId]);
  ```

### 3. **Database Design**

#### Normalized Schema
- **Events Table**: Core event data
- **Guests Table**: Guest list linked to events (Foreign Key: event_id)
- **Gallery Photos**: Media files linked to events
- **Notifications**: Event-driven updates for users
- Proper relationships to avoid data redundancy

#### ACID Compliance
- MySQL transactions ensure data integrity
- Prepared statements prevent injection attacks
- Referential integrity through foreign keys

### 4. **Frontend Architecture**

#### Component-Based Design
- Reusable components (buttons, cards, forms) in CSS
- JavaScript utility functions for common tasks
- Consistent styling across all pages

#### Responsive Design
- Mobile-first CSS approach
- Hamburger menu for navigation (50px button on mobile)
- Media queries (`@media (max-width: 768px)`)
- Flexbox/Grid layouts for adaptability

#### State Management
- User session stored server-side
- Event theme preference persisted in database
- Notification state managed through database records
- Tab switching handled via JavaScript with data attributes

### 5. **Code Quality & Maintainability**

#### Organized Directory Structure
```
php-app/
├── config/          # Configuration & database setup
├── includes/        # Reusable functions & components
├── pages/           # Page controllers/handlers
├── api/             # API endpoints for AJAX
├── assets/          # CSS, JavaScript, static files
└── uploads/         # User-generated content
```

#### Error Handling
- Redirects for invalid access
- Alert messages for user feedback
- Server-side validation on all forms
- Graceful handling of missing resources

#### Code Documentation
- Inline comments explaining complex logic
- Consistent naming conventions (`$eventId`, `$guest`, etc.)
- Function names reflect their purpose (`getEventById()`, `sanitize()`, etc.)

---

## 🌐 Computer Networks Concepts Used

### 1. **HTTP/HTTPS Protocol**

#### Request-Response Model
EventHub operates on HTTP request-response cycles:
- User submits form → Browser sends POST request
- Server processes → Database transaction
- Server responds with HTML or redirects
- Browser renders response

#### HTTP Methods
- **GET**: Retrieve data (event details, guest lists, photos)
- **POST**: Submit forms (create/edit events, upload photos)
- **Redirects (302)**: After successful operations for PRG pattern

#### URL Routing
- Custom router (`router.php`) maps URLs to pages
- RESTful-style URLs: `/pages/event-details.php?event_id={id}`
- Public event pages: `/e/{event-slug}` for external sharing

### 2. **DNS & Domain Management**

#### Domain Configuration
- Primary domain: `hostevent.ct.ws` (InfinityFree hosted)
- DNS resolution maps domain to InfinityFree servers
- SSL/HTTPS certificate (free via InfinityFree)

#### Client-Server Communication
```
User Browser → DNS Resolution → InfinityFree Server (192.x.x.x) → PHP Application → MySQL Database
                                        ↓
                            Response (HTML/JSON) → Browser Renders
```

### 3. **Session & Cookie Management**

#### Session Handling
- PHP sessions store user authentication state
- Session ID stored in cookies on client browser
- Server-side session data in memory/file system
- Prevents unauthorized access to protected pages

```php
session_start();
if (!isset($_SESSION['user_id'])) {
    redirect('/pages/login.php');
}
```

### 4. **API Architecture**

#### RESTful API Endpoints (AJAX)
- `/api/get-notifications.php`: Fetch notifications asynchronously
- `/api/announcements.php`: Get event announcements
- `/api/generate-qr.php`: Generate QR codes for guests

#### Data Format
- JSON responses for AJAX requests
- Enables real-time updates without page refresh
- Reduces bandwidth compared to full HTML responses

### 5. **Data Transmission & Compression**

#### HTTP Headers
- Content-Type: Specifies response format (HTML, JSON)
- Cache-Control: Prevents browser caching issues
- CORS headers (if needed): Allow cross-origin requests

#### File Upload Handling
- Multipart/form-data encoding for photo uploads
- Server validates file type and size (5MB limit, image files only)
- Secure file storage outside webroot (partially)

### 6. **Networking Layers (OSI Model)**

#### Application Layer (Layer 7)
- HTTP/HTTPS protocols
- PHP application logic
- Database queries

#### Transport Layer (Layer 4)
- TCP protocol for reliable delivery
- Port 80 (HTTP) / 443 (HTTPS)
- Port 5000 for local development server

#### Internet Layer (Layer 3)
- IP addressing (IPv4 for routing)
- DNS for domain-to-IP resolution

#### Data Link Layer (Layer 2-1)
- Ethernet/WiFi for local connections
- MAC addresses for devices on network

### 7. **Latency & Performance**

#### Frontend Optimization
- CSS minification for faster loading
- Font Awesome CDN for icon delivery
- Chart.js library from CDN reduces server load
- Lazy loading for images

#### Backend Optimization
- Database indexing on frequently queried columns (event_id, user_id)
- Prepared statements reduce query parsing time
- Efficient SQL queries with proper JOINs

#### Geographic Distribution
- Hosted on InfinityFree (geographically distributed servers)
- CDN usage (Font Awesome, Chart.js) reduces latency
- Assets cached by browser to reduce repeated downloads

---

## 🏗️ How It All Comes Together

### User Registration & Login Flow
```
1. User visits /pages/register.php
2. Submits credentials via POST (HTTPS encrypted)
3. Server sanitizes input → validates → hashes password
4. Data stored in MySQL (users table)
5. PHP sets session cookie in browser
6. Redirect to dashboard
```

### Event Creation Flow
```
1. Host navigates to create event form
2. Fills details (title, date, theme, guests)
3. POST request with multipart data (including photos)
4. Server validates each field
5. Creates event record in database
6. Stores photo files in /uploads/ folder
7. Inserts gallery_photos records linking to event
8. Returns success message to user
9. Browser renders updated event list
```

### Public Event Page Access Flow
```
1. Guest receives /e/{event-slug} URL (via WhatsApp/Email)
2. Browser requests page via HTTP
3. Server queries database for event details
4. Retrieves associated photos, announcements, functions
5. Renders public-facing HTML (no login required for published events)
6. Guest can search for their name, leave wishes, ask questions
7. AJAX calls send data to backend for real-time updates
```

### Notification System (Real-time Updates)
```
1. Event host approves guest check-in
2. Database notification record created
3. Guest refreshes page or API polls /api/get-notifications.php
4. JavaScript receives JSON response
5. Displays notification in dropdown (fixed position to prevent overflow)
6. Text properly wrapped with word-break CSS rules
```

### Mobile Responsiveness
```
CSS Media Query: @media (max-width: 768px)
├── Hamburger menu replaces horizontal navigation
├── Notification dropdown width: calc(100vw - 1.5rem)
├── Fixed positioning prevents viewport overflow
├── Proper text wrapping with overflow-wrap: break-word
└── Touch-friendly button sizes (50px minimum)
```

---

## 🛠️ Technology Stack Explanation

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | HTML5, CSS3, JavaScript | User interface & interactivity |
| **Backend** | PHP 8.3 | Server-side logic & business rules |
| **Database** | MySQL | Persistent data storage |
| **Hosting** | InfinityFree | Server infrastructure & deployment |
| **Authentication** | PHP Sessions, password_hash() | User security |
| **Icons** | Font Awesome CDN | UI elements |
| **Charts** | Chart.js | Data visualization |

---

## 📊 Software Engineering Best Practices Applied

### Version Control
- Clean commit history (implied best practice)
- Organized file structure for maintainability

### Testing Strategy
- Manual testing on multiple devices (desktop, mobile)
- Browser cache clearing to verify updates
- QA testing before production deployment

### Documentation
- Inline code comments for complex logic
- Clear function names describing purpose
- `replit.md` tracks project status and features

### Deployment Process
- Local development with `php -S 0.0.0.0:5000`
- Upload to production (InfinityFree) via File Manager
- Cache invalidation strategy (hard refresh for users)

---

## 🔐 Key Security Measures

1. **Password Security**: `password_hash()` with bcrypt algorithm
2. **SQL Injection Prevention**: Prepared statements with parameter binding
3. **XSS Prevention**: `sanitize()` function removes HTML/special characters
4. **Session Security**: Server-side session storage
5. **Access Control**: Role-based access (only hosts can edit their events)
6. **File Upload Security**: Type and size validation

---

## 🚀 Scalability Considerations

### Current Implementation
- Single-server architecture (InfinityFree)
- MySQL database on same server
- Session data in memory

### Future Improvements for Scale
1. **Caching Layer**: Redis for session & query caching
2. **Load Balancing**: Distribute traffic across servers
3. **Database Replication**: Master-slave MySQL setup
4. **CDN**: Global content delivery network for static assets
5. **Microservices**: Separate services for notifications, analytics
6. **Database Indexing**: Optimize queries for large datasets

---

## 📚 Learning Outcomes

### Software Engineering
- ✅ MVC architecture design
- ✅ SOLID principles (SRP demonstrated)
- ✅ Security best practices
- ✅ Database design & normalization
- ✅ Responsive design patterns
- ✅ Code organization & maintainability

### Computer Networks
- ✅ HTTP/HTTPS protocol mechanics
- ✅ DNS resolution & domain management
- ✅ Session & cookie management
- ✅ API design (RESTful endpoints)
- ✅ Data transmission & compression
- ✅ OSI model application
- ✅ Performance optimization & latency reduction

---

## 🎓 Educational Value

**EventHub serves as an excellent case study for:**
- Building production-ready web applications
- Implementing security in web apps
- Understanding full-stack web development
- Practical application of networking concepts
- Real-world deployment & hosting considerations

---

## 📞 Production Deployment

**Live at:** https://hostevent.ct.ws

- Hosts can create and manage events
- Guests can RSVP and check-in
- Real-time notifications
- Mobile-responsive interface
- Secure authentication

---

**Built with core principles of Software Engineering and Computer Networks** 🚀
