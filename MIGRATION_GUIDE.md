# Yari → Aura Voice Chat Migration Guide

This guide details how to integrate features, UI components, and assets from the Yari APK into your Aura Voice Chat app.

---

## Overview: App Comparison

| Aspect | Yari (Source) | Aura (Target) |
|--------|---------------|---------------|
| **Package** | com.funny.yari | com.aura.voicechat |
| **Tech Stack** | Flutter + Android Native | Kotlin + Jetpack Compose |
| **Theme** | Yellow (#FFC000, #FFE601) | Purple (#C9A8F1, #D958FF) |
| **Games** | Cocos Creator HTML5 | Native Kotlin (planned) |
| **Backend** | Unknown server | Your own Node.js/Prisma |

**Good News:** Aura is already native Android Kotlin with Jetpack Compose, so you won't need to "remove Flutter" - you'll be adding Yari's features to your existing native codebase.

---

## Phase 1: Asset Migration (Easiest)

### 1.1 Drawable Resources to Copy

Copy from Yari's `res/drawable-xxhdpi-v4/` to Aura's `res/drawable/`:

| Yari File | Aura Rename | Color Change Needed |
|-----------|-------------|---------------------|
| `bean.png` | `ic_coin_yari.png` | Yes → Gold/Purple |
| `admin.png` | `ic_admin.png` | Yes → Purple |
| `gift_*.png` | `gift_*.png` | No |
| `level_*.png` | `level_*.png` | No |
| `cinema_*.png` | `cinema_*.png` | Slight purple tint |
| `treasure_*.png` | `treasure_*.png` | No |

**Script to copy drawables:**
```bash
# From yari-clone repo root
cp res/drawable-xxhdpi-v4/bean.png /path/to/aura/app/src/main/res/drawable/
cp res/drawable-xxhdpi-v4/box_*.png /path/to/aura/app/src/main/res/drawable/
cp res/drawable-xxhdpi-v4/gift_*.png /path/to/aura/app/src/main/res/drawable/
cp res/drawable-xxhdpi-v4/level_*.webp /path/to/aura/app/src/main/res/drawable/
```

### 1.2 Animation Files to Copy

Copy from `assests/` to Aura's `assets/`:

| File Type | Yari Path | Use Case |
|-----------|-----------|----------|
| SVGA | `assests/*.svga` | Gift animations |
| Lottie | `assests/flutter_assets/assets/images/lottie/*.json` | Loading, effects |
| MP4 | `assests/*.mp4` | VIP level-up, treasure |

**Key animation files:**
```
calculator_entrance.svga      → Game entrance effect
gift_icon.svga                → Gift sending animation
level_up_bg.svga              → Level up celebration
red_bag_icon.svga             → Red envelope (event feature)
svip_level_up_anim.mp4        → VIP level up video
treasure_open_*.mp4           → Treasure box reveals
```

### 1.3 Flutter Images to Convert

The Flutter PNG images can be directly used in Compose:
```
assests/flutter_assets/assets/images/login/    → Auth screens
assests/flutter_assets/assets/images/home/     → Home screen
assests/flutter_assets/assets/images/me/       → Profile screen
assests/flutter_assets/assets/images/family/   → Family screen
```

---

## Phase 2: Layout Conversion (XML → Compose)

### 2.1 Room Screen (Highest Priority)

**Yari XML files:**
- `res/layout/room_activity.xml` - Main room layout
- `res/layout/room_microphone.xml` - Mic seats
- `res/layout/room_bottom_bar.xml` - Bottom controls
- `res/layout/live_room_cinema.xml` - Video mode

**Convert to Compose for:** `ui/room/RoomScreen.kt`

**Example conversion:**

Yari XML (room_microphone.xml):
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">
    <!-- 8 microphone seats -->
</LinearLayout>
```

Aura Compose equivalent:
```kotlin
@Composable
fun MicrophoneSeats(seats: List<MicSeat>) {
    LazyVerticalGrid(
        columns = GridCells.Fixed(4),
        modifier = Modifier.fillMaxWidth()
    ) {
        items(seats) { seat ->
            MicrophoneSeatItem(
                seat = seat,
                modifier = Modifier
                    .aspectRatio(1f)
                    .padding(4.dp)
            )
        }
    }
}

@Composable
fun MicrophoneSeatItem(seat: MicSeat) {
    Box(
        modifier = Modifier
            .clip(CircleShape)
            .background(
                brush = Brush.verticalGradient(
                    colors = listOf(Purple80, Purple40)
                )
            )
    ) {
        if (seat.user != null) {
            AsyncImage(
                model = seat.user.avatarUrl,
                contentDescription = seat.user.name
            )
        } else {
            Icon(
                imageVector = Icons.Default.Add,
                tint = AccentCyan
            )
        }
    }
}
```

### 2.2 Key Layouts to Convert

| Yari Layout | Aura Screen | Priority |
|-------------|-------------|----------|
| `room_activity.xml` | `RoomScreen.kt` | HIGH |
| `activity_chat.xml` | `MessageScreen.kt` (exists) | MEDIUM |
| `fragment_gift_list.xml` | `GiftSheet.kt` | HIGH |
| `dialog_gift_pop.xml` | `GiftPopup.kt` | HIGH |
| `activity_wallet.xml` | `WalletScreen.kt` (exists) | MEDIUM |
| `live_room_cinema.xml` | `CinemaMode.kt` | MEDIUM |
| `dialog_red_bag.xml` | `RedEnvelopeDialog.kt` | LOW |
| `teen_patti_play_view.xml` | `TeenPattiScreen.kt` | LOW |

---

## Phase 3: Game Integration

### 3.1 Option A: Embed HTML5 Games (Easiest)

Use WebView to load Yari's Cocos games directly:

```kotlin
// In GamesScreen.kt
@Composable
fun CocosGameWebView(gameId: String) {
    val context = LocalContext.current
    
    AndroidView(
        factory = { ctx ->
            WebView(ctx).apply {
                settings.javaScriptEnabled = true
                settings.domStorageEnabled = true
                loadUrl("file:///android_asset/games/$gameId/index.html")
            }
        }
    )
}
```

**Copy games to:** `app/src/main/assets/games/`
```
games/
├── lucky777/          (from fe-game-lucky777-yr)
├── super77/           (from fe-game-super77-yr)
├── crazy-fruit/       (from fe-game-crazy-fruit-yr)
├── greedy/            (from fe-game-greedy-yr)
└── cocos-runtime/     (from fe-game-cocos-js-yr)
```

### 3.2 Option B: Rebuild Games Natively (Better UX)

Aura already has native game screens:
- `Lucky777Screen.kt`
- `Lucky77ProScreen.kt`
- `LuckyFruitScreen.kt`
- `GreedyBabyScreen.kt`
- `GiftWheelScreen.kt`

Use Yari's games as **visual reference** to improve your existing implementations.

---

## Phase 4: Feature Mapping

### Features Aura Already Has ✅

| Feature | Aura Location | Yari Equivalent |
|---------|---------------|-----------------|
| VIP/SVIP | `ui/vip/` | Same concept |
| Wallet (Coins/Diamonds) | `ui/wallet/` | Same concept |
| CP System | `ui/cp/` | Same concept |
| Family System | `ui/family/` | Same concept |
| Games | `ui/games/` | Similar games |
| Daily Rewards | `ui/dailyreward/` | Same concept |
| Medals | `ui/medals/` | Same concept |
| Referrals | `ui/referral/` | Same concept |
| Store | `ui/store/` | Same concept |

### Features to Add from Yari

| Feature | Yari Source | New Aura Location |
|---------|-------------|-------------------|
| Cinema Mode | `live_room_cinema.xml` | `ui/room/CinemaMode.kt` |
| Red Envelope | `dialog_red_bag.xml` | `ui/events/RedEnvelopeScreen.kt` |
| Teen Patti | `teen_patti_*.xml` | `ui/games/TeenPattiScreen.kt` |
| Treasure Box | `dialog_treasure_*.xml` | `ui/events/TreasureScreen.kt` |
| Custom Gift Upload | `activity_upload_custom_gift.xml` | `ui/gifts/CustomGiftScreen.kt` |
| PK Battle | `dialog_pk_ending.xml` | `ui/room/PKBattleScreen.kt` |
| Room Lock | `dialog_lock_room_pwd.xml` | `ui/room/RoomLockDialog.kt` |

---

## Phase 5: Color Theme Conversion

### 5.1 Color Mapping

| Yari Color | Hex | Aura Replacement | Hex |
|------------|-----|------------------|-----|
| Primary Yellow | #FFC000 | Purple80 | #C9A8F1 |
| Bright Yellow | #FFE601 | AccentMagenta | #D958FF |
| Gold | #FECC35 | AccentGold | #FFD700 |
| Green accent | #1BD9B3 | AccentCyan | #35E8FF |
| Dark BG | #1E1E23 | DarkCanvas | #12141A |

### 5.2 Gradient Conversion

**Yari gradients:**
```kotlin
// Yellow gradient (Yari)
Brush.linearGradient(
    colors = listOf(Color(0xFFFFEF3D), Color(0xFFFFCC02))
)
```

**Aura equivalent:**
```kotlin
// Purple gradient (Aura)
Brush.linearGradient(
    colors = listOf(GradientPurpleStart, GradientPurpleEnd)
)

// Or for buttons:
Brush.linearGradient(
    colors = listOf(Purple80, AccentMagenta)
)
```

---

## Phase 6: Admin/Panel Features

### Aura's Existing Panels (Keep These)

Your app already has these systems documented:
- **Owner CMS** (`docs/owner-cms.md`)
- **Admin Panel** (`docs/admin-panel.md`)
- **Reseller System** (`docs/reseller-system.md`)
- **Country Admin** (via Owner CMS)

### Yari Panel Features to Consider

From Yari's manifest and layouts, they have:
- Room settings management
- User blocking system
- Kick history
- Custom gift moderation
- Report handling

These are likely **similar to what you already have** in your Owner CMS.

---

## Implementation Priority

### Week 1: Quick Wins
1. ✅ Copy drawable assets (icons, backgrounds)
2. ✅ Copy animation files (SVGA, Lottie)
3. ✅ Embed HTML5 games via WebView

### Week 2: Room Enhancements
1. Add Cinema Mode to Room
2. Implement Treasure Box feature
3. Add Red Envelope for events

### Week 3: Visual Polish
1. Convert key Yari layouts to Compose
2. Apply consistent purple theme to new features
3. Add gift animations

### Week 4: Advanced Features
1. Teen Patti game (if needed)
2. PK Battle system
3. Custom gift upload

---

## File Inventory: What to Copy

### From `res/drawable-xxhdpi-v4/` (Copy ~50 files)
```
bean.png, diamond.png, coin.png
gift_*.png (all gift icons)
box_*.png (treasure boxes)
level_*.png (level badges)
badge_*.png (honor badges)
frame_*.png (avatar frames)
```

### From `assests/` (Copy ~30 files)
```
*.svga (all animation files)
*.mp4 (video animations)
flutter_assets/assets/images/lottie/*.json
```

### From `assests/fe-game-*` (Copy 4 game folders)
```
fe-game-lucky777-yr/
fe-game-super77-yr/
fe-game-crazy-fruit-yr/
fe-game-greedy-yr/
```

---

## Summary

**What's possible:** YES, you can integrate most of Yari's features into Aura.

**What's already done:** Aura already has 80%+ of the same features (VIP, wallet, CP, family, games, etc.)

**Main additions needed:**
1. Cinema mode for rooms
2. Treasure/Lucky draw events
3. Visual assets (icons, animations)
4. Some UI polish borrowing Yari's layouts

**Biggest advantage:** Your app is already native Kotlin/Compose, so no Flutter removal needed. Just add features and assets.

---

*Generated for Aura Voice Chat by Hawkaye Visions LTD*
