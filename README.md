Create a Voice Virtual Assistant with ElevenLabs
Prerequisites: Python fundamentals, API usage
Versions: Python 3.11, python-dotenv 1.0.1, elevenlabs 1.54.0, elevenlabs[pyaudio]
Read Time: 60 minutes

# Introduction
Hey! 👋 I'm Abhay sharma! 

AI around language was getting more and more popular: ChatGPT released in 2022, and multiple companies were releasing APIs around managing text transcription and voice synthesis.

Despite this, voice-based conversational AI was still in its early stages and voice assistants like the one in ChatGPT were just starting to be released in beta.

That's why I decided to create my own! Back then, APIs were limited in functionality so I had to build my own pipeline with one tool per task:

🎤 PyAudio to record the user's voice
⌨️ Deepgram to transcribe the voice to text
🤖 OpenAI GPT-3 to generate a response
📈 ElevenLabs to convert the response to speech
🔊 Pygame to play the response
💻 Taipy to display the conversation
🤝 And a lot of Python code to glue everything together

# Setting Up the Environment
## Install Required Packages
Before we start, make sure you have Python installed. Then, install the required dependencies:

[pip install elevenlabs elevenlabs[pyaudio] python-dotenv]

Processing audio requires additional dependencies on Linux and MacOS:

For Linux, you need to install portaudio19:
[sudo apt install portaudio19]

For MacOS, you need to install portaudio:
[brew install portaudio]

## Setting up ElevenLabs
ElevenLabs provides a Conversational AI API that we will use to create our Voice Assistant.

🎤 The API records the user's voice through the microphone
🖨️ It processes it to know when the user has finished speaking or is interrupting the assistant
🤖 It calls an LLM model to generate a response
📈 It synthesizes the response into speech
🔊 It plays the synthesized speech through the speakers


Sign up at ElevenLabs and follow the instructions to create an account.

Once signed in, go to "Conversational AI".



Go to "Agents".


Click on "Start from blank".


Create a .env file at the root of your project folder. We will use this file to store our API credentials securely. This way they won't be hardcoded in the script. In this .env file, add your Agent ID:


AGENT_ID=your_agent_id

There shouldn’t be spaces around the = in a .env file.

Go to the "Security" tab, enable the "First message" and "System prompt" overrides, and save. This will allow us to customize the assistant's first message and system prompt using Python code.


Click on your profile and go to "API keys". Create a new API key and copy it to your .env file:
API_KEY="sk_XXX...XXX"

Important: Make sure to save your .env file after adding the credentials.



ElevenLabs is now set up and ready to be used in our Python script!

Note: ElevenLabs works with a credit system. When you sign up, you get 10,000 free credits which amount to 15 minutes of conversation. You can buy more credits if needed.

# Building the Voice Assistant
## Load Environment Variables
Create a Python file (e.g., voice_assistant.py) and load your API credentials:

import os
from dotenv import load_dotenv

load_dotenv()

AGENT_ID = os.getenv("AGENT_ID")
API_KEY = os.getenv("API_KEY")

## 2. Configure ElevenLabs Conversation API
We will set up the ElevenLabs client and configure a conversation instance.

We'll start by importing the necessary modules:

from elevenlabs.client import ElevenLabs
from elevenlabs.conversational_ai.conversation import Conversation
from elevenlabs.conversational_ai.default_audio_interface import DefaultAudioInterface
from elevenlabs.types import ConversationConfig

We will then configure the conversation with the agent's first message and system prompt.

We are going to inform the assistant that the user has a schedule and prompt it to help the user. In this part you can customize:

The user's name: what the assistant will call the user
The schedule: the user's schedule that the assistant will use to provide help
The prompt: the message that the assistant will receive when the conversation starts to understand the context of the conversation
The first message: the first message the assistant will say to the user
Prompts are used to provide context to the assistant and help it understand the user's needs.

Here's my example:

user_name = "Alex"
schedule = "Sales Meeting with Taipy at 10:00; Gym with Sophie at 17:00"
prompt = f"You are a helpful assistant. Your interlocutor has the following schedule: {schedule}."
first_message = f"Hello {user_name}, how can I help you today?"

Underneath in the same file, we are then going to set this configuration to our ElevenLabs agent:

conversation_override = {
    "agent": {
        "prompt": {
            "prompt": prompt,
        },
        "first_message": first_message,
    },
}

config = ConversationConfig(
    conversation_config_override=conversation_override,
    extra_body={},
    dynamic_variables={},
)

client = ElevenLabs(api_key=API_KEY)

conversation = Conversation(
    client,
    AGENT_ID,
    config=config,
    requires_auth=True,
    audio_interface=DefaultAudioInterface(),
)

## 3. Implement Callbacks for Responses
We'll also need to handle assistant responses by printing the assistant's responses and user transcripts, as well as handling the situation where the user interrupts the assistant. We can do so by implementing a few callback functions underneath our configuration.

def print_agent_response(response):
    print(f"Agent: {response}")

def print_interrupted_response(original, corrected):
    print(f"Agent interrupted, truncated response: {corrected}")

def print_user_transcript(transcript):
    print(f"User: {transcript}")

## 4. Start the Voice Assistant Session
Finally, initiate the conversation session in the same file:

conversation = Conversation(
    client,
    AGENT_ID,
    config=config,
    requires_auth=True,
    audio_interface=DefaultAudioInterface(),
    callback_agent_response=print_agent_response,
    callback_agent_response_correction=print_interrupted_response,
    callback_user_transcript=print_user_transcript,
)

conversation.start_session()

# Running the Assistant
Please make sure your audio devices are correctly set up in your system settings before running the code.

Execute the script:

python voice_assistant.py

The assistant will start listening for input and responding in real time!

You can stop the assistant at any time by closing the terminal.

# Conclusion
Congratulations! 🎉

You've successfully built a Voice Virtual Assistant using ElevenLabs API. You can extend its capabilities by integrating it with home automation, calendars, or other APIs to make it even more useful.

Stay creative and keep experimenting with AI-powered assistants!

