# HexChat-Weather-Crypto-IMDB-Bot-AddOn
HexChat-Weather-Crypto-IMDB-Bot-AddOn - Looks up weather, crypto prices, imdb ratings via API keys in a python script for hexchat.

Sure! I will include "s0berage" as the author in the script's comment section and also include it in the printed output when the script is loaded. This helps clarify who developed the script. Below is the updated script with the author attribute included.

### Updated HexChat Script with Author

```python
import hexchat
import requests
import time

# Script Information
SCRIPT_NAME = "Weather, Crypto, and IMDB plugin"
AUTHOR = "s0berage"
VERSION = 'v1.1.111'
DESCRIPTION = "A HexChat plugin to get weather, cryptocurrency prices, and IMDB ratings."

# Set your API keys here
WEATHER_API_KEY = 'your_openweather_api_key'
CRYPTO_API_URL = 'https://api.coingecko.com/api/v3/simple/price'
IMDB_API_KEY = 'your_omdb_api_key'

# Flood protection settings
COOLDOWN_TIME = 10  # Time in seconds to wait before the same user can issue another command
user_last_command_time = {}

def current_time():
    return time.time()

def fetch_weather(city, country):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city},{country}&appid={WEATHER_API_KEY}&units=metric"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        temp = data['main']['temp']
        weather_desc = data['weather'][0]['description']
        return f"Current weather in {city}, {country}: {temp}°C, {weather_desc}."
    else:
        return "Weather data not found."

def fetch_crypto_price(coin):
    url = f"{CRYPTO_API_URL}?ids={coin}&vs_currencies=usd"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        if coin in data:
            price = data[coin]['usd']
            return f"Current {coin.upper()} price: ${price}."
        else:
            return "Crypto data not found for this coin."
    else:
        return "Error fetching crypto data."

def fetch_imdb_data(query):
    url = f"http://www.omdbapi.com/?t={query}&apikey={IMDB_API_KEY}"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        if data['Response'] == 'True':
            title = data['Title']
            year = data['Year']
            rating = data['imdbRating']
            return f"{title} ({year}): IMDB Rating: {rating}"
        else:
            return "IMDB data not found for this title."
    else:
        return "Error fetching IMDB data."

def check_flood(user):
    current_time_sec = current_time()
    last_time = user_last_command_time.get(user, 0)
    
    if current_time_sec - last_time < COOLDOWN_TIME:
        return True  # Flood detected
    else:
        user_last_command_time[user] = current_time_sec
        return False  # No flood

def command_weather(word, utterance, direction):
    user = hexchat.get_info('nick')
    if check_flood(user):
        hexchat.command("SAY Please wait before sending another command.")
        return

    if len(word) > 2:
        city = word[1]
        country = word[2]
        weather_info = fetch_weather(city, country)
        hexchat.command(f"SAY {weather_info}")
    else:
        hexchat.command("SAY Usage: .w cityname countryname")

def command_crypto(word, utterance, direction):
    user = hexchat.get_info('nick')
    if check_flood(user):
        hexchat.command("SAY Please wait before sending another command.")
        return

    if len(word) > 1:
        coin = word[1].lower()
        crypto_info = fetch_crypto_price(coin)
        hexchat.command(f"SAY {crypto_info}")
    else:
        hexchat.command("SAY Usage: .crypto coinname")

def command_imdb(word, utterance, direction):
    user = hexchat.get_info('nick')
    if check_flood(user):
        hexchat.command("SAY Please wait before sending another command.")
        return

    if len(word) > 1:
        title = ' '.join(word[1:])  # Join the remaining words as the title
        imdb_info = fetch_imdb_data(title)
        hexchat.command(f"SAY {imdb_info}")
    else:
        hexchat.command("SAY Usage: .imdb movie_or_show_title")

# Register commands
hexchat.hook_command("w", command_weather)
hexchat.hook_command("crypto", command_crypto)
hexchat.hook_command("imdb", command_imdb)

# Print banner for the script
hexchat.prnt(f"{SCRIPT_NAME} (Author: {AUTHOR}, Version: {VERSION}) loaded.")
hexchat.prnt(DESCRIPTION)
hexchat.prnt("For more scripts, visit: https://www.github.com/s0berage")
```

### Steps to Deploy
1. **Replace API Keys**: Replace `'your_openweather_api_key'` and `'your_omdb_api_key'` with your actual API keys.
2. **Save the Script**: Save the updated code in a file named `weather_crypto_imdb.py`.
3. **Load the Script in HexChat**:
   - Open HexChat.
   - Go to `Settings -> Plugin and Scripts -> Load`.
   - Select your `weather_crypto_imdb.py` file.

### Usage
- Use the following commands on HexChat to get information:
  - **Weather**: `.w cityname countryname`
  - **Cryptocurrency Price**: `.crypto coinname`
  - **IMDB Ratings**: `.imdb movie_or_show_title`

This version of the script includes the author information, versioning, and advertisement, making it user-friendly and informative. Test it in HexChat to ensure all features work as expected!
