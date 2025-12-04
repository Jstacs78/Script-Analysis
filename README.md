Script Microphone Assigner – Detailed Documentation (Expanded Version)

Purpose The Script Microphone Assigner is a tool designed for theatre
and live performance planning. It automatically analyzes a script—either
pasted text or an uploaded PDF—to determine which characters have the
most lines, then assigns microphones accordingly. - Character with the
most lines → Mic 1 - Second most lines → Mic 2, etc.

How It Works

1.  Script Input Users can paste text or upload a .txt/.pdf file.

2.  PDF Text Extraction The app uses PyMuPDF to extract text
    page-by-page.

3.  Character & Dialogue Detection A refined regex system detects:

-   Character names
-   Dialogue belonging to each character It handles mixed‑case names,
    names without colons, multi-line dialogue, and unusual formatting.

4.  Exclusion Logic To avoid false positives, the extractor ignores:

-   Scene headings
-   ACT and TITLE lines
-   Stage directions such as (whispering), [thunder], or any line
    starting with “(” or “[”.

5.  Line Counting Counts valid spoken lines per character.

6.  Microphone Assignment Characters ranked by number of lines:

-   Most lines → Mic 1
-   Next → Mic 2

Features - Flexible input - Advanced character extraction - Smart
filtering - Clear line counts - Gradio UI

Current Issues Resolved - Improved regex accuracy - Fixed TypeError
during model post‑processing - Disabled fitz temporarily - Integrated
fine‑tuned BERT model

Model Training Summary - Fine‑tuned bert-base-uncased on simulated
data - Low accuracy was expected due to small dataset - Model integrated
into the app as a secondary fallback

  -------------------------------------------------------------
  ADDED SECTION: Regex vs Hugging Face Model & Training Notes
  -------------------------------------------------------------

As I’ve stated earlier, my main approach for this project is a
regex‑based extraction method for identifying characters and their
lines. While I experimented with machine learning models, the regex
system has proven far more reliable and stable across diverse script
formats.

Why Regex?

Regex requires no formal training data, but it does require extensive
iteration. My work process involved: - Testing the extractor on real
scripts - Criticizing each incorrect output - Refining the regex
patterns again and again - Repeating this until the extraction was
consistently correct

Essentially, each iteration served as a “training cycle,” but instead of
adjusting model weights, I adjusted regular expressions and structural
logic.

Below is an example of the type of regex logic the system uses:

    import re

    character_colon_pattern = re.compile(
        r'^([A-Za-z][A-Za-z0-9 \-\']+?)(?:\s*\(.*?\))?\s*:\s*(.*)$'
    )

    uppercase_line_pattern = re.compile(
        r'^[A-Z][A-Z0-9 \-\']+$'
    )

    stage_direction_pattern = re.compile(
        r'^\s*(\(|\[).*?(\]|\))\s*$'
    )

    def extract_character_lines(script):
        character_lines = {}
        current_character = None
        lines = script.split("\n")

        for line in lines:
            if stage_direction_pattern.match(line.strip()):
                continue

            match = character_colon_pattern.match(line.strip())
            if match:
                character = match.group(1).strip()
                text = match.group(2).strip()
                character_lines.setdefault(character, []).append(text)
                current_character = character
                continue

            if uppercase_line_pattern.match(line.strip()):
                current_character = line.strip()
                character_lines.setdefault(current_character, [])
                continue

            if current_character:
                character_lines[current_character].append(line.strip())

        return character_lines

Hugging Face Model Usage

I also implemented a Hugging Face token classification model as an
experimental backup system.
The model used was:

-   bert-base-uncased

This worked, but the accuracy was limited due to small training data.
Machine learning models require large, high‑quality datasets, which was
not available during development.

Major Issue With the Hugging Face Model

During earlier demos, the primary model I was using from Hugging Face
became private/deleted/unavailable:

-   patrickvonplaten/layout-lmv3-base-finnq

This left me stranded because that was the model I had been training and
using for several demos. After it disappeared, I had no access to it
anymore, which further reinforced my decision to rely on regex as the
main extraction engine.

Conclusion

While the app technically supports a machine‑learning model, the
regex‑based extraction system is the primary and most reliable method
used in the final application.

------------------------------------------------------------------------

Running the Application

Install: pip install gradio PyMuPDF

Run: python mic_assigner_app.py

Usage Instructions 1. Paste script text or upload a file 2. Submit 3.
View character line counts & microphone assignments

Next Steps - Build a larger dataset for ML improvement - Re-enable PDF
extraction - Improve post-processing - Add export options
