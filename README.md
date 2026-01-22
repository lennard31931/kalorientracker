{"name":"kalorientracker","version":"0.1.0","private":true,"scripts":{"dev":"vite","build":"vite build","preview":"vite preview","lint":"eslint . --ext .js,.jsx","format":"prettier --write ."},"dependencies":{"react":"^18.2.0","react-dom":"^18.2.0","lucide-react":"^0.268.0","recharts":"^2.5.0"},"devDependencies":{"vite":"^5.0.0","tailwindcss":"^4.0.0","postcss":"^8.0.0","autoprefixer":"^10.0.0","eslint":"^8.0.0","prettier":"^2.0.0"}}
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173
  }
});
<!doctype html>
<html lang="de">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Kalorientracker Pro</title>
    <link rel="icon" href="/favicon.png" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';
import './index.css';

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
[CONTENT OMITTED FOR BREVITY]
@tailwind base;
@tailwind components;
@tailwind utilities;

/* small helper styles */
html, body, #root {
  height: 100%;
}
module.exports = {
  content: ['./index.html', './src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {}
  },
  plugins: []
};
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
};
// Vercel-style serverless function (place in /api/anthropic.js)
export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  const key = process.env.ANTHROPIC_API_KEY;
  if (!key) {
    return res.status(500).json({ error: 'Missing ANTHROPIC_API_KEY environment variable' });
  }

  try {
    const { userMessage, context } = req.body || {};

    const systemPrompt = `Du bist ein freundlicher Ernährungsberater-Chatbot für eine Kalorientracker-App.`;
    const userPrompt = `
Aktuelle Nutzerdaten:
- Heutige Kalorien: ${context?.totalCalories ?? '0'} von ${context?.goal ?? '0'} kcal
- Verbleibend: ${context?.remaining ?? '0'} kcal
- BMI: ${context?.bmi ?? 'N/A'}
- Gewicht: ${context?.userWeight ?? 'N/A'} kg, Ziel: ${context?.targetWeight ?? 'N/A'} kg
- Größe: ${context?.height ?? 'N/A'} cm
- Einträge heute: ${context?.todayEntries ?? 0}
- Wochendurchschnitt: ${Math.round(context?.weekAverage ?? 0)} kcal/Tag
- Protein: ${context?.totalProtein ?? 0}g, Kohlenhydrate: ${context?.totalCarbs ?? 0}g, Fett: ${context?.totalFat ?? 0}g

Beantworte die folgende Frage kurz, freundlich und hilfreich auf Deutsch. Gib praktische Tipps zur Ernährung und Motivation:

"${userMessage}"
    `;

    const payload = {
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1000,
      messages: [
        { role: 'system', content: systemPrompt },
        { role: 'user', content: userPrompt }
      ]
    };

    const r = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${key}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(payload)
    });

    const data = await r.json();

    const text =
      data?.content?.[0]?.text ||
      data?.completion ||
      data?.output?.[0]?.content?.[0]?.text ||
      JSON.stringify(data);

    return res.status(200).json({ text });
  } catch (err) {
    console.error('Anthropic proxy error', err);
    return res.status(500).json({ error: 'AI request failed' });
  }
}  
[OPTIONAL NETLIFY FUNCTION OMITTED IN TOOL CALL]
name: CI

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
      - name: Install
        run: npm ci
      - name: Build
        run: npm run build
node_modules
/dist
/.env
/.env.local
/.env.*.local
/.vercel
/.netlify
/build
.DS_Store
[CONTENT OMITTED FOR BREVITY IN TOOL CALL]
