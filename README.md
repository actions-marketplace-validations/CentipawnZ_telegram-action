# 🚀 Telegram Notify Action

A powerful, robust GitHub Action to send Telegram notifications, supporting text messages, markdown formatting, and media attachments (photos, documents, videos, and audio).

Designed natively using `bash` and `curl` for lightweight execution without the overhead of heavy Docker images, making it universally compatible with GitHub runners.

## ✨ Features
- 🚀 **Native Composite Action:** Avoids the overhead of pulling huge Docker-action wrappers, runs instantly on any Linux/macOS runner.
- 💬 **Topic Support:** Direct messages to a specific Telegram forum topic using `topic_id`.
- 🖼️ **Media Attachments:** Send local files or URLs seamlessly (Photos, Documents, Videos, Audio).
- ✍️ **Secure Multi-line Handling:** Safely escapes complex multi-line messages and special characters.

## 📥 Inputs

| Parameter                  | Required | Default      | Description                                                                                                                    |
|----------------------------|----------|--------------|--------------------------------------------------------------------------------------------------------------------------------|
| `chat_id`                  | **Yes**  |              | Unique identifier for the target chat (e.g. `-100123456789`) or username of the target channel.                                |
| `token`                    | **Yes**  |              | Telegram Bot API authorization token.                                                                                          |
| `message`                  | No*      |              | Content of the message. Supports multi-line strings. Used as caption if media is provided. (*Required if no media provided).   |
| `topic_id`                 | No       |              | Unique identifier for the target message thread (topic ID) if the Chat uses Forum format.                                      |
| `format`                   | No       | `MarkdownV2` | The parsing mode for the message. Can be `Markdown`, `MarkdownV2`, or `HTML`.                                                  |
| `disable_web_page_preview` | No       | `false`      | Disables link previews for URLs in the message (only applies to text messages).                                                |
| `photo`                    | No       |              | Path to local photo file or direct URL to an image.                                                                            |
| `document`                 | No       |              | Path to local document file or direct URL.                                                                                     |
| `video`                    | No       |              | Path to local video file or direct URL.                                                                                        |
| `audio`                    | No       |              | Path to local audio file or direct URL.                                                                                        |

> **Note:** You can only send one media type per action step. If multiple media inputs are provided, it prioritizes in order: `photo` > `document` > `video` > `audio`.

## 🛠 Usage Examples

### 1. Simple Text Message (with Markdown)
```yaml
name: Notify Telegram
on: [push]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Text
        uses: CentipawnZ/telegram-action@main
        with:
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          format: "HTML"
          message: |
            🚀 <b>Pipeline Activated</b>
            Repository: ${{ github.repository }}
            Commit: ${{ github.sha }}
```

### 2. Send a File / Document
```yaml
      - name: Build and Send APK
        uses: CentipawnZ/telegram-action@main
        with:
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          document: ./build/app-release.apk
          message: "🎉 New Android release build is ready!"
```

### 3. Send an Image with Caption
```yaml
      - name: Send Image
        uses: CentipawnZ/telegram-action@main
        with:
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          photo: https://example.com/status-image.png
          message: "Dashboard Status Screenshot"
```

### 4. Send Message to a Specific Topic
```yaml
      - name: Notify Forum Topic
        uses: CentipawnZ/telegram-action@main
        with:
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          token: ${{ secrets.TELEGRAM_TOKEN }}
          topic_id: "42"
          message: "This message goes to thread/topic #42"
```

## 🔍 How to get `chat_id` and `topic_id`
1. Access the Telegram API to get the bot's updates:
   ```bash
   curl https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates
   ```
2. Look for the message object in the JSON response.
3. Use the `chat.id` value for `chat_id`. 
4. If your message is inside a Topic, find `message_thread_id` and map it to `topic_id`.
