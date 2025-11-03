# Heuristic Hangman Solver

A high-performance Hangman solver built for a machine learning hackathon. This agent uses a powerful heuristic and statistical approach based on **n-grams** (trigrams, bigrams, unigrams) and **dynamic word list filtering** to make optimal guesses and achieve a high score.

## Problem Statement

The challenge is to create an autonomous agent that can win at Hangman with the highest possible efficiency. The goal is to maximize the win rate while minimizing the number of wrong guesses, as defined by the hackathon's scoring formula.

## 🧠 Core Logic: The N-gram Waterfall

This agent uses a purely **statistical and heuristic approach**. It does not use Reinforcement Learning; instead, it relies on a powerful, pre-computed statistical model of the `corpus.txt` file.

The "brain" of the agent is a 5-step **decision-making hierarchy** (or "waterfall") that it executes every single turn to find the most optimal letter to guess.

### The Decision-Making Hierarchy

The `choose_action` function attempts to find a guess in this specific order of priority:

1.  **Step 1: Check Trigrams (Highest Priority)**
    The agent first scans the masked word for known two-letter sequences next to a blank (e.g., `TH_`, `_HE`, or `T_E`). It then queries its pre-computed trigram statistics to find the most frequent letter that completes this pattern (e.g., guessing 'E' for `TH_`).

2.  **Step 2: Check Bigrams (Medium-High Priority)**
    If no trigram patterns are found, the agent looks for single known letters next to a blank (e.g., `T_` or `_E`). It uses its bigram statistics to guess the most frequent letter that follows or precedes that letter (e.g., guessing 'O' for `T_` or 'R' for `_E`).

3.  **Step 3: Dynamic Corpus Filtering (The Main Heuristic)**
    If no simple n-gram patterns match, the agent executes its main heuristic:
    * It filters the *entire 50,000-word corpus* down to a new, temporary list of `candidate_words`.
    * This list *only* contains words that perfectly match the current game state (correct length, all known letters in the right spots, and no letters from the `wrong_guesses` list).

4.  **Step 4: Candidate Frequency (Medium-Low Priority)**
    After building the `candidate_words` list, the agent calculates the unigram (single letter) frequency *from within this new, filtered list*. It then guesses the most common letter from this list, as this is the most likely letter to appear in the remaining valid words.

5.  **Step 5: Global Fallback (Lowest Priority)**
    In the rare case that the `candidate_words` list is empty (no word in the corpus matches the state), the agent falls back to its pre-computed **global unigram frequency**. It guesses the most common letter in the entire English language (e.g., 'e', 'a', 'o') that it hasn't already tried.



---

## 🚀 How to Run

This project is set up as a Google Colab notebook.

1.  Open the `.ipynb` file in Google Colab.
2.  Run the first cell (the one with `files.upload()`).
3.  Upload your `corpus.txt` file when prompted.
4.  Upload your `test.txt` file when prompted.
5.  Run the subsequent cells to initialize the `HangmanAgent`, run the evaluation, and see the final score.

## 🏆 Evaluation

The agent is evaluated against the `test.txt` file (using 2000 games as per the final code). The score is calculated using the official hackathon formula:

`Final Score = (Success Rate * 2000) - (Total Wrong Guesses * 5) - (Total Repeated Guesses * 2)`

### Example Results

```
==============================
     EVALUATION RESULTS
==============================
Games Won:                604  / 2000
Success Rate:             30.20%
Total Wrong Guesses:      10490
Total Repeated Guesses:   0
----------------------------------
FINAL SCORE:              -51846
==============================
```
