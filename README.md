# Wiplist Embed

A lightweight JavaScript SDK for embedding Wiplist job and time creation into any website.

## Installation

**CDN (script tag)**

```html
<script src="https://embed.wiplist.io/embed.js"></script>
```

**npm**

```bash
npm install @wiplist/embed
```

## Quick start

**Script tag**

Add the script to your HTML, then initialise with your API key:

```html
<script src="https://embed.wiplist.io/embed.js"></script>
<script>
  const wiplist = Wiplist({
    apiKey: "your_api_key",
    onJobCreated: function (job) {
      console.log("Created:", job.job_number, job.job_title);
    },
  });

  wiplist.createJob();
</script>
```

Download [demo.html](demo.html) for a working script tag example you can open directly in your browser.

**npm / ES module**

```js
import Wiplist from "@wiplist/embed";

const wiplist = Wiplist({ apiKey: "your_api_key" });
wiplist.createJob();
```

The API is identical regardless of how you load the SDK — only the installation method differs.

Get your API key from Wiplist under **Settings → API Keys**.

## Configuration

```js
const wiplist = Wiplist({
  // Required
  apiKey: "your_api_key",

  // Required: act as this user for created records.
  apiUser: "user@company.com",

  // Optional: modal height — any CSS value. Defaults to '70vh'.
  modalHeight: "600px",

  // Callbacks
  onJobCreated: function (job) {
    /* ... */
  },
  onTimeCreated: function (time) {
    /* ... */
  },
  onConnected: function () {
    /* ... */
  },
  onError: function (error) {
    /* ... */
  },
});
```

| Option          | Type     | Required | Description                                       |
| --------------- | -------- | -------- | ------------------------------------------------- |
| `apiKey`        | string   | Yes      | Your API key                                      |
| `apiUser`       | string   | Yes      | Email of the user to act as for created records   |
| `modalHeight`   | string   | No       | CSS height of the modal (default `"70vh"`)        |
| `onJobCreated`  | function | No       | Called when a job is created                      |
| `onTimeCreated` | function | No       | Called when a time entry is created               |
| `onConnected`   | function | No       | Called when the widget authenticates successfully |
| `onError`       | function | No       | Called on connection or auth failure              |

## Methods

### `wiplist.createJob(options?)`

Opens the job creation form.

```js
// Basic
wiplist.createJob();

// With pre-filled fields
wiplist.createJob({
  team_id: "xxxxx",
  user_id: "xxxxx",
  company_id: "xxxxx",
  contact_id: "xxxxx",
  workflow_id: "xxxxx",
  status: Wiplist.JobStatus.IN_PROGRESS,
  type: Wiplist.JobType.FIXED_PRICE,
  job_title: "Kitchen Renovation",
  description: "Full remodel",
  company_name: "Acme Corp",
});
```

| Option         | Type   | Description                              |
| -------------- | ------ | ---------------------------------------- |
| `team_id`      | string | Associate with a team                    |
| `user_id`      | string | Assign to this user                      |
| `company_id`   | string | Associate with a company                 |
| `contact_id`   | string | Associate with a contact                 |
| `workflow_id`  | string | Use this workflow                        |
| `status`       | number | Initial status (see `Wiplist.JobStatus`) |
| `type`         | number | Job type (see `Wiplist.JobType`)         |
| `job_title`    | string | Pre-fill the job title                   |
| `description`  | string | Pre-fill the description                 |
| `company_name` | string | Pre-fill or look up a client by name     |

### `wiplist.createTime(options?)`

Opens the time entry form.

```js
// Basic
wiplist.createTime();

// Pre-select a job
wiplist.createTime({ job_id: "xxxxx" });
```

| Option    | Type   | Description           |
| --------- | ------ | --------------------- |
| `job_id`  | string | Pre-select this job   |
| `user_id` | string | Log time as this user |

### `wiplist.close()`

Programmatically closes the modal.

### `wiplist.destroy()`

Removes the widget from the DOM and cleans up event listeners. Call this if you need to re-initialise with a different API key.

## Status constants

Use `Wiplist.JobStatus` instead of raw integers for readability:

```js
Wiplist.JobStatus.PENDING; // 200
Wiplist.JobStatus.IN_PROGRESS; // 210
```

## Type constants

Use `Wiplist.JobType` instead of raw integers for readability:

```js
Wiplist.JobType.NON_BILLABLE; // 10
Wiplist.JobType.FIXED_PRICE; // 20
```

## Callback payloads

### `onJobCreated(job)`

```js
{
  id:           "xxxxx",           // hashid
  team_id:      "xxxxx",           // nullable
  user_id:      "xxxxx",           // nullable
  company_id:   "xxxxx",           // nullable
  workflow_id:  "xxxxx",           // nullable
  status:       200,
  type:         20,
  job_number:   "J-5432",
  job_title:    "Kitchen Renovation",
  description:  "Full remodel...",
  date_due:     "180000384",       // nullable
  created_at:   "180000384",
}
```

### `onTimeCreated(time)`

```js
{
  id:          "xxxxx",            // hashid
  job_id:      "xxxxx",
  description: "Cabinet work",     // nullable
  unit_price:  75.00,              // nullable
  time_total:  480,                // minutes
  date_start:  "180000384",
  date_end:    "180000384",
  created_at:  "180000384",
}
```

## Troubleshooting

**Widget fails to load**

Check the browser console. The most common cause is a Content Security Policy blocking the iframe. Add `embed.wiplist.io` to your `frame-src` directive:

```html
<meta
  http-equiv="Content-Security-Policy"
  content="frame-src https://embed.wiplist.io; script-src 'self' https://embed.wiplist.io;"
/>
```

**"Connection failed" / `onError` fires**

The API key is invalid or the widget couldn't reach the Wiplist API. Verify the key in your dashboard under **Settings → Widget API Keys**.

**Modal opens but form is blank / users don't load**

Your domain may not be listed as an allowed origin for the API key. Check the key settings in the dashboard.

## Browser support

Chrome, Firefox, Safari, Edge — latest two versions. iOS Safari and Chrome for Android supported.

## License

MIT
