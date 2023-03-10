## โจ Trigger.dev Basic Starter

This repo is a very simple starting point for creating your Trigger.dev workflows.

Currently this repo only has a single [customEvent](https://docs.trigger.dev/triggers/custom-events) trigger:

```ts
import { Trigger, customEvent } from "@trigger.dev/sdk";
import { z } from "zod";

new Trigger({
  id: "basic-starter",
  name: "Basic Starter",
  on: customEvent({
    name: "basic.starter",
    schema: z.object({ id: z.string() }),
  }),
  async run(event, ctx) {
    await ctx.logger.info("Hello world from inside trigger.dev");

    return event;
  },
}).listen();
```

## ๐ Deploy

We've made it really easy to deploy this repo to Render.com, if you don't already have a Node.js server to host your triggers.

[Render.com](https://render.com) is a super-fast way to deploy webapps and servers (think of it like a modern Heroku)

<a href="https://render.com/deploy?repo=https://github.com/brudex/triggerexample">
  <img width="144px" src="https://render.com/images/deploy-to-render-button.svg" alt="Deploy to Render">
</a>

> **Note** Make sure you use your "live" trigger.dev API Key when deploying to a server

## ๐ป Run locally

First, in your terminal of choice, clone the repo and install dependencies:

```sh
git clone https://github.com/brudex/triggerexample.git
cd triggerexample
npm install
```

Then execute the following command to create a `.env` file with your development Trigger.dev API Key:

```sh
echo "TRIGGER_API_KEY=<APIKEY>" >> .env
```

And finally you are ready to run the process:

```sh
npm run dev
```

You should see a message output in your terminal like the following:

```
[trigger.dev]  โจ Connected and listening for events [basic-starter]
```

## ๐งช Test it

After successfully running this template locally, head over to your [Trigger.dev Dashboard](https://app.trigger.dev) and you should see your newly created workflow:

![workflow list](https://imagedelivery.net/3TbraffuDZ4aEf8KWOmI_w/34fb0fa8-3649-4c8e-4b27-d31540f3cb00/width=1200)

Click on the workflow in the list and you should come to the Workflow overview page:

![workflow overview](https://imagedelivery.net/3TbraffuDZ4aEf8KWOmI_w/8d68044b-104f-472c-837f-dd1ca62e9d00/width=1200)

Click on the "Test your workflow" button and fill in the JSON needed for [this workflow's](src/index.ts#L7) customEvent Trigger:

![workflow test](https://imagedelivery.net/3TbraffuDZ4aEf8KWOmI_w/dbfdfeed-4230-44ec-5e6c-1e87412a5200/width=1200)

After click "Run Test" you'll be redirected to the Run Details page:

![workflow run](https://imagedelivery.net/3TbraffuDZ4aEf8KWOmI_w/18b46eaa-95d1-49c5-774f-507819360a00/width=1200)

## ๐บ Go Live

After you are happy with your campaign and deploy it live to Render.com (or some other hosting service), you can send custom events that Trigger your workflow using the [sendEvent](https://docs.trigger.dev/reference/send-event) function from the `@trigger.dev/sdk`, or simply by making requests to our [`events`](https://docs.trigger.dev/api-reference/events/sendEvent) API endpoint.

Here is an example of sending the custom event to trigger the workflow contained in this repo using `fetch`:

```ts
const eventId = ulid();
const event = {
  name: "basic.starter",
  payload: {
    id: "user_1234",
  },
};

const response = await fetch("https://app.trigger.dev/api/v1/events", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${process.env.TRIGGER_API_KEY}`,
  },
  body: JSON.stringify({
    id: eventId,
    event,
  }),
});
```

## โ๏ธ Customize

You can easily adapt this workflow to a different event relevant to your app. For example, we have a workflow that runs when a user is created and it looks like this:

```ts
import { Trigger, customEvent } from "@trigger.dev/sdk";
import * as slack from "@trigger.dev/slack";
import { z } from "zod";

new Trigger({
  id: "new-user",
  name: "New user",
  on: customEvent({
    name: "user.created",
    schema: z.object({ id: z.string() }),
  }),
  async run(event, ctx) {
    const user = await prisma.user.find({
      where: { id: event.id },
    });

    await slack.postMessage("๐จ", {
      channelName: "new-users",
      text: `New user signed up: ${user.email}`,
    });
  },
}).listen();
```

Be sure to check out more over on our [docs](https://docs.trigger.dev)
