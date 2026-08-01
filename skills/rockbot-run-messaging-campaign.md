---
name: Run an audio-messaging campaign
description: Authenticate to the Rockbot v5 API, upload an audio asset, create a zone-level messaging campaign, attach the asset, and enable or immediately play it.
api: openapi/rockbot-openapi.yml
operations: [createAccessToken, uploadMessagingAssetZone, listMessagingAssetsByZone, createMessagingCampaignZone, addMessagingCampaignAsset, enableMessagingCampaign, playMessagingCampaign, listMessagingCampaignsByZone]
---

# Run an audio-messaging campaign

Deliver an in-location audio message to a Rockbot zone. Base URL `https://api.rockbot.com/v5`.

## Authenticate

1. **createAccessToken** — `POST /api-clients/token` with `{client_id, client_secret}`; keep the `access_token`.
2. Send `Authorization: Bearer <access_token>` on all calls (24-hour tokens).

## Steps

1. **uploadMessagingAssetZone** — `POST /ext/messaging/asset/zone` (multipart/form-data) to upload the audio file. Confirm with **listMessagingAssetsByZone** (`GET /ext/messaging/assets/zone`).
2. **createMessagingCampaignZone** — `POST /ext/messaging/campaign/zone` to create the campaign. Schedule recurrence with iCal RRULE syntax.
3. **addMessagingCampaignAsset** — `POST /ext/messaging/campaign/add_asset` to attach the uploaded asset to the campaign.
4. **enableMessagingCampaign** — `POST /ext/messaging/campaign/enable` to activate it on schedule, or **playMessagingCampaign** (`POST /ext/messaging/campaign/play`) to trigger immediate playback.
5. Verify with **listMessagingCampaignsByZone** (`GET /ext/messaging/campaigns/zone`).

## Rules

- Uploads use `multipart/form-data`; all other calls use `application/json`.
- Default rate limit 1 request/second; honor `429`.
- Write operations are not idempotent — do not blindly retry create/enable/play calls.
- The signage product mirrors these operations under `/ext/signage/...` if you need on-screen campaigns instead.
