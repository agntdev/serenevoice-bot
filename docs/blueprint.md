# Text-to-Audio Voice Generator — Bot specification

**Archetype:** custom

**Voice:** professional and concise — write every user-facing message, button label, error, and empty state in this voice.

Telegram bot that converts user text (≤800 chars) into short audio files using a calm female voice, sending results directly to the originating chat with format options and content policy enforcement.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Telegram users needing quick text-to-speech snippets
- Content creators
- Language learners

## Success criteria

- User receives audio file within 5 seconds of valid text submission
- 100% compliance with content policy rules
- 99% error-free processing for valid inputs

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open main menu with usage instructions
- **/help** (command, actor: user, command: /help) — Display usage guidelines and limits
- **/voice** (command, actor: user, command: /voice) — Show current voice profile (Calm Female)
- **/settings** (command, actor: user, command: /settings) — Toggle between OGG voice message and MP3 format

## Flows

### Text-to-Audio Core Flow
_Trigger:_ User message

1. Receive text message
2. Validate length/character limits
3. Detect language
4. Generate audio using calm female voice
5. Send audio with metadata caption

_Data touched:_ TextRequest, AudioFile, JobRecord

### Help Command Flow
_Trigger:_ /help

1. Display usage instructions
2. Show format options
3. Explain content policy

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **TextRequest** _(retention: session)_ — User-submitted text with metadata
  - fields: text_content, character_count, language_detected, timestamp
- **AudioFile** _(retention: persistent)_ — Generated speech file
  - fields: file_id, format_type, duration_seconds, storage_url
- **JobRecord** _(retention: persistent)_ — Request metadata for troubleshooting
  - fields: user_id, timestamp, text_hash, language, file_id, duration

## Integrations

- **Telegram** (required) — Bot API messaging and audio delivery
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Configure admin error notifications
- Adjust character limit (default 800)
- Set daily request limits (default 20)

## Notifications

- Admin error alerts for repeated generation failures

## Permissions & privacy

- Store request metadata for 30 days
- Delete audio files after 30 days
- No third-party data sharing

## Edge cases

- Text exceeding 800 characters
- Unsupported language detected
- Content policy violations (voice imitation requests)
- API rate limits exceeded

## Required tests

- End-to-end text-to-audio flow with valid input
- Error handling for policy violations
- Format switching between OGG and MP3

## Assumptions

- Default voice remains calm female without celebrity imitation
- 30-day retention covers most re-download needs
- 20 requests/day limit prevents abuse
