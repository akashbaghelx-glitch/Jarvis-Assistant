import speech_recognition as sr
import pyttsx3
import webbrowser
import pyautogui
import os
import time
from datetime import datetime

# --- INITIALIZATION ---
engine = pyttsx3.init()
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)
engine.setProperty('rate', 200)

def get_now():
    return datetime.now().strftime("%H:%M:%S")

def speak(text):
    current_time = get_now()
    print(f"[{current_time}] Assistant: {text}")
    engine.say(text)
    engine.runAndWait()

def take_command():
    r = sr.Recognizer()
    r.dynamic_energy_threshold = True
    r.energy_threshold = 300
    with sr.Microphone() as source:
        print(f"\n[{get_now()}] [READY] Listening...")
        r.adjust_for_ambient_noise(source, duration=1)
        try:
            audio = r.listen(source, timeout=5, phrase_time_limit=8)
            print(f"[{get_now()}] Recognizing...")
            query = r.recognize_google(audio, language='en-in')
            print(f"[{get_now()}] User said: {query}")
            return query.lower()
        except:
            return "none"

# --- MAIN PROGRAM LOOP ---
speak("System fully optimized, Boss. Standing by.")

while True:
    input_text = take_command()

    if input_text == "none":
        continue

    if "jarvis" in input_text or "assistant" in input_text:
        # Clean the word 'jarvis' out of the command
        command = input_text.replace("jarvis", "").replace("assistant", "").strip()

        if command == "":
            speak("Yes? I am listening.")
            command = take_command()

        # --- 1. PRIORITY: OPENING APPLICATIONS ---
        if "open chrome" in command or "google chrome" in command:
            speak("Opening Google Chrome.")
            os.startfile(r"C:\Program Files\Google\Chrome\Application\chrome.exe")

        elif "open brave" in command:
            speak("Opening Brave.")
            os.startfile(r"C:\Program Files\BraveSoftware\Brave-Browser\Application\brave.exe")

        elif "notepad" in command:
            speak("Opening Notepad.")
            os.system("notepad.exe")

        # --- 2. DRIVES & SYSTEM FOLDERS ---
        elif "open c drive" in command:
            speak("Opening C Drive.")
            os.startfile("C:")

        elif "open d drive" in command:
            if os.path.exists("D:"):
                speak("Opening D Drive.")
                os.startfile("D:")
            else:
                speak("I cannot find a D Drive on this system.")

        elif "windows folder" in command:
            speak("Opening Windows folder.")
            os.startfile(r"C:\Windows")

        # --- 3. SMARTER DESKTOP SEARCH ---
        elif "desktop" in command:
            speak("Searching desktop...")
            desktop_path = os.path.join(os.path.join(os.environ['USERPROFILE']), 'Desktop')
            # Clean keywords to find the actual filename
            target = command.replace("open", "").replace("file", "").replace("folder", "").replace("on desktop", "").replace("desktop", "").replace("pdf", "").strip()
            
            found = False
            for item in os.listdir(desktop_path):
                if target.lower() in item.lower():
                    speak(f"Found it. Opening {item}.")
                    os.startfile(os.path.join(desktop_path, item))
                    found = True
                    break
            if not found: speak(f"I searched for {target} but couldn't find it.")

        # --- 4. SMARTER YOUTUBE LOGIC ---
        elif "youtube" in command:
            if "play" in command or "search" in command:
                # Clean up the query strings
                term = command.replace("play", "").replace("search", "").replace("youtube", "").replace("on", "").strip()
                speak(f"Playing {term} on YouTube")
                webbrowser.open(f"https://www.youtube.com/results?search_query={term}")
            else:
                speak("Opening YouTube.")
                webbrowser.open("https://www.youtube.com")

        # --- 5. SMARTER GOOGLE SEARCH ---
        elif "google" in command or "search" in command:
            query = command.replace("search", "").replace("google", "").replace("for", "").replace("on", "").strip()
            if query != "":
                speak(f"Searching Google for {query}")
                webbrowser.open(f"https://www.google.com/search?q={query}")

        # --- 6. SYSTEM INFO & EXIT ---
        elif "time" in command:
            now = datetime.now().strftime("%I:%M %p")
            speak(f"It is {now}")

        elif "thank" in command:
            speak("Always a pleasure.")

        elif "exit" in command or "stop" in command:
            speak("Going offline. Goodbye Boss.")
            break
