# Yari APK - Comprehensive Reverse Engineering Analysis Report

## Executive Summary

This repository contains extracted files from **Yari** (package: `com.funny.yari`), a social audio/voice chat application version 1.4.6 (build 146). The app is a hybrid Flutter + Android Native application with embedded mini-games and extensive social features.

---

## 1. Application Overview

| Property | Value |
|----------|-------|
| **App Name** | Yari |
| **Package Name** | com.funny.yari |
| **Version** | 1.4.6 (Build 146) |
| **Min SDK** | Android 24 (Android 7.0) |
| **Target SDK** | Android 35 (Android 15) |
| **Primary Tech Stack** | Flutter + Android Native (Kotlin/Java) |
| **Game Engine** | Cocos Creator (for mini-games) |

---

## 2. What We Have In Full (Usable Resources)

### 2.1 UI Layout Files (XML) - ✅ COMPLETE
All 280+ Android XML layout files are available and can be used to recreate the UI structure:

**Main Screens:**
- `activity_launch.xml` - Splash/launch screen
- `room_activity.xml` - Main chat room
- `tab_main_page.xml` - Main tab navigation
- `activity_chat.xml` - Direct messaging
- `activity_invite.xml` - User invitation
- `activity_relation.xml` - Friend relationships

**Chat & Messaging:**
- `item_chat.xml`, `item_chat_text.xml`, `item_chat_voice.xml` - Chat bubbles
- `item_chat_gift.xml` - Gift messages
- `item_chat_system.xml` - System messages
- `fragment_message_list.xml` - Message list

**Room Features:**
- `room_microphone.xml` - Microphone seat UI
- `live_room_cinema.xml` - Cinema/video watching together
- `live_room_game_view.xml` - Game integration view
- `live_room_bottom_gift_list.xml` - Gift panel

**User Profile:**
- `settings_bind_account_activity.xml` - Account binding
- `wallet_activity.xml` - Wallet/currency
- `activity_svip_settings.xml` - VIP settings

### 2.2 Drawable Resources (PNG/WebP/XML) - ✅ COMPLETE
14MB of visual assets including:
- App icons and launcher icons
- User level badges (lv0-lv100)
- Gift icons and animations
- Room theme backgrounds
- Button states and selectors
- Navigation icons
- Avatar frames and decorations

### 2.3 Flutter Assets - ✅ COMPLETE
All Flutter image assets are available in `/assests/flutter_assets/assets/`:
- Login screens (`login_*.png`)
- Home screens (`home_*.png`)
- Profile/Me screens (`me_*.png`)
- Family/Group screens (`family_*.png`)
- Lottie animations (`*.json`)
- SVGA animations (`*.svga`)

### 2.4 Animation Files - ✅ COMPLETE
- SVGA animations (gift effects, celebrations)
- Lottie JSON animations
- MP4 video animations (treasure box reveals, VIP effects)

### 2.5 Game Assets - ✅ COMPLETE
5 embedded mini-games with full HTML5/JavaScript source:

| Game | Directory | Status |
|------|-----------|--------|
| Cocos JS Runtime | `fe-game-cocos-js-yr` | ✅ Full JS |
| Crazy Fruit | `fe-game-crazy-fruit-yr` | ✅ Full source |
| Greedy Game | `fe-game-greedy-yr` | ✅ Full source |
| Lucky 777 Slots | `fe-game-lucky777-yr` | ✅ Full source |
| Super 77 Slots | `fe-game-super77-yr` | ✅ Full source |

### 2.6 Color and Style Resources - ✅ COMPLETE
- Color definitions in `/res/color/`
- Theme styles (AppTheme, AppTheme2, etc.)
- Shape drawables for rounded corners, gradients

---

## 3. What Can Be Reverse Engineered

### 3.1 UI Structure and Layout - ⭐ HIGH POTENTIAL
**What's possible:**
- Complete screen flow and navigation
- All view hierarchies and component relationships
- Styling, colors, dimensions from XML
- Custom view names (can infer functionality)

**Limitations:**
- No direct access to view logic without decompiling DEX

### 3.2 Data Models - ⭐ HIGH POTENTIAL
The Flutter Dart files reveal data structures:

```dart
// Store item model shows e-commerce structure
- id, type, name, icon, resUrl
- price, originalPrice, timePrices
- expireDays, rarity, privilege
- reviewStatus, state
```

### 3.3 API Endpoints (Inferrable) - ⭐ MEDIUM POTENTIAL
From AndroidManifest.xml and code patterns, likely APIs for:
- User authentication (Google, Facebook, Phone)
- Room management
- Gifting system
- Wallet/billing
- Chat/messaging
- Push notifications (Firebase)

### 3.4 Feature List (from layout names) - ⭐ HIGH POTENTIAL

**Social Features:**
- Voice chat rooms with multiple microphone seats
- Friend system (add/follow/block)
- CP (Couple Pairing) system
- Family/Group system
- Direct messaging with text/voice/image

**Monetization:**
- Virtual wallet (diamonds, beans, energy)
- Gift sending with animations
- VIP/SVIP subscription system
- In-app purchases (Google Play Billing)

**Entertainment:**
- Mini-games (slots, fruit games)
- Cinema mode (watch together)
- Red bag/Red envelope events
- Treasure box/lucky draw
- Teen Patti card game

**User Experience:**
- Custom themes
- Avatar frames
- Vehicle animations
- Honor medals/badges
- Level system (0-100+)

### 3.5 Mini-Games - ⭐ VERY HIGH POTENTIAL
The Cocos Creator games are in JavaScript and can be:
- Fully run standalone
- Analyzed for game logic
- Modified and reskinned
- Used as reference for reimplementation

---

## 4. What Cannot Be Easily Recovered

### 4.1 Compiled Code (DEX Files) - ❌ REQUIRES TOOLS
- 5 DEX files totaling ~24MB of compiled bytecode
- Can be decompiled with tools like jadx, dex2jar
- Code will be obfuscated without ProGuard mapping

### 4.2 Native Libraries - ❌ NOT INCLUDED
- Split APK architecture - native libs in separate APKs
- Missing: `libflutter.so`, game SDKs, audio processing libs

### 4.3 Server-Side Logic - ❌ NOT ACCESSIBLE
- API implementations
- Real-time messaging protocol details
- User authentication verification
- Anti-cheat mechanisms

### 4.4 Firebase/Backend Configuration - ❌ SENSITIVE DATA
- API keys may be present but shouldn't be used
- Server URLs and endpoints need reverse engineering

---

## 5. Activity/Screen Inventory

### Authentication Screens
| Activity | Purpose |
|----------|---------|
| SplashActivity | App launch/splash screen |
| BindPhoneActivity | Phone number binding |
| BindAccountActivity | Social account linking |
| ActivityDeleteAccount | Account deletion |

### Main Features
| Activity | Purpose |
|----------|---------|
| TabMainActivity | Main navigation tabs |
| RoomActivity | Voice chat room |
| ActivityChat | 1-on-1 messaging |
| ActivityMessageList | Message list |

### Monetization
| Activity | Purpose |
|----------|---------|
| WalletActivity | Virtual currency wallet |
| RechargeRecordActivity | Purchase history |
| WithdrawExchangeActivity | Currency conversion |

### VIP Features
| Activity | Purpose |
|----------|---------|
| ActivitySvipSettings | VIP configuration |
| ActivitySvipCustomTheme | Custom theme creation |

### Entertainment
| Activity | Purpose |
|----------|---------|
| Cinema screens | Watch videos together |
| Teen Patti views | Card game |
| Game views | Mini-game containers |

---

## 6. Recommendations for Reverse Engineering

### Phase 1: UI Reconstruction
1. Use XML layouts to build screen mockups
2. Extract all drawable resources for visual assets
3. Map navigation flow from activity declarations

### Phase 2: Data Model Analysis
1. Decompile DEX files using jadx
2. Focus on `com.funny.yari` package
3. Extract API call patterns

### Phase 3: Game Extraction
1. Games are standalone HTML5 - can run directly
2. Host games on local server
3. Analyze JavaScript for game logic

### Phase 4: Flutter Analysis
1. Use Flutter reverse engineering tools
2. Extract Dart AOT compiled code if needed
3. Analyze `lib/` structure for app logic

---

## 7. Third-Party SDK Integration

| SDK | Purpose |
|-----|---------|
| Firebase | Analytics, Crashlytics, Messaging |
| Facebook SDK | Login, sharing |
| Google Play Billing | In-app purchases |
| Google Sign-In | Authentication |
| AppsFlyer | Attribution/analytics |
| AWS S3 | File storage/upload |
| OkHttp3 | Network requests |
| Glide | Image loading |
| PAG | Animation rendering |
| UCrop | Image cropping |

---

## 8. File Statistics

| Category | Count/Size |
|----------|------------|
| Layout XML files | 280+ |
| Drawable resources | 400+ files, 14MB |
| DEX files | 5 files, ~24MB |
| Flutter assets | 200+ files |
| Mini-game bundles | 5 games |
| Animation files | 50+ (SVGA, Lottie, MP4) |

---

## 9. Conclusion

This extracted APK provides **excellent resources** for understanding and potentially recreating the UI and user experience of the Yari app. The most valuable assets are:

1. **Complete UI layouts** - Can rebuild all screens
2. **Visual assets** - All icons, images, and animations
3. **Mini-games** - Fully functional JavaScript games
4. **Data models** - Understanding of app data structures

For full functionality reverse engineering, additional DEX decompilation and network traffic analysis would be needed.

---

*Report generated: December 2024*
