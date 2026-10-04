# Sanoj's Study Room

A personal learning companion with human-written lesson notes and Google's open-weight Gemma model, served through Hugging Face Inference Providers, personalizing explanations, quizzes, and practice.

Each learner can optionally add a name to their profile. The chosen name is saved only in that browser and sent with tutor requests so Gemma can address the current learner.

## What is included

The study guide currently covers beginner topics in:

- JavaScript
- HTML
- CSS

Each topic has human-written notes, a practical analogy, code examples, and a five-question multiple-choice quiz. Gemma uses the lesson context to explain concepts, gives personalized feedback on each quiz answer, and creates practice exercises. Progress is saved in the browser's local storage.

The Hugging Face token is used only by the Express server and is never sent to the browser.

## Run locally

Install dependencies:

```bash
npm install
```

Create a `.env` file from `.env.example` and set a Hugging Face token with Inference Providers access:

```env
HF_TOKEN=hf_your_token_here
HF_MODEL=google/gemma-3-4b-it:fastest
PORT=3000
TRUST_PROXY_HOPS=0
```

Keep `.env` private; it is ignored by Git.

Start the API server:

```bash
npm start
```

In a second terminal, start the Vite development server:

```bash
npm run dev
```

Open the Vite URL shown in the terminal. Its `/api` requests are proxied to the Express server on port 3000.

To run the production version, build the frontend and start the Express static-file server:

```bash
npm run build
npm start
```

The production server uses the generated `dist` directory. Build it before starting the server.

## Deploy

The app can be deployed as a single Node web service.

Build command:

```bash
npm install && npm run build
```

Start command:

```bash
npm start
```

Set `HF_TOKEN` and optionally `HF_MODEL` in the deployment environment. Render's `HF_TOKEN` setting is configured as a secret.
The tutor endpoint is limited to 30 requests per IP address per 15 minutes. The Render config sets `TRUST_PROXY_HOPS=1` so rate limiting uses the original client address behind Render's proxy; keep this matched to the proxy count of your hosting platform.

## Add or update lessons

The lesson notes and examples are authored in `src/App.vue` in the `lessons` object. The server sends these notes as context to Gemma for the selected topic and mode.
