# Script-Analysis
input a script output a mic list based on the amout of lines a character has. ex in shrek the musical shrek has the most amout of lines so he will be assiagned to mic 1


Extracted lines with variations:
JOHN: ["This is John's first line."]
MARY: ["Mary's first line."]
PETER: ["Peter's line without a colon."]
ANNA: ["Anna's line."]
MIXED CASE CHARACTER: ['Line for mixed case.']
SHORTY: ['Short line.', 'Another short line.']

--- Documentation of Changes and Issues ---
Changes made to extract_character_lines function:
1. Added a more flexible regex pattern (`character_colon_pattern`) to handle mixed-case character names, hyphens, apostrophes, spaces, and optional parenthetical information before the colon.
2. Introduced `uppercase_line_pattern` to identify potential character names on lines by themselves (without a colon).
3. Added logic to check the line following an `uppercase_line_pattern` match to confirm if it's likely dialogue, thus attributing the uppercase line as the character name (`potential_character` and `last_line_was_character` flags).
4. Refined the exclusion logic (`exclusion_pattern`) to more robustly identify and skip scene headings and act titles.
5. Used `stage_direction_pattern` to explicitly identify and exclude lines that start with '(' or '[' from being considered dialogue.
6. Modified the logic for attributing consecutive lines to the `current_character` to ensure they are not stage directions.
7. Adjusted the post-processing filter to remove common ensemble/group names that might be incorrectly identified as individual characters.
8. Ensured that the text extracted after a character name and colon (`remaining_line`) is also checked against the `stage_direction_pattern` before being added as a line.
9. Temporarily excluded the `fitz` import and `read_pdf` function due to persistent `ModuleNotFoundError` to focus on refining the core text parsing logic.

Issues encountered and resolved:
1. Initial regex was too strict (only uppercase, required colon), missing many valid character names. Resolved by using more flexible regex and patterns.
2. Difficulties in distinguishing character names on their own line from scene headings or other uppercase text. Partially resolved by checking the subsequent line for dialogue and adding specific exclusion patterns.
3. Stage directions within dialogue lines were sometimes included. Resolved by explicitly checking for lines starting with '(' or '[' and excluding them.
4. Short uppercase words or abbreviations were sometimes incorrectly identified as characters. Partially resolved by refining the post-processing filter based on line count and character name length, and adding exclusions for common ensemble names.
5. Handling of parenthetical information after character names needed refinement. The updated regex now captures this separately, though the primary character name is extracted before the parenthesis.
6. Reconstructing dialogue lines accurately when character names appear on a separate line required state tracking (`potential_character`, `last_line_was_character`).
7. Encountered persistent `ModuleNotFoundError` for `fitz`. Temporarily resolved by removing the import and related function to proceed with text parsing refinement. Re-integration of PDF reading will be necessary later.

Further Considerations/Potential Issues:
- Complex stage directions or character name formats not covered by current patterns.
- Scripts with inconsistent formatting or unusual layouts.
- Performance on very large scripts.




Summary:
Data Analysis Key Findings
The project successfully identified and listed all necessary libraries, models, accounts, and platforms, including transformers, gradio, PyMuPDF, bert-base-uncased, Python, and potential deployment platforms.
The initial regex-based script parsing logic was refined to handle a wider variety of script formats, such as mixed-case names, names without colons, and improved exclusion of stage directions and scene headings.
Due to the inability to download real copyright-free scripts from Project Gutenberg, dummy text and PDF scripts were created and used to simulate the data collection and manual annotation process.
A Hugging Face token classification model (bert-base-uncased) was successfully fine-tuned on the small, simulated annotated dataset.
The model training process completed successfully after resolving a ModuleNotFoundError for seqeval and a TypeError caused by an invalid argument in TrainingArguments.
Model evaluation on the dummy data showed low performance metrics (precision, recall, F1-score), which was expected given the limited size and diversity of the training data.
The fine-tuned model was successfully integrated into the Gradio application, enabling processing of both text and PDF script inputs.
During the final testing phase, a TypeError was encountered in the model's post-processing logic (.item() on an integer) and successfully fixed.
Simulated end-to-end testing confirmed that the application runs without crashing but produces inaccurate results due to the model's low performance on the dummy data.
The Gradio interface was refined with clearer input labels, more informative output (including total lines and character counts), and basic processing feedback to improve user experience.
Comprehensive documentation covering all aspects of the project, including resources, parsing logic, data annotation (simulated), model training/evaluation, model integration, testing results, and deployment preparation steps (containerization, hosting, etc.), was generated.
Insights or Next Steps
The most critical next step is to significantly expand and refine the annotated dataset using real-world scripts to improve the model's accuracy in identifying characters and lines.
Further work is needed to make the post-processing logic more robust, especially for accurately reconstructing multi-token character names and dialogue lines from the model's token-level predictions, and to handle complex script formatting variations.
- Accuracy might still be limited without a model, especially for ambiguous cases.
- The `fitz` import issue needs to be addressed to support PDF input.
--- End Documentation ---
