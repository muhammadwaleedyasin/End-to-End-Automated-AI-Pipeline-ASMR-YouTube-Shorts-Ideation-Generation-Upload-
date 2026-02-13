# Automated AI YouTube Shorts Factory for ASMR

An n8n workflow that automatically generates, produces, and publishes AI-generated ASMR YouTube Shorts videos. The entire pipeline is automated - from idea generation to YouTube upload.

## Overview

This workflow creates satisfying ASMR-style videos (like kinetic sand slicing, soap cutting, etc.) using AI for content ideation, video generation, and sound design. Videos are automatically uploaded to YouTube with notifications sent via Telegram and email.

## Workflow Pipeline

### Step 1: AI Ideation
- **Schedule Trigger**: Runs every 30 minutes (configurable)
- **Generate Trendy Idea**: AI brainstorms viral ASMR concepts
- **Enrich Idea into Plan**: Expands the idea into a full production plan with captions, environment descriptions, and sound prompts

### Step 2: Scene Generation & Video Creation
- **Log to Google Sheets**: Tracks all ideas and their production status
- **Prompts AI Agent**: Generates detailed scene-by-scene descriptions
- **Create Clips**: Generates 3 video clips using Wavespeed AI (Seedance)
- **Create Sounds**: Generates matching ASMR sound effects using Fal AI (MMAudio)

### Step 3: Final Assembly
- **Sequence Video**: Stitches the 3 clips together using Fal AI FFmpeg API
- Creates a 30-second vertical video (9:16 aspect ratio)

### Step 4: Distribution
- **Upload to YouTube**: Publishes the video as a public Short
- **Update Google Sheets**: Logs the final video URL
- **Send Notifications**: Alerts via Telegram and Gmail

## Requirements

### API Keys & Credentials

You'll need to configure the following credentials in n8n:

| Service | Purpose | Credential Type |
|---------|---------|-----------------|
| **OpenAI** | AI idea generation (GPT-4.1) | OpenAI API Key |
| **Wavespeed AI** | Video generation (Seedance) | HTTP Header Auth |
| **Fal AI** | Sound effects & video compositing | HTTP Header Auth |
| **Google Sheets** | Idea tracking & logging | Google OAuth2 |
| **YouTube** | Video upload | Google OAuth2 |
| **Telegram** | Notifications | Telegram Bot Token |
| **Gmail** | Email notifications | Google OAuth2 |

### Google Sheets Setup

Create a Google Sheet with the following columns:
- `idea` - The video concept
- `caption` - YouTube caption with hashtags
- `production` - Status (In Progress / Done)
- `sound_prompt` - Sound description
- `environment_prompt` - Visual environment description
- `final_output` - Generated video URL
- `youtube_url` - Published YouTube URL

## Configuration

Before running the workflow, update these placeholder values:

1. **Google Sheets**: Replace `YOUR_GOOGLE_SHEET_ID` and `YOUR_SHEET_NAME` in:
   - `3. Log New Idea to Sheet`
   - `Update Final Video to Sheet`
   - `Update Sheet with Youtube Link`

2. **Telegram**: Replace `YOUR_CHAT_ID` in the `Telegram Notification` node

3. **Gmail**: Replace `user@example.com` in the `Gmail Notification` node with your email

## How to Import

1. Open your n8n instance
2. Go to **Workflows** > **Import from File**
3. Select the `Automated AI YouTube Shorts Factory for ASMR (Seedance).json` file
4. Configure all credentials (see Requirements section)
5. Update placeholder values (see Configuration section)
6. Activate the workflow

## Workflow Nodes

| Node | Type | Description |
|------|------|-------------|
| Schedule Trigger | Trigger | Starts workflow every 30 minutes |
| 1. Generate Trendy Idea | AI Agent | Creates viral ASMR concepts |
| 2. Enrich Idea into Plan | AI Agent | Expands idea into production plan |
| 3. Log New Idea to Sheet | Google Sheets | Records idea for tracking |
| Prompts AI Agent | AI Agent | Generates detailed scene descriptions |
| Unbundle Prompts | Code | Extracts individual scenes |
| Create Clips | HTTP Request | Calls Wavespeed AI for video generation |
| Wait for Clips | Wait | 2-minute delay for video processing |
| Get Clips | HTTP Request | Retrieves generated video clips |
| Create Sounds | HTTP Request | Calls Fal AI for sound generation |
| Wait for Sounds | Wait | 1-minute delay for audio processing |
| Get Sounds | HTTP Request | Retrieves generated audio |
| List Elements | Code | Aggregates video URLs |
| Sequence Video | HTTP Request | Combines clips using FFmpeg API |
| Wait for Final Video | Wait | 1-minute delay for compositing |
| Get Final Video | HTTP Request | Retrieves final video |
| Update Final Video to Sheet | Google Sheets | Logs final video URL |
| Download Final Video | HTTP Request | Downloads video for upload |
| Upload to YouTube | YouTube | Publishes to YouTube |
| Update Sheet with Youtube Link | Google Sheets | Records YouTube URL |
| Telegram Notification | Telegram | Sends success message |
| Gmail Notification | Gmail | Sends email notification |

## Video Specifications

- **Duration**: ~30 seconds (3 x 10-second clips)
- **Aspect Ratio**: 9:16 (vertical/portrait)
- **Resolution**: 480p (Seedance Lite)
- **Style**: ASMR / Satisfying content

## Cost Considerations

This workflow uses multiple AI APIs that have associated costs:
- **OpenAI**: GPT-4.1 API calls for idea generation
- **Wavespeed AI**: Video generation credits
- **Fal AI**: Audio generation and video compositing

Monitor your API usage and set appropriate rate limits.

## Customization

### Change Video Theme
Edit the system prompts in:
- `1. Generate Trendy Idea` node
- `2. Enrich Idea into Plan` node
- `Prompts AI Agent` node

### Adjust Schedule
Modify the `Schedule Trigger` node interval (default: 30 minutes)

### Change Video Duration
Update the `duration` parameter in `Create Clips` node (default: 10 seconds per clip)

## Troubleshooting

- **Videos not generating**: Check Wavespeed AI API key and credits
- **No sound**: Verify Fal AI credentials
- **YouTube upload fails**: Ensure YouTube OAuth is properly configured
- **Sheets not updating**: Check Google Sheets permissions

## Tech Stack

- [n8n](https://n8n.io/) - Workflow automation
- [OpenAI GPT-4.1](https://openai.com/) - AI content generation
- [Wavespeed AI (Seedance)](https://wavespeed.ai/) - AI video generation
- [Fal AI](https://fal.ai/) - Audio generation & video processing
- [YouTube Data API](https://developers.google.com/youtube/v3) - Video publishing
- [Google Sheets API](https://developers.google.com/sheets/api) - Data logging
