---
layout: page
title: Chaukidar (Watchman)
description: AI safety audit platform for multilingual harmful-request refusal testing.
img:
importance: 1
category: Side Projects
github: https://github.com/HopzAlot/Chaukidar
giscus_comments: false
---


**Chaukidar** is an AI safety audit platform that evaluates whether language models refuse harmful requests consistently across English, Urdu, Punjabi, Pashto, and Sindhi.

The project focuses on an important safety gap: a model may refuse dangerous requests in English, but behave differently when the same intent appears in regional languages, translated prompts, or native-adapted phrasing.

---

## System Overview

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/chaukidar.png" title="Chaukidar dashboard" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Chaukidar's audit interface for scanning refusal coverage across languages, harm categories, and prompt tracks.
</div>

- **Frontend:** Responsive web interface for running and reviewing audits
- **Audit Scope:** English, Urdu, Punjabi, Pashto, and Sindhi
- **Safety Focus:** Harmful-request refusal consistency
- **Prompt Tracks:** English seed prompts, translated baselines, and native-adapted prompts
- **Output:** Coverage-style audit results that make language-specific safety gaps easier to spot

---

## Core Idea

Chaukidar compares model behavior across prompt variants:

> **English refusal baseline vs translated prompt vs native-adapted prompt**

This makes it easier to identify cases where safety alignment is strong in English but weaker in another language or cultural context.

---

## Key Features

- **Multilingual audit matrix** for scanning refusal coverage across language and harm categories
- **Side-by-side prompt strategy** using English seed, translated, and native-adapted prompts
- **Past audit views** for revisiting previous model safety checks
- **Sample audit flow** for quickly demonstrating the platform
- **Clear visual reporting** to highlight where model refusal behavior changes
- **Regional language focus** for safety testing beyond English-only evaluation

---

## Why It Matters

Many AI safety evaluations are strongest around English data. Chaukidar helps surface hidden risks by testing whether safety behavior survives translation, local phrasing, and language-specific expression.

For multilingual users, this kind of audit can help ensure harmful requests are handled consistently, not just in the highest-resource language.

---

## Link to the Live Project

You can try the deployed project here:  
**[Chaukidar](https://chaukidar.vercel.app/)**

## Link to the CodeBase

You can explore the source code here:  
**[GitHub: Chaukidar](https://github.com/HopzAlot/Chaukidar)**

**Small Note:** The backend is deployed on Render, so if it has been inactive, it may take around a minute to wake up. Chaukidar also goes for tea break from time to time hehe...
