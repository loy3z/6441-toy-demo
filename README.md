# RPG Authority Lab

A local, self-contained toy demo for the research question:

> How do online RPGs use server-side validation to defend against client-side data manipulation, and where can these defences fall short?

The demo contains two controlled experiments.

Experiment 1 loads player-controlled values from `public/client-data.json` and compares two purchase paths:

- **Client-trusted:** the server accepts `clientBalance` and `clientPrice` from the request.
- **Server-authoritative:** the client submits only `itemId`; the server reads the real balance and price before deciding.

Experiment 2 compares two ways to process a one-time quest reward:

- **Validation-only:** the server checks that the quest is complete but does not record whether the reward was already claimed.
- **Idempotency-protected:** the server checks the claimed state and records the request ID, so the same reward is accepted only once.

## Run

```powershell
node server.js
```

Open `http://127.0.0.1:4173`.

## Test

```powershell
node --test
```

## Suggested Demonstration

1. Open `public/client-data.json`.
2. Change `gold` from `100` to `1000`, leave the Moonsteel Sword price at `500`, then save the file.
3. In the browser, select **Reload JSON**.
4. In **Client-trusted** mode, submit a purchase for the Moonsteel Sword. The server accepts the tampered values.
5. Select **Reset server**. The server returns to 100 gold, while the modified local JSON remains unchanged.
6. Switch to **Server-authoritative**, select **Reload JSON**, and submit the same purchase. The server rejects it because its authoritative balance is still 100.
7. Restore the JSON values when the demonstration is complete.

For the reward experiment:

1. Select **Reset server**.
2. Leave the reward mode on **Validation-only** and select **Claim 100 gold** several times. Each identical claim is accepted.
3. Reset the server again and select **Idempotency-protected**.
4. Submit the reward claim once, then repeat it. The first claim is accepted and the replay is rejected.

The webpage does not provide controls for editing gold or price. Tampering happens only by changing the local JSON file. The intentionally vulnerable endpoint is for localhost-only educational testing and must not be reused in a real application.
