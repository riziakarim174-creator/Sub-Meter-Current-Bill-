
# ⚡ বিদ্যুৎ বিল হিসাব — Electricity Bill Splitter

A single-file web app for landlords who split one main-meter electricity bill between several sub-meter holders (tenants). Enter each sub-meter's reading and the month's total bill, and the app works out every tenant's share, tracks who has paid, and carries unpaid dues into the next month.

No server, no build step, no dependencies. Everything runs in the browser and is saved on the device.

- **Languages:** Bangla and English (switch on the login page or in the profile menu)
- **Works offline:** yes
- **Stack:** plain HTML + CSS + JavaScript, `localStorage` for data

---

## Features

| Area | What it does |
|---|---|
| **Accounts** | Each user has a username and PIN (4–8 digits). Data is kept separately per user on the same browser. Login is remembered on the device until you log out. PIN can be changed from the profile menu. |
| **Meter setup** | Add main meters (e.g. `5A`) and the sub-meters under them (e.g. `501`–`506`) once, with holder name, mobile number and meter number. Meter cards are green and expand on click. |
| **Monthly bill** | Previous readings fill in automatically from the last month. You only enter the current readings and the month's total bill. The demand charge defaults to ৳136 per sub-meter and can be changed. |
| **Reading entry (offline)** | When the device is offline the app opens straight into reading entry. Enter every sub-meter's current reading and save. When you are back online those readings are already in the bill form. |
| **Fixed previous readings** | The previous reading is read-only. To change it, edit the previous month's current reading. It is only editable in the very first month, when no earlier month exists. |
| **Offline-reading protection** | Readings saved from reading entry are locked in the full bill view. Changing one asks for confirmation first. |
| **Payments** | Mark each sub-meter paid or due, with the payment date. |
| **Arrears carry-forward** | If January is unpaid, February shows *January due + February bill*. Paying February marks January paid too, and both get the payment date. |
| **History** | Month-by-month history, plus a history for each sub-meter (units, bill, due, paid date). |
| **Meter replacement** | If a sub-meter is replaced, tick *meter change* for that month (see the rule below). |
| **Delete and undo** | Deleting a meter or sub-meter asks for confirmation. Deleted meters go to a *Deleted meters* list and can be restored. A deleted main meter is only hidden — its history is kept. |
| **No duplicates** | A meter or sub-meter with the same name or number cannot be added twice. If it exists in history, the app asks whether to bring it back and fills in everything from history. |
| **Backup / restore** | Download all data as a JSON file and restore it later from the History tab. |

---

## How the bill is calculated

For one main meter with *n* sub-meters:

1. Each sub-meter holder pays the **full demand charge** (not divided).
2. What is left of the bill is shared by usage:

```
remaining  = total bill − (demand charge × n)
units_i    = current reading_i − previous reading_i
rate       = remaining ÷ total units
share_i    = demand charge + units_i × rate
```

3. Amounts are rounded to paisa and the last sub-meter absorbs any rounding difference, so the shares always add up exactly to the main meter's bill. The app shows a green confirmation when they match.

**Example** — total bill ৳2000, demand charge ৳136, three sub-meters using 50, 60 and 30 units:

```
remaining = 2000 − (136 × 3) = 1592
rate      = 1592 ÷ 140 = ৳11.371 / unit
A = 136 + 50 × 11.371 = ৳704.57
B = 136 + 60 × 11.371 = ৳818.29
C = 136 + 30 × 11.371 = ৳477.14        → total ৳2000.00 ✓
```

### Meter replacement rule

In the month a sub-meter is replaced, the holder pays for last month's units:

```
billed units = new meter's units + max(0, last month's units − new meter's units)
```

Example: January 56 units. In February the meter is replaced and the new meter shows 30 units, so February is billed as 30 + (56 − 30) = **56 units**. From March the new meter works like any other. The old meter's last reading, its month and the new meter's details are stored in the February history.

---

## Run it

1. Download `index.html` (the app is this one file).
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari — desktop or mobile).
3. Create an account, add your meters and sub-meters, and start entering readings.

### Host it on GitHub Pages

1. Put the file in the repository root and name it `index.html`.
2. In the repository go to **Settings → Pages**, choose the `main` branch and the `/ (root)` folder, and save.
3. Open the URL GitHub gives you.

Opening it from Pages needs internet the first time. For offline use on a phone, open the downloaded file directly, or add the page to your home screen after it has loaded once.

---

## Where your data lives

- Everything is stored in the browser's `localStorage`, under keys that include the username. Nothing is sent to any server.
- Data stays on **that browser on that device**. It does not sync between phones or computers.
- Clearing the browser's site data or cache deletes it. **Use *Download backup* in the History tab regularly.**
- The PIN keeps casual users out, but it is not strong security. Data is not encrypted, so do not store anything sensitive.

---

## Project structure

```
.
├── index.html   # the whole app: markup, styles and logic
└── README.md
```

---

## বাংলায় সংক্ষেপে

বাড়িওয়ালার জন্য একটা ছোট ওয়েব অ্যাপ। মেইন মিটারের মোট বিল থেকে প্রতিটি সাব-মিটার হোল্ডারের ভাগ নিজে নিজেই হিসাব হয়, কে টাকা দিয়েছে আর কার বাকি আছে তা ট্র্যাক হয়, এবং বাকি টাকা পরের মাসের বিলে যোগ হয়ে যায়।

- মিটার ও সাব-মিটার **একবারই** যোগ করতে হয়। প্রতি মাসে শুধু বর্তমান রিডিং আর মোট বিল দিলেই হয়, আগের রিডিং আগের মাস থেকে নিজে আসে।
- প্রতিটি সাব-মিটার হোল্ডার পুরো ডিমান্ড চার্জ (ডিফল্ট ৳১৩৬) দেয়। বাকি টাকা ব্যবহৃত ইউনিট অনুযায়ী ভাগ হয়।
- জানুয়ারি বাকি থাকলে ফেব্রুয়ারিতে দুই মাসের বিল একসাথে দেখায়। ফেব্রুয়ারি পরিশোধ করলে জানুয়ারিও পরিশোধিত হয়ে যায়, তারিখসহ।
- অফলাইনে খুললে সরাসরি রিডিং এন্ট্রি পেজ আসে। সেভ করা রিডিং অনলাইনে এলে নিজে থেকে বিলের ফর্মে বসে যায়।
- ডিলিট করা মিটার ফিরিয়ে আনা যায়, আর একই নাম বা নম্বরের মিটার দ্বিতীয়বার যোগ করা যায় না।
- ডেটা শুধু ওই ব্রাউজারেই থাকে। হারানোর ঝুঁকি কমাতে হিস্ট্রি ট্যাব থেকে নিয়মিত ব্যাকআপ নিন।

---

## License

Add the license you want to use (for example MIT) as a `LICENSE` file in the repository.
