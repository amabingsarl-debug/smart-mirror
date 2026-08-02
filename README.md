# SMART MIRROR

SMART MIRROR is an AI virtual try-on progressive web app with a personal style studio and wardrobe. It creates a personal fashion avatar from profile choices and optional face/body references, then lets users see how garments look on them before buying, save looks, generate short presentation videos, and preview products from an internal e-commerce demo.

The project is designed for both consumer use and B2B e-commerce integration: a shop can add a "See on me" button so a customer can instantly visualize a selected product on their own avatar before buying.

## What it does

- Builds a style profile with gender, height range, body shape, usual clothing sizes, shoe size, and optional identity photos.
- Creates or selects a full-body avatar with front, side, and back views.
- Provides an internal shop where products can be opened with a "See on me" flow.
- Generates AI try-on images that preserve the user's face, skin tone, body proportions, garment details, color, material, and selected size.
- Saves generated outfits into a personal wardrobe/studio.
- Generates short 5-second vertical fashion videos from saved avatar or outfit views.
- Supports mobile-first PWA installation, offline shell/media caching, and multilingual UI foundations.
- Stores generated media, manifests, and provenance data on Backblaze B2.

## Backblaze B2 and Genblaze usage

Backblaze B2 is the durable media layer for the project. Identity references, garment inputs, generated avatar images, try-on outputs, videos, task files, and manifests are stored in B2 with signed delivery URLs.

Genblaze is used to orchestrate the generative media pipeline and connect the app's AI generation steps with B2 storage. The backend keeps hashes and manifests so generated outputs can be traced back to their inputs.

## AI pipeline

- `functions/main.py` exposes Firebase HTTPS functions for identity upload, avatar/try-on image generation, and video generation status.
- `generate_tryon` accepts avatar/profile data and garment references, uploads inputs to B2, calls the image generation pipeline, and returns multi-view outputs.
- `generate_kling_video` and `get_kling_video_status` create a short turntable-style outfit video from generated views, then store the final MP4 in B2.
- YouCam skin analysis is used to help preserve skin tone and face identity.
- Gemini image generation is used through the Genblaze-backed pipeline for avatar and garment rendering.

## Tech stack

- React 19, Next 16, Vinext
- Firebase Hosting, Firebase Functions, Firebase Auth
- Python 3.12 backend functions
- Backblaze B2 via `genblaze-s3`
- Genblaze pipeline orchestration
- Google Gemini image generation
- Perfect Corp YouCam API for face and skin context
- Kling video generation for short outfit videos
- Three.js, React Three Fiber, Drei, and Avaturn SDK foundations for 3D/avatar experiences

## Local setup

Requirements:

- Node.js 22.13 or newer
- Python 3.12 for Firebase Functions
- Firebase CLI
- Backblaze B2 credentials
- Genblaze-compatible image pipeline credentials
- YouCam and video generation credentials where available

Install and run the web app:

```bash
npm install
npm run dev
```

Build the production app:

```bash
npm run build
```

Install backend dependencies:

```bash
python -m pip install -r functions/requirements.txt
```

## Environment variables

Secrets are read from local environment files or Firebase Functions secrets and must not be committed to GitHub.

Expected backend secrets include:

- `GEMINI_API_KEY`
- `YOUCAM_API_KEY`
- `B2_KEY_ID`
- `B2_APPLICATION_KEY`
- `B2_BUCKET_NAME`
- `B2_ENDPOINT`
- video generation API credentials

## Current hackathon notes

This repository contains the working hackathon implementation. Some production hardening remains:

- The email-code flow includes a demo mode for fast judging.
- User profile and wardrobe state are partly local-first for the demo.
- The internal shop is a sample catalog built to demonstrate the future B2B widget/API flow.
- Real merchant billing, account recovery, and partner onboarding are future production work.

Live demo: https://smartmirror-ci.web.app/
