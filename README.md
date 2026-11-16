# Restaurant Chatbot

Food-ordering chatbot: a Dialogflow ES agent (embedded in `frontend/home.html` via Dialogflow Messenger) that calls a FastAPI backend through webhook fulfillment. Orders are stored in MySQL.

## Webhook

A single `POST /` endpoint reads the Dialogflow `WebhookRequest`, routes on the intent name and replies with `fulfillmentText`.

| Intent | Handler | Purpose |
|---|---|---|
| `order.add-context: ongoing-order` | `add_to_order` | Add items/quantities to the session's order |
| `order.remove-context: ongoing-order` | `remove_from_order` | Remove items from the order |
| `order.complete - context: ongoing-order` | `complete_order` | Save the order to MySQL, return order ID and total |
| `track.order- context: ongoing-tracking` | `track_order` | Look up order status by ID |

Intent names must match the Dialogflow console exactly. Dialogflow needs a public HTTPS URL for the webhook (e.g. ngrok or a deployed host).

## Run

```bash
pip install fastapi uvicorn mysql-connector-python
cp .env.example .env   # then set the variables in your shell/environment
uvicorn main:app --reload
```

In-progress orders are held in memory per Dialogflow session and are lost on restart.
