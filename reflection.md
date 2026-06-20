# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?\
  The game seemed to have a lot of functionalities described. However, some of them did not work in the expected way. This may
  cause confusion on the user end when they are trying to frame the purpose of the game.
- List at least two concrete bugs you noticed at the start  
  When I enter something that is lower than the secret, the hint says to "GO LOWER".\
  When I enter something that is higher than the secret, the hint says to "GO HIGHER".\
  The number of difficulty level doesn't match the expectations: there are more attempts allowed for normal ones than easy ones.\
  This would make sense if the range was less for the easy level but it is actually 1-100 for all three modes.\
  I don't think it lets me guess after I click "New Game".

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guess of 91 | "Too Low" hint shown | "Too High" hint shown | none |
| Guess of 93 | "Too High" hint shown | "Too Low" hint shown | none |
| New Game Button | Game state resets | Guessing does not work | none |

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
    Claude Code
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
    AI suggestion: "if new_game:\
        st.session_state.attempts = 0\
        st.session_state.secret = random.randint(1, 100)\
        st.session_state.status = "playing"  # <-- ADD THIS\
        st.success("New game started.")\
        st.rerun()\
    This ensures the status is reset back to "playing" when starting a new game."\
    The original code did not let the user play a new game (it basically glitched and behaved unexpectedly).\
    This suggestion was helpful because once the script was rerun to start a new game, the status was in "playing" mode.\
    This communicated the idea that the user had started a new game from scratch (with relevant fields reset).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).
    AI suggestion: "st.session_state.attempts += 1 # CUT THIS LINE HERE\
    ok, guess_int, err = parse_guess(raw_guess)\
    if not ok:\
      st.session_state.history.append(raw_guess)\
      st.error(err)\
    else:\
      st.session_state.attempts += 1 # PASTE LINE HERE\
      st.session_state.history.append(guess_int)\
      secret = st.session_state.secret"
    The AI was getting a little confused when I was trying to solve the problem of the notification history list being updated at\
    the right moment. I was already uncertain of how the proposed solution would work, and was proven correct when I entered a\
    random string as a guess ("Attempts" and "Attempts left: " remained unchanged). What the AI gave was an incorrect suggestion\
    because if the user did not enter a valid guess, their input would not have been recognized as an actual attempt (as\
    represented by the incrementing of the attempts object). I fixed this issue by keeping the "st.session_state.attempts += 1"\
    line at its original spot.
---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
    I checked if the output was behaving in the expected and correct way. I performed this verification by manually testing sample
    inputs on the application and running the pytest application. I also ran all tests in test_game_logic.py at the very end.
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
  This was the output for the following test before I refactored the test_game_logic.py logic:
        "def test_winning_guess():
            # If the secret is 50 and guess is 50, it should be a win
            result = check_guess(50, 50)
    >       assert result == "Win"
    E       AssertionError: assert ('Win', '🎉 Correct!') == 'Win'

    tests/test_game_logic.py:6: AssertionError"
    Since check_guess had two return values, the test failed to return the boolean value of the single outcome return value. It
    was mainly looking for the first (outcome) value that represented the game's result. As a result of this error, this test
    failed.

- Did AI help you design or understand any tests? How?
    AI helped me design a new test that checked whether the score was being updated correctly after the user inputs an incorrect
    guess. In particular, I wanted to make sure that the score wasn't accidentally being incremented on every even attempt (which
    is what the original code did). This helped distinguish between the consequences of a correct and an incorrect guess based on
    the goals of the game.
---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?\
    Whenever you run the app using Streamlit, it will automatically open a new browser tab that contains the web application. It
    does this by connecting to a port. After you save your changes to the file, Streamlit will rerun on its own without you having
    to terminate the running session.
---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.\
    The chat modes are very helpful because it emphasizes the idea that the user has full control of how they want to receive
    information. The idea that edits can't be performed directly on the code unless the user approves in some modes is important in
    order to avoid unexpected behavior due to making too many modifications at once. The AI assistant I used clearly highlighted
    the potential differences in code logic so that the user can easily follow.
- What is one thing you would do differently next time you work with AI on a coding task?
    I would be more specific in my prompts when creating new test cases.
- In one or two sentences, describe how this project changed the way you think about AI generated code.
    I realized that AI can be a powerful tool for debugging. Providing examples of where things go wrong as part of the prompt can
    help not only the AI helper but also the developer to better understand what the code is trying to accomplish.
