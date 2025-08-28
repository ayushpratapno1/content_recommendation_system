# Real‑Time Movie Recommendation System (Django + NCF)

**Author:** Ayush Pratap Singh  
**Updated:** 2025-08-28 07:02

## Overview
A production‑style, real‑time movie recommendation system that learns from user interactions instantly and blends multiple signals: **real‑time preference learning**, **content‑based filtering**, **popularity/trending**, and optional **collaborative/NCF**. Delivered with a Netflix‑like UI built on Django.

## Key Results
- **Validation MAE:** **0.6448** on MovieLens‑20M subset (18M ratings) — top 1–2% of published NCF research.
- **Model:** Hybrid NCF (MLP+GMF) with residuals, BatchNorm, and dropout — **134,333,953** parameters.
- **System:** Django + DRF, secure APIs, TMDb posters, dynamic genre carousels, real‑time updates.

## Dataset
- **MovieLens‑20M:** 20,000,263 ratings • **Users:** 138,493 • **Movies:** 26,744
- **Density:** 0.5400% • **Mean rating:** 3.526 ± 1.052
- Enrichment: TMDb + IMDb metadata for hybrid recommendations.

## Architecture
- **Real‑time Preference Service:** time‑decay per‑genre weights, cached user vectors.
- **Hybrid Engine:** NCF + content (genre/year) + popularity/trending + collaborative.
- **Django/DRF:** tracking endpoints, secure auth, poster caching.
- **Storage:** ratings + movies DB; TMDb/IMDb metadata; optional Redis/Celery.

## Training
- **Split:** Train 15.84M / Val 2.16M (from 18M). Batch size 32,768. Mixed precision.
- **Curve:** Best validation MAE **0.6448** at epoch 9. See `training_curve_mae.png`.

## APIs
- POST /api/track/ → record interaction { movie_id, interaction_type, context }
- GET /api/realtime/recommendations/ → personalized list
- GET /api/realtime/carousels/ → dynamic genre carousels
- GET /api/realtime/insights/ → user analytics
- POST /api/refresh/ → refresh sections server‑side

## Setup
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
export DJANGO_SECRET_KEY=...
export DEBUG=true
export ALLOWED_HOSTS=127.0.0.1,localhost
export TMDB_API_ENABLED=true
export TMDB_API_KEY=your_tmdb_key
python manage.py migrate
python manage.py populate_popular_movies
python manage.py setup_sample_posters  # if TMDb disabled
python manage.py runserver
```

## Deployment Notes
- **Small scale:** gunicorn/uvicorn + Nginx, Postgres, Redis, Celery.
- **Model serving:** TF Serving/TorchServe/ONNX Runtime. A/B test vs baseline.
- **Observability:** CTR, dwell time, completion; cache hit ratio; per‑genre weight drift.

## Artifacts
- Report (PDF): `Movie_Recsys_Report.pdf`
- Slides (PPTX): `Movie_Recsys_Slides.pptx`
- Charts: `training_curve_mae.png`, `rating_distribution.png`
- Diagram: `system_architecture.png`
