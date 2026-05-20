# Android Text To Speech (TTS) App in Java

A beginner-friendly Android Text To Speech application using Java in Android Studio.

This project includes:

- ✅ Play Text
- ✅ Pause Text
- ✅ Resume Text
- ✅ Stop Text
- ✅ Pitch Control
- ✅ Speed Control
- ✅ SeekBar Support
- ✅ Beginner Friendly Code

---

# 📱 App Features

| Feature | Description |
|---|---|
| Play | Start speaking text |
| Pause | Pause current speech |
| Resume | Continue from paused position |
| Stop | Stop speech completely |
| Pitch Control | Change voice tone |
| Speed Control | Change speaking speed |

---

# 🛠 Requirements

- Android Studio (Latest Version Recommended)
- Java
- Minimum SDK: 21+

---

# 📂 Project Structure

```bash
app/
 ├── java/com/example/ttsapp/
 │    └── MainActivity.java
 │
 └── res/layout/
      └── activity_main.xml
```

---

# 🚀 Step 1: Create New Project

1. Open Android Studio
2. Click **New Project**
3. Select **Empty Views Activity**
4. Choose:
   - Language: Java
   - Minimum SDK: 21+
5. Click Finish

---

# 🚀 Step 2: Add XML Layout

Open:

```bash
res/layout/activity_main.xml
```

Replace everything with:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/main"
    android:orientation="vertical"
    android:padding="20dp">

    <Button
        android:id="@+id/btnPlay"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Play"/>

    <Button
        android:id="@+id/btnPause"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Pause"
        android:layout_marginTop="10dp"/>

    <Button
        android:id="@+id/btnResume"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Resume"
        android:layout_marginTop="10dp"/>

    <Button
        android:id="@+id/btnStop"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Stop"
        android:layout_marginTop="10dp"/>

    <TextView
        android:id="@+id/tvPitch"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Pitch : 1.0"
        android:textSize="18sp"
        android:layout_marginTop="20dp"/>

    <SeekBar
        android:id="@+id/seekPitch"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

    <TextView
        android:id="@+id/tvSpeed"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Speed : 1.0"
        android:textSize="18sp"
        android:layout_marginTop="20dp"/>

    <SeekBar
        android:id="@+id/seekSpeed"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

</LinearLayout>
```

---

# 🚀 Step 3: Add Java Code

Open:

```bash
MainActivity.java
```

Replace everything with:

```java
package com.example.ttsapp;

import android.os.Bundle;
import android.os.Handler;
import android.speech.tts.TextToSpeech;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.TextView;

import androidx.activity.EdgeToEdge;

import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import androidx.appcompat.app.AppCompatActivity;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private TextToSpeech textToSpeech;

    private Button btnPlay, btnPause, btnResume, btnStop;

    private SeekBar seekPitch, seekSpeed;

    private TextView tvPitch, tvSpeed;

    private String fullText =
            "Hello! This is Android Text To Speech example.";

    private int currentIndex = 0;

    private boolean isPaused = false;
    private boolean isSpeaking = false;

    private float pitch = 1.0f;
    private float speed = 1.0f;

    private Handler handler = new Handler();

    private Runnable progressRunnable = new Runnable() {
        @Override
        public void run() {

            if (isSpeaking && !isPaused) {

                currentIndex += 5;

                if (currentIndex > fullText.length()) {
                    currentIndex = fullText.length();
                }

                handler.postDelayed(this, 300);
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        btnPlay = findViewById(R.id.btnPlay);
        btnPause = findViewById(R.id.btnPause);
        btnResume = findViewById(R.id.btnResume);
        btnStop = findViewById(R.id.btnStop);

        seekPitch = findViewById(R.id.seekPitch);
        seekSpeed = findViewById(R.id.seekSpeed);

        tvPitch = findViewById(R.id.tvPitch);
        tvSpeed = findViewById(R.id.tvSpeed);

        textToSpeech = new TextToSpeech(this, status -> {

            if (status == TextToSpeech.SUCCESS) {

                textToSpeech.setLanguage(Locale.US);

                textToSpeech.setPitch(pitch);

                textToSpeech.setSpeechRate(speed);
            }
        });

        // Pitch SeekBar
        seekPitch.setMax(200);
        seekPitch.setProgress(100);

        seekPitch.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {

                pitch = progress / 100.0f;

                if (pitch < 0.1f) {
                    pitch = 0.1f;
                }

                tvPitch.setText("Pitch : " + pitch);

                textToSpeech.setPitch(pitch);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {}

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {}
        });

        // Speed SeekBar
        seekSpeed.setMax(200);
        seekSpeed.setProgress(100);

        seekSpeed.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {

                speed = progress / 100.0f;

                if (speed < 0.1f) {
                    speed = 0.1f;
                }

                tvSpeed.setText("Speed : " + speed);

                textToSpeech.setSpeechRate(speed);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {}

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {}
        });

        // Play
        btnPlay.setOnClickListener(v -> {

            currentIndex = 0;

            startSpeech(fullText);

        });

        // Pause
        btnPause.setOnClickListener(v -> {

            if (textToSpeech != null && textToSpeech.isSpeaking()) {

                textToSpeech.stop();

                isPaused = true;
                isSpeaking = false;
            }

        });

        // Resume
        btnResume.setOnClickListener(v -> {

            if (isPaused) {

                String remainingText =
                        fullText.substring(currentIndex);

                startSpeech(remainingText);

            }

        });

        // Stop
        btnStop.setOnClickListener(v -> {

            if (textToSpeech != null) {

                textToSpeech.stop();

                currentIndex = 0;

                isPaused = false;
                isSpeaking = false;
            }

        });

    }

    private void startSpeech(String text) {

        textToSpeech.setPitch(pitch);

        textToSpeech.setSpeechRate(speed);

        textToSpeech.speak(
                text,
                TextToSpeech.QUEUE_FLUSH,
                null,
                "TTS_ID"
        );

        isPaused = false;
        isSpeaking = true;

        handler.post(progressRunnable);
    }

    @Override
    protected void onDestroy() {

        if (textToSpeech != null) {

            textToSpeech.stop();

            textToSpeech.shutdown();
        }

        super.onDestroy();
    }
}
```

---

# 🚀 Step 4: Run Project

Click:

```bash
Run ▶
```

Now test:

- Play Button
- Pause Button
- Resume Button
- Stop Button
- Pitch SeekBar
- Speed SeekBar

---

# 🎯 Understanding Pitch & Speed

| Value | Effect |
|---|---|
| 0.5 | Deep / Slow Voice |
| 1.0 | Normal Voice |
| 2.0 | High / Fast Voice |

---

# ⚠ Important Note

Android TextToSpeech does NOT support real pause/resume.

This project creates a custom pause system using:

- Saved text index
- Stop speech
- Restart from saved position

---

# 🧠 Beginner Tips

- Start with small projects
- Learn Button Click Events
- Practice SeekBar
- Understand TextToSpeech lifecycle
- Always shutdown TTS in `onDestroy()`

---

# ❤️ Made For Beginners

This project is beginner-friendly and easy to understand for Android Java learners.

Happy Coding 🚀
