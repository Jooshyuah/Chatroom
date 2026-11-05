# Chatroom

Real-time chat for registered people, with public rooms, direct messages, profiles, replies, emoji reactions, voice calls, and moderation tools.

## Run locally

```sh
npm install
npm start
```

Open [http://localhost:4173](http://localhost:4173). The app creates `data/chatroom.json` for accounts, messages, rooms, and profile settings; avatar and chat images are stored in `data/avatars/` and `data/attachments/`. Passwords are stored as salted scrypt hashes. `Joshua Boudreau` is the default Owner; set `CHATROOM_OWNER_EMAIL` to designate a different account. Owner assignment is not based on signup order.

Run the isolated two-user messaging test with `npm test`.

## School deployment
```sh
npm install
npm start
```

Put Chatroom behind a trusted school gateway. The gateway must enforce the `10.13.1.83` IP rule and the device MAC rule at the router/access point; a browser/server cannot inspect a remote device's MAC address. The provided MAC starts with `cO`, but `O` is not a valid hexadecimal MAC character; confirm whether `C0:C7:DB:9F:1B:90` was intended before configuring the ACL. The gateway must strip any incoming `X-Chatroom-Gateway-Key` header and inject its own secret only after access checks. Set the same high-entropy `CHATROOM_GATEWAY_TOKEN` on Chatroom. In production, the app rejects requests that do not contain this header. Do not expose the app port around the gateway.

Set `CHATROOM_IMAGE_MODERATION_URL` to a trusted service that accepts the raw image body and its `Content-Type`, then returns JSON `{ "safe": true }` only when approved. Optionally set `CHATROOM_IMAGE_MODERATION_KEY` for bearer authentication. Missing, failing, or negative moderation blocks both chat images and avatar photos.

Set `CHATROOM_ICE_SERVERS` to a JSON array of STUN/TURN server entries for school networks, for example `[{"urls":"turn:turn.example.org:3478","username":"...","credential":"..."}]`. Prefer short-lived TURN credentials. Voice requires HTTPS and microphone permission in browsers.

Set a unique `SESSION_SECRET` and use a durable session store and database before deployment. The default Express memory session store and JSON-file persistence are intended for local/single-process use, not production. The profanity dictionary is a useful filter, not a guarantee against every spelling or newly coined slur; moderation review is still needed for a school deployment.
