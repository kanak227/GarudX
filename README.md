# GarudX - Comprehensive Healthcare Management System 🏥

A modern, full-stack healthcare management platform built with React 18, TypeScript, and Firebase. GarudX connects doctors, pharmacies, pathology labs, and patients in a unified digital healthcare ecosystem.

## ✨ Key Features

### 🩺 Doctor Portal
- **Patient Management**: Complete EHR system with patient records, medical history
- **Video Consultations**: WebRTC-powered video calls with real-time chat
- **Prescription System**: Digital prescription creation and management
- **Queue Management**: Real-time patient queue with appointment scheduling
- **Analytics Dashboard**: Comprehensive statistics and insights
- **EHR Integration**: Electronic Health Records with form management

### 💊 Pharmacy Portal
- **Inventory Management**: Complete medicine stock tracking and management
- **CSV Import/Export**: Bulk medicine data management with demo datasets
- **Order Processing**: Prescription fulfillment and order tracking
- **Analytics Dashboard**: Sales insights and low stock alerts
- **Integration**: Seamless connection with doctor prescriptions
- **Authentication**: Role-based access with Firebase Auth

### 🔬 Pathology Portal
- **Lab Management**: Laboratory registration and accreditation system
- **Test Catalog**: Comprehensive diagnostic test management
- **Report System**: Digital report generation and delivery
- **Integration**: Connected workflow with doctor referrals
- **Dashboard**: Lab-specific analytics and insights

### � Patient Experience
- **Mock Patient System**: Simulated patient interactions for testing
- **App Download Portal**: Patient mobile app distribution
- **WebRTC Integration**: Browser-based video consultations
- **Real-time Communication**: Integrated chat during consultations

## 🛠️ Technology Stack

- **Frontend**: React 19, TypeScript, Tailwind CSS
- **State Management**: React Hooks, Context API
- **Backend**: Firebase (Firestore, Auth, Storage, Hosting)
- **Real-time Communication**: WebRTC, Socket.io, Simple-peer
- **Charts & Analytics**: Chart.js, React-chartjs-2
- **Build Tool**: Vite
- **Routing**: React Router DOM
- **Styling**: Tailwind CSS with custom themes
- **Icons**: Lucide React
- **Development**: ESLint, TypeScript, Concurrently

## 🚀 Quick Start

### Prerequisites
- **Node.js** 16+ and npm
- **Firebase project** with Firestore, Auth, and Storage enabled
- **Modern browser** with WebRTC support for video calls

### Installation & Setup

1. **Clone and navigate to project:**
   ```bash
   git clone [repository-url]
   cd doctor-portal
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Firebase Configuration:**
   The project is pre-configured with Firebase. Update `src/config/firebase.ts` for your project:
   ```typescript
   const firebaseConfig = {
     apiKey: "your-api-key",
     authDomain: "your-project.firebaseapp.com",
     projectId: "your-project-id",
     storageBucket: "your-project.appspot.com",
     messagingSenderId: "your-sender-id",
     appId: "your-app-id"
   };
   ```

4. **Deploy Firebase Security Rules:**
   ```bash
   firebase deploy --only firestore:rules
   firebase deploy --only storage
   ```

5. **Start the application:**
   
   **Option A: Full stack with WebRTC**
   ```bash
   npm run dev:full  # Starts both signaling server and React app
   ```
   
   **Option B: Development only**
   ```bash
   npm run dev  # React app only (port 5173)
   ```
   
   **Option C: Signaling server separately**
   ```bash
   npm run signaling-server  # WebRTC signaling (port 3001)
   npm run dev               # React app (port 5173)
   ```

6. **Access the application:**
   - Main app: `http://localhost:5173`
   - Signaling server: `http://localhost:3001` (for WebRTC)

## 🏗️ Project Architecture

### Application Structure
```
doctor-portal/
├── public/
│   ├── demo-medicines.csv      # Sample pharmacy inventory
│   └── demo-tests.csv          # Sample pathology tests
├── src/
│   ├── components/             # Reusable UI components
│   │   ├── DoctorDashboard.tsx # Main doctor interface (2,476 lines)
│   │   ├── PatientManager.tsx  # Patient management system
│   │   ├── VideoCall.tsx       # WebRTC video calling
│   │   ├── pharmacy/           # Pharmacy-specific components
│   │   └── pathology/          # Pathology-specific components
│   ├── pages/                  # Route-based page components
│   │   ├── LoginEnhanced.tsx   # Enhanced login system
│   │   ├── SignupEnhanced.tsx  # Multi-role signup
│   │   ├── pharmacy/           # Pharmacy pages
│   │   └── pathology/          # Pathology pages
│   ├── services/               # Business logic and API calls
│   │   ├── authService.ts      # Authentication management
│   │   ├── webrtc.ts          # WebRTC implementation
│   │   ├── chatService.ts     # Real-time messaging
│   │   └── ehrService.ts      # Electronic Health Records
│   ├── config/
│   │   └── firebase.ts        # Firebase configuration
│   └── types/                 # TypeScript type definitions
├── signaling-server.cjs       # WebRTC signaling server
└── firebase configuration files
```

### 📊 Firebase Data Schema

#### Core Collections

**`users` Collection**
```typescript
{
  uid: string;
  email: string;
  role: 'doctor' | 'pharmacy' | 'pathology' | 'patient';
  firstName: string;
  lastName: string;
  createdAt: Timestamp;
  emailVerified: boolean;
}
```

**`doctors` Collection**
```typescript
{
  userId: string;              // Reference to users collection
  specialization: string;
  licenseNumber: string;
  experience: number;
  consultationFee: number;
  availability: object;
  verified: boolean;
}
```

**`pharmacies` Collection**
```typescript
{
  userId: string;
  pharmacyName: string;
  licenseNumber: string;
  address: object;
  operatingHours: object;
  verified: boolean;
  medicines: object[];         // Inventory management
}
```

**`patients` Collection**
```typescript
{
  firstName: string;
  lastName: string;
  dateOfBirth: string;
  gender: string;
  phone: string;
  email: string;
  medicalHistory: object[];
  allergies: string[];
  emergencyContact: object;
}
```

**`appointments` Collection**
```typescript
{
  doctorId: string;
  patientId: string;
  scheduledFor: Timestamp;
  status: 'scheduled' | 'in-progress' | 'completed' | 'cancelled';
  type: 'consultation' | 'follow-up' | 'emergency';
  callSessionId?: string;      // WebRTC session reference
  notes?: string;
}
```

**`prescriptions` Collection**
```typescript
{
  appointmentId: string;
  doctorId: string;
  patientId: string;
  medicines: Array<{
    name: string;
    dosage: string;
    frequency: string;
    duration: string;
    instructions: string;
  }>;
  createdAt: Timestamp;
  status: 'active' | 'completed' | 'cancelled';
}
```

**`callSessions` Collection**
```typescript
{
  appointmentId: string;
  doctorId: string;
  patientId: string;
  startTime: Timestamp;
  endTime?: Timestamp;
  duration?: number;
  chatMessages: Array<{
    senderId: string;
    message: string;
    timestamp: Timestamp;
    type: 'text' | 'file' | 'voice';
  }>;
  callQuality: object;
}
```
```

## 🧪 Testing & Demo

### Demo Data
The project includes pre-configured demo data:
- **Demo medicines**: `/public/demo-medicines.csv` (32+ medicine entries)
- **Demo tests**: `/public/demo-tests.csv` (pathology test catalog)

### Testing Different Portals

#### 1. Doctor Portal Testing
```bash
# Access: http://localhost:5173/doctor/dashboard
```
- **Patient Management**: Add, edit, and manage patient records
- **Video Calls**: Test WebRTC functionality (requires signaling server)
- **EHR System**: Create and manage electronic health records
- **Queue Management**: Real-time patient queue updates
- **Analytics**: View dashboard statistics and charts

#### 2. Pharmacy Portal Testing
```bash
# Signup: http://localhost:5173/signup (select Pharmacy role)
# Access: http://localhost:5173/pharmacy/dashboard
```
- **Inventory Management**: Upload CSV files for bulk medicine data
- **Order Processing**: Manage prescription orders
- **Analytics**: View sales and stock analytics
- **Profile Management**: Update pharmacy information

#### 3. Pathology Portal Testing
```bash
# Signup: http://localhost:5173/signup (select Pathology role)
# Access: http://localhost:5173/pathology/dashboard
```
- **Lab Management**: Configure laboratory settings
- **Test Catalog**: Manage diagnostic tests
- **Report System**: Generate and manage test reports

#### 4. WebRTC Video Call Testing
```bash
# Start signaling server first
npm run signaling-server

# Test patient simulation
# Access: http://localhost:5173/mock-patient
```
- **Doctor Side**: Use "Call Patient" button in queue
- **Patient Side**: Automatic acceptance in demo mode
- **Features**: Video, audio, screen sharing, real-time chat
- **Session Recording**: Call metadata stored in Firebase

### Sample Testing Workflow
1. **Start services**: `npm run dev:full`
2. **Create accounts**: Visit signup pages for different roles
3. **Add sample data**: Use Firebase Console or demo CSV files
4. **Test video calls**: Use mock patient for WebRTC testing
5. **Monitor real-time**: Check Firestore for live updates

### Debug Tools
- **Firebase Debug**: Built-in Firebase connection testing
- **WebRTC Debug**: Console logging for call quality
- **Chat Testing**: `debug-chat.html` for isolated chat testing
- **E2E Testing**: `test-chat-e2e.cjs` for complete workflow testing

## 🎨 UI/UX Features

### Design System
- **Consistent Theming**: Role-based color schemes (Blue for doctors, Orange for pharmacy, Purple for pathology)
- **Responsive Design**: Mobile-first approach with Tailwind CSS
- **Component Library**: Reusable components with consistent styling
- **Accessibility**: Proper ARIA labels and keyboard navigation

### Key Components

#### Navigation & Layout
- **Sidebar Navigation**: Collapsible sidebar with role-based menu items
- **Header**: User profile dropdown, notifications, search functionality
- **Breadcrumbs**: Clear navigation hierarchy
- **Loading States**: Sophisticated loading screens and skeletons

#### Dashboard Components
- **Statistics Cards**: Real-time metrics with animated counters
- **Charts & Analytics**: Chart.js integration for data visualization
- **Activity Feeds**: Live updates using Firestore listeners
- **Quick Actions**: Contextual action buttons and shortcuts

#### Patient Management
- **Patient Table**: Sortable, searchable patient records
- **Avatar System**: Generated initials-based avatars
- **Search & Filter**: Advanced patient search capabilities
- **Queue Management**: Real-time patient queue with drag-and-drop

#### Video Call Interface
- **Video Controls**: Mute, camera toggle, screen sharing
- **Chat Integration**: Side-panel chat during calls
- **Call Quality**: Real-time connection monitoring
- **Recording Status**: Visual indicators for call recording

#### Forms & Data Entry
- **Multi-step Forms**: Progressive form completion
- **Validation**: Real-time form validation with error states
- **File Upload**: Drag-and-drop file handling
- **CSV Import**: Bulk data import with preview and validation

## ⚙️ Configuration & Customization

### Environment Setup
Create environment variables for different deployment stages:

```bash
# .env.local (development)
VITE_FIREBASE_API_KEY=your_dev_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_dev_auth_domain
VITE_FIREBASE_PROJECT_ID=your_dev_project_id
VITE_SIGNALING_SERVER_URL=http://localhost:3001

# .env.production
VITE_FIREBASE_API_KEY=your_prod_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_prod_auth_domain
VITE_FIREBASE_PROJECT_ID=your_prod_project_id
VITE_SIGNALING_SERVER_URL=https://your-signaling-server.com
```

### Theme Customization
Update `tailwind.config.js` for custom branding:

```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
        pharmacy: {
          500: '#f59e0b',
          600: '#d97706',
        },
        pathology: {
          500: '#8b5cf6',
          600: '#7c3aed',
        }
      }
    }
  }
}
```

### Adding New Portal Types
1. **Create portal-specific components** in `src/components/[portal-name]/`
2. **Add authentication logic** in `src/services/authService.ts`
3. **Update routing** in `WorkingAppRouter.tsx`
4. **Create signup flow** in `src/pages/[portal-name]/`
5. **Add Firebase collections** for portal-specific data

### WebRTC Configuration
Customize video call settings in `src/services/webrtc.ts`:

```typescript
const rtcConfig = {
  iceServers: [
    { urls: 'stun:stun.l.google.com:19302' },
    { urls: 'stun:stun1.l.google.com:19302' },
    // Add TURN servers for production
  ],
  iceCandidatePoolSize: 10,
};
```

## 🐛 Troubleshooting

### Common Issues & Solutions

#### Development Environment
```bash
# Clear cache and reinstall dependencies
rm -rf node_modules package-lock.json
npm install

# Fix TypeScript errors
npm run build  # Check for compilation errors
```

#### Firebase Issues
```bash
# Check Firebase connection
# Open browser console and look for Firebase debug messages
# Verify Firebase configuration in src/config/firebase.ts

# Deploy security rules
firebase deploy --only firestore:rules
firebase deploy --only storage

# Check Firestore indexes
# Firebase Console → Firestore → Indexes
```

#### WebRTC Connection Issues
```bash
# Ensure signaling server is running
npm run signaling-server

# Check browser permissions
# Camera and microphone access required
# Test in different browsers (Chrome recommended)

# Network issues
# Check firewall settings for ports 3001 (signaling) and 5173 (dev)
# Verify STUN/TURN server connectivity
```

#### Real-time Updates Not Working
```bash
# Check Firestore listeners
# Open browser DevTools → Console
# Look for onSnapshot error messages

# Verify Firebase rules
# Ensure authenticated users have read/write access
# Check user authentication status
```

### Debug Tools & Commands
```bash
# Run with detailed logging
DEBUG=* npm run dev

# Test Firebase connection
# Visit: http://localhost:5173/test-firebase-complete.html

# Test WebRTC independently
# Visit: http://localhost:5173/debug-chat.html

# Run E2E tests
node test-chat-e2e.cjs
```

### Performance Optimization
```bash
# Analyze bundle size
npm run build
npm run preview

# Check for memory leaks
# Browser DevTools → Performance tab
# Monitor Firestore listener cleanup

# Optimize images and assets
# Compress images in public/ folder
# Use WebP format when possible
```

## 📦 Deployment Guide

### Production Build
```bash
# Create optimized production build
npm run build

# Preview production build locally
npm run preview
```

### Firebase Hosting Deployment
```bash
# Install Firebase CLI (if not already installed)
npm install -g firebase-tools

# Login to Firebase
firebase login

# Initialize Firebase hosting (if not already done)
firebase init hosting

# Deploy to Firebase
firebase deploy

# Deploy specific services
firebase deploy --only hosting
firebase deploy --only firestore:rules
firebase deploy --only storage
```

### Alternative Deployment Options

#### Vercel Deployment
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod
```

#### Netlify Deployment
```bash
# Build the project
npm run build

# Deploy to Netlify (drag dist/ folder or use CLI)
npm install -g netlify-cli
netlify deploy --prod --dir=dist
```

#### Docker Deployment
```dockerfile
# Dockerfile
FROM node:18-alpine as builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Environment-Specific Configuration
```bash
# Production environment variables
VITE_FIREBASE_PROJECT_ID=your-prod-project
VITE_SIGNALING_SERVER_URL=https://your-signaling-server.com
VITE_APP_VERSION=1.0.0

# Staging environment variables  
VITE_FIREBASE_PROJECT_ID=your-staging-project
VITE_SIGNALING_SERVER_URL=https://staging-signaling.com
VITE_APP_VERSION=staging
```

## 🔒 Security & Privacy

### Firebase Security Rules

#### Firestore Rules (`firestore.rules`)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only access their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Role-based access for medical records
    match /patients/{patientId} {
      allow read, write: if request.auth != null && 
        (getUserRole(request.auth.uid) == 'doctor' || 
         getUserRole(request.auth.uid) == 'pathology');
    }
    
    // Prescription access
    match /prescriptions/{prescriptionId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && 
        getUserRole(request.auth.uid) == 'doctor';
    }
    
    // Pharmacy inventory
    match /pharmacies/{pharmacyId} {
      allow read, write: if request.auth != null && 
        (resource.data.userId == request.auth.uid || 
         getUserRole(request.auth.uid) == 'doctor');
    }
    
    function getUserRole(uid) {
      return get(/databases/$(database)/documents/users/$(uid)).data.role;
    }
  }
}
```

#### Storage Rules (`storage.rules`)
```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // Profile images
    match /profile-images/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Medical documents (doctor and patient access)
    match /medical-documents/{patientId}/{allPaths=**} {
      allow read, write: if request.auth != null && 
        (request.auth.uid == patientId || 
         getUserRole(request.auth.uid) == 'doctor');
    }
    
    // Chat attachments during video calls
    match /chat-attachments/{callId}/{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### Data Privacy Compliance
- **HIPAA Compliance**: Implement audit logging for medical record access
- **GDPR Compliance**: User data deletion and export capabilities
- **Encryption**: All data encrypted in transit and at rest via Firebase
- **Access Logs**: Monitor and log all data access attempts
- **Data Retention**: Implement automated data retention policies

### Authentication Security
```typescript
// Implement additional security measures
import { onAuthStateChanged, signOut } from 'firebase/auth';

// Session timeout
const SESSION_TIMEOUT = 30 * 60 * 1000; // 30 minutes

// Two-factor authentication for sensitive operations
const requireTwoFactor = async (operation: string) => {
  // Implement 2FA verification
};

// Audit trail for critical actions
const logSecurityEvent = async (action: string, userId: string) => {
  await addDoc(collection(db, 'security_logs'), {
    action,
    userId,
    timestamp: new Date(),
    ip: await getUserIP(),
    userAgent: navigator.userAgent
  });
};
```

### WebRTC Security
```typescript
// Secure WebRTC configuration
const rtcConfig = {
  iceServers: [
    { urls: 'stun:stun.l.google.com:19302' },
    // Use TURN servers with authentication for production
    {
      urls: 'turn:your-turn-server.com:3478',
      username: 'your-turn-username',
      credential: 'your-turn-password'
    }
  ],
  iceCandidatePoolSize: 10,
};
```

## 🚀 Future Roadmap

### Phase 1: Enhanced Features (Q1 2025)
- [ ] **AI Integration**: ChatGPT-powered medical assistant for diagnosis support
- [ ] **Mobile App**: React Native companion app for patients
- [ ] **Telemedicine API**: RESTful API for third-party integrations
- [ ] **Advanced Analytics**: ML-powered insights and predictive analytics
- [ ] **Multi-language Support**: Internationalization (i18n) implementation

### Phase 2: Enterprise Features (Q2 2025)
- [ ] **Hospital Network**: Multi-hospital management system
- [ ] **Insurance Integration**: Real-time insurance verification and claims
- [ ] **Laboratory Network**: Integration with external pathology labs
- [ ] **Pharmacy Chain**: Multi-branch pharmacy management
- [ ] **Billing System**: Comprehensive billing and invoice management

### Phase 3: Advanced Capabilities (Q3 2025)
- [ ] **IoT Integration**: Medical device data integration
- [ ] **Blockchain**: Secure medical record blockchain storage
- [ ] **Voice Commands**: Voice-controlled navigation and dictation
- [ ] **AR/VR Support**: Virtual reality for medical training and consultation
- [ ] **Edge Computing**: Offline-first capabilities with sync

### Current Development Status
- ✅ **Core Platform**: Doctor, Pharmacy, Pathology portals
- ✅ **WebRTC Video Calls**: Real-time video consultations
- ✅ **Firebase Integration**: Authentication, Firestore, Storage
- ✅ **EHR System**: Electronic Health Records management
- ✅ **Real-time Chat**: In-call messaging and file sharing
- 🔄 **Mobile Optimization**: Progressive Web App (PWA) features
- 🔄 **Performance**: Bundle optimization and lazy loading

## 🤝 Contributing

### Development Workflow
1. **Fork the repository** and create a feature branch
2. **Follow TypeScript strict mode** for type safety
3. **Use conventional commits** for clear history
4. **Write tests** for new features using Jest/Testing Library
5. **Update documentation** for any new features or changes

### Code Style Guidelines
```bash
# Install development dependencies
npm install

# Run linting
npm run lint

# Run TypeScript checks
npx tsc --noEmit

# Format code
npx prettier --write src/
```

### Project Structure Guidelines
- **Components**: Keep components focused and reusable
- **Services**: Separate business logic from UI components
- **Types**: Maintain comprehensive TypeScript definitions
- **Tests**: Write tests for critical functionality
- **Documentation**: Update README for significant changes

### Contribution Areas
- 🐛 **Bug Fixes**: Report and fix issues
- ⚡ **Performance**: Optimize bundle size and runtime performance  
- 🎨 **UI/UX**: Improve design and user experience
- 📱 **Mobile**: Enhance mobile responsiveness
- 🔒 **Security**: Strengthen authentication and authorization
- 📊 **Analytics**: Add more comprehensive reporting features
- 🌐 **Accessibility**: Improve WCAG compliance
- 📖 **Documentation**: Enhance guides and API documentation

## 📊 Project Stats

- **Total Lines of Code**: 15,000+ lines
- **Main Components**: 25+ React components
- **Firebase Collections**: 10+ Firestore collections
- **WebRTC Features**: Video, audio, screen sharing, chat
- **Supported Roles**: Doctor, Pharmacy, Pathology, Patient
- **Tech Stack**: React 19, TypeScript, Firebase, WebRTC
- **Build Tool**: Vite with optimized production builds
- **Testing**: E2E tests and Firebase integration tests

---

**🏥 GarudX - Revolutionizing Healthcare Management**

*Built with ❤️ for healthcare professionals worldwide*

**Repository**: [GarudX on GitHub](https://github.com/kanak227/GarudX)  
**License**: MIT  
**Version**: 1.0.0  
**Last Updated**: October 2025
