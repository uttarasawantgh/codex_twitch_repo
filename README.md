# 🚀 StreamSidekick AI
> High-Energy Interactive Twitch Co-Host powered by Local Compute & Resilient Cloud Fallback.

StreamSidekick AI turns local open-source vision-language models into a real-time, interactive stream co-host. Built for Twitch streamers and hackathon performance, it monitors chat commands, dynamically suggests stream activities, and responds instantly without blocking the streamer's live workflow.

## 🔑 Key Features
- **Dual-Endpoint Architecture:** Primary inference runs locally on NVIDIA DGX Hardware (Qwen2.5-VL-7B-Instruct @ localhost:8001), with instant zero-downtime failover to OpenAI (gpt-4o-mini).
- **Non-Blocking Execution:** Uses asyncio.to_thread around synchronous model completions, keeping the TwitchIO WebSocket event loop responsive under rapid stress tests.
- **Twitch-Optimized Output:** Strips Markdown and metadata, outputting clean, unformatted plain text tailored for standard Twitch chat limits.

## 🛠️ Tech Stack & Architecture
- **Primary Engine:** Local vLLM Container (Qwen/Qwen2.5-VL-7B-Instruct)
- **Cloud Fallback:** OpenAI API (gpt-4o-mini)
- **Chat Pipeline:** Python 3.11+, twitchio 2.10.0
- **Concurrency:** Python asyncio + asyncio.to_thread
- **Dev Environment:** OpenAI Codex CLI

---

## 🧪 Verified Test Cases & Scenarios

1. **Standard Co-Host Query (Success Case)**
   - **Trigger:** `!sidekick what should the streamer do next?`
   - **Behavior:** Evaluates the query via local vLLM (`Qwen2.5-VL-7B-Instruct`) and returns 3 concise, actionable stream ideas under 350 characters.

2. **Proactive Empty Prompt Handling**
   - **Trigger:** `!sidekick` (no query text)
   - **Behavior:** Proactively feeds a default prompt logic, returning 3 interactive stream ideas (e.g., *"1. Play Guess the Song, 2. Host a Dance Off, 3. Meme Marathon!"*).

3. **Special Characters & Long Query Safety**
   - **Trigger:** `!sidekick what should we do next??? also should we code or play games or ask chat questions!?!`
   - **Behavior:** Parses multi-part questions and heavy punctuation without crashing, returning a clean plain-text itinerary.

4. **Rapid Stress Test (Async Event Loop Safety)**
   - **Trigger:** Sending multiple `!sidekick` triggers in rapid succession back-to-back.
   - **Behavior:** `asyncio.to_thread` runs model inference off the main event loop, processing concurrent requests without dropping WebSocket heartbeats.

5. **Zero-Downtime Cloud Fallback**
   - **Trigger:** Local vLLM Docker container stopped or network timeout.
   - **Behavior:** Strict 3-second connection timeout catches the connection exception and fails over seamlessly to `gpt-4o-mini`, notifying chat: `@user Sidekick is currently operating on backup mode!`.

- **Local Inference Latency:** < 500ms average response time on local vLLM.
- **Resilience:** Gracefully falls back to cloud API upon local engine shutdown or connection timeout.
---

## ⚙️ Quickstart & Setup

Create a `.env` file or export your environment variables:
```bash
export TMI_TOKEN="oauth:your_twitch_oauth_token"
export TWITCH_CHANNEL="your_channel_name"
export BOT_PREFIX="!"
export OPENAI_BASE_URL="http://localhost:8001/v1"
export OPENAI_API_KEY="your_openai_key"  # Used for cloud fallback
---

## 👤 Author & Acknowledgments

* **Author:** Uttara Sawant  
* **GitHub:** [@uttarasawantgh](https://github.com/uttarasawantgh)
* **Gemini:** Utilized as an AI collaborator for architectural troubleshooting, debugging complex structural normalization logic, and optimizing code documentation.
