# Aura Voice Chat - Complete Project Blueprint

> **Goal:** Transform Aura Voice Chat into a complete Yari clone with purple theme, using Option A (update auravoicechatdoc repo)

## Project Overview

| Property | Value |
|----------|-------|
| **Target Repo** | github.com/venomvex/auravoicechatdoc |
| **Source Reference** | Yari APK (this repo) |
| **Tech Stack** | Kotlin + Jetpack Compose |
| **Min SDK** | 24 (Android 7.0) |
| **Backend** | AWS (Cognito, S3, EC2) - Already configured |
| **Auth** | Google + Facebook Sign-In (Already setup) |

---

## Phase 1: Foundation Setup (Day 1)

### 1.1 Update build.gradle

```kotlin
// android/app/build.gradle.kts
android {
    compileSdk = 35
    
    defaultConfig {
        applicationId = "com.aura.voicechat"
        minSdk = 24  // Android 7.0
        targetSdk = 35
        versionCode = 1
        versionName = "1.0.0"
    }
}
```

### 1.2 Dependencies to Add

```kotlin
dependencies {
    // Animation libraries
    implementation("com.github.AnylifeDev:svgaplayer-android:2.6.1")  // SVGA animations
    implementation("com.airbnb.android:lottie-compose:6.3.0")         // Lottie
    
    // WebView for games
    implementation("androidx.webkit:webkit:1.9.0")
    
    // Voice/Video SDK (choose one)
    implementation("io.agora.rtc:full-sdk:4.2.6")  // Agora
    // OR
    implementation("io.livekit:livekit-android:1.5.0")  // LiveKit
    
    // Image loading
    implementation("io.coil-kt:coil-compose:2.5.0")
    implementation("io.coil-kt:coil-gif:2.5.0")
    
    // AWS
    implementation("aws.sdk.kotlin:cognitoidentityprovider:1.0.0")
    implementation("aws.sdk.kotlin:s3:1.0.0")
}
```

### 1.3 Copy Assets from Yari

Create script to copy assets:

```bash
# From yari-clone repo
# 1. Drawable icons (recolor yellow to purple)
cp res/drawable-xxhdpi-v4/*.png ../auravoicechatdoc/android/app/src/main/res/drawable/

# 2. SVGA animations
mkdir -p ../auravoicechatdoc/android/app/src/main/assets/svga
cp assests/*.svga ../auravoicechatdoc/android/app/src/main/assets/svga/

# 3. Lottie animations
mkdir -p ../auravoicechatdoc/android/app/src/main/assets/lottie
cp assests/flutter_assets/assets/images/lottie/*.json ../auravoicechatdoc/android/app/src/main/assets/lottie/

# 4. MP4 videos
mkdir -p ../auravoicechatdoc/android/app/src/main/assets/videos
cp assests/*.mp4 ../auravoicechatdoc/android/app/src/main/assets/videos/

# 5. Games (HTML5)
mkdir -p ../auravoicechatdoc/android/app/src/main/assets/games
cp -r assests/fe-game-lucky777-yr ../auravoicechatdoc/android/app/src/main/assets/games/lucky777
cp -r assests/fe-game-super77-yr ../auravoicechatdoc/android/app/src/main/assets/games/super77
cp -r assests/fe-game-crazy-fruit-yr ../auravoicechatdoc/android/app/src/main/assets/games/crazyfruit
cp -r assests/fe-game-greedy-yr ../auravoicechatdoc/android/app/src/main/assets/games/greedy
```

---

## Phase 2: Screen Implementation Plan

### 2.1 Existing Aura Screens (111 Kotlin files) → ENHANCE

| Aura Screen | Status | Yari Reference Layout | Action |
|-------------|--------|----------------------|--------|
| `LoginScreen.kt` | ✅ Exists | `activity_launch.xml` | Redesign UI |
| `RoomScreen.kt` | ✅ Exists | `room_activity.xml` | Major redesign |
| `WalletScreen.kt` | ✅ Exists | `wallet_activity.xml` | Redesign UI |
| `VipScreen.kt` | ✅ Exists | `activity_svip_settings.xml` | Redesign UI |
| `GamesScreen.kt` | ✅ Exists | `live_room_game_view.xml` | Add WebView games |
| `FamilyScreen.kt` | ✅ Exists | Flutter assets | Redesign UI |
| `CpScreen.kt` | ✅ Exists | `dialog_become_cp.xml` | Redesign UI |
| `MedalsScreen.kt` | ✅ Exists | `dialog_honor_medal.xml` | Redesign UI |
| `ProfileScreen.kt` | ✅ Exists | Flutter assets | Redesign UI |
| `MessagesScreen.kt` | ✅ Exists | `activity_chat.xml` | Redesign UI |

### 2.2 New Screens to Create

| Screen | Yari Reference | Priority | Backend Endpoint |
|--------|---------------|----------|------------------|
| `CinemaScreen.kt` | `live_room_cinema.xml` | HIGH | `/rooms/{id}/cinema` |
| `TreasureBoxScreen.kt` | `dialog_treasure_*.xml` | HIGH | `/games/treasure` |
| `RedEnvelopeScreen.kt` | `dialog_red_bag.xml` | MEDIUM | `/events/redenvelope` |
| `TeenPattiScreen.kt` | `teen_patti_*.xml` | MEDIUM | `/games/teenpatti` |
| `PKBattleScreen.kt` | `dialog_pk_ending.xml` | MEDIUM | `/rooms/{id}/pk` |
| `CustomGiftScreen.kt` | `activity_upload_custom_gift.xml` | LOW | `/gifts/custom` |
| `RoomLockScreen.kt` | `dialog_lock_room_pwd.xml` | LOW | `/rooms/{id}/lock` |
| `BlockListScreen.kt` | `block_list_activity.xml` | LOW | `/users/blocked` |

---

## Phase 3: Complete File Structure

```
android/app/src/main/java/com/aura/voicechat/
├── AuraApplication.kt
├── MainActivity.kt
│
├── data/
│   ├── api/
│   │   ├── AuraApiService.kt           # Retrofit API definitions
│   │   ├── AuthApi.kt
│   │   ├── RoomApi.kt
│   │   ├── GiftApi.kt
│   │   ├── WalletApi.kt
│   │   └── UserApi.kt
│   ├── models/
│   │   ├── User.kt
│   │   ├── Room.kt
│   │   ├── Gift.kt
│   │   ├── Message.kt
│   │   ├── VipTier.kt
│   │   ├── Medal.kt
│   │   ├── Family.kt
│   │   └── CpPartner.kt
│   └── repository/
│       ├── AuthRepository.kt
│       ├── RoomRepository.kt
│       ├── GiftRepository.kt
│       ├── WalletRepository.kt
│       └── UserRepository.kt
│
├── di/
│   ├── NetworkModule.kt
│   ├── RepositoryModule.kt
│   └── ViewModelModule.kt
│
├── services/
│   ├── AuraPushNotificationService.kt
│   ├── VoiceRoomService.kt
│   └── MediaPlaybackService.kt
│
└── ui/
    ├── navigation/
    │   └── AuraNavGraph.kt
    │
    ├── theme/
    │   ├── Color.kt              # Purple theme
    │   ├── Theme.kt
    │   ├── Type.kt
    │   └── Shapes.kt
    │
    ├── components/              # Reusable components
    │   ├── AuraButton.kt
    │   ├── AuraCard.kt
    │   ├── AuraAvatar.kt
    │   ├── AuraMicSeat.kt
    │   ├── AuraGiftItem.kt
    │   ├── AuraLevelBadge.kt
    │   ├── AuraVipBadge.kt
    │   ├── SvgaPlayer.kt
    │   ├── LottieAnimation.kt
    │   └── WebGameView.kt
    │
    ├── auth/
    │   ├── LoginScreen.kt
    │   ├── LoginViewModel.kt
    │   ├── OtpVerificationScreen.kt
    │   ├── OtpVerificationViewModel.kt
    │   ├── RegisterScreen.kt
    │   └── RegisterViewModel.kt
    │
    ├── home/
    │   ├── HomeScreen.kt
    │   ├── HomeViewModel.kt
    │   ├── PopularTab.kt
    │   ├── MineTab.kt
    │   └── RoomCard.kt
    │
    ├── room/
    │   ├── RoomScreen.kt
    │   ├── RoomViewModel.kt
    │   ├── RoomActivity.kt
    │   ├── components/
    │   │   ├── MicrophoneSeats.kt
    │   │   ├── RoomHeader.kt
    │   │   ├── RoomChat.kt
    │   │   ├── RoomBottomBar.kt
    │   │   ├── GiftPanel.kt
    │   │   └── EmojiPanel.kt
    │   ├── cinema/
    │   │   ├── CinemaScreen.kt
    │   │   └── CinemaViewModel.kt
    │   ├── pk/
    │   │   ├── PKBattleScreen.kt
    │   │   └── PKBattleViewModel.kt
    │   └── settings/
    │       ├── RoomSettingsScreen.kt
    │       └── RoomLockDialog.kt
    │
    ├── games/
    │   ├── GamesScreen.kt
    │   ├── GamesViewModel.kt
    │   ├── Lucky777Screen.kt
    │   ├── Lucky77ProScreen.kt
    │   ├── LuckyFruitScreen.kt
    │   ├── GreedyBabyScreen.kt
    │   ├── GiftWheelScreen.kt
    │   ├── TeenPattiScreen.kt
    │   ├── TreasureBoxScreen.kt
    │   └── WebGameContainer.kt
    │
    ├── gifts/
    │   ├── GiftsScreen.kt
    │   ├── GiftsViewModel.kt
    │   ├── GiftDialog.kt
    │   ├── GiftAnimation.kt
    │   └── CustomGiftScreen.kt
    │
    ├── wallet/
    │   ├── WalletScreen.kt
    │   ├── WalletViewModel.kt
    │   ├── RechargeScreen.kt
    │   ├── WithdrawScreen.kt
    │   └── TransactionHistoryScreen.kt
    │
    ├── vip/
    │   ├── VipScreen.kt
    │   ├── VipViewModel.kt
    │   ├── VipBenefitsDialog.kt
    │   └── VipPurchaseDialog.kt
    │
    ├── profile/
    │   ├── ProfileScreen.kt
    │   ├── ProfileViewModel.kt
    │   ├── EditProfileScreen.kt
    │   ├── VisitorsScreen.kt
    │   └── FollowersScreen.kt
    │
    ├── messages/
    │   ├── MessagesScreen.kt
    │   ├── MessagesViewModel.kt
    │   ├── ChatScreen.kt
    │   ├── ChatViewModel.kt
    │   └── NotificationsScreen.kt
    │
    ├── social/
    │   ├── cp/
    │   │   ├── CpScreen.kt
    │   │   ├── CpViewModel.kt
    │   │   └── CpInviteDialog.kt
    │   ├── friends/
    │   │   ├── FriendsScreen.kt
    │   │   └── FriendsViewModel.kt
    │   └── family/
    │       ├── FamilyScreen.kt
    │       ├── FamilyViewModel.kt
    │       └── CreateFamilyDialog.kt
    │
    ├── events/
    │   ├── EventsScreen.kt
    │   ├── EventsViewModel.kt
    │   ├── RedEnvelopeScreen.kt
    │   └── DailyRewardsDialog.kt
    │
    ├── medals/
    │   ├── MedalsScreen.kt
    │   ├── MedalsViewModel.kt
    │   └── MedalDetailDialog.kt
    │
    ├── store/
    │   ├── StoreScreen.kt
    │   ├── StoreViewModel.kt
    │   ├── FramesTab.kt
    │   ├── EffectsTab.kt
    │   └── ItemDetailDialog.kt
    │
    ├── level/
    │   ├── LevelScreen.kt
    │   └── LevelViewModel.kt
    │
    ├── referral/
    │   ├── ReferralScreen.kt
    │   ├── ReferralViewModel.kt
    │   ├── GetCoinsTab.kt
    │   └── GetCashTab.kt
    │
    ├── settings/
    │   ├── SettingsScreen.kt
    │   ├── SettingsViewModel.kt
    │   ├── AccountSettingsScreen.kt
    │   ├── PrivacySettingsScreen.kt
    │   └── AboutScreen.kt
    │
    ├── search/
    │   ├── SearchScreen.kt
    │   └── SearchViewModel.kt
    │
    ├── ranking/
    │   ├── RankingScreen.kt
    │   └── RankingViewModel.kt
    │
    └── kyc/
        ├── KycScreen.kt
        └── KycViewModel.kt
```

---

## Phase 4: Implementation Schedule

### Week 1: Core Foundation
| Day | Tasks |
|-----|-------|
| Day 1 | Update gradle, copy assets, setup theme |
| Day 2 | Redesign LoginScreen, RegisterScreen |
| Day 3 | Redesign HomeScreen with tabs |
| Day 4 | Implement RoomScreen skeleton |
| Day 5 | Add MicrophoneSeats, RoomChat |
| Day 6 | Integrate voice SDK (Agora/LiveKit) |
| Day 7 | Testing and bug fixes |

### Week 2: Social Features
| Day | Tasks |
|-----|-------|
| Day 8 | Redesign ProfileScreen |
| Day 9 | Implement MessagesScreen, ChatScreen |
| Day 10 | Add CpScreen, FriendsScreen |
| Day 11 | Implement FamilyScreen |
| Day 12 | Add GiftPanel with animations |
| Day 13 | SVGA/Lottie animation integration |
| Day 14 | Testing and bug fixes |

### Week 3: Monetization & Games
| Day | Tasks |
|-----|-------|
| Day 15 | Redesign WalletScreen |
| Day 16 | Implement VipScreen |
| Day 17 | Add GamesScreen with WebView |
| Day 18 | Implement TreasureBoxScreen |
| Day 19 | Add StoreScreen |
| Day 20 | Implement ReferralScreen |
| Day 21 | Testing and bug fixes |

### Week 4: Polish & Launch
| Day | Tasks |
|-----|-------|
| Day 22 | Add CinemaScreen |
| Day 23 | Implement MedalsScreen |
| Day 24 | Add EventsScreen, RedEnvelope |
| Day 25 | Settings, About screens |
| Day 26 | Full app testing |
| Day 27 | Bug fixes, optimization |
| Day 28 | Release preparation |

---

## Phase 5: API Endpoints Required

### Authentication
```
POST /auth/login/google
POST /auth/login/facebook  
POST /auth/login/phone
POST /auth/otp/send
POST /auth/otp/verify
POST /auth/register
POST /auth/logout
```

### Users
```
GET  /users/me
PUT  /users/me
GET  /users/{id}
GET  /users/{id}/profile
POST /users/follow/{id}
POST /users/unfollow/{id}
GET  /users/{id}/followers
GET  /users/{id}/following
GET  /users/search?q=
POST /users/block/{id}
GET  /users/blocked
```

### Rooms
```
GET  /rooms
GET  /rooms/popular
GET  /rooms/mine
POST /rooms/create
GET  /rooms/{id}
PUT  /rooms/{id}
DELETE /rooms/{id}
POST /rooms/{id}/join
POST /rooms/{id}/leave
POST /rooms/{id}/mic/{seatId}
POST /rooms/{id}/kick/{userId}
POST /rooms/{id}/lock
POST /rooms/{id}/unlock
GET  /rooms/{id}/messages
POST /rooms/{id}/messages
```

### Gifts
```
GET  /gifts
GET  /gifts/categories
POST /gifts/send
GET  /gifts/received
GET  /gifts/sent
POST /gifts/custom/upload
```

### Wallet
```
GET  /wallet/balance
POST /wallet/recharge
POST /wallet/withdraw
GET  /wallet/transactions
POST /wallet/exchange
```

### VIP
```
GET  /vip/status
GET  /vip/tiers
POST /vip/purchase
GET  /vip/benefits
```

### Social
```
GET  /cp/status
POST /cp/invite/{userId}
POST /cp/accept/{inviteId}
POST /cp/reject/{inviteId}
POST /cp/dissolve
GET  /friends
POST /friends/invite/{userId}
GET  /family
POST /family/create
POST /family/join/{familyId}
```

### Games
```
GET  /games
POST /games/play/{gameId}
GET  /games/history
POST /games/treasure/open
```

### Events
```
GET  /events/active
GET  /events/daily-rewards
POST /events/daily-rewards/claim
POST /events/redenvelope/send
POST /events/redenvelope/claim/{id}
```

### Medals
```
GET  /medals
GET  /medals/my
POST /medals/claim/{id}
```

### Store
```
GET  /store/items
GET  /store/categories
POST /store/purchase
GET  /store/inventory
```

### Rankings
```
GET  /rankings/users
GET  /rankings/rooms
GET  /rankings/gifts
GET  /rankings/cp
```

---

## Phase 6: Color Theme Mapping

### Yari Yellow → Aura Purple Conversion

| Element | Yari Color | Aura Color |
|---------|------------|------------|
| Primary | #FFC000 | #C9A8F1 |
| Primary Variant | #FFE601 | #D958FF |
| Accent | #FECC35 | #35E8FF |
| Gold/Coins | #FFD700 | #FFD700 (keep) |
| Background Dark | #1E1E23 | #12141A |
| Surface | #2A2A2F | #1A1C24 |
| Card | #333338 | #22242E |
| Success | #1BD9B3 | #4CAF50 |
| Error | #FF4D4D | #F44336 |
| Text Primary | #FFFFFF | #FFFFFF |
| Text Secondary | #B3B3B3 | #B3FFFFFF |

### Gradient Conversions

```kotlin
// Yari Yellow Gradient
val YariGradient = Brush.linearGradient(
    colors = listOf(Color(0xFFFFEF3D), Color(0xFFFFCC02))
)

// Aura Purple Gradient  
val AuraGradient = Brush.linearGradient(
    colors = listOf(Color(0xFFC9A8F1), Color(0xFFD958FF))
)

// Aura Glow Gradient (for buttons, highlights)
val AuraGlowGradient = Brush.linearGradient(
    colors = listOf(Color(0xFFD958FF), Color(0xFF35E8FF))
)
```

---

## Phase 7: Asset Inventory

### Drawable Icons to Copy (from res/drawable-xxhdpi-v4/)

**Room icons:**
- `admin.png`, `announce_edit_icon.png`
- `chat_mic_icon.png`, `chat_record_icon.png`
- `bottom_settings.png`, `bottom_profile_report.png`

**Gift icons:**
- `bean.png`, `diamond.png`
- `gift_*.png` (all gift images)
- `box_one.jpg`, `box_two.jpg`, `box_three.jpg`

**Level badges:**
- All level images from flutter assets

**Avatar frames:**
- Frame images from drawable

### SVGA Animations (from assests/)
```
calculator_entrance.svga      → Game entrance
cp_become_bg.svga            → CP formation
gift_icon.svga               → Gift sending
level_up_bg.svga             → Level up
red_bag_icon.svga            → Red envelope
svip_click_btn.svga          → VIP button
treasure_box_air_bottom_bg.svga → Treasure
```

### Lottie Animations (from flutter_assets/assets/images/lottie/)
```
around_volume.json           → Voice indicator
bottom_tip.json              → UI tips
```

### MP4 Videos (from assests/)
```
receive_daily_reward.mp4     → Daily reward animation
svip_level_up_anim.mp4       → VIP level up
svip_sigin.mp4               → VIP sign in
treasure_level_*.mp4         → Treasure animations
treasure_open_*.mp4          → Treasure opening
```

---

## Phase 8: Testing Checklist

### Authentication
- [ ] Google Sign-In works
- [ ] Facebook Sign-In works
- [ ] Phone OTP works
- [ ] Registration flow complete
- [ ] Session persistence works

### Room
- [ ] Room list loads
- [ ] Room creation works
- [ ] Join room works
- [ ] Mic seats work
- [ ] Voice transmission works
- [ ] Chat messages work
- [ ] Gift sending works
- [ ] Animations play

### Games
- [ ] Games list loads
- [ ] WebView games work
- [ ] Game results save
- [ ] Coins/diamonds update

### Wallet
- [ ] Balance displays
- [ ] Recharge works
- [ ] Withdraw works
- [ ] Transaction history shows

### Social
- [ ] Profile loads
- [ ] Follow/unfollow works
- [ ] CP system works
- [ ] Friends list works
- [ ] Family system works
- [ ] Messages work

---

## Getting Started Command

To begin implementation, run in the auravoicechatdoc repo:

```bash
# 1. Create new branch
git checkout -b feature/yari-ui-overhaul

# 2. Copy Yari assets
./scripts/copy-yari-assets.sh

# 3. Update dependencies
./gradlew clean build

# 4. Start with auth screens
# Edit: android/app/src/main/java/com/aura/voicechat/ui/auth/LoginScreen.kt
```

---

## Summary

This blueprint provides a complete roadmap for transforming Aura Voice Chat into a Yari clone with:

✅ **111 existing Kotlin files** to enhance  
✅ **~20 new screens** to create  
✅ **445 Yari layouts** as reference  
✅ **All visual assets** (icons, animations, videos)  
✅ **5 HTML5 games** ready to embed  
✅ **4-week implementation timeline**

**Next Step:** Begin Phase 1 - Foundation Setup in the auravoicechatdoc repo.

---

*Blueprint created for Aura Voice Chat by Hawkaye Visions LTD*
