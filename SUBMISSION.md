# Sanoj's Study Room

## What it is

A personal learning companion first made for Sanoj Ahamed, who is learning web development. Other learners can personalize the name in their browser. Human-written lesson notes give the tutor a grounded starting point, and Gemma personalizes the learning experience instead of simply handing over answers.

## Learning experience

The guide currently includes beginner lessons for JavaScript, HTML, and CSS. Each topic has human-written notes, an analogy, an example, and a five-question quiz. Gemma powers three interactive modes:

- **Learn** — explains the selected topic in simple language with an example.
- **Check understanding** — presents five questions one at a time and asks Gemma to give personalized feedback after every answer.
- **Practice** — creates a small exercise with a goal, starter example, and hint.

Completed topics are saved locally in the learner's browser. Study Buddy is built for one real learner, Sanoj Ahamed, and uses encouraging explanations and small steps to make technical topics less intimidating.

## Built with

Vue 3, Vite, Node.js, Express, and Google's open-weight Gemma 3 4B IT through Hugging Face Inference Providers. The Hugging Face token stays on the server and is never exposed to the browser.

The tutor API applies a per-IP request limit to help protect the inference account from accidental overuse.

## Why open innovation matters

Using an open-weight model makes the tutor's model choice visible and replaceable. The model ID is configurable, so the project can compare compatible open models or move inference to a different provider without changing the learning interface. Keeping the model behind a small server API also means the learner's browser never needs access to a provider secret.

## Demo and source

- **Live demo:** [ADD YOUR DEPLOYED URL]
- **GitHub:** [ADD YOUR PUBLIC REPOSITORY URL]

## Run locally

Create `.env` from `.env.example` and add a Hugging Face token with Inference Providers access. Then run:

```bash
npm install
npm start
```

In another terminal:

```bash
npm run dev
```

Publish this write-up on DEV with `#devchallenge`, `#weekendchallenge`, and `#hf26challenge`.
