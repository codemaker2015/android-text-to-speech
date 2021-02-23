# Android Text To Speech

TextToSpeech as the name suggests is used to synthesize speech from the text string.

You can set the language of your choice. You can set the pitch, speed as well your own speech from a custom file.

TextToSpeech needs to be initialized first. For this, you need to implement the TextToSpeech.OnInitListener interface and override the method: onInit.
Example:

```
TextToSpeech tts = new TextToSpeech(this, this);
```
Once this is done the onInit gets triggered.

In this method, we check whether the feature is available on our device or not.

```
 @Override
    public void onInit(int status) {

        if (status == TextToSpeech.SUCCESS) {

            int result = tts.setLanguage(Locale.US);

            if (result == TextToSpeech.LANG_MISSING_DATA
                    || result == TextToSpeech.LANG_NOT_SUPPORTED) {
                Toast.makeText(getApplicationContext(), "Language not supported", Toast.LENGTH_SHORT).show();
            } else {
                 //Disable the button if any.
            }

        } else {
            Toast.makeText(getApplicationContext(), "Init failed", Toast.LENGTH_SHORT).show();
        }
    }
```
How to speak the text?
```
tts.speak(text, TextToSpeech.QUEUE_FLUSH, null, null);
```
The first parameter is the text that would be spoken.

The second parameter defines that the previous input is flushed so as to begin a clean slate. Alternatively, we can use QUEUE_ADD to add the current text to the speech.

The third parameter is the bundle that is passed.
Fourth is the utterance id string.

OnUtteranceProgressListener is used to listen for the tts events: start, done, error.

In this, we can retrieve the bundle utterance string that was passed.

The OnUtteranceProgressListener must be defined before the speak method is called.
To change the pitch and speed we can do:

```
tts.setPitch(2f);
tts.setSpeechRate(2f);
```

addSpeech method is used to add a custom speech instead of using the Android’s default speech.

Once the speech is over, you need to stop the TTS instance in order to release the resources using the tts.shutdown() method.

Let’s get onto the business end where we implement TextToSpeech in our application.

Text To Speech Android Code
The code for the activity_main.xml layout is given below:

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="https://schemas.android.com/apk/res/android"
    xmlns:app="https://schemas.android.com/apk/res-auto"
    xmlns:tools="https://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/enterYouText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:text="Welcome Codemaker"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />


    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="SPEAK TEXT"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/enterYouText" />

    <Button
        android:id="@+id/btnChangePitch"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="Change Pitch"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/btnChangeSpeed" />

    <Button
        android:id="@+id/btnChangeSpeed"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="8dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="Change Speed"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button" />

</android.support.constraint.ConstraintLayout>
```
The code for MainActivity.java is given below:

```
package com.vsoft.androidtexttospeech;
import android.speech.tts.TextToSpeech;
import android.speech.tts.UtteranceProgressListener;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import java.util.Locale;

public class MainActivity extends AppCompatActivity implements TextToSpeech.OnInitListener {


    Button button, btnChangePitch, btnChangeSpeed;
    EditText editText;
    private TextToSpeech tts;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tts = new TextToSpeech(this, this);

        button = findViewById(R.id.button);
        btnChangePitch = findViewById(R.id.btnChangePitch);
        btnChangeSpeed = findViewById(R.id.btnChangeSpeed);
        editText = findViewById(R.id.enterYouText);

        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                speakOut();
            }
        });

        btnChangePitch.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                tts.setPitch(0.2f);
            }
        });

        btnChangeSpeed.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                tts.setSpeechRate(2f);
            }
        });
    }

    @Override
    public void onInit(int status) {

        if (status == TextToSpeech.SUCCESS) {
            int result = tts.setLanguage(Locale.US);

            if (result == TextToSpeech.LANG_MISSING_DATA
                    || result == TextToSpeech.LANG_NOT_SUPPORTED) {
                Toast.makeText(getApplicationContext(), "Language not supported", Toast.LENGTH_SHORT).show();
            } else {
                button.setEnabled(true);
            }

        } else {
            Toast.makeText(getApplicationContext(), "Init failed", Toast.LENGTH_SHORT).show();
        }
    }

    private void speakOut() {
    
        tts.setOnUtteranceProgressListener(new UtteranceProgressListener() {
            @Override
            public void onStart(String s) {

                final String keyword = s;
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(getApplicationContext(), "Started" + keyword, Toast.LENGTH_SHORT).show();
                    }
                });
            }

            @Override
            public void onDone(String s) {

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(getApplicationContext(), "Done ", Toast.LENGTH_SHORT).show();
                    }
                });
            }

            @Override
            public void onError(String s) {


                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(getApplicationContext(), "Error ", Toast.LENGTH_SHORT).show();
                    }
                });
            }
        });

        Bundle params = new Bundle();
        params.putString(TextToSpeech.Engine.KEY_PARAM_UTTERANCE_ID, "");

        String text = editText.getText().toString();
        tts.speak(text, TextToSpeech.QUEUE_FLUSH, params, "Dummy String");
    }

    @Override
    public void onDestroy() {
        if (tts != null) {
            tts.stop();
            tts.shutdown();
        }
        super.onDestroy();
    }
}

```
The tts listeners run on a separate thread. Hence to perform any UI actions from them, we need to use the runOnUiThread function.

