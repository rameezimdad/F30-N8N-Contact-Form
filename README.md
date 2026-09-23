# n8n Contact Form → Google Sheets

A ready-made website contact form that sends every submission to an **n8n Webhook**. n8n then saves it as a new row in **Google Sheets** and can email you an alert. You don't need a backend, a database or PHP. It's a single `index.html` file.

## 🎬 Video Tutorial

[![Watch the tutorial on YouTube](https://img.shields.io/badge/YouTube-Watch%20the%20full%20tutorial-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://youtu.be/YqorbcCGAqE)

Watch the step-by-step build: https://youtu.be/YqorbcCGAqE

> Developed by **Mohammad Rameez Imdad (Rameez Scripts)**
> YouTube: [@rameezimdad](https://www.youtube.com/@rameezimdad) (Subscribe for more!)

## Features

- 9 fields: Name, Email, Phone, Company, City, Budget, Subject, Preferred Date, Message
- Sends the data as form-urlencoded, so the browser skips the CORS preflight and works with any n8n Webhook
- Test mode: add `?test` to the page URL to use the n8n **Test URL**
- Demo-fill button (ⓘ icon) that fills the form with sample data
- Sending overlay, success and error popups (SweetAlert2), mobile friendly

## 1. Paste your own n8n URLs

Open `index.html` and find these two lines near the bottom:

```js
const N8N_TEST_URL = 'PASTE_YOUR_N8N_TEST_URL_HERE';
const N8N_LIVE_URL = 'PASTE_YOUR_N8N_PRODUCTION_URL_HERE';
```

Copy both URLs from your n8n **Webhook** node (**Test URL** and **Production URL** tabs) and paste them in. They look like this:

```
https://your-name.app.n8n.cloud/webhook-test/contact-form
https://your-name.app.n8n.cloud/webhook/contact-form
```

Until you paste them, the form shows a "Webhook URL missing" message instead of sending.

## 2. Google Sheet headers

Create a sheet and paste these headers into **A1:J1**:

```
Timestamp	Name	Email	Phone	Company	City	Budget	Subject	Preferred Date	Message
```

## 3. n8n workflow

1. **Webhook** node: HTTP Method `POST`, Path `contact-form`.
2. **Google Sheets** node: **Append Row in Sheet**, then choose **Map Each Column Manually**:

| Column | Value |
|---|---|
| Timestamp | `{{ $now.toUTC().toISO() }}` |
| Name | `{{ $json.body.name }}` |
| Email | `{{ $json.body.email }}` |
| Phone | `{{ $json.body.phone }}` |
| Company | `{{ $json.body.company }}` |
| City | `{{ $json.body.city }}` |
| Budget | `{{ $json.body.budget }}` |
| Subject | `{{ $json.body.subject }}` |
| Preferred Date | `{{ $json.body.preferred_date }}` |
| Message | `{{ $json.body.message }}` |

   Under **Add option**, set **Cell Format** to **Use Format From n8n**. This keeps the leading 0 on phone numbers.
3. *(Optional)* **Gmail → Send a message** after the Sheets node for an email alert. Use `{{ $('Webhook').item.json.body.name }}` rather than `$json`, because after the Sheets node `$json` holds the Sheets result, not the form data.
4. Click **Publish** (older versions: set the workflow to **Active**).

## 4. Put the form online

Upload `index.html` to any static host, such as [Netlify Drop](https://app.netlify.com/drop), GitHub Pages or Hostinger.

- Testing: open `your-site.com/?test`, after clicking **Listen for test event** in n8n
- Live: open `your-site.com`, once the workflow is published

## Quick fixes

| Problem | Fix |
|---|---|
| "Webhook is not registered" | With `?test`, click **Listen for test event** first. Without it, the workflow must be published. |
| Row added but the cells are empty | Use `$json.body.name`, not `$json.name`. |
| Phone lost its leading 0 | Set **Cell Format** to **Use Format From n8n**. |

## 📩 Let's Work Together

- 💬 WhatsApp: https://whatsapp.rameezscripts.com
- 📧 Email: Contact@rameezscripts.com

_Built by Rameez Scripts._
