---
title: "Prompting Best Practices"
date: 2026-04-08
tags:
  - AI提示词
  - 提示工程
  - Lovable
  - UI设计
  - 产品设计
aliases:
  - Lovable提示词最佳实践
  - prompting best practices
source: "[[Lovable Documentation]]"
---

# Prompting Best Practices

> [!info] 来源
> [Lovable Documentation](https://docs.lovable.dev/prompting/prompting-one)

> [!tip] 核心一句话
> Plan before you prompt, build by component, use real content, and apply design buzzwords to get consistent, high-quality results.

---

## Phase 1: Lay the Foundation

### 1. Plan Before You Prompt

Before using Lovable, define what you're building. Answer these four questions:

- **What** is this product or feature?
- **Who** is it for?
- **Why** will they use it?
- **What** is the one key action the user should take?

> 模糊的想法产生模糊的输出，清晰的思维导致清晰的结果。

### 2. Map the User Journey Visually

Design isn't about individual screens—it's about what happens between them。

- What does the user see first?
- What builds trust?
- What gives them confidence to act?
- Where does that action lead?

> "You're not stacking blocks. You're guiding behavior."

### 3. Get the Design Right First

Your visual language is a foundation, not a polish layer. Choose a direction early:
- Calm and elegant
- Bold and disruptive
- Premium and sleek

Then feed that style directly into your prompt using **buzzwords, tone descriptors, and UI patterns**.

> "You don't prompt your way into good design. You prompt from it."

---

## Phase 2: Think in Systems

### 4. Prompt by Component, Not Page

Lovable works best when you build your UI in **modular parts**—not full pages at once.

> "A full-page prompt gets you noise. A section-based prompt gets you signal."

**Example:**
> Create a floating menu bar with glassmorphism effect. Include Home, Search, Music, Favorites, Add, Profile, and Settings icons. Add gentle floating animation and smooth hover interactions.

### 5. Design with Real Content

Lovable does not work well with placeholder content like "lorem ipsum" or "feature 1".

> "Design loves constraints. Real content creates the right ones."

**Example:**
> Hero section with headline: "Design Calmly." Subtext: "Turn stress into structure with Lovable." CTA: "Start Building Free." Use copy-centered layout with generous vertical spacing.

### 6. Think in Atoms

Think like a system. Describe **cards, badges, toggles, chips, form fields, dropdowns**, etc.

> "The smaller the part, the smarter the response."

**Example:**
> Create a card with a user profile picture, name, and a follow button. Add a badge for verified users, and show a tooltip when hovering over the badge.

### 7. Use Buzzwords to Dial in Aesthetic

Buzzwords are the fastest way to guide vibe. Lovable understands terms like:
- minimal, expressive, cinematic, playful, premium, developer-focused

> "Design isn't just structure. It's tone. Buzzwords define it."

**Example:**
> Design a landing page hero that feels premium and cinematic. Use layered depth, translucent surfaces, soft motion blur, and dramatic contrast between headline and background.

---

## Phase 3: Build with Precision

### 8. Use Prompt Patterns for Layouts

Use structured, repeatable patterns. Think of them as **layout recipes**.

Most patterns follow a "header → content → action" structure.

**Example:**
> Create a feature section with a centered headline, followed by three horizontally aligned cards. Each card includes an icon on top, a headline, and a short description. Cards should have soft shadows and lift on hover.

### 9. Add Visuals via URL

Embed product demos, Midjourney-generated clips, or tutorial videos using URLs.

**Example:**
> Embed a product demo video from Midjourney. Use this URL: https://cdn.midjourney.com/video/... Place it below the feature section in a full-width card with a soft shadow.

### 10. Layer Context with the Edit Button

Use the edit function to focus on specific layers or elements. Be precise—say exactly what you want to change and what should stay the same.

**Example:**
> Change the CTA button text to "Get Started" and increase the padding to 24px horizontal. Keep the existing background color and font.

---

## Phase 4: Iterate and Ship

### 11. Build with Lovable Cloud in Mind

When designing with Cloud in mind, start by anticipating:
- **Auth logic** — What should the UI show if the user is logged in vs logged out?
- **Dynamic content** — Are you pulling user data from a table?
- **States** — What happens if the data is empty, still loading, or fails?

### 12. Version Control is Your Friend

Think in iterations. Make one meaningful change at a time.

> "Autosave doesn't mean auto-organized. Build intentionally. Iterate with awareness."

---

## 让 Lovable 问澄清问题

One of the most effective ways to get better results is to let it fill in the gaps:

> "Ask me any questions you need in order to fully understand what I want from this feature and how I envision it."

---

#AI提示词 #产品设计 #UI开发