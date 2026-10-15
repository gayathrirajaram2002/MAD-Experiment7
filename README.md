# Mobile Application Development Lab: Adaptive UI using ListView and ImageView

**Student Name:** Gayathri R  
**USN:** 25MCAR0093  
**Course:** Master of Computer Applications (MCA)  
**Platform:** Android (Kotlin)  
**IDE:** Android Studio  

---

## 1. Overview and Problem Statement

This experiment demonstrates the design and implementation of an **Adaptive User Interface (UI)** on Android utilizing `ListView` and `ImageView` components. Mobile devices present diverse display characteristics—varying from compact smartphone screens in portrait orientation to expanded tablet displays or widescreen landscape modes. 

A static single-column list provides suboptimal user experience on wider surfaces, wasting horizontal real estate. Conversely, forcing multi-pane views onto narrow screens leads to clutter and reduced readability. This project implements a dynamic layout configuration strategy that natively detects configuration changes and transitions smoothly between:
1. **Single-Pane View (Portrait):** A fluid, scrollable list displaying icons, titles, and descriptive subtitles.
2. **Master-Detail Dual-Pane View (Landscape):** A two-column interface hosting the list on the left pane ($40\%$ width) and a high-resolution contextual preview `ImageView` on the right pane ($60\%$ width).

---

## 2. Core Concepts and Technologies

### 2.1 Configuration Qualifiers & Adaptive Resource Splitting
Android resolves resources at runtime using qualifier directories based on device configuration:
- `res/layout/activity_main.xml`: Serves default screen orientations (Portrait). It encapsulates a full-width `ListView`.
- `res/layout-land/activity_main.xml`: Serves landscape configurations. It arranges a split layout utilizing `LinearLayout` weighted distributions (`layout_weight="4"` and `layout_weight="6"`), providing concurrent list navigation and detailed image inspection.

### 2.2 ViewHolder Pattern for Memory Efficiency
Populating repetitive UI components inside `ListView` through standard `findViewById()` invocations incurs significant overhead during high-velocity scrolling. The custom `AdaptiveListAdapter` leverages an explicit `ViewHolder` static class pattern to cache view references within the recycled view hierarchy (`convertView.tag`), ensuring zero frame drops and smooth 60fps scrolling.

### 2.3 Aspect Ratio Preservation with `scaleType`
- **Thumbnail Images (`item_row.xml`):** Bound with `scaleType="centerCrop"` to ensure uniformity across arbitrary aspect-ratio inputs without distorting dimensions.
- **Preview Detail Image (`detailImageView`):** Configured with `scaleType="fitCenter"` to preserve original image boundaries and prevent accidental clipping when scaling up on wide screens.

---

## 3. Project Directory and File Structure

```text
AdaptiveListApp/
├── app/
│   ├── manifests/
│   │   └── AndroidManifest.xml
│   ├── kotlin+java/
│   │   └── com/example/adaptivelistapp/
│   │       ├── AdaptiveListAdapter.kt    # Custom BaseAdapter with ViewHolder pattern
│   │       ├── ItemModel.kt              # POJO/Data class holding row item attributes
│   │       └── MainActivity.kt           # Lifecycle management, view binding & event dispatch
│   └── res/
│       ├── layout/
│       │   ├── activity_main.xml         # Portrait layout (Single-pane ListView)
│       │   └── item_row.xml              # Reusable row template for ListView items
│       ├── layout-land/
│       │   └── activity_main.xml         # Landscape adaptive layout (Master-Detail dual-pane)
│       └── values/
│           ├── colors.xml                # Color tokens preventing dark mode inversion bugs
│           ├── strings.xml               # UI string constants and accessibility descriptions
│           └── themes.xml                # Application theme definition
├── build.gradle.kts (Module :app)        # Module build configurations (SDK 35/36, dependencies)
└── README.md                             # Lab documentation and verification report
```

---

## 4. Key Implementation Details

### Data Model (`ItemModel.kt`)
```kotlin
package com.example.adaptivelistapp

data class ItemModel(
    val title: String,
    val subtitle: String,
    val imageResId: Int
)
```

### Orientation Null-Safe Binding (`MainActivity.kt`)
```kotlin
val detailImageView: ImageView? = findViewById(R.id.detailImageView)

// Graceful null handling: Updates preview only when landscape layout is active
listView.setOnItemClickListener { _, _, position, _ ->
    val selectedItem = sampleData[position]
    detailImageView?.setImageResource(selectedItem.imageResId)
}
```

---

## 5. Main Application Output

![Main Output](screenshots/output_main.png)
*Figure 1: Application running in standard portrait execution mode.*

---

## 6. Test Cases and Verification

### Test Case 1: Verification of Identity and Launch Execution
* **Objective:** Verify initial application state, basic `ListView` loading, and confirm student authentication attributes.
* **Input / Action:** Launch application on an Android Virtual Device (AVD).
* **Expected Result:** Header or active activity displays student credentials (**Gayathri R**, **USN: 25MCAR0093**), and the `ListView` populates the dataset without null references or black-screen artifacts.
* **Status:** Passed.




![Screenshot](screenshots/.png)


---

## 7. Instructions for Execution

1. Clone this repository:
   ```bash
   git clone https://github.com/<your-username>/AdaptiveListApp.git
   ```
2. Open the project folder in **Android Studio**.
3. Allow Gradle to sync dependencies (`build.gradle.kts`).
4. Ensure target device/emulator runs on **API Level 24** or higher.
5. Click **Run ▶** to deploy.
