# TerraDiscord

TerraDiscord is a Infrastructure-as-Code tool that allows you to manage your server structure through JSON files. You can define roles, categories, channels, and permissions in a single configuration file and apply it to any server where the bot has administrator privileges.

## Core Features
- Manage server name and description via JSON.
- Automated creation of roles with specific colors and permissions.
- Category and channel management (Text, Voice, News, Forum, Stage).
- Permission overrides for specific roles on a per-channel basis.
- Export your current server structure back to JSON.

## Setup
1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
2. Create a `.env` file in the root directory:
   ```env
   DISCORD_TOKEN=your_bot_token_here
   ```
3. Run the bot:
   ```bash
   python main.py
   ```

## Folder Structure
- `presets/`: Place your custom JSON configurations here.
- `examples/`: Simple templates showing specific features. Files starting with `.` are ignored.
- `exports/`: Generated JSON files from the `/export` command.

## JSON Schema Guide
A standard configuration file follows this structure:

```json
{
  "server_name": "My Community",
  "server_description": "Optional description",
  "roles": [
    {
      "name": "Admin",
      "hex": "#ff0000",
      "hoist": true,
      "mentionable": true,
      "permissions": {
        "administrator": true
      }
    }
  ],
  "categories": [
    {
      "name": "General",
      "channels": [
        {
          "name": "welcome",
          "type": "text",
          "permissions": {
            "@everyone": { "send_messages": false },
            "Admin": { "send_messages": true }
          }
        },
        { "name": "General Voice", "type": "voice" }
      ]
    }
  ]
}
```

### Channel Types
Supported types are: `text`, `voice`, `news`, `forum`, `stage`.

## JSON Syntax Reference

The JSON configuration is composed of top-level keys and nested objects. If a key is omitted, the bot uses its default behavior.

### Top-Level Keys
- `server_name` (String): Changes the name of the Discord server.
- `server_description` (String): Changes the server description (requires Community settings).
- `roles` (List): A list of role objects to be created.
- `categories` (List): A list of category objects containing channels.

### Role Object
- `name` (String, Required): The name of the role.
- `hex` (String): Hex color code (e.g., `#3498db`).
- `hoist` (Boolean): If true, the role is displayed separately in the member list. Default: `false`.
- `mentionable` (Boolean): If true, the role can be mentioned by anyone. Default: `false`.
- `permissions` (Object): A set of server-wide permissions (Key: name, Value: `true`/`false`).

### Category and Channel Objects
Categories contain a list of `channels`. Each channel object supports:
- `name` (String, Required): The name of the channel.
- `type` (String): The channel type. Options: `text`, `voice`, `news`, `forum`, `stage`. Default: `text`.
- `permissions` (Object): Permission overrides for roles. Key: Role name or `@everyone`. Value: Object of permissions (`true`/`false`).

## Permission Names
TerraDiscord uses the standard discord.py naming convention. Below are the most common permission names you can use in both `roles` and channel `permissions`:

### General Permissions
- `administrator` (Use with caution)
- `manage_guild` (Manage Server)
- `manage_roles`
- `manage_channels`
- `view_audit_log`
- `manage_webhooks`
- `manage_emojis_and_stickers`

### Membership Permissions
- `kick_members`
- `ban_members`
- `moderate_members` (Timeout)
- `create_instant_invite`
- `change_nickname`
- `manage_nicknames`

### Text Channel Permissions
- `view_channel`
- `send_messages`
- `send_tts_messages`
- `manage_messages`
- `embed_links`
- `attach_files`
- `read_message_history`
- `mention_everyone`
- `add_reactions`
- `use_external_emojis`

### Voice Channel Permissions
- `connect`
- `speak`
- `stream` (Video/Screen share)
- `use_embedded_activities`
- `mute_members`
- `deafen_members`
- `move_members`
- `priority_speaker`

## Slash Commands
All commands require Administrator permissions on the server.

- `/add [preset]`: Adds roles and channels from the JSON to the current server. It does not delete existing data.
- `/set [preset]`: Wipes the server (roles and channels) and rebuilds it from the JSON. The channel where the command is used is skipped during deletion.
- `/reset`: Deletes all manageable roles and channels from the server.
- `/export`: Scans the current server and saves the structure to a new JSON file in the `exports/` folder.

## Permissions
When defining permissions in JSON (either for roles or channel overrides), use the standard discord.py permission names (e.g., `manage_messages`, `view_channel`, `connect`). If a permission is not specified, it defaults to false for roles and neutral (default) for channel overrides.
