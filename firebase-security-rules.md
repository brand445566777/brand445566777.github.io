# Firebase Security Rules for AdMob Crawler Access

## Overview

This document provides the Firebase Realtime Database and Firestore security rules configuration that allows Google AdMob crawlers to access your app's content while maintaining security for user data.

## Problem Statement

Google AdMob crawlers need to access your app's content to:
- Verify app functionality
- Scan for policy compliance
- Index content for ad relevance
- Test ad delivery

Without proper configuration, Firebase security rules may block crawler access, causing:
- Ad serving issues
- Policy violation reports
- Reduced ad performance
- Crawler access denied errors

## Solution: Public Content Rules

### For Firestore Database

Create a rule that allows public read access to non-sensitive data while protecting sensitive information:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow public read access to emergency helpline directory
    match /emergencies/{document=**} {
      allow read: if true;
      allow write: if false;
    }

    // Allow public read access to helpline categories
    match /categories/{document=**} {
      allow read: if true;
      allow write: if false;
    }

    // Allow public read access to regional helplines
    match /regions/{document=**} {
      allow read: if true;
      allow write: if false;
    }

    // Protect user data - only authenticated users can read/write their own data
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }

    // Protect user preferences - only authenticated users
    match /userPreferences/{userId} {
      allow read, write: if request.auth.uid == userId;
    }

    // Protect call history - only authenticated users
    match /callHistory/{userId}/{document=**} {
      allow read, write: if request.auth.uid == userId;
    }

    // Default deny all other access
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

### For Realtime Database

If using Firebase Realtime Database, configure rules as follows:

```json
{
  "rules": {
    "emergencies": {
      ".read": true,
      ".write": false
    },
    "categories": {
      ".read": true,
      ".write": false
    },
    "regions": {
      ".read": true,
      ".write": false
    },
    "users": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    },
    "userPreferences": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    },
    "callHistory": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

## Implementation Steps

### Step 1: Access Firebase Console

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Select your project: "pakistan-emergency-helpline"
3. Navigate to **Firestore Database** or **Realtime Database**

### Step 2: Update Security Rules

#### For Firestore:

1. Click on **Rules** tab
2. Replace the existing rules with the provided Firestore rules above
3. Click **Publish**

#### For Realtime Database:

1. Click on **Rules** tab
2. Replace the existing rules with the provided Realtime Database rules above
3. Click **Publish**

### Step 3: Test Rules

Use Firebase's built-in rule simulator:

1. Click **Rules** tab
2. Click **Simulate** (or use the simulator at the bottom)
3. Test read/write operations:
   - **Collection**: `emergencies`
   - **Document**: `police`
   - **Operation**: `read`
   - **Expected Result**: `Allow`

4. Test protected data:
   - **Collection**: `users`
   - **Document**: `user123`
   - **Operation**: `read`
   - **Expected Result**: `Deny` (without authentication)

## AdMob Crawler User-Agent

Google AdMob crawlers use the following user-agents:

```
Googlebot/2.1 (+http://www.google.com/bot.html)
Googlebot-Image/1.0
AdsBot-Google (+http://www.google.com/adsbot.html)
```

These crawlers will access your public data (emergencies, categories, regions) but cannot access protected data (users, preferences, call history) without authentication.

## Best Practices

### 1. Separate Public and Private Data

- **Public Collections**: `emergencies`, `categories`, `regions`
  - Read: `true` (allow all)
  - Write: `false` (deny all)

- **Private Collections**: `users`, `userPreferences`, `callHistory`
  - Read: `authenticated only`
  - Write: `authenticated only`

### 2. Index Public Collections

For better performance with AdMob crawlers:

1. Go to **Firestore** → **Indexes**
2. Create composite indexes for:
   - `emergencies`: `region`, `category`, `name`
   - `categories`: `name`, `priority`
   - `regions`: `name`, `country`

### 3. Monitor Access

Use Firebase Analytics to monitor crawler access:

1. Go to **Analytics** → **Realtime**
2. Look for requests from `Googlebot` user-agent
3. Verify that public data is being accessed

### 4. Rate Limiting (Optional)

If you want to limit crawler access:

```javascript
// Firestore example with rate limiting
match /emergencies/{document=**} {
  allow read: if request.auth == null || request.auth.token.iss == 'https://securetoken.google.com/pakistan-emergency-helpline';
}
```

## Troubleshooting

### Issue: AdMob Crawler Getting "Access Denied"

**Solution**: Ensure your public collections have `.read: true` in the rules.

### Issue: User Data Being Exposed

**Solution**: Verify that user collections have authentication checks:
```javascript
allow read, write: if request.auth.uid == userId;
```

### Issue: Slow Crawler Access

**Solution**: Add indexes to public collections for faster queries.

## Security Checklist

- [ ] Public collections have read-only access
- [ ] Private collections require authentication
- [ ] User data is protected with UID checks
- [ ] Rules have been tested in the simulator
- [ ] Indexes are created for public collections
- [ ] Analytics is monitoring crawler access
- [ ] No sensitive data in public collections

## Additional Resources

- [Firebase Security Rules Documentation](https://firebase.google.com/docs/rules)
- [Firestore Security Rules Guide](https://firebase.google.com/docs/firestore/security/start)
- [Google AdMob Crawler Documentation](https://support.google.com/admanager/answer/1261738)
- [Firebase Best Practices](https://firebase.google.com/docs/database/usage/best-practices)
