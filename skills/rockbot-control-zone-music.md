---
name: Control zone music playback
description: Authenticate to the Rockbot v5 API and control music for a location — check what's playing, start/stop, adjust volume, skip, and apply a temporary playlist override.
api: openapi/rockbot-openapi.yml
operations: [createAccessToken, getNowPlaying, startMusic, stopMusic, setVolume, skipTrack, listZonePlaylists, createPlaylistOverride, deleteAllPlaylistOverrides]
---

# Control zone music playback

Use the Rockbot v5 API (base URL `https://api.rockbot.com/v5`) to control in-location music for a zone.

## Authenticate

1. Obtain a `CLIENT_ID` / `CLIENT_SECRET` from Rockbot support (email support@rockbot.com).
2. Call **createAccessToken** — `POST /api-clients/token` with `{client_id, client_secret}`. Store the returned `access_token`.
3. Send `Authorization: Bearer <access_token>` on every subsequent call. Tokens last 24 hours — refresh before expiry.

## Steps

1. **getNowPlaying** — `GET /ext/music/now_playing` to see the current track and queue.
2. To resume or halt playback, call **startMusic** (`POST /ext/music/start`) or **stopMusic** (`POST /ext/music/stop`).
3. **setVolume** — `POST /ext/music/volume` with an integer `volume` 0–100.
4. **skipTrack** — `POST /ext/music/skip` to advance a track. Limited to 6 skips per hour; back off on 429.
5. To change programming, **listZonePlaylists** (`GET /ext/zones/{zone_id}/playlists`, paginated), then **createPlaylistOverride** (`POST /ext/zones/{zone_id}/playlists/override`) to apply a temporary override.
6. Remove overrides with **deleteAllPlaylistOverrides** (`DELETE /ext/zones/{zone_id}/playlists/override`).

## Rules

- Default rate limit is 1 request/second — serialize calls and honor `429 Too Many Requests`.
- Playback and override operations are not idempotent (no idempotency-key); avoid blind retries on write calls.
- Paginated list responses use the envelope `{total_count, page_size, page, page_count, data}` with `limit`/`offset` params.
