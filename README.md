
## Setup Instructions

1. **Create a Replit Account:**  
   Create a Replit account at [https://replit.com](https://replit.com).

2. **Create a Repl:**  
   After creating an account, create a new Repl. Select the **Python** template.

3. **Paste in the Code:**  
   Paste the provided code into the `main.py` file in your newly created Repl.

4. **Create Two Discord Webhooks:**  
   - **Main Tracker Webhook:**  
     Create a Discord webhook for the main tracker. This will be used to send notifications about detected items.
   - **Status Webhook:**  
     Create a second Discord webhook for status updates. This will be used to send periodic status updates about the script's health and activity.

   You can create webhooks in your Discord server by following these steps:  
   - Open your Discord server and go to **Server Settings** > **Integrations** > **Webhooks**.
   - Create a new webhook and copy its URL.

5. **Replace the `TrackerName`:**  
   Firstly, replace the variable `TrackerName` with the name of your tracker.

6. **Replace the Webhook URLs:**  
   Then, replace the `WebhookURL` and `StatusWebhookURL` variables with the Discord webhook URLs you just created.  
   - `WebhookURL` is for the **main tracker webhook**.  
   - `StatusWebhookURL` is for the **status updates webhook**.

7. **Start Gorilla Tag and Use Unity Explorer Mod:**  
   Start Gorilla Tag while using the Unity Explorer mod. You can download the mod from this link:  
   `https://github.com/sinai-dev/UnityExplorer`

8. **Select the PlayFab Authenticator:**  
   Select **Networking Scripts > PlayFab Authenticator > gorillanetworking** and scroll down until you see the `_sessionTicket` field.

9. **Copy the Session Ticket:**  
   Copy the value of the `_sessionTicket` field.

10. **Paste the Session Ticket:**  
    Paste the copied value into the `SessionTickets` list in the script.

11. **Run the Script:**
    Finally, run the script in your Replit Python virtual environment.
