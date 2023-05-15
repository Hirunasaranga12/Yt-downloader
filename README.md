# Yt-downloader
This bot can dowload yt videos
Python
import os
import requests
import json

from telegram.ext import Updater, CommandHandler, MessageHandler, Filters

# Get your API key from https://api.telegram.org/botfather
API_KEY = "YOUR_API_KEY"

# Create the Updater object
updater = Updater(API_KEY)

# Define a command handler for the /start command
def start(update, context):
    # Send a message to the user
    update.message.reply_text("Hi! I'm a YouTube video downloader bot. I can download videos from YouTube and send them to you.")

# Define a message handler for all other messages
def message(update, context):
    # Get the message text
    message = update.message.text

    # If the message is a YouTube link, download the video and send it to the user
    if message.startswith("https://www.youtube.com/watch?v="):
        # Get the video ID from the link
        video_id = message.split("=")[1]

        # Download the video
        response = requests.get("https://www.googleapis.com/youtube/v3/videos?part=snippet,contentDetails&id=" + video_id)
        video_info = json.loads(response.content)

        # Get the video title
        title = video_info["items"][0]["snippet"]["title"]

        # Get the video format
        formats = video_info["items"][0]["contentDetails"]["formats"]

        # Choose the best format
        best_format = formats[0]

        # Download the video
        with open(title + "." + best_format["ext"], "wb") as f:
            f.write(requests.get(best_format["url"]).content)

        # Send the video to the user
        update.message.reply_text("Here is the video you requested:")
        update.message.reply_document(title + "." + best_format["ext"])

# Start the bot
updater.start_polling()

# Run the bot indefinitely
updater.idle()
