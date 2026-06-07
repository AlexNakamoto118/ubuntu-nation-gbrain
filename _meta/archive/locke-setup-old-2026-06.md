# Locke Setup Manifest

Agent: Locke (local, macOS).
VPS agent: remote orchestrator on Hostinger, Telegram-connected.

## Architecture
- Shared Telegram chat between VPS agent and Locke.
- GitHub repos: ubuntu-nation-gbrain, jarvis-3000-brain.
- Dual kanban:
  - demovps — cross-agent board (VPS ↔ Locke)
  - locke-team — Locke-specific local team board
- VPS has r/w push access to GitHub brain repos.
- Local (Locke) clones repos at ~/.hermes/gbrain/*
