# Technical Documentation - Plated

Plated is not just a recipe manager; it is a **local-first ingestion engine** for the culinary web. Built with Expo and React Native, it leverages **Gemini 2.0 Flash** to parse unstructured media (TikToks, Reels, long-form text) into structured, chemically viable JSON data.

## 🚀 Tech Stack

### Core Framework

* **Expo SDK 54 & React Native 0.81:** The foundation for a high-performance, cross-platform experience.
* **Expo Router v6:** Handles file-based routing and deep linking, critical for the **Share Intent** flow.
* **TypeScript:** Strictly typed throughout to ensure reliability when handling AI-generated schemas.

### AI & Intelligence Layer

* **Google Gemini 2.0 Flash:** We chose Flash for its massive context window and low latency. It powers the core extraction pipeline and the "Chef Brain" behind Magic Swap.
* **Supadata:** Handles the raw retrieval of video transcripts and metadata from social platforms (TikTok/IG) before passing context to Gemini.

### State & Persistence (Offline First)

* **Zustand v5:** Global state management for recipes and user journey.
* **MMKV:** We replaced standard Async Storage with MMKV for synchronous, high-performance storage. This ensures the app works perfectly offline—critical for users in grocery stores with poor signal.

### UI & Interaction

* **HeroUI Native:** Provides the "Chef-Approved" aesthetic.
* **React Native Reanimated v4:** Powers the fluid "Pull-to-Create" and "Magic Swap" micro-interactions.
* **TailwindCSS v4 (via Uniwind):** For rapid, utility-first styling.

---

## 🏗️ Architecture

Plated follows a **Local-First, Cloud-Assisted** architecture. The heavy lifting (AI processing) happens in the cloud, but the data lives on the device.

### The "Ingestion Pipeline" (Video-to-JSON)

The core technical challenge was converting "influencer chatter" into actionable data.

1. **Ingestion:** User triggers `expo-share-intent` directly from TikTok.
2. **Extraction:** `Supadata` fetches the raw transcript and thumbnail.
3. **Normalization (The AI Core):** We stream the transcript to **Gemini 2.0 Flash** with a strict **JSON Schema**. This forces the LLM to separate storytelling from instruction, normalize units (e.g., "a pinch" -> "1g"), and structure the data.
4. **Hydration:** The resulting JSON is hydrated into the local Zustand store, making it instantly editable and searchable via **Fuse.js**.

### Magic Swap Logic

Magic Swap isn't just text replacement; it's a semantic transformation.

* **Input:** Current Recipe JSON + User Prompt (e.g., "Make this Aldi-friendly").
* **Processing:** Gemini analyzes the chemical role of ingredients (binding vs. flavoring) to generate a chemically viable substitute, rewriting the step-by-step instructions to match.

---

## 💰 RevenueCat Implementation

We use RevenueCat to power **Plated Pro**, gating the high-compute AI features.

### Implementation Strategy

We chose a **Hybrid Gating** model to maximize conversion:

1. **Hook (Free):** Users get 5 "Magic Scans" stored locally.
2. **Gate (Pro):** Once the local counter hits 5, or if the user attempts a "Magic Swap," we check the `pro` entitlement.

### Integration Details

* **Service Layer:** A singleton `RevenueCatService` initializes the SDK and handles the `Purchases.configure()` logic on launch.
* **Reactive State:** We use a custom `usePro` hook that listens to the `customerInfoUpdate` listener. This ensures that if a user buys Pro, the "Magic Swap" UI unlocks instantly without a reload.
* **Paywall:** We leverage RevenueCat's **Native Paywalls** for a polished, native purchase experience that supports Monthly, Yearly, and Lifetime tiers.
