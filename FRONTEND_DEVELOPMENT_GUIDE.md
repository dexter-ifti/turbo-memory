# 🗳️ Frontend Development Guide - Decentralized Voting System

## 📋 Overview

This document provides a comprehensive guide for frontend developers to build the user interface for our blockchain-based voting system. The backend and smart contracts are fully implemented and tested, providing robust APIs and blockchain functionality with a complete multi-role authentication system.

## 🏗️ System Architecture

### Backend Infrastructure
- **API Server**: Node.js + Express.js (Port: 3000)
- **Database**: MongoDB with Mongoose ODM
- **Blockchain**: Ethereum-based smart contracts (Ganache for development)
- **Authentication**: Multi-tier JWT-based system with role-specific authentication methods

### Smart Contracts
- **SimplifiedVotingSystem.sol**: Main voting contract with comprehensive features
- **Deployed Features**: Candidate registration, voter registration, voting, results announcement, emergency controls

### Authentication Architecture
The system implements a **three-tier authentication system**:

1. **Admin Authentication**: Traditional email/password with JWT tokens
2. **Voter Authentication**: Wallet signature-based authentication with MetaMask integration
3. **Candidate Authentication**: Wallet signature-based authentication with MetaMask integration

#### Authentication Flow Overview
```
┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│    Admin    │───▶│ Email/Pass   │───▶│  JWT Token      │
└─────────────┘    └──────────────┘    └─────────────────┘

┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│   Voter     │───▶│ Wallet Sign  │───▶│  JWT Token      │
└─────────────┘    └──────────────┘    └─────────────────┘

┌─────────────┐    ┌──────────────┐    ┌─────────────────┐
│ Candidate   │───▶│ Wallet Sign  │───▶│  JWT Token      │
└─────────────┘    └──────────────┘    └─────────────────┘
```

## 🎯 Core Features to Implement

### 1. **Multi-Role Authentication System**
- **Admin Login**: Email/password authentication with JWT tokens
- **Voter Login**: MetaMask wallet signature authentication
- **Candidate Login**: MetaMask wallet signature authentication
- Role-based access control and session management
- Automatic token management and renewal
- Secure logout and session cleanup

### 2. **Admin Dashboard & Management**
- Admin authentication with email/password
- Election creation and management with blockchain deployment
- User verification and approval systems
- Real-time election monitoring and analytics
- Emergency controls and system administration
- Results announcement and validation

### 3. **Voter Portal**
- Wallet-based registration and authentication
- Election discovery and registration
- Secure voting interface with MetaMask integration
- Vote confirmation and blockchain tracking
- Voting history and digital certificates
- Election results viewing

### 4. **Candidate Portal**
- Wallet-based registration and profile management
- Election participation and campaign tools
- Real-time vote tracking and analytics
- Performance monitoring and results
- Profile customization and manifesto management

### 5. **Blockchain Integration**
- MetaMask wallet connection and management
- Smart contract interaction for voting
- Transaction monitoring and confirmation
- Network validation and switching
- Gas fee estimation and optimization

## 📱 Recommended Pages & Components

### Core Pages

#### 1. **Landing Page** (`/`)
- System overview and features
- Election announcements
- Public election results
- Authentication links

#### 2. **Authentication Pages**
- `/login` - Multi-role login with dynamic authentication based on selected role
  - **Admin Role**: Email/password form with traditional authentication
  - **Voter Role**: Wallet connection interface with MetaMask integration
  - **Candidate Role**: Wallet connection interface with signature verification
- `/register` - Role-based registration with appropriate forms for each user type
- `/register/voter` - Wallet-based voter registration with profile creation
- `/register/candidate` - Wallet-based candidate registration with manifesto and profile
- `/admin/register` - Admin-only registration for system administrators

#### 3. **Admin Dashboard** (`/admin`)
- Dashboard with statistics and analytics
- Election management interface
- User verification and management
- System monitoring

#### 4. **Voter Portal** (`/voter`)
- Voter dashboard with registered elections
- Election participation interface
- Vote casting with blockchain integration
- Voting history and certificates

#### 5. **Candidate Portal** (`/candidate`)
- Candidate profile management
- Election registration interface
- Campaign analytics and vote tracking
- Results and performance data

#### 6. **Election Pages**
- `/elections` - Public elections list
- `/elections/:id` - Election details and live results
- `/elections/:id/vote` - Secure voting interface
- `/elections/:id/results` - Detailed results with analytics



## 🔌 API Documentation

### Base URL
```
http://localhost:3000/api
```

### Authentication Headers
```javascript
// Role-specific authentication headers
const getAuthHeaders = (userRole) => {
  const tokenKey = userRole === 'admin' ? 'adminToken' :
                   userRole === 'voter' ? 'voterToken' :
                   'candidateToken';
  
  const token = localStorage.getItem(tokenKey);
  
  return {
    "Authorization": `Bearer ${token}`,
    "Content-Type": "application/json",
    "X-User-Role": userRole
  };
};
```

---

## 📊 Admin APIs

### Authentication
```javascript
// Admin Login (Email/Password)
POST /api/admin/login
{
  "email": "admin@example.com",
  "password": "password123"
}

// Response
{
  "success": true,
  "data": {
    "token": "jwt_token",
    "user": {
      "adminId": "ADMIN_...",
      "name": "Admin Name",
      "email": "admin@example.com",
      "role": "admin",
      "permissions": ["CREATE_ELECTION", "MANAGE_ELECTION"]
    }
  }
}
```

### Dashboard & Analytics
```javascript
// Get Dashboard Stats
GET /api/admin/dashboard
// Headers: Authorization Bearer token

// Response
{
  "success": true,
  "data": {
    "stats": {
      "totalElections": 5,
      "activeElections": 2,
      "totalVoters": 150,
      "totalCandidates": 25
    },
    "recentElections": [...] // Last 5 elections
  }
}
```

### Election Management
```javascript
// Create Election
POST /api/election/create
{
  "title": "University Student Council Election 2024",
  "description": "Annual election for student representatives",
  "electionType": "student",
  "adminPrivateKey": "0x...",
  "maxCandidates": 10,
  "registrationStartTime": "2024-01-15T09:00:00Z",
  "registrationEndTime": "2024-01-20T17:00:00Z",
  "votingStartTime": "2024-01-25T09:00:00Z",
  "votingEndTime": "2024-01-25T17:00:00Z"
}

// Response
{
  "success": true,
  "data": {
    "election": {...},
    "contractAddress": "0x...",
    "transactionHash": "0x..."
  }
}

// Set Election Timing
PUT /api/election/{contractAddress}/timing
{
  "title": "Updated Title",
  "description": "Updated Description",
  "startTimeFromNow": 3600, // seconds from now
  "durationInSeconds": 86400, // 24 hours
  "adminPrivateKey": "0x..."
}

// Get All Elections
GET /api/election?page=1&limit=10&status=active&search=student

// Get Election Details
GET /api/election/{contractAddress}

// Emergency Stop
POST /api/election/{contractAddress}/emergency-stop
{
  "adminPrivateKey": "0x...",
  "reason": "Security concern detected"
}

// Announce Results
POST /api/election/{contractAddress}/announce-results
{
  "adminPrivateKey": "0x..."
}
```

### User Management
```javascript
// Get All Voters
GET /api/admin/voters?page=1&limit=20&status=verified&search=john

// Verify Voter
PUT /api/admin/voters/{voterId}/verify
{
  "status": "verified", // or "rejected"
  "reason": "Documents verified successfully"
}

// Get All Candidates
GET /api/admin/candidates?page=1&limit=20&status=pending

// Verify Candidate
PUT /api/admin/candidates/{candidateId}/verify
{
  "status": "verified",
  "reason": "Eligibility confirmed"
}
```

### Election Analytics
```javascript
// Get Election Analytics
GET /api/admin/elections/{contractAddress}/analytics

// Response
{
  "success": true,
  "data": {
    "election": {
      "title": "Election Title",
      "status": "results_announced",
      "totalRegistered": 100,
      "totalVoted": 85,
      "turnoutPercentage": "85.00"
    },
    "voteDistribution": [
      {
        "candidateId": "...",
        "name": "John Doe",
        "party": "Student Party",
        "votes": 45,
        "percentage": 52.94
      }
    ],
    "demographics": {
      "byAge": {"18-29": 60, "30-49": 25, "50+": 15},
      "byGender": {"Male": 55, "Female": 42, "Other": 3}
    },
    "timeline": {
      "created": "2024-01-10T10:00:00Z",
      "votingStarted": "2024-01-25T09:00:00Z",
      "votingEnded": "2024-01-25T17:00:00Z",
      "resultsAnnounced": "2024-01-25T18:00:00Z"
    }
  }
}
```

---

## 🗳️ Voter APIs

### Authentication
```javascript
// Voter Login (Wallet Signature Authentication)
POST /api/voter/login
{
  "walletAddress": "0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5",
  "signature": "0x...", // Signed message from MetaMask
  "message": "Login to Voting System as voter\nAddress: 0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5\nTimestamp: 1724419800000"
}

// Response
{
  "success": true,
  "message": "Login successful",
  "data": {
    "voter": {
      "voterId": "VOTER_...",
      "name": "John Doe",
      "walletAddress": "0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5",
      "age": 25,
      "gender": "Male",
      "email": "john@example.com",
      "verificationStatus": "verified",
      "isEligible": true,
      "isRegisteredOnChain": false,
      "lastLogin": "2025-08-23T14:30:00Z"
    },
    "token": "jwt_token_here"
  }
}

```

### Registration & Profile
```javascript
// Register Voter
POST /api/voter/register
{
  "name": "John Doe",
  "age": 25,
  "gender": "Male",
  "walletAddress": "0x...",
  "email": "john@example.com",
  "phone": "+1234567890",
  "address": {
    "street": "123 Main St",
    "city": "City",
    "state": "State",
    "zipCode": "12345",
    "country": "India"
  }
}

// Register for Specific Election
POST /api/voter/register-election
{
  "contractAddress": "0x...",
  "walletAddress": "0x...",
  "privateKey": "0x..." // For blockchain registration
}

// Get Voter Profile
GET /api/voter/profile/{walletAddress}

// Get Voter's Elections
GET /api/voter/{walletAddress}/elections
```

### Voting
```javascript
// Cast Vote
POST /api/voter/vote
{
  "contractAddress": "0x...",
  "candidateId": 1,
  "privateKey": "0x..." // For blockchain transaction
}

// Response
{
  "success": true,
  "message": "Vote cast successfully",
  "data": {
    "transactionHash": "0x...",
    "blockNumber": 12345,
    "votedAt": "2024-01-25T14:30:00Z"
  }
}
```

---

## 👥 Candidate APIs

### Authentication
```javascript
// Candidate Login (Wallet Signature Authentication)
POST /api/candidate/login
{
  "walletAddress": "0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5",
  "signature": "0x...", // Signed message from MetaMask
  "message": "Login to Voting System as candidate\nAddress: 0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5\nTimestamp: 1724419800000"
}

// Response
{
  "success": true,
  "message": "Login successful",
  "data": {
    "candidate": {
      "candidateId": "CANDIDATE_...",
      "name": "Jane Smith",
      "party": "Progressive Party",
      "walletAddress": "0x742d35Cc6467C7e62b0dE3f6Ec80c6F07b23eAa5",
      "age": 30,
      "gender": "Female",
      "email": "jane@example.com",
      "verificationStatus": "verified",
      "isActive": true,
      "isRegisteredOnChain": false,
      "lastLogin": "2025-08-23T14:30:00Z"
    },
    "token": "jwt_token_here"
  }
}

```

### Registration & Profile
```javascript
// Register Candidate
POST /api/candidate/register
{
  "name": "Jane Smith",
  "party": "Progressive Party",
  "manifesto": "Detailed manifesto content...",
  "age": 30,
  "gender": "Female",
  "walletAddress": "0x...",
  "email": "jane@example.com",
  "phone": "+1234567890",
  "address": {...},
  "education": "MBA in Public Administration",
  "experience": "5 years in local government",
}

// Register for Election
POST /api/candidate/register-election
{
  "contractAddress": "0x...",
  "walletAddress": "0x...",
  "privateKey": "0x..."
}

// Get Candidates for Election
# Get Candidates for Election
GET /api/candidate/election/{contractAddress}

# Get Candidate Profile  
GET /api/candidate/profile/{walletAddress}
// Headers: Authorization Bearer token (optional, but recommended for security)

# Note: Some candidate endpoints require authentication
# Include JWT token in headers: "Authorization": "Bearer {token}"
```
```

---

## ⛓️ Blockchain APIs

### Contract Information
```javascript
// Get Blockchain Status
GET /api/blockchain/status

// Response
{
  "success": true,
  "data": {
    "connected": true,
    "network": "ganache",
    "chainId": 1337,
    "blockNumber": 1234,
    "gasPrice": "20000000000"
  }
}

// Get Contract Info
GET /api/blockchain/contract-info/{contractAddress}

// Response
{
  "success": true,
  "data": {
    "contractAddress": "0x...",
    "electionInfo": {
      "title": "Election Title",
      "description": "Election Description",
      "startTime": 1706184000,
      "endTime": 1706270400,
      "isActive": true,
      "totalVotes": 85,
      "resultsAnnounced": false
    },
    "votingStatus": "InProgress", // NotStarted, InProgress, Ended
    "candidateCount": 5,
    "candidates": [
      {
        "candidateId": 1,
        "name": "John Doe",
        "party": "Party A",
        "candidateAddress": "0x...",
        "votes": 45,
        "isActive": true
      }
    ]
  }
}
```


## 🔔 Error Handling & User Feedback

### Common Error Scenarios
1. **Blockchain Connection Errors**
2. **Transaction Failures**
3. **Authentication Timeout**
4. **Validation Errors**
5. **Network Issues**

### Error Response Format
```javascript
{
  "success": false,
  "message": "User-friendly error message",
  "error": "Technical error details",
  "code": "ERROR_CODE",
  "timestamp": "2024-01-25T14:30:00Z"
}
```

### User Feedback Components
- **Toast Notifications** for success/error messages
- **Loading Spinners** for blockchain transactions
- **Progress Indicators** for multi-step processes
- **Confirmation Dialogs** for irreversible actions

---

## 🚀 Development Setup

### Environment Variables
```env
# API Configuration
REACT_APP_API_URL=http://localhost:3000/api
REACT_APP_WS_URL=ws://localhost:3000

# Blockchain Configuration
REACT_APP_BLOCKCHAIN_NETWORK=ganache
REACT_APP_GANACHE_URL=http://127.0.0.1:8545

# MetaMask Configuration
REACT_APP_CHAIN_ID=1337
REACT_APP_NETWORK_NAME=Ganache Local
```

### Sample Package.json
```json
{
  "name": "voting-system-frontend",
  "version": "1.0.0",
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.8.0",
    "axios": "^1.3.0",
    "ethers": "^6.7.0",
    "@metamask/detect-provider": "^2.0.0",
    "react-query": "^3.39.0",
    "@mui/material": "^5.11.0",
    "recharts": "^2.5.0",
    "socket.io-client": "^4.6.0",
    "react-hot-toast": "^2.4.0"
  }
}
```

---

## 📋 Implementation Checklist

### Phase 1: Core Setup ✅
- [ ] Project setup with chosen framework
- [ ] API client configuration
- [ ] Multi-role authentication system
- [ ] Basic routing structure
- [ ] Web3 integration setup
- [ ] MetaMask connection handling

### Phase 2: Admin Features ✅
- [ ] Admin dashboard with analytics
- [ ] Election creation and management
- [ ] User verification interfaces
- [ ] Emergency controls
- [ ] Results announcement

### Phase 3: Voter Features ✅
- [ ] Voter registration and verification
- [ ] Election registration interface
- [ ] Secure voting interface
- [ ] Vote history and tracking
- [ ] Results viewing

### Phase 4: Candidate Features ✅
- [ ] Candidate registration and profiles
- [ ] Election participation interface
- [ ] Campaign management tools
- [ ] Performance analytics

### Phase 5: Advanced Features ✅
- [ ] Real-time updates and notifications
- [ ] Advanced analytics and charts
- [ ] Mobile responsiveness
- [ ] Security enhancements
- [ ] Testing and optimization

---


### Web3 Integration
```javascript
// hooks/useWeb3.js
import { useState, useEffect } from 'react';
import { ethers } from 'ethers';
import detectEthereumProvider from '@metamask/detect-provider';

export const useWeb3 = () => {
  const [provider, setProvider] = useState(null);
  const [account, setAccount] = useState(null);
  const [connected, setConnected] = useState(false);

  const connectWallet = async () => {
    try {
      const provider = await detectEthereumProvider();
      if (provider) {
        const ethersProvider = new ethers.BrowserProvider(provider);
        await ethersProvider.send("eth_requestAccounts", []);
        const signer = await ethersProvider.getSigner();
        const address = await signer.getAddress();
        
        setProvider(ethersProvider);
        setAccount(address);
        setConnected(true);
        
        return { provider: ethersProvider, account: address };
      } else {
        throw new Error('Please install MetaMask');
      }
    } catch (error) {
      console.error('Wallet connection failed:', error);
      throw error;
    }
  };

  return { provider, account, connected, connectWallet };
};
```

---

## 📞 Support & Resources

### Backend API Reference
- Full API documentation available in backend README.md
- Postman collection for API testing
- Smart contract ABI files in `/contracts/compiled/`

### Development Resources
- Smart contract interaction examples in `/backend/scripts/`
- Database models in `/backend/models/`
- Blockchain service methods in `/backend/services/blockchainService.js`

### Testing Data
- Use Ganache accounts for testing
- Test elections can be created through admin APIs
- Sample voter and candidate data available in test scripts

### Authentication Testing
```bash
# Test all authentication methods
npm run test-voter-login      # Test voter wallet authentication
npm run test-candidate-login  # Test candidate wallet authentication

# Admin authentication can be tested via API directly:
curl -X POST http://localhost:3000/api/admin/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"password123"}'
```

### Frontend Testing Examples
```javascript
// Test wallet connection for voters/candidates
const testWalletConnection = async () => {
  try {
    if (typeof window.ethereum === 'undefined') {
      throw new Error('MetaMask not installed');
    }
    
    const accounts = await window.ethereum.request({
      method: 'eth_requestAccounts'
    });
    
    console.log('Connected wallet:', accounts[0]);
    return accounts[0];
  } catch (error) {
    console.error('Wallet connection failed:', error);
  }
};

// Test role-based authentication
const testRoleBasedLogin = async (role) => {
  const { login } = useAuth();
  
  try {
    switch (role) {
      case 'admin':
        await login('admin', {
          email: 'admin@example.com',
          password: 'password123'
        });
        break;
      case 'voter':
      case 'candidate':
        await login(role);
        break;
    }
    
    console.log(`${role} login successful`);
  } catch (error) {
    console.error(`${role} login failed:`, error);
  }
};
```


---



### 📋 **Next Steps for Frontend Development**

1. **Start Development Server**: Begin with the implemented authentication system
2. **Test Login Flows**: Verify all three authentication methods work correctly
3. **Build Role-specific Dashboards**: Create admin, voter, and candidate interfaces
4. **Implement Protected Routes**: Add route guards using the authentication context
5. **Add Profile Management**: Build user profile pages for each role type
6. **Create Election Interfaces**: Develop voting, candidate registration, and admin panels

