# Self-Hosted Media Automation Platform

This project describes my technical setup of a personal project I've recently completed and have been utilizing for personal use. Essentially this is a media-server that I'm hosting on the cloud, integrating with different providers and heavily using open-source github projects to develop a pipeline for requesting any movie/show (via a public-facing website I setup such that users with authenticated plex accounts authorized by me via plex) to be automatically downloaded and uploaded to my media server. The media server is hosted via Plex which can be accessed from any device (such as an Apple TV, Roku, Playstation, Google TV) to play content from my library given the user is granted access by me. The infrastructure utilizes docker heavily to containerize the many different open-source applications used for this pipeline, updates to my linux cloud server and docker yaml are primarily done via terminal commands. I've utilized AI to help "Vibe-Code" the setup but mostly there are a lot of online guides that provide further instruction. AI helped primarily with making sure I understood the broader concepts and with syntax/drafting commands.

## Brief description
A containerized, multi-user media platform built from a bare cloud instance, with networking, security, and ~20 orchestrated services configured entirely via terminal and Docker.

## Infrastructure

- **Cloud instance:** Provisioned and hardened an ARM-based cloud VM (Oracle Cloud, Ampere/aarch64, Ubuntu 24.04) as the sole host for all storage and compute — SSH key-only auth, firewall scoped to only the ports actually needed.
- **Reverse proxy & HTTPS:** Deployed **Caddy** as a single public-facing entry point, mapping distinct subdomains (via dynamic DNS, custom domain-names registered for free via duckdns) to internal service ports, so each app gets its own clean URL without exposing raw ports. Caddy automates TLS certificate issuance and renewal (Let's Encrypt) for every domain with zero manual cert management.
- **Selective exposure:** Only user-facing apps are routed through Caddy externally; every admin/management interface is bound to loopback and reachable solely via SSH tunnel — a deliberate least-exposure security boundary, not a default.
- **Container orchestration:** The entire stack — media server, automation pipeline, download clients, security tooling — is defined and version-controlled in a single Docker Compose file, managed entirely through the terminal (no GUI dependency).

## Application Stack

- **Media delivery:** Plex Media Server, tuned for direct-play delivery to minimize CPU-bound transcoding on constrained hardware
- **Content acquisition:** Dual-instance Sonarr/Radarr architecture (standard + anime-specific, to handle divergent numbering/release conventions), Prowlarr for centralized indexer management, and parallel torrent/Usenet download clients (qBittorrent, SABnzbd).
- **Network-isolated downloading:** All download-client traffic is routed through a dedicated WireGuard VPN container (Gluetun), with automated port-forward synchronization keeping the client in sync with the VPN provider's dynamically assigned port — the host's real IP is never exposed to peers or trackers.
- **Quality & security automation:** Regex-driven Custom Format scoring (informed by community best practices) to prefer efficient codecs and reject problematic releases; automated malware-pattern blocklisting on incoming downloads (Via community-maintained blacklists configured through cleanuparr); scheduled library-lifecycle rules for storage management.
- **Self-service access:** A request portal (Overseerr/Seerr) lets end users browse and request content without needing any backend access, routed automatically to the correct content-type pipeline.

## Engineering Highlights

- Diagnosed cross-architecture (amd64/arm64) Docker image incompatibilities across multiple third-party tools and migrated to properly-supported alternatives.
- Root-caused a breaking upstream API change (HTTP response-code semantics) that silently broke automation, isolating the exact spec deviation before switching tools.
- Designed a unified UID/GID permission model (shared group, setgid inheritance, consistent umask) across a dozen independently-configured containers to eliminate recurring file-ownership conflicts.
- Solved IP-reputation blocking (Cloudflare) on specific external services by selectively routing only the affected traffic through a SOCKS5 proxy — without adding VPN dependency to the rest of the stack.

## Stack
`Docker Compose` · `Linux/Ubuntu` · `Bash` · `Caddy` · `Let's Encrypt` · `Plex` · `Sonarr/Radarr` · `Prowlarr` · `qBittorrent` · `SABnzbd` · `Gluetun (WireGuard)` · `Overseerr/Seerr` · `Cleanuparr` · `Maintainerr` · 
