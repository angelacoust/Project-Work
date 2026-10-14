---
project_outline: "[[00-project/01-project-outline]]"
---
# Nutritional Receipt Scanner and Recipe Generator

## 1. Actors and permissions

| Role             | Needs                                                                                          | Responsibility / access                                                                                                                                                                         |
| ---------------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Application user | Turn supermarket purchases into ingredients, meal recommendations, and nutritional information | Uploads receipts; reviews, corrects, and confirms extracted items and quantities; opens saved receipts; manages the pantry and basic staples; chooses a recipe; views its nutritional breakdown |
Meal-planning, ingredient-review, and nutrition-focused users describe needs of this same actor. There is no administrator role, sharing permissions, login method, or multi-user access model. 

The pantry and staples list are user data maintained by hand, not a separate role. OCR, the LLM, recipe matching, and nutritional calculation are system components, not human permission roles. Their suggestions cannot confirm ingredients on the user's behalf.

## 2. Use cases / user stories

### UC1: Upload and scan a receipt

- **Actor:** Application user
- **Precondition:** The user has a photo of a supermarket receipt or ticket.
- **Main flow:**
    1. The user selects and uploads the photo.
    2. The application processes it with OCR and an LLM
    3. The application suggests food items and quantities
    4. The application presents the suggestions as an editable shopping list.
- **Alternative / error flows:** If the image cannot be processed or no usable items are extracted, show the problem and let the user submit another photo. Uncertain fields remain suggestions for manual review.
- **Postcondition:** An unconfirmed list is available for validation; extraction alone does not make it final.

### UC2: Review, correct, and save the receipt

- **Actor:** Application user
- **Precondition:** A receipt has produced an editable list.
- **Main flow:**
    1. The user checks the suggested items against the receipt.
    2. The user corrects food-item information if needed.
    3. The user reviews fields that the system could not establish reliably
    4. The user confirms the corrected list.
	5. The application saves the confirmed receipt to the user's receipts list.
- **Alternative / error flows:** Missing or unusable quantities remain unresolved until corrected. If the user has not confirmed the list, it is not used as validated recommendation input. If saving fails, show the failure instead of implying the receipt is saved.
- **Postcondition:** The receipt is saved in the receipts list with user-reviewed data rather than unchecked OCR/LLM output.

### UC3: Browse saved receipts and open one

- **Actor:** Application user
- **Precondition:** At least one confirmed receipt has been saved.
- **Main flow:**
    1. The user opens the receipts list, which shows the user's saved receipts.
    2. The user taps a receipt.
    3. The application opens it and shows its validated items and quantities, with the option to get recipe recommendations from it (UC4).
- **Alternative / error flows:** If there are no saved receipts, the list says so and points the user to receipt upload. 
- **Postcondition:** One saved receipt is open as the basis for recommendations.

### UC4: Get recommendations from a receipt and select a recipe

- **Actor:** Application user
- **Precondition:** The user has opened a saved, validated receipt (UC3).
- **Main flow:**
    1. The application shows the receipt's ingredients as the base for the recommendation.
    2. Optionally, the user switches on the pantry and/or the enabled staples to add them to this request (see UC7).
    3. The application matches the receipt's ingredients, plus any added pantry items and staples, to meal recommendations.
    4. The user reviews the suggested recipes.
    5. The user selects a recipe for nutritional calculation.
    6. The user selects the servings for the recipe.
- **Alternative / error flows:** If no viable recommendation is available, state that outcome and let the user change the pantry and staples toggles or open another receipt.
- **Postcondition:** A selected recipe is available for the nutrition step. The user cannot start a recommendation from hand-picked ingredients; it always starts from a saved receipt

### UC5: View the recipe's macronutrient breakdown

- **Actor:** Application user
- **Precondition:** A recipe is selected, with the ingredient quantities and nutritional data needed for calculation.
- **Main flow:**
    1. The application uses the recipe's ingredient quantities and nutritional data.
    2. It applies deterministic calculations.
    3. It displays the selected recipe's macronutrient breakdown.
    4. Optionally, the user saves the viewed recipe (UC6). If the user does not save it, the recipe is not stored.
- **Alternative / error flows:** If required nutritional data or usable quantities are missing, show the limitation instead of presenting an unsupported result as accurate.
- **Postcondition:** The user sees a breakdown tied to the selected recipe and calculation inputs. The recipe is stored only if the user chose to save it. Reporting units and portion basis remain to be decided.

### UC6: Keep receipts and saved recipes

- **Actor:** Application user
- **Precondition:** Confirmed receipt data is available, or the user has chosen to save a generated recipe.
- **Main flow:**
    1. The application stores the confirmed receipts, with their validated items. It stores a generated recipe only when the user explicitly saves it; recipes the user does not save are not stored.
    2. The current interaction ends.
    3. The stored data can be retrieved in a later interaction.
- **Alternative / error flows:** If saving fails, distinguish that failure from successful storage. The receipts list (UC3) is the way to retrieve saved receipts, and the saved recipes list (UC8) is the way to retrieve saved recipes.
- **Postcondition:** Persistence preserves the saved receipts, the recipes the user saved, and the pantry and staples state beyond the current interaction.

### UC7: Manage the pantry and basic staples 

- **Actor:** Application user
- **Precondition:** None. The user can open the pantry at any time; items from saved receipts can be added to it.
- **Main flow:**
    1. The user opens the pantry view.
    2. The user adds, edits, or removes pantry items and their quantities by hand, including previously bought ingredients kept from saved receipts.
    3. The user switches basic staples (for example oil, salt, pasta) on or off to show what is available at home.
    4. The application saves the pantry and staples state.
- **Alternative / error flows:** If saving fails, show the failure instead of implying the pantry is updated. Items with unusable names or quantities stay marked for correction.
- **Postcondition:** The pantry and staples state is available as an optional add-on to a receipt-based recommendation (UC4). The application does not track expiry dates or reduce stock automatically; the user keeps the pantry up to date.

### UC8: Browse saved recipes and open one

- **Actor:** Application user
- **Precondition:** The user has saved at least one generated recipe (UC6).
- **Main flow:**
    1. The user opens the saved recipes list, which shows the recipes the user has saved.
    2. The user taps a recipe.
    3. The application opens it and shows the recipe again with its macronutrient breakdown (UC5).
- **Alternative / error flows:** If there are no saved recipes, the list says so and points the user to the receipts list to get recommendations. If a stored recipe cannot be loaded, show the failure instead of an empty recipe.
- **Postcondition:** One saved recipe is open and shown with its macronutrient breakdown.

## 3. Functional requirements

| Requirement                                                  | Acceptance criteria                                                                                                                                                                                                                                                                                                                                                                                        | Priority |
| ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| FR1: Upload receipt photos.                                  | Given a supported receipt photo, when the user uploads it, then the application makes it available for extraction. Supported formats and limits remain to be defined.                                                                                                                                                                                                                                      | Must     |
| FR2: Extract food items and quantities using OCR and an LLM. | Given a readable receipt photo, when extraction finishes, then suggested items and quantities appear in an editable list, not as automatically confirmed data.                                                                                                                                                                                                                                             | Must     |
| FR3: Correct extraction suggestions manually.                | Given an extracted list, when the user changes an item or quantity, then the review list shows the corrected value.                                                                                                                                                                                                                                                                                        | Must     |
| FR4: Confirm reviewed ingredient data.                       | Given an unconfirmed list, when the user confirms the reviewed data, then that data becomes the validated basis for recommendations and is saved as a receipt. Before confirmation it is not treated as final.                                                                                                                                                                                             | Must     |
| FR5: List saved receipts and open one.                       | Given one or more saved receipts, when the user opens the receipts list and taps a receipt, then its validated items are shown and recommendations can be requested from it.                                                                                                                                                                                                                               | Must     |
| FR6: Recommend viable recipes.                               | Given an open saved receipt with validated items, when recommendations are requested, then suggested recipes are matched to that receipt's ingredients, plus pantry items and enabled staples if the user has switched them on, under the agreed matching rules. Pantry and staples add-ons are proposed. A receipt that is not validated is not used. If no recipe is viable, the user sees that outcome. | Must     |
| FR7: Select a recommended recipe.                            | Given recipe recommendations, when the user chooses one, then its ingredient data becomes the input to the nutritional calculation.                                                                                                                                                                                                                                                                        | Must     |
| FR8: Calculate and display macronutrients.                   | Given complete recipe quantities and nutritional data, when calculation runs, then a breakdown is displayed. Repeating with the same inputs and data produces the same result.                                                                                                                                                                                                                             | Must     |
| FR9: Preserve receipts and saved recipes.                    | Given successfully saved receipts (with their validated items) and recipes the user saved, when a later interaction retrieves them, then the stored records remain available and unchanged.                                                                                                                                                                                                                        | Must     |
| FR10: Handle unresolved extraction and nutrition inputs.     | Given a required field that is missing or unusable, when the user reaches the affected step, then the application identifies what needs correction or data rather than claiming a complete result.                                                                                                                                                                                                         | Must     |
| FR11: Maintain ingredient-inventory data.                    | Given confirmed ingredient information stored in the agreed inventory model, when it is retrieved, then the recorded items and quantities are preserved. Expiry tracking and automatic stock consumption are out of scope for the MVP                                                                                                                                                                      | Should   |
| FR12: Provide responsive interaction.                        | Given the same receipt-to-recipe flow, when the user reviews items, opens a saved receipt, and selects a recipe, then the required information and controls remain usable.                                                                                                                                                                                                                                 | Must     |
| FR13: Manage the pantry manually.                            | Given the pantry view, when the user adds, edits, or removes an item or quantity, then the change is shown and kept after the interaction ends. The application does not change pantry quantities on its own.                                                                                                                                                                                              | Should   |
| FR14: Toggle basic staples.                                  | Given a predefined list of common household staples, when the user switches a staple on or off, then its state is saved and only enabled staples can be added to a recommendation.                                                                                                                                                                                                                         | Should   |
| FR15: Add pantry and staples to a recommendation.            | Given an open saved receipt, when the user switches the pantry and/or the enabled staples on or off before requesting recommendations, then matching uses the receipt's ingredients plus only the add-ons that are on. Recommendations are never started from the pantry or staples alone.                                                                                                                 | Should   |
| FR16: List saved recipes.                                    | Given one or more recipes the user has saved, when the user opens the saved recipes list, then those recipes are listed; with none stored, the list says so.                                                                                                                                                                                                                              | Should   |
| FR17: Open a saved recipe.                                   | Given the saved recipes list, when the user taps a recipe, then it is shown again with its macronutrient breakdown.                                                                                                                                                                                                                                                                                        | Should   |
| FR18: Save a recipe.                                         | Given a recipe whose macronutrient breakdown is displayed, when the user chooses to save it, then it is stored and appears in the saved recipes list; a recipe the user does not save is not stored.                                                                                                                                                                                                       | Should   |

Priority: **Must** = required for the final product; **Should** = important; **Could** = optional if time permits.

## 4. Business rules and constraints

| Rule or constraint                                                                                                      | Rationale                                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| The user is the final validator of extracted items and quantities.                                                      | OCR and LLM output can contain uncertain fields or incorrect suggestions.                                                                             |
| Recommendations start from a saved, validated receipt, plus the pantry and enabled staples when the user includes them. | Recipes are meant to use what was just bought, and people often already have other ingredients at home. There is no separate ingredient-picking flow. |
| A receipt must be confirmed by the user before it is saved and used for recommendations.                                | Unchecked OCR/LLM output must not feed recipes or nutrition.                                                                                          |
| Basic staples are toggled by the user.                                                                                  | The application cannot know what is in the user's kitchen; a staple counts only when switched on.                                                     |
| The pantry is maintained manually                                                                                       | Keeping it by hand avoids stock-tracking logic in the MVP; the user is responsible for keeping it up to date.                                         |
| The pantry and staples are optional add-ons to a receipt-based recommendation                                           | Lets the user get recipes from the receipt only, or from the receipt plus the pantry and staples.                                                     |
| Expiry tracking and automatic stock consumption are not part of the MVP                                                 | They would add complexity beyond the first basic version.                                                                                             |
| Nutritional calculations are deterministic.                                                                             | A repeatable calculation must be separate from uncertain AI extraction.                                                                               |
| A calculation requires usable quantities and nutritional data.                                                          | Correct arithmetic cannot compensate for unknown units or missing input data.                                                                         |
| Missing data must not be presented as a confirmed accurate breakdown.                                                   | The proposal prioritises accuracy; uncertainty must stay visible.                                                                                     |
| Saved receipts and user-saved recipes persist beyond the current interaction.                                            | Persistent state is an explicit part of the proposal, and the receipts list depends on it.                                                            |
| Later extensions remain outside the initial flow.                                                                       | They must not distract from the core scanning, validation, and recommendation work.                                                                   |

## 5. User interface and workflow

Proposed views derived from the source flow:

| View | Main information | User action / next step |
| --- | --- | --- |
| Receipt upload | Selected receipt photo and processing state | Submit a photo for extraction; retry if processing fails |
| Shopping-list review | Suggested items, quantities, and fields needing attention | Edit, confirm, and save the receipt |
| Receipts list | The user's saved receipts | Tap a receipt to open it |
| Receipt detail | The saved receipt's validated items and quantities, with pantry and staples toggles (proposed) | Request recipe recommendations from this receipt |
| Pantry and staples (proposed) | Pantry items with quantities, and the list of basic staples with on/off switches | Add, edit, or remove pantry items; switch staples on or off |
| Recipe recommendations | Recipes matched to the receipt (and any pantry and staples added), or a no-result message | Select a recipe or return to the receipt |
| Nutritional breakdown | Selected recipe and calculated macronutrients, with unresolved-data notices where needed | Inspect the result or return to recipe selection |
| Saved recipes list | The user's previously generated recipes | Tap a recipe to open it again with its macronutrient breakdown |

**Main navigation:** Receipt upload → shopping-list review (confirm and save) → receipts list → receipt detail → recipe recommendations → nutritional breakdown. The receipts list can be opened at any time to return to a saved receipt. The pantry and staples view can be opened from the receipt detail, from the recipe recommendations step, or directly, and returns the user to where they came from. Also there will be a menu bar on the bottom of the application with options to go to receipt upload, receipt list, pantry, saved recipes.

**Correction path:** Return to review when items or quantities need changing, or reopen the receipt from the list. The next matching or calculation step must use the updated confirmed data.

**Persistence boundary:** Confirmed receipts (with their validated items) are stored on confirmation, and generated recipes are stored only when the user saves them; the receipts list is how the user returns to receipts and the saved recipes list is how the user returns to recipes; the pantry and staples state is stored alongside them

## 6. Non-functional requirements

| Requirement                                                                     | How it will be verified                                                                                                                                                                     |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The main flow is understandable and supports manual correction.                 | Walk through upload, correction, opening a saved receipt, recommendations, and nutrition without bypassing the validation step; record confusing interactions for revision.                 |
| Interfaces remain usable.                                                       | Review the same flow at screen sizes, checking readable item information and accessible editing controls.                                                                                   |
| Nutritional calculations are repeatable and correct for the agreed input model. | Use reference ingredient quantities and nutritional values; compare calculated results with independently worked reference results. Define units and rounding before testing.               |
| Extraction errors can be corrected without invalid data silently advancing.     | Use receipt examples with uncertain item names or quantities and verify the review and correction path.                                                                                     |
| Persistent state is retained correctly.                                         | Save a receipt, a recipe the user chose to save, and pantry/staples records (the last proposed), end the interaction, reopen the receipt from the list and retrieve the others later, and compare the stored data. |
| Pantry and staples add-ons behave as set                                        | Add, edit, and remove pantry items, switch staples and the pantry toggle on and off, and verify that recommendations use the receipt plus only the add-ons that are on.                     |
| Processing and storage failures are visible.                                    | Exercise failed extraction, unavailable recipe data, missing nutritional data, and failed saving; verify that the interface distinguishes failure from success.                             |
| The AI-assisted pipeline remains practical for interactive use.                 | Measure processing behavior after choosing providers and a prototype                                                                                                                        |

## 7. Open questions and risks

| Question / risk                                                                 | Impact                                                                                                | Owner               | Resolution / decision                                                                                                                                    |
| ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| How are abbreviated receipt names mapped to ingredients?                        | Wrong ingredient identities affect recommendations and nutrition.                                     | Student             | Define normalization and review rules using sample receipts.                                                                                             |
| Which quantity units and conversions are supported?                             | Receipt quantities may not directly support recipe calculations.                                      | Student             | Define the quantity model, required corrections, and conversion limits.                                                                                  |
| Where do recipes and nutritional values come from?                              | Coverage, calculation quality, and recipe matching depend on these sources.                           | Student             | Evaluate sources and their terms; document the selected approach.                                                                                        |
| What makes a recipe viable for a receipt's ingredients?                         | Recommendations may require ingredients the user did not buy.                                         | Student             | Agree matching rules, missing-ingredient behavior, and quantity handling.                                                                                |
| What is the nutrition reporting basis?                                          | Whole-recipe, portion, and unit choices change interpretation.                                        | Student             | Define reporting basis, macronutrient fields, and rounding before implementation.                                                                        |
| How does inventory change after a recommendation or cooking?                    | Incorrect stock assumptions could mislead later recommendations.                                      | Student             | Define inventory scope; do not assume cooking a recipe automatically consumes stock. Automatic consumption and expiry tracking are excluded from the MVP |
| Will the pantry and staples be adopted, and what is the default staples list?   | The pantry is beyond the source proposal; scope and the list need agreement.                          | Student, supervisor | Review the elaboration; define the default list and how pantry items are named and quantified.                                                           |
| Can a manually maintained pantry stay accurate enough?                          | A stale pantry can lead to recipes that need ingredients the user no longer has.                      | Student             | Keep maintenance simple, let the user leave the pantry off per recommendation, and test with the review flow.                                            |
| What are user profiles for, and is authentication needed?                       | Determines storage ownership and access-control design.                                               | Student             | Specify profile fields and account behavior without assuming dietary features are in the MVP.                                                            |
| Which OCR/LLM tools and deployment arrangement are suitable?                    | Accuracy, latency, cost, and privacy depend on the choices.                                           | Student             | Prototype extraction and compare options before committing to a stack.                                                                                   |
| What should be saved, when, and for how long? What does the receipts list show? | Storage errors or unclear retention affect usability and privacy; the list depends on what is stored. | Student             | Define receipt and recipe storage, the receipts list contents, retrieval, deletion, and failure behavior.                                                |
| How will accuracy and usability be evaluated?                                   | The proposal's goals are not yet backed by an evaluation protocol.                                    | Student             | Choose representative inputs and verification methods; agree any thresholds.                                                                             |
| When a saved recipe is reopened, are the stored macronutrient values shown or are they recalculated? | Recalculation could change the values the user saw before; showing stored values could hide data updates. | Student | Decide whether saved recipes keep their calculated macronutrients or recalculate on opening. |