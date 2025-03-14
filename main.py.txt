from colorama import Fore, init
import requests
import random
import time
import asyncio

init()

TrackerName = "Your Tracker"
SessionTickets = ["YOUR_SESSION_TICKET_HERE"]
WEBHOOK_URL = "YOUR_WEBHOOK_URL"
STATUS_WEBHOOK_URL = "YOUR_STATUS_WEBHOOK_URL"

Cosmetics = [
    {
    "CosmeticName":"Stick",
    "CosmeticId":"LBAAK." 
    },
    {
    "CosmeticName":"Admin Badge",
    "CosmeticId":"LBAAD." 
    },
    {
    "CosmeticName":"Illustrator Badge",
    "CosmeticId":"LBAGS." 
    },
    {
    "CosmeticName":"Finger Painter",
    "CosmeticId":"LBADE." 
    }
]

Colors = [
    { 
    "ColorName": "Red",
    "ColorHex": 0xff0000,
    "Activated": True,
    }
]

Codes = [
    "JMANCURLY", "ELLIOT"
]

def get_active_color():
    for ColorData in Colors:
        if ColorData['Activated']:
            return ColorData['ColorHex']
    return 0x000000

def FreeWebhook(item, code, region, player_count, board_position, content="@everyone"):
    try:
        webhook_data = {
            "content": content,
            "embeds": [{
                "title": f"🔍 Detection Alert 🔍",
                "description": f"Item detected by {TrackerName}",
                "color": 0xFFD700,
                "fields": [
                    {"name": "💫 Item","value": f"```{item}```","inline": False},
                    {"name": "🌍 Location","value": f"```Room: {code}\nRegion: {region}```","inline": True},
                    {"name": "📈 Room Data","value": f"```Players: {player_count}\nPosition: {board_position}```","inline": True}
                ],
                "footer": {"text": f"{TrackerName} | src by elian | discord.gg/4wnShQZA4E"}
            }]
        }

        response = requests.post(WEBHOOK_URL, json=webhook_data)
        response.raise_for_status()
    except Exception as e:
        print(Fore.RED + f"Error in FreeWebhook: {str(e)}")

def StartWebhook():
    try:
        Color = get_active_color()
        current_time = time.strftime("%H:%M:%S", time.localtime())

        free_embed = [{
            "title": "🔍 System Initialization",
            "description": f"The {TrackerName} is now operational and scanning.",
            "color": 0x00FF00,
            "fields": [
                {"name": "📊 Parameters","value": f"```Total Codes: {len(Codes)}\nActive Tickets: {len(SessionTickets)}\nTargeted Items: {len(Cosmetics)}```","inline": False},
                {"name": "💫 Status","value": "```🟢 Active```","inline": True},
                {"name": "🕒 Time","value": f"```{current_time}```","inline": True},
            ],
            "footer": {"text": f"{TrackerName} | src by elian | discord.gg/4wnShQZA4E"}
        }]

        requests.post(STATUS_WEBHOOK_URL, json={"content": "", "embeds": free_embed})
    except Exception as e:
        print(Fore.RED + f"Error in StartWebhook: {str(e)}")

def CheckCode():
    try:
        for code in Codes:
            SessionTicket = random.choice(SessionTickets)
            for region in ['EU', 'US', 'USW']:
                try:
                    print(Fore.CYAN + f"Checking room code: {code}{region}")
                    headers = {"X-Authorization": SessionTicket}
                    json = {"SharedGroupId": code + region}

                    response = requests.post(
                        url="https://63FDD.playfabapi.com/Client/GetSharedGroupData",
                        headers=headers,
                        json=json,
                        timeout=10
                    )

                    requestjson = response.json()

                    if 'code' not in requestjson:
                        print(Fore.YELLOW + f"Invalid response for {code}{region}")
                        continue

                    if requestjson['code'] == 200 and 'data' in requestjson:
                        room_data = requestjson['data'].get('Data', {})
                        player_count = len(room_data)
                        board_position = 0

                        if player_count > 0:
                            print(Fore.LIGHTBLACK_EX + f"Checked Room {code}{region} with {player_count} players.")
                        else:
                            print(Fore.LIGHTBLACK_EX + f"Checked Room {code}{region} - Empty room (0 players).")

                        for key, value in room_data.items():
                            board_position += 1
                            concat = value.get('Value', '')

                            for CosmeticData in Cosmetics:
                                if CosmeticData['CosmeticId'] in concat:
                                    content = "@everyone"
                                    FreeWebhook(CosmeticData['CosmeticName'], code, region, player_count, board_position, content)
                                    print(Fore.GREEN + f"Found {CosmeticData['CosmeticName']} in code: {code}")

                    elif requestjson['code'] == 429:
                        print(Fore.RED + f"Rate limit hit for {code}{region}")
                        continue

                except requests.exceptions.RequestException as e:
                    print(Fore.RED + f"Request error for {code}{region}: {str(e)}")
                    time.sleep(1)
                    continue

    except Exception as e:
        print(Fore.RED + f"Error in CheckCode: {str(e)}")

if __name__ == "__main__":
    StartWebhook()
    while True:
        try:
            CheckCode()
            time.sleep(1)
        except Exception as e:
            print(Fore.RED + f"Error in main loop: {e}")
            print(Fore.YELLOW + "Retrying in 30 seconds...")
            time.sleep(30)
            continue
