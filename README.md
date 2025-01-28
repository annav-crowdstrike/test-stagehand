# test-stagehand

# 🤘 Welcome to Stagehand!

Hey! This is a project built with [Stagehand](https://github.com/browserbase/stagehand).

You can build your own web agent using: `npx create-browser-app`!

## Setting the Stage

Stagehand is an SDK for automating browsers. It's built on top of [Playwright](https://playwright.dev/) and provides a higher-level API for better debugging and AI fail-safes.

## Curtain Call

Get ready for a show-stopping development experience. Just run:

```bash
npm install && npm start
```

## What's Next?

### Add your API keys

Required API keys/environment variables are in the `.env.example` file. Copy it to `.env` and add your API keys.

```bash
cp .env.example .env && nano .env # Add your API keys to .env
```

### Custom .cursorrules

We have custom .cursorrules for this project. It'll help quite a bit with writing Stagehand easily.

### Run on Browserbase

To run on Browserbase, add your API keys to .env and change `env: "LOCAL"` to `env: "BROWSERBASE"` in [stagehand.config.ts](stagehand.config.ts).

### Use Anthropic Claude 3.5 Sonnet

1. Add your API key to .env
2. Change `modelName: "gpt-4o"` to `modelName: "claude-3-5-sonnet-latest"` in [stagehand.config.ts](stagehand.config.ts)
3. Change `modelClientOptions: { apiKey: process.env.OPENAI_API_KEY }` to `modelClientOptions: { apiKey: process.env.ANTHROPIC_API_KEY }` in [stagehand.config.ts](stagehand.config.ts)

# Reproducing the issue

1. Clone the Repository:

```bash
git clone git@github.com:annav-crowdstrike/test-stagehand.git
cd test-stagehand
```

2. Create an AI API key:

   - Go to genaihub profile and create/copy an API key

3. Set up Environment variables:
   - Copy `.env.example` to the `.env` file

```bash
git .env.example .env // copies .env.example to the .env file
```

- Insert your API key into the `.env` file (OPENAI_API_KEY)
- Insert OPENAI_BASE_URL

4. Install dependencies

```bash
npm install
```

5. Start the application

```bash
npm run start
```

6. Check the issue from the terminal

```bash
BadRequestError: 400 status code (no body)
```

# Debugging the issue

1. Enabling debugging

   - Run the application with tne DEBUG environment set to \* to log all debug information for every action in the application

```bash
DEBUG=* npm run start
```

- You can also debug a specific action, in this case you need to specify the action name instead of \*, ex. `DEBUG=request:*`

2. Add `console.log` statements
   - Another debugging option is to add `console.log` directly into the `node_modules/` code
   - We are mostly interested in code from `node_modules/openai/core.js`, you can start by adding `console.log` to the `makeRequest` method.
   - For example, you can insert a couple of lines after the line 315 to see an issue we get from a genaihub api call

```js
//...
// inside makeRequest method
const controller = new AbortController();
const response = await this.fetchWithTimeout(
  url,
  req,
  timeout,
  controller
).catch(exports.castToError);
const errText = await response
  .text()
  .catch((e) => (0, exports.castToError)(e).message);
// insert these lines
const errText = await response
  .text()
  .catch((e) => (0, exports.castToError)(e).message);
console.log("makeRequest", { errText });
//...
```
