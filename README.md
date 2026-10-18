# UbeKeso Cart

A responsive web app (it fills the whole browser window on computers, tablets and phones) for an ube cheese turon cart in Cebu City, now with **customer accounts, a staff role, an owner (admin) account and live order updates**.

Built for the **Event-Driven Programming** final project. The app logic is Python (**PyScript** in the browser, plus a small standard-library Python server). There is no JavaScript logic.

## How to run

1. Install Python 3 (python.org).
2. Open a terminal in this folder and run:

   ```
   python run.py
   ```

   (Some computers use `python3 run.py` or `py run.py`.)
3. The browser opens the app.

To try it on phones, run `python run.py --lan`, then open the address it prints (for example `http://192.168.1.20:8000/index.html`) on a phone on the same Wi-Fi. Orders placed on the phone appear on the other devices within about 3 seconds.

Notes
- Internet is needed for PyScript and Google Fonts. Do not double-click `index.html`; use `run.py`.
- The app now needs `run.py` (the server stores accounts and orders). It will not work on a static host such as GitHub Pages.

## Start fresh (reset the database)

The `data.json` in this folder is already brand new: one owner account, no customers, no staff, no orders, no sales, no loyalty cards, every ingredient at 0 and an empty activity log.

To wipe everything again at any time (for example before your presentation), stop the server with Ctrl+C and run:

```
python reset_data.py
```

Type `YES` when asked (or use `python reset_data.py --yes`). Your logo and promo video in `media/` are not touched.

## Logo, About us & FAQs, and the promo video

- **Logo:** your Ube-Kesoturon picture is used on the loading screen, sign in, create account, the About page, and as the browser icon. The files are in `media/` (`logo.jpg`, `logo-small.jpg`, `logo-icon.png`). Replace them with another picture of the same names to change the logo.
- **About us & FAQs:** a new page anyone can open, even before signing in. Find it with **About us & FAQs** on the sign in screen, on the customer **Account** tab, and at the bottom of the staff/owner **Home**. The questions are written in `faq_items()` in `main.py`; prices and the number of stamps update by themselves from `server.py`.
- **Promo video space:** a "Watch: Ube-Kesoturon" card on the staff/owner **Home**, on the customer **Order** screen and on the About page. It shows a "Promo video coming soon" box until you add a video. To add yours, save it as `media/promo.mp4` (`promo.webm` or `promo.mov` also work) and refresh the page. No restart is needed. Tip: keep it short and compressed (under about 30 MB) so it loads quickly on phones.

## Accounts

| Who | How they get an account | What they see |
| --- | --- | --- |
| **Customer** | "Create a customer account" on the sign in screen: name, **Gmail address** (verified with a 6-digit code emailed to it), password (8+ characters) | Order ahead, My orders (live status), My card (loyalty stamps), Account |
| **Owner (admin)** | Created automatically the first time the server starts | Everything: Home dashboard with "Needs attention", all orders, Staff accounts and activity log, Stock, Loyalty, Reports |
| **Staff** | Created by the owner on the Staff tab (the + button) | Home, Sell, Orders, Loyalty, Stock (no Reports, no staff list) |

**Owner login (first run):** `admin@ubekeso.ph` / `Admin#2026`. The app shows a "Change your password" banner until you change it. To use your own from the start, set `UBEKESO_ADMIN_EMAIL` and `UBEKESO_ADMIN_PASSWORD` before the first run.

Sign in with your Gmail (the owner's first account, `admin@ubekeso.ph`, signs in with its own email).

## The live order flow

1. A customer places an order (pieces, extra cheese, pickup time, cash or e-wallet at pickup).
2. Staff and the owner see it in **Orders** with a red badge on the tab and a "New order from ..." message.
3. Staff tap **Start frying**, **Mark ready**, then **Picked up and paid**. The customer's **My orders** screen follows each step and shows a message when it is ready.
4. The owner sees every order, who started it, and the activity log of what each staff member did and when (the owner can delete single entries or clear the log). The Staff tab also shows who is online right now.
5. When an order is picked up, it is added to today's sales and the customer gets a loyalty stamp.

Customers can cancel an order while it is still **New**.

## Gmail accounts and emailed codes

Customers and staff must use a **Gmail address**. A Gmail is accepted only if it is real **and active**:

1. The customer types their name, Gmail and password and taps **Send me a code**.
2. The server emails a 6-digit code to that Gmail (valid 10 minutes, 5 tries).
3. The account is created only after the correct code is typed. A fake, mistyped or unused Gmail never receives the code, so it can never be registered.

The same emailed code is used for **Forgot password**. Dots and `+tags` are ignored for Gmail (`m.aria+x@gmail.com` is the same inbox as `maria@gmail.com`), so one inbox cannot be registered twice. Staff accounts created by the owner must also be Gmail addresses.

### Turn on real email sending (one time)

1. Use a Gmail account for the cart (for example `ubekesocart@gmail.com`). Turn on **2-Step Verification** in its Google Account.
2. In Google Account > Security > **App passwords**, create one named "UbeKeso Cart". Google shows a 16-letter password.
3. Copy `mail_settings.example.json` to `mail_settings.json` and fill it in:

   ```
   { "gmail_address": "ubekesocart@gmail.com", "app_password": "abcd efgh ijkl mnop" }
   ```
4. Restart `python run.py`. It prints "Gmail sending is ON".

`mail_settings.json` is never sent to browsers. Do not share it or upload it to GitHub. Instead of the file you can set the environment variables `UBEKESO_SMTP_USER` and `UBEKESO_SMTP_PASSWORD`.

Until this is set up the app runs in **demo mode**: the code is shown inside the app so you can still test, but that does not prove a Gmail is real.

## Your data is always saved

Every change (orders, accounts, stock, stamps, sales) is written to `data.json` on the computer that runs `run.py`, right away, and forced onto the disk. It stays there when people sign out, when phones go offline, when the browser closes and when you stop and restart the server. Sign-ins also survive restarts.

- Writes are crash-safe (a temporary file is swapped in), so a power cut cannot leave a half-written file.
- A copy of `data.json` is kept every day in the `backups/` folder (last 14 days). If `data.json` is ever damaged, the newest backup is restored automatically on start.
- `python reset_data.py` copies your old data to `backups/` before wiping it.
- If a phone loses Wi-Fi, the app keeps its sign in and reconnects by itself. An order sent while offline does not go through; the app shows "Connection lost" and you tap again once back online.
- The computer running `run.py` must be on for the app to open. To use it from anywhere, run it on an always-on computer or a hosting service.

## Files

| File | What it does |
| --- | --- |
| `index.html` | Page shell and PyScript loader |
| `style.css`, `extra.css` | Styles |
| `main.py` | The app in the browser: state, screens, event handlers, live polling |
| `server.py` | Backend: accounts, roles, orders, activity log, password reset, saving to `data.json` |
| `run.py` | Starts the server and opens the browser |
| `reset_data.py` | Erases all data and starts the database fresh |
| `media/` | Logo pictures and your promo video (`promo.mp4`) |
| `pyscript.toml` | PyScript config |
| `data.json` | Created on first run; all accounts, orders and stock. Delete it to start fresh |
| `backups/` | Daily copies of `data.json` (last 14 days) |
| `mail_settings.json` | Your sending Gmail + App password (you create it; see above) |

## Event-Driven Programming in `main.py`

| Event | Where | Handler |
| --- | --- | --- |
| `click` (event delegation on `data-action`) | all buttons, tabs, steppers, chips | `on_click` -> `CLICK_ACTIONS` |
| `submit` | sign in, sign up, forgot and reset password, change password, new card, pre-order, stock, new staff | `on_submit` -> `SUBMIT_HANDLERS` |
| `input` | Loyalty search box | `on_input` |
| `change` | Extra cheese switch (sale and customer order), pickup time | `on_change` |
| `keydown` | Escape closes a form sheet | `on_keydown` |
| `setInterval` timer, every 3 s | live updates: asks the server what changed, redraws when something did | `on_tick` -> `poll` |
| `visibilitychange` | refreshes at once when you return to the tab | `on_visible` |
| `setTimeout` timer | toast message hides itself | `toast` |

The screen is never redrawn while someone is typing in a field or has a form open; the update is applied right after.

## Security notes

- Passwords are stored only as salted PBKDF2-SHA256 hashes. Sign in is rate limited (5 wrong tries = 1 minute wait).
- Every action is checked on the server by role. A customer cannot create staff or move orders, even by editing the page.
- `server.py`, `run.py`, `reset_data.py` and `data.json` are never served to browsers. Only pictures and videos inside `media/` are shared.
- Running over plain HTTP on your own Wi-Fi is fine for a school project. For a public deployment use HTTPS and a real database.

## Things you can change

At the top of `server.py` (`CONFIG`): `price`, `extra_cheese_price` (5 is a placeholder; set your real price), `daily_target`, `month_goal`, `cost_per_piece`, stamps for a free piece, low-stock levels and minimum password length. The app picks them up automatically.

Behavior choices where the wireframes were silent
- Starting to fry an order subtracts those pieces from stock; "Picked up and paid" adds the order to today's sales.
- One stamp is added per sale or per picked-up order.
- Today's count resets at midnight and is saved to the week's history. Sales and the Reports numbers start at zero.
- Everything starts empty: no orders, no sales, every ingredient at 0 (use Add delivery to enter stock) and no sample loyalty cards.
