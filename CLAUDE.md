# CLAUDE.md — OpenClaw Setup Guide

## Project Purpose

This repository contains a **complete, zero-to-hero setup guide** for OpenClaw — an open-source reimplementation of the 1997 Captain Claw platformer game. The guide is written so that someone with zero prior knowledge can follow every step.

## Key Details

- **OpenClaw repo**: https://github.com/pjasicek/OpenClaw
- **Target platform**: WSL2 (Ubuntu) on Windows Server 2022 / Windows 10+
- **Language**: The guide is in English, written in Markdown
- **Audience**: Complete beginners — every command must be explained

## Project Structure

```
openclaw-setup-guide/
├── CLAUDE.md              # This file — project instructions for Claude
├── README.md              # Overview and quick links
├── 01-wsl-setup.md        # Step 1: Install WSL2 + Ubuntu
├── 02-dependencies.md     # Step 2: Install build tools and libraries
├── 03-clone-and-build.md  # Step 3: Clone OpenClaw and compile
├── 04-game-files.md       # Step 4: Get and place CLAW.REZ game assets
├── 05-run-and-play.md     # Step 5: Launch the game
├── 06-troubleshooting.md  # Common errors and fixes
└── assets/                # Screenshots and reference images (if any)
```

## Writing Rules

1. Every guide file must start with a clear **What you'll do** section
2. Every command must have a one-line explanation above it
3. Use numbered steps — never skip a step
4. Include expected output after commands so the reader can verify success
5. Mark optional steps clearly with "(Optional)"
6. Cross-reference other guide files when needed
7. Keep language simple — no jargon without explanation
