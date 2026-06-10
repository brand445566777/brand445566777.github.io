# Pakistan Emergency & Utility Helpline - Design Plan

## Overview
A comprehensive, offline-first mobile app providing quick access to emergency, security, hospital, and government helpline numbers across Pakistan. The app prioritizes fast access during crises with color-coded categories, one-tap dialing, and province-wise organization.

---

## Screen List

1. **Home Screen** - National Emergency Numbers (Primary)
2. **Federal Screen** - Islamabad Emergency & Services
3. **Punjab Screen** - Punjab Province Services
4. **Sindh Screen** - Sindh Province Services
5. **Balochistan Screen** - Balochistan Province Services
6. **Khyber Pakhtunkhwa Screen** - KPK Province Services
7. **Gilgit Baltistan Screen** - GB Province Services
8. **Azad Kashmir Screen** - AJK Province Services
9. **Favorites Screen** - User's Saved Helplines
10. **Settings Screen** - Language, Theme, About

---

## Primary Content & Functionality

### Home Screen (National Emergency)
**Purpose:** Immediate access to critical national emergency numbers during crisis
- Large, prominent buttons for top-priority services
- Color-coded by category (Red: Rescue/Fire, Blue: Police/Security, Green: Traffic, Purple: Welfare, Orange: Utilities)
- Each button shows service name, number, and icon
- One-tap direct calling via ACTION_DIAL
- Quick navigation tabs to other provinces at bottom

### Province Screens (Federal, Punjab, Sindh, Balochistan, KPK, GB, AJK)
**Purpose:** Organized access to regional emergency and utility services
- Categorized sections: Emergency & Police, Security Agencies, Health & Hospitals, Utilities & Government
- Scrollable vertical layout with consistent card design
- Each card displays: icon (left), service name, phone number, call button
- Color-coded by service type for quick visual scanning
- Back button to return to home

### Favorites Screen
**Purpose:** Quick access to user's most-used helplines
- Display saved favorite numbers
- Ability to remove from favorites
- Empty state with guidance if no favorites added
- Same card design as other screens for consistency

### Settings Screen
**Purpose:** App configuration and information
- Language toggle (English/Urdu)
- Dark/Light mode toggle
- About section with disclaimer and privacy policy
- Contact/Feedback option
- App version information

---

## Key User Flows

### Flow 1: Emergency Call (Primary Use Case)
1. User opens app
2. Sees National Emergency screen with large buttons
3. Taps desired emergency service (e.g., "RESCUE 1122")
4. Phone dialer opens with number pre-filled
5. User confirms and makes call

### Flow 2: Find Regional Service
1. User opens app
2. Scrolls down or taps province tab
3. Navigates to desired province screen
4. Finds service in categorized section
5. Taps call button → phone dialer opens

### Flow 3: Save Favorite
1. User finds a helpline number
2. Taps star/favorite icon on card
3. Number is saved to Favorites screen
4. User can access from Favorites tab for quick access

### Flow 4: Change Language/Theme
1. User taps Settings tab
2. Toggles language or theme preference
3. App updates immediately
4. Preference is saved for next session

---

## Color System (Locked)

| Color | Category | Hex (Light) | Hex (Dark) |
|-------|----------|------------|-----------|
| **Red** | Rescue, Ambulance, Fire Brigade | #EF4444 | #F87171 |
| **Blue** | Police, Army, Rangers, FIA, Security | #0284C7 | #0EA5E9 |
| **Green** | Traffic, Highway, Motorway, Transport | #22C55E | #4ADE80 |
| **Purple** | Women, Child, Tourist, Complaints | #A855F7 | #D946EF |
| **Orange** | Utilities, Disaster, NADRA, Government | #F97316 | #FB923C |
| **White/Gray** | Hospitals, Health Services | #F5F5F5 | #1E2022 |

---

## Visual Design Rules

### Typography
- **Font Weight:** Bold for all headings and button text
- **Font Size Hierarchy:**
  - Screen Title: 28px, Bold
  - Section Header: 18px, Bold
  - Card Title: 16px, Bold
  - Card Subtitle/Number: 14px, Medium
  - Button Text: 16px, Bold

### Layout & Spacing
- **Button Height:** 60dp
- **Corner Radius:** 16dp (rounded corners on all cards and buttons)
- **Padding:** 16dp standard padding, 8dp between elements
- **Icon Placement:** Left side of card, 24x24dp size
- **Scrollable Vertical Arrangement:** All screens use ScrollView for flexibility

### Card Design
- Icon (left) + Service Name (center) + Number (below) + Call Button (right)
- Rounded corners (16dp)
- Subtle shadow/elevation
- Color-coded left border or background tint
- Minimum touch target: 48x48dp for accessibility

### Interactive Elements
- **Press Feedback:** Scale 0.97 + opacity 0.9 on tap
- **Haptic Feedback:** Light impact on button press (iOS/Android)
- **Call Button:** Prominent, always accessible on each card

---

## Data Structure

### Helpline Entry
```
{
  id: string
  category: "emergency" | "police" | "rescue" | "fire" | "health" | "utility" | "government" | "security"
  categoryColor: "red" | "blue" | "green" | "purple" | "orange" | "white"
  name: string (English)
  nameUrdu: string
  number: string
  icon: string (Material Icons name)
  province: "national" | "federal" | "punjab" | "sindh" | "balochistan" | "kpk" | "gb" | "ajk"
  description?: string
  isFavorite: boolean
}
```

### Province Sections
Each province screen organizes entries into:
- Emergency & Police
- Security Agencies
- Health & Hospitals
- Utilities & Government Services

---

## Accessibility & Responsiveness

- **Text Contrast:** Minimum 4.5:1 ratio for readability
- **Touch Targets:** Minimum 48x48dp for all interactive elements
- **Orientation:** Portrait only (9:16 aspect ratio)
- **Safe Area:** Proper handling of notches and home indicators
- **Dark Mode:** Full support with adjusted colors maintaining contrast

---

## Extra Features (Phase 2 - Optional)

- **Search Bar:** Quick search across all helplines
- **Favorites System:** Save and organize frequently used numbers
- **Offline Access:** All data stored locally, works without internet
- **Urdu Support:** Full Urdu translations for all helplines
- **Dark Mode:** System-wide dark theme support
- **Share Functionality:** Share helpline numbers via SMS/WhatsApp
- **Recent Calls:** Quick access to recently dialed numbers

---

## Technical Implementation Notes

- **Data Storage:** Local JSON file (no backend required for MVP)
- **Permissions:** CALL_PHONE permission for direct dialing
- **Offline:** 100% offline functionality, no internet required
- **Performance:** Lightweight, minimal dependencies
- **Localization:** English/Urdu support via i18n
- **State Management:** React Context for favorites and settings
- **Persistence:** AsyncStorage for user preferences and favorites

