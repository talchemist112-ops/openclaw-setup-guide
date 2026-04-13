# OpenClaw Setup Guide

A complete, step-by-step guide to install and run **OpenClaw** — an open-source reimplementation of the classic 1997 Captain Claw platformer game.

This guide is written for **Windows users** using **WSL2 (Windows Subsystem for Linux)** with Ubuntu. No prior Linux or programming experience required.

## What is OpenClaw?

OpenClaw is a fan-made, open-source recreation of **Captain Claw**, a 2D side-scrolling platformer originally made by Monolith Productions in 1997. The OpenClaw project rewrites the game engine from scratch in C++ so it runs on modern computers.

**Important**: You need the original game's asset file (`CLAW.REZ`) to play. OpenClaw is just the engine — it does not include the game's graphics, sounds, or levels.

## Guide Structure

Follow these guides **in order**:

| Step | Guide | What You'll Do |
|------|-------|----------------|
| 1 | [WSL Setup](01-wsl-setup.md) | Install WSL2 and Ubuntu on Windows |
| 2 | [Dependencies](02-dependencies.md) | Install build tools and libraries in Ubuntu |
| 3 | [Clone and Build](03-clone-and-build.md) | Download OpenClaw source code and compile it |
| 4 | [Game Files](04-game-files.md) | Get and place the original CLAW.REZ file |
| 5 | [Run and Play](05-run-and-play.md) | Launch the game |
| 6 | [Troubleshooting](06-troubleshooting.md) | Fix common errors |

## Requirements

- **Windows 10 (version 2004+)** or **Windows 11** or **Windows Server 2022**
- Internet connection
- At least **2 GB free disk space**
- The original Captain Claw game file (`CLAW.REZ`)

## Quick Reference

- **OpenClaw GitHub**: https://github.com/pjasicek/OpenClaw
- **License**: GPL-3.0 (open source)
- **Original Game**: Captain Claw (1997) by Monolith Productions

---

*This guide was created to help anyone set up OpenClaw from scratch, even with zero technical knowledge.*
