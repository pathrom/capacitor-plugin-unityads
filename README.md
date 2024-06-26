# capacitor-plugin-unityads

Unity ADS Integration for Capacitor & Ionic Projects.

## ⚠ NOTE

This plugin is in beta. Currently it only supports interstitial ads but rewarded ads and banner ads are planned.

Also, IOS support is still under development.

## Install

```bash
npm install @pathrom/capacitor-plugin-unityads # (or yarn, pnpm)
npx cap sync
```

## Table of Contents

- [capacitor-plugin-unityads](#capacitor-plugin-unityads)
  - [⚠ NOTE](#-note)
  - [Install](#install)
  - [Table of Contents](#table-of-contents)
  - [Basic Example with Svelte](#basic-example-with-svelte)
  - [API](#api)
    - [initAds(...)](#initads)
    - [loadAds(...)](#loadads)
    - [displayAd()](#displayad)
  - [Events](#events)

## Basic Example with Svelte

```html
<script>
  import { onMount } from "svelte";
  import { UnityAds } from "unity-ads-capacitor";

  let ready = false;
  let message = "";
  let isSkipped;

  const unityGameId = "YOUR_GAME_ID";
  const testMode = true; //change this to false in production!
  const adUnitId = "YOUR_AD_UNIT_AD";

  onMount(() => {
    UnityAds.addListener("initialized", () => {
      message = "initialized";
      UnityAds.loadAds({
        adUnitId,
      });
    });

    UnityAds.addListener("initializationError", ({ error }) => {
      message = "initializationError: " + error;
    });

    UnityAds.addListener("adShown", ({ state }) => {
      message = "ad completed state: " + state; // SKIPPED or COMPLETED
      UnityAds.loadAds({
        adUnitId,
      });
    });

    UnityAds.addListener("adShowError", ({ error }) => {
      message = "adShowError: " + error;
    });

    UnityAds.addListener("adLoaded", () => {
      message = "ad loaded";
      ready = true;
    });

    UnityAds.addListener("adLoadError", ({ error }) => {
      message = "adLoadError: " + error;
    });

    UnityAds.initAds({
      unityGameId,
      testMode,
    });
  });

  const handleClick = () => {
    UnityAds.loadAds({
      adUnitId,
    });

    UnityAds.displayAd();
  };
</script>

<div>
  {#if ready}
  <button on:click="{handleClick}">Show ad</button>
  {:else}
  <p>not ready!</p>
  {/if}

  <p>{message}</p>
</div>
```

## API

### initAds(...)

```typescript
initAds(options: { unityGameId: string; testMode: boolean; }) => void
```

---

### loadAds(...)

```typescript
loadAds(options: { adUnitId: string; }) => void
```

---

### displayAd()

```typescript
displayAd() => void
```

---

## Events

| Event                 | Returns              | Description                                  |
| --------------------- | -------------------- | -------------------------------------------- |
| `initialized`         | `null`               | Fires when Unity SDK is initialized.         |
| `adLoaded`            | `null`               | Fires when AD is loaded.                     |
| `adShowStart`         | `null`               | Fires when AD starts showing.                |
| `adShowClick`         | `null`               | Fires when user clicks the AD.               |
| `adShown`             | `{ state: "SKIPPED" \| "COMPLETED"; }` | Fires when AD is completed or skipped.                  |
| `initializationError` | `{ error: string; }` | Fires when error occurs in initialization.   |
| `adLoadError`         | `{ error: string; }` | Fires when error occurs while loading an AD. |
| `adShowError`         | `{ error: string; }` | Fires when error occurs while showing an AD. |