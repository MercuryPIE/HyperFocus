# HyperFocus

## Overview
HyperFocus is a Java application developed as a group project for CAB302 at QUT.
It blocks websites and applications for a set period, helping users maintain focus and improve productivity.

---

## How It Works
- Users select websites and applications to block.
- Users set a timer for how long the block should last.
- The app prevents access until the timer ends, automatically restoring access afterward. (in theory)

---

## Key Code Snippets
Here are some snippets that highlight my contributions to the project:

### Timer Logic
The timer logic manages both delayed and running timers, handling offsets and durations, including edge cases like timers crossing midnight.
```java
//Timer.java:154
private void CreateTimer(){
    Timer_State = (Timer_Offset != 0.0) ? TimerState.Delayed : TimerState.Running;

    this.Timer_Start = LocalTime.now().plusSeconds((long) Timer_Offset);
    this.Timer_End = Timer_Start.plusSeconds((long) Timer_Duration);

    boolean TimeCheck = Timer_End.isBefore(Timer_Start);
    Running_Counting_Duration = Duration.between(Timer_Start, Timer_End).plusHours(TimeCheck ? 24 : 0);
    Running_Total_Duration = Running_Counting_Duration;

    if(Timer_State == TimerState.Delayed){
        Delayed_Counting_Duration = Duration.between(LocalTime.now(),Timer_Start).plusHours(TimeCheck ? 24 : 0);
        Delayed_Total_Duration = Delayed_Counting_Duration;
    }
}
```

### Timer Button State Manager
The button state manager dynamically updates the states of the timer buttons based on the timer's current status (delayed, running, finished). This Ensures that the UI always reflects the available actions per state.
```java
// TimeController.java:205
public void ButtonStateManager(){
    Button[] AllButtons = new Button[]{PauseButton, ResumeButton, StopButton, RestartButton, ReturnButton};

    ButtonStates[] States;
    switch(timer.getTimerState()) {

        case Running -> States = getRunningState();
        case Delayed, Restarting -> States = getPreRunState();
        case Paused -> States = getPausedState();
        case Stopped, Finished -> States = getEndState();
        default -> States = new ButtonStates[0];
    }
    UpdateButtons(AllButtons, States);

    Platform.runLater(() -> {
        ButtonStates[] LimpStates = new ButtonStates[]{
                ButtonStates.DisplayGraphic,    // Pause
                ButtonStates.DisplayGraphic,    // Resume
                ButtonStates.DisplayGraphic,    // Stop
                ButtonStates.DisplayGraphic,    // Restart
                ButtonStates.DisplayGraphic     // Return
        };
        UpdateButtons(AllButtons, LimpStates);
    });
}
```

### Dynamic Colour & Style Configuration Loader
The Application allows users to pick their own colors for the UI. It dynamically loads a custom CSS file and automatically assigns the optimal text colour based on the luminance of the given colour, ensuring readability regardless of user UI colour preferences.
```java
// ColourControl.java:98
private void LoadColourPaletteFromFile(){
    for (ColourPaletteKeys Key : ColourPaletteKeys.values()){

        String CSSVariable = "-" + Key.name() + "Colour";
        Pattern CSSPattern = Pattern.compile(CSSVariable + ": ([#0-9a-fA-F]+);");
        Matcher Match = CSSPattern.matcher(CSSFileContent);

        if (Match.find()){
            CurrentColourPalette.put(Key,Color.web(Match.group(1)));
        }
    }
}

// ColourControl.java:155
private Color getBestTextColour(Color CurrentColour){

    double RedLight = 0.2126 * CurrentColour.getRed();
    double GreenLight = 0.7152 * CurrentColour.getGreen();
    double BlueLight = 0.0722 * CurrentColour.getBlue();

    double TotalLuminance = RedLight + GreenLight + BlueLight;
    double LightThreshold = 0.75;

    return TotalLuminance > LightThreshold ? Color.BLACK : Color.WHITE;
}
```
---
### My Contribution
- Implemented all timer functionality, including delayed and running timers, duration calculations, and edge-case handling (e.g., timers crossing midnight).
- Designed and implemented dynamic UI customization, allowing users to pick colors and load a custom CSS stylesheet in the JavaFX/FXML interface.
- Ensured that my code was modular, maintainable, and well-documented, making it easy to extend, modify, and understand.
- Managed most of the Git commit history (19,500+ additions, 40+ commits), reflecting my active role in development.
- Assisted with testing and debugging to ensure reliable timer behavior and dynamic style loading.

### Notes:
As of the current build, the website/application blocking feature is not functioning as intended as well as the preset functionality. However, all other implemented features including timer logic, button state management, and dynamic UI/colour customisation are fully working as intended and demonstrate the implemented functionality.
