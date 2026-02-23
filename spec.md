# BucketList App — Product Specification

## Overview

BucketList is a web app that combines a visceral "life in weeks" visualization with an actionable bucket list and AI-powered goal planner. Inspired by Tim Urban's "Your Life in Weeks," the app makes the finite nature of time tangible, then gives users tools to make the most of it.

## Core Concept

Three connected pieces form the product:

### 1. Life Grid

Users enter their birthdate and a life expectancy (default ~80 years = ~4,160 weeks). The app renders a grid of small squares — one per week of the user's entire life. Past weeks are filled dark. Future weeks are empty/outlined. A count of weeks lived and weeks remaining is displayed prominently.

The grid is the emotional anchor of the app. It makes time concrete and creates urgency.

### 2. Bucket List

Users add items they want to accomplish, optionally categorized (travel, creative, personal, adventure, etc.). Items can be marked complete or deleted. Each item has a "Plan this" button that connects it to the goal planner.

### 3. Goal Planner (AI-Powered)

When a user clicks "Plan this" on a bucket list item, the app calls the Claude API to break it down into 5-8 concrete micro-goals with suggested timeframes. These micro-goals are displayed as an interactive checklist. Users can track progress, and eventually anchor goals to actual calendar dates.

## Tech Stack

- **Next.js** — React framework with built-in API routes
- **Tailwind CSS** — Utility-first styling
- **localStorage** — Client-side persistence for v1 (no database, no auth)
- **Claude API (Anthropic SDK)** — Powers the goal breakdown feature
- **No authentication for v1** — Ship something working first, add accounts later

## Implementation Stages

### Stage 1 — Life Grid

Build a Next.js app with Tailwind. Create a single page with:

- A form that accepts a birthdate and life expectancy in years
- A grid of small squares representing every week of the user's life
- Past weeks (already lived) rendered as filled/dark squares
- Future weeks rendered as empty/outlined squares
- A displayed count of weeks lived and weeks remaining

This is the foundation. It must work and look right before moving on.

### Stage 2 — Bucket List

Add a bucket list section below the life grid:

- Text input to add new items
- Items stored in localStorage
- Each item displays a title and a "Plan this" button
- Items can be marked complete or deleted
- Optional category tags (travel, creative, personal, adventure, etc.)

### Stage 3 — AI Goal Planner

When a user clicks "Plan this" on a bucket list item:

- Open a panel or modal
- Call an API route at `/api/plan` that takes the bucket list item as input
- The API route calls the Anthropic Claude API with a structured system prompt
- Return a breakdown of 5-8 micro-goals with suggested timeframes
- Display the micro-goals as an interactive checklist
- Store progress in localStorage

#### Claude System Prompt for Goal Planning

```
You are a life coach helping someone accomplish a bucket list item. Given the item, break it down into 5-8 concrete, achievable micro-goals ordered by sequence. For each, give it a name, a 1-sentence description, and a suggested timeframe (e.g. "2 weekends", "1 month", "ongoing"). Return JSON only.
```

#### Expected Response Format

```json
{
  "micro_goals": [
    {
      "name": "Research destinations",
      "description": "Spend an evening researching the top 3 destinations that fit your budget and interests.",
      "timeframe": "1 evening"
    }
  ]
}
```

### Stage 4 — Polish

- Connect micro-goals to actual calendar dates
- Add a simple timeline view
- Make the life grid visually compelling (animations, color themes)
- Responsive design for mobile

## Data Model (localStorage v1)

### User Profile
```json
{
  "birthdate": "1990-05-15",
  "lifeExpectancy": 80
}
```

### Bucket List Items
```json
[
  {
    "id": "uuid",
    "title": "Hike the Inca Trail",
    "category": "adventure",
    "completed": false,
    "createdAt": "2026-01-15T00:00:00Z"
  }
]
```

### Micro-Goals (per Bucket List Item)
```json
{
  "bucketItemId": "uuid",
  "goals": [
    {
      "id": "uuid",
      "name": "Research permits",
      "description": "Look into permit availability and booking windows.",
      "timeframe": "1 evening",
      "completed": false,
      "targetDate": null
    }
  ]
}
```

## Design Principles

- **Simplicity first** — No auth, no database, no over-engineering in v1
- **Emotional impact** — The life grid should hit hard; that's the differentiator
- **Actionable, not just aspirational** — The AI planner turns wishes into steps
- **Incremental delivery** — Each stage produces a working, usable app

## Future Considerations (Post-v1)

- User accounts and cloud persistence (Prisma + SQLite or PostgreSQL)
- Sharing bucket lists publicly or with friends
- Calendar integration (Google Calendar, Apple Calendar)
- Notifications and reminders for upcoming micro-goal deadlines
- Multiple life grid visualizations (by year, by month)
- Export/import bucket list data
