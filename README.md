# 🏋️ Workout Prediction API
https://workoutpredictionbackend.onrender.com/
A FastAPI‑based backend for fitness tracking, food logging, lifestyle monitoring, and workout/diet prediction.

---

## 📂 User Profile Schema

```json
{
  "name": "string",
  "age": 0,
  "gender": "string",
  "height": 0,
  "weight": 0,
  "fitness_level": "string"
}
```

- **Purpose:** Store basic user information for personalization and prediction.
- **Notes:** `fitness_level` can be Beginner, Intermediate, or Expert.

---

## 🍎 Food Logging

### Food Log Schema
```json
{
  "food_name": "string",
  "calories": 0,
  "protein": 0
}
```

- **Flow:**
  1. User searches for food.
  2. If food exists in **Postgres DB**, use local data.
  3. If not, query **Food Data API** (`developer.c0r.ai`) with free API key (Russia + GCC coverage).
  4. Save the searched food into our DB for future use.
  5. User logs consumption.

---

## 📈 Progress Tracking

### Progress Log Schema
```json
{
  "weight": 0,
  "calories_consumed": 0,
  "workout_completed": true,
  "energy_level": 0
}
```

- **Purpose:** Track daily progress for weight, calories, workouts, and energy.
- **Usage:** Supports monitoring trends and feeding prediction models.

---

## 🤖 Prediction Model Options

```json
{
  "gender": ["Female", "Male"],
  "equipment": ["Barbell", "Dumbbell", "Body Only", "Cable", "Machine", "Kettlebells", "Bands", "Exercise Ball", "Foam Roll", "E-Z Curl Bar", "Medicine Ball", "Other"],
  "difficulty_level": ["Beginner", "Expert", "Intermediate"],
  "target_muscle_group": ["Abdominals","Abductors","Adductors","Biceps","Calves","Chest","Forearms","Glutes","Hamstrings","Lats","Lower Back","Middle Back","Neck","Quadriceps","Shoulders","Traps","Triceps"],
  "workout_type": ["Cardio", "HIIT", "Strength", "Yoga"],
  "experience_level": { "1": "Beginner", "2": "Intermediate", "3": "Expert" }
}
```

---

## ⚙️ Prediction Functions

### 1. `predict_calories(payload)`
- **Inputs:** weight_kg, avg_bpm, session_duration_hrs, bmi, age  
- **Output:** `{ "calories_burned": float }`

### 2. `predict_workout(payload)`
- **Inputs:** age, gender, bmi, avg_bpm, session_duration_hrs, experience_level, workout_frequency_days_per_week  
- **Output:** `{ "workout_type": string }`

### 3. `predict_diet(payload)`
- **Inputs:** calories, proteins, fats, carbs, fat_percentage  
- **Output:** `{ "diet_type": string }`

### 4. `predict_exercise(payload)`
- **Inputs:** workout_type, target_muscle_group, equipment, difficulty_level  
- **Output:** `{ "exercise_name": string }`

### 5. `predict_meal(payload)`
- **Inputs:** bmi, calories_burned, workout_type, proteins, carbs, fats  
- **Output:** `{ "meal_type": string }`

### 6. `predict_all(user)`
- **Pipeline:**  
  - Predict calories burned  
  - Predict workout type  
  - Predict diet type  
  - Predict exercise name  
  - Predict meal type  
- **Output:**  
```json
{
  "calories_burned": 0,
  "workout_type": "string",
  "diet_type": "string",
  "exercise_name": "string",
  "meal_type": "string"
}
```

---

## 🛠️ Implementation Notes

- Encoders/decoders (`enc`, `dec`) ensure categorical values are mapped correctly.
- `WORKOUT_TYPE_MAP` normalizes workout categories (e.g., HIIT → Plyometrics).
- Models are loaded via `ModelLoader.get_models()` and `ModelLoader.get_encoders()`.
- Predictions combine **user profile**, **food logs**, and **progress logs** for holistic recommendations.

---

## 📊 Example Usage

### Request
```json
{
  "age": 25,
  "gender": "Male",
  "weight_kg": 70,
  "height": 175,
  "bmi": 22.9,
  "avg_bpm": 120,
  "session_duration_hrs": 1,
  "experience_level": 2,
  "workout_frequency_days_per_week": 4,
  "proteins": 80,
  "fats": 60,
  "carbs": 200,
  "fat_percentage": 18
}
```

### Response
```json
{
  "calories_burned": 450.5,
  "workout_type": "Strength",
  "diet_type": "High Protein",
  "exercise_name": "Bench Press",
  "meal_type": "Balanced Meal"
}
```

---

## 📌 Summary
This API:
- Manages **user profiles**  
- Logs **food intake** (local DB + external Food API fallback)  
- Tracks **progress**  
- Provides **predictions** for calories, workouts, diet, exercises, and meals  
