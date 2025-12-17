# 🎧 Content‑Based Spotify Recommender system on 114k Spotify Tracks

A data science project that builds a **content-based music recommender system** by clustering Spotify tracks using their audio features and Principal Component Analysis (PCA). The system recommends similar-sounding songs without relying on any user interaction history.

## 🚀 Project Overview

Modern music platforms host millions of tracks, posing a significant challenge for users seeking new music that truly aligns with their tastes. This project addresses the "cold start" problem (new users/new items) and personal discovery by focusing purely on the acoustic profile of the music itself.

The goal is to group songs into "sound neighborhoods" and recommend tracks based on **acoustic similarity**.

### Key Ideas and Techniques

* **Feature Engineering:** Represent each song as a vector using Spotify's comprehensive audio features (danceability, energy, acousticness, tempo, etc.).
* **Dimensionality Reduction:** Apply **Principal Component Analysis (PCA)** to reduce the feature space and denoise correlated features (e.g., separating loudness and energy).
* **Clustering:** Use **K-Means clustering** on the PCA components to automatically discover 5 distinct "sound profiles" or clusters.
* **Recommendation:** Build a **Content-Based Recommender** using **cosine similarity** in the reduced PCA space to find "nearest-neighbor" tracks within the same sound cluster.

---

## 💾 Dataset

| Metric | Details |
| :--- | :--- |
| **Source** | Public Spotify tracks dataset (Kaggle-style, derived from Spotify API) |
| **Size** | ~114,000 unique tracks |
| **Core Features** | `danceability`, `energy`, `acousticness`, `instrumentalness`, `liveness`, `valence`, `speechiness`, `tempo`, `loudness`, `duration_ms` |
| **Labels** | `popularity`, `genre` (Used for analysis/interpretation, not for clustering/model training) |

The dataset allows for analysis of how a song's acoustic profile (how it sounds) relates to its popularity, genre, and its assigned sound cluster.

---

## 🛠️ Methodology & Pipeline

The project follows a standard data science pipeline for unsupervised learning:

### 1. Exploratory Data Analysis (EDA)

* **Goal:** Understand the distribution of key features and justify the need for dimensionality reduction.
* **Key Findings:** Strong positive correlation observed between `energy` and `loudness`, and a strong negative correlation between `acousticness` and `energy`, which necessitates a feature transformation step like PCA.
  

### 2. Data Preprocessing

* **Outlier Handling:** Removed tracks with extreme `duration_ms` (e.g., long spoken word or classical pieces) to ensure the features reflect typical songs.
* **Standardization:** All selected numeric audio features were scaled using `StandardScaler` (mean 0, std 1). This is a critical step before applying distance-based algorithms like PCA and K-Means.

### 3. Principal Component Analysis (PCA)

* **Goal:** Reduce the dimensionality of the feature space while retaining maximum variance.
* **PCA Fitting:** PCA was fitted on the standardized audio features.
* **Variance Explained:** We selected `n_components` to explain **≥ 90%** of the total variance. The analysis showed that **10 principal components (PCs)** were sufficient to capture this threshold, reducing the feature space from the original dimension down to 10.
  

### 4. K-Means Clustering

* **Goal:** Group the songs into acoustically similar clusters.
* **Clustering Space:** K-Means was applied directly to the 10-dimensional PCA representation of the songs.
* **Optimal *k* Selection:** Evaluated the performance across various $k$ values using the **Silhouette Score** (higher is better) and the **Davies–Bouldin Index (DBI)** (lower is better).
* **Result:** A value of **$k=5$** was chosen as the optimal balance point, yielding 5 distinct, interpretable sound profiles (e.g., High-Energy Dance vs. Low-Energy Acoustic).
  

### 5. Content-Based Recommendation Engine

The core recommender operates on the 10-dimensional PCA space:

1.  **Input:** A seed track ID.
2.  **Neighborhood Search:** The seed track's PCA vector is used to find its corresponding K-Means cluster (its "sound neighborhood").
3.  **Similarity Calculation:** **Cosine similarity** is computed between the seed track's PCA vector and the PCA vectors of **all other songs within the same cluster**.
4.  **Output:** The Top-N most similar songs are returned.

This process ensures that recommendations are based purely on **how the songs sound** (their content), not on what other users have liked.

---

## 🎯 Key Results and Insights

### Feature Correlations

* The project confirmed classic acoustic relationships: **Energy** and **Loudness** are heavily intertwined, while **Acousticness** is inversely related to both.
* Crucially, individual audio features show only a **weak correlation with song popularity**, confirming that "hits" are not defined by a single acoustic factor, and validating the use of a multivariate approach (PCA/Clustering).

### Interpretable Sound Clusters

The 5 K-Means clusters represent distinct, easily interpretable sound profiles:

1.  **Cluster 1 (Club/Party):** Very high in Danceability and Energy; low in Acousticness.
2.  **Cluster 2 (Calm Acoustic):** High Acousticness, low Energy, medium-low Tempo/Loudness.
3.  **Cluster 3 (Sad/Quiet):** Very low Energy, low Valence (mood), and low Loudness.
4.  **Cluster 4 (Mid-Tempo Pop):** Moderately high in all features, representing the average pop soundscape.
5.  **Cluster 5 (Instrumental/Niche):** High Instrumentalness, low Speechiness, often lower Popularity.

### Recommendation Potential

The system successfully surfaces acoustically similar tracks across different artists and genres. It excels at:

* **Discovery:** Highlighting high-quality, but low-popularity, songs that share a sound profile with a known "hit."
* **Cold Start:** Providing meaningful recommendations instantly for any song, even brand new ones, without needing any historical user data.

---
