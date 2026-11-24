# Demonware CTR Server

A standalone Demonware server for **Crash Team Racing Nitro-Fueled**, written in Go. Supports authentication, matchmaking, player services, and the Pit Stop economy for PS4 and PC/shadPS4 clients (titles 5775 and 5760).

---

## Features

- **Authentication (Auth)**:
  - Native HTTPS support on port `8460` for official clients.
  - Plain HTTP support on port `8080` (ideal for emulators, shadPS4, and local development with `host_overrides.json` without TLS certificates).
  - Stable PID calculation compatible with local or backend profiles.
- **Lobby and Matchmaking (TCP 3074-3080)**:
  - Demonware encryption (HMAC-SHA1, HKDF, AES-128-CBC) and remote method call (RMC) emulation.
  - Creation, discovery, and joining of public and private race lobbies.
  - Player state and presence synchronization.
- **NAT & STUN Discovery (UDP 3074)**:
  - Public IP detection and NAT introductions between players for P2P gameplay.
- **Pit Stop and Economy (`bdEconomy` & `bdMarketplace`)**:
  - Functional Pit Stop store with rotating items and bundles.
  - Wumpa Coins: persistent balance, rewards for completed races, and weekend bonuses.
- **Grand Prix and Challenges (`bdAchievements`)**:
  - Active challenge system (daily, weekly, themed) and Window Shopping.
- **Statistics and Leaderboards (`bdStats`)**:
  - Track time recording and global leaderboards.
- **Object Storage (`bdObjectStore`)**:
  - Serving of Beenox configuration files (`NetConfig_23_ps4_na.igx`, `MarketplaceConfig_2_ps4_na.igx`, etc.).
- **Monitoring Dashboard**:
  - Statistics endpoint `/api/stats` and health endpoint `/healthz` on port `8093` with token protection.

---

## Building and Starting the Server

### Requirements

- **Go 1.23** or later installed on your system.

### 1. Build

On **Windows**:

```cmd
scripts\build.bat
```
On **Linux / VPS**:

```bash
chmod +x scripts/build.sh
./scripts/build.sh
```
Or directly with `go build`:

```bash
# For your current system:
go build -o bin/server-ps4.exe ./server

# For Linux ARM64 (e.g., an Ubuntu VPS on ARM):
GOOS=linux GOARCH=arm64 go build -o bin/server-ps4-linux-arm64 ./server
```

### 2. Configure

Copy `example.env` to `.env` and adjust it for your environment:

```bash
cp example.env .env
```
Key settings:

- Set the public host address in `.env` to an address reachable by players. Use `127.0.0.1` for local testing and keep both host entries consistent with `example.env`.
- `DEMONWARE_HTTP_PORT`: Port for unencrypted authentication (`8080` recommended for PC/shadPS4).
- `LOBBY_PORTS`: TCP ports for lobbies (default: `3074,3075,3076,3077,3078,3079,3080`).
- `NAT_PORT`: UDP port for STUN/NAT (default: `3074`).

### 3. Run

- On **Windows**: Double-click `start-server.bat` or run `.\bin\server-ps4.exe`.
- On **Linux / VPS**: `./bin/server-ps4-linux-arm64` (or configure it as a systemd service).

---

## Connecting the Game (CTR PC / shadPS4)

In the CTR game folder:

1. Open your `bbport.ini` configuration file.
2. Enable online mode and point it to your server's IP address:
   ```ini
   [Online]
   online=1
   online_server=YOUR_IP_OR_127.0.0.1
   ```
3. If you play over a local network or the Internet, make sure ports `8080`, `8460`, `3074-3080` (TCP), and `3074` (UDP) are open on your firewall/router.

---

## Unit Tests

Tests live alongside the Go source in `server/`. Run them using the supplied scripts:

On **Windows**:

```cmd
scripts\test.bat
```
On **Linux / macOS**:

```bash
chmod +x scripts/test.sh
./scripts/test.sh
```

---

## Credits and Acknowledgments

Demonware CTR Server builds on community reverse engineering and server development:

- **[Nextendo Network](https://github.com/NextendoNetwork)**: Creators of the base Demonware server architecture in Go and the console network emulation ecosystem.
- **[nx-mod](https://github.com/nx-mod)**: Creator of the original repository and Demonware protocol engine.
- **[CollectingW](https://github.com/CollectingW)**: Original research and implementation of the online layer for Crash Team Racing Nitro-Fueled (Title 5775).
- **RyuPC**: Cleanup, standardization for PC / PS4, module separation, authentication handshake fixes, and portable packaging.

---

## License

Licensed under the PolyForm Shield License 1.0.0. See [LICENSE.md](LICENSE.md) for the full terms.
