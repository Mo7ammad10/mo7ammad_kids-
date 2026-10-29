# Mo7ammad10 Kids — starter MVP

This is a secure-by-default starter project for a transparent parental-supervision app:
- Android parent app + child app (Flutter)
- Child app can also be built for iOS, but iOS capabilities are restricted by Apple
- Node.js API with pairing codes, authenticated devices, consent-aware commands, and audit logs

## Important scope
This starter does **not** secretly activate cameras/microphones, record ambient audio, hide itself, bypass OS permissions, or silently take over a screen. Remote screen sharing/camera/microphone features must use OS-approved APIs, visible indicators, and explicit on-device user action. iOS requires Apple-supported Screen Time APIs/entitlements for some parental controls, and cannot be treated like Android.

## Layout
- `server/` — Node.js + Express API, SQLite database, JWT authentication
- `flutter_app/` — Flutter starter UI/API client (single codebase; select Parent or Child role at setup)
- `docs/PLATFORM_LIMITS.md` — platform-specific limits and next steps

## Run the server
Requires Node.js 20+.

```bash
cd server
cp .env.example .env
npm install
npm start
```

Set a long random `JWT_SECRET` in `.env`. For deployment, use HTTPS/TLS, backups, rate limiting, and a managed secret store. Do not expose the server over plain HTTP on the public internet.

## Run the Flutter app
Install Flutter SDK, then:

```bash
cd flutter_app
flutter pub get
flutter run
```

Set the API base URL in `lib/config.dart`. For an Android emulator use `http://10.0.2.2:3000`; for a physical phone, use the server's LAN IP during local testing. Production must use HTTPS.

## MVP workflow
1. Parent creates a pairing code.
2. Child app enters the code and pairs.
3. Child device reports its app version and online heartbeat.
4. Parent can view the paired device and request an allowed action.
5. Every command is logged and must be acknowledged by the child app.

The sample includes a minimal API client and UI scaffold. It does not yet implement OS-level location, Screen Time, screen sharing, or app-usage integrations; those need platform-specific native code and permissions.

## API quick reference
- `POST /api/auth/register` — create a parent account
- `POST /api/auth/login` — get JWT
- `POST /api/pairing/create` — parent creates short-lived pairing code
- `POST /api/pairing/claim` — child claims code and registers device
- `GET /api/devices` — parent lists paired devices
- `POST /api/devices/:id/heartbeat` — child heartbeat
- `POST /api/devices/:id/commands` — parent queues an allowed command
- `GET /api/devices/:id/commands` — child fetches pending commands
- `POST /api/commands/:id/ack` — child acknowledges a command
- `GET /api/audit` — authenticated audit trail

All endpoints other than register/login require a Bearer JWT. In a production build, add email verification, account recovery, stronger rate limiting, refresh-token rotation, and security review.
