# Cheikh-Project
const express = require("express"); const bodyParser = require("body-parser"); const fetch = require("node-fetch"); require("dotenv").config(); const app = express(); const port = 3000; // Middleware app.use(bodyParser.json()); app.use(express.static("public")); // Serve frontend files // OpenAI and Runway API keys const OPENAI_API_KEY = process.env.OPENAI_API_KEY; const RUNWAY_API_KEY = process.env.RUNWAY_API_KEY; // Generate script endpoint app.post("/generate-script", async (req, res) => { const { genre, plot, characters } = req.body; const prompt = `Write a TV show script. Genre: ${genre}. Plot: ${plot}. Characters: ${characters}.`; try { const response = await fetch("https://api.openai.com/v1/completions", { method: "POST", headers: { "Content-Type": "application/json", Authorization: `Bearer ${OPENAI_API_KEY}`, }, body: JSON.stringify({ model: "text-davinci-003", prompt: prompt, max_tokens: 1000, temperature: 0.7, }), }); const data = await response.json(); const script = data.choices[0].text; res.json({ script }); } catch (error) { res.status(500).json({ error: error.message }); } }); // Generate video endpoint app.post("/generate-video", async (req, res) => { const { script } = req.body; try { const response = await fetch("https://api.runwayml.com/v1/videos", { method: "POST", headers: { "Content-Type": "application/json", Authorization: `Bearer ${RUNWAY_API_KEY}`, }, body: JSON.stringify({ prompt: script, // Use the generated script as the video prompt max_length: 10, // Limit video length for simplicity resolution: "720p", }), }); const data = await response.json(); res.json({ videoUrl: data.output_url }); } catch (error) { res.status(500).json({ error: error.message }); } }); // Start the server app.listen(port, () => { console.log(`App running at http://localhost:${port}`); });
