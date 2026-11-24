# AY.ai

AY.ai is a media-forensics system that analyzes text, images, audio, and video for signs of AI generation or manipulation. It brings scan results, supporting evidence, and review tools together in one web application.

## Access AY.ai

- **Web app:** [https://ay-ai-pkc.vercel.app](https://ay-ai-pkc.vercel.app)
- **Repository:** [https://github.com/psycheo/ay-ai](https://github.com/psycheo/ay-ai)

Open the web app and sign in with an account provisioned for the system. If you do not have access, contact the AY.ai administrator to request an invitation. Staff and administrator features require the appropriate account role.

## What the system does

Depending on the selected detector and configured models, AY.ai can:

- Analyze writing for signals associated with AI-generated or AI-refined text.
- Inspect images for synthetic-media signals and provide visual evidence, such as heatmaps, when supported by the selected detector.
- Analyze audio and video using available speech, sound, frame, and temporal analysis pipelines.
- Return a verdict, confidence and risk information, explanations, and supporting analysis details. Uncertain or conflicting evidence can be marked **Need Review**.
- Save scan history and private media to Supabase for signed-in users, subject to the deployment’s configuration and retention policies.
- Provide staff tools for reviewing saved scans and organizing submitted content into a searchable library. Administrative pages are restricted by account role.

AY.ai is a decision-support and review tool. Detector results are estimates, not proof of authorship or authenticity. Performance varies by model, media quality, language, and content type. Optional research detectors are experimental and may require separate model weights and configuration; they may not be available in the hosted app.

## Run locally

### Requirements

- Git
- Node.js 22 or newer and npm
- Python 3.11
- A Supabase project for sign-in, scan history, and private media storage

Clone the repository and install the frontend dependencies:

```powershell
git clone https://github.com/psycheo/ay-ai.git
cd ay-ai
npm ci
Copy-Item .env.example .env
```

Edit `.env` and set the Supabase project URL and public anon key. The default local API address is `http://localhost:8000`:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your_public_anon_key
VITE_API_URL=http://localhost:8000
```

Never put a Supabase service-role key or other server secret in frontend `VITE_*` variables or commit secrets to Git.

Install the Python backend and download the default image detector weights:

```powershell
cd backend
py -3.11 -m venv .venv
.venv\Scripts\python.exe -m pip install --upgrade pip
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe training\download_community_forensics_model.py
cd ..
```

The default text model is downloaded from Hugging Face when the backend starts. The Community Forensics image weights are downloaded separately and stored under `backend/models/community_forensics`. Additional SAFE, xRayon, and audio/video research models are optional and need their own checkpoints and settings.

### Configure Supabase

Local startup does not create the database schema or storage policies. Apply the SQL migrations in `supabase/migrations` to your Supabase project in order, then configure authentication and storage policies. Keep credentials and downloaded model files out of version control.

### Start the app

From the repository root:

```powershell
npm.cmd run dev
```

The frontend is served at [http://localhost:5173](http://localhost:5173) and the API at [http://localhost:8000](http://localhost:8000). Keep the terminal open while using the app; press **Ctrl+C** to stop both services. The API health endpoint is [http://localhost:8000/health](http://localhost:8000/health). Allow time for model initialization before running scans.

You can also run the services separately. In one terminal, start the frontend from the repository root with `npm run dev:frontend`. In another terminal, start the backend:

```powershell
cd backend
.venv\Scripts\python.exe main.py
```

## Deployment overview

The frontend is a Vite application and the detection API is a Python FastAPI service. The hosted setup uses Vercel for the frontend, Railway for the backend, and Supabase for authentication, database records, and private media storage. Deployments need the appropriate environment variables, an exact frontend origin in the backend’s `CORS_ORIGINS`, and the Supabase migrations and policies. See [backend/README.md](backend/README.md) and [docs/railway-media-deployment.md](docs/railway-media-deployment.md) for backend and deployment details.

## Project structure

| Path | Purpose |
| --- | --- |
| `src/` | React frontend and application pages |
| `backend/` | FastAPI service, detection pipelines, and model integrations |
| `supabase/` | SQL migrations, functions, and Supabase configuration |
| `docs/` | Implementation notes, model assessments, and deployment documentation |
| `tests/` | Frontend and integration tests |

## More information

- [Backend API, models, and deployment notes](backend/README.md)
- [Railway media deployment guide](docs/railway-media-deployment.md)
- [Upload size limits](docs/upload-size-limits.md)
- [Attributions and external assets](ATTRIBUTIONS.md)
