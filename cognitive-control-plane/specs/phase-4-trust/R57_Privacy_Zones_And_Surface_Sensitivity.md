# R57 — Privacy Zones and Surface Sensitivity

## User capability

The assistant can adapt what it reveals based on surface and privacy context.

Example:

```text
Car voice with possible passenger:
Assistant: You have a personal appointment at 3 PM.
```

Instead of reading sensitive appointment details aloud.

## Problem

The same answer may be safe on desktop but inappropriate on voice, car, glasses, notifications, or public surfaces.

Without privacy zones, the assistant may:

- reveal sensitive details through notifications
- read private context aloud in a shared space
- show too much on glasses or small HUDs
- expose work/health/finance context on the wrong surface
- reuse private details where a generic answer would be safer

## MVP behavior

The runtime should resolve a privacy/surface sensitivity context before response shaping.

Minimum surface categories:

- `desktop_private`
- `mobile_private`
- `telegram_private`
- `voice_private`
- `car_voice_possible_passenger`
- `glasses_public_or_semi_public`
- `notification_preview`
- `unknown_surface`

Minimum privacy outputs:

- `privacy_zone`
- `surface_type`
- `sensitive_detail_allowed`
- `notification_detail_allowed`
- `voice_detail_allowed`
- `screen_detail_allowed`
- `redaction_required`
- `safe_summary_required`

## Runtime ownership

The runtime owns privacy-zone and surface-sensitivity policy.

The orchestrator consumes the policy during response assembly.

Integration adapters provide surface metadata but do not decide privacy behavior.

The memory substrate may expose sensitivity metadata but does not own response redaction policy.

## Contract shape

Illustrative object:

```yaml
privacy_context:
  surface_type: car_voice
  privacy_zone: car_voice_possible_passenger
  sensitive_detail_allowed: false
  voice_detail_allowed: false
  safe_summary_required: true
  reason_summary:
    - voice_surface
    - passenger_presence_unknown
```

## Must not happen

The assistant must not:

- reveal sensitive details in notification previews
- read sensitive personal, health, financial, or work details aloud when passenger/public context is unknown
- assume glasses or car surfaces are private
- bypass privacy rules because the user asked a short question like `status`
- expose raw memory content when a safe summary is enough

## Minimal tests

Implementation is sufficient when tests can prove:

1. Desktop/private surfaces may receive normal detail when allowed.
2. Car voice with unknown passenger context uses safe summaries for sensitive items.
3. Notification preview suppresses sensitive details.
4. Glasses/public surfaces prefer concise safe summaries.
5. Unknown surface defaults conservative.
6. Trace summaries record privacy zone without exposing suppressed details.

## Notes for later phases

Action permissions should account for allowed surfaces.

Watch notifications must obey notification privacy rules.

Delegated work summaries must adapt to the active privacy zone.
