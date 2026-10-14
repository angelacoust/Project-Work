---
name: Ángela Costa Trigo
neptun: IE2DPE
supervisor: Sári Dávid
github: https://github.com/angelacoust/Project-Work
---
# Nutritional Receipt Scanner and Recipe Generator

A mobile and web application that turns supermarket receipt photos into editable food-item lists, then uses validated ingredients, together with a manually maintained pantry, to recommend recipes and calculate their macronutrient profiles. OCR and an LLM suggest the receipt contents, while the user confirms or corrects items and quantities before they are used. The project combines uncertain-input handling, deterministic nutritional calculations, ingredient-inventory management, and persistent storage of receipts and generated recipes.

## Objectives

- **Primary objective:** Support the flow from a supermarket receipt photo to a user-selected recipe with a calculated macronutrient breakdown.
- **Problem:** Receipt data must become usable ingredients and quantities before it can support meal planning. OCR and LLM suggestions may contain uncertain fields, and nutrition calculations need validated inputs.
- **Target users / stakeholders:** People planning meals from supermarket purchases and checking the nutritional breakdown of recipes. Reviewing ingredients and checking nutrition are needs within the same user flow, not separate permission roles.
- **Observable success criteria proposed for the initial application:**
    - A receipt photo produces an editable list of suggested food items and quantities.
    - The user can correct extraction errors and confirm the ingredients needed for cooking.
    - Recommendations use the confirmed selection (plus the user's pantry and enabled staples, proposed) rather than unchecked extraction output.
    - The user can select a recipe and view its macronutrient breakdown.
    - Repeating a nutritional calculation with the same validated inputs and nutritional data produces the same result.
    - Saved receipts and generated recipes remain available after the current interaction ends.
- **Constraints:** Mobile and web interaction; user validation of AI suggestions; deterministic nutritional calculations; persistent receipt and recipe data. Frameworks, providers, nutritional data sources, budget, dates, and quantitative evaluation thresholds are not specified; no credentials in the repository.

## Scope

### In scope

- Uploading a photo of a supermarket receipt or ticket.
- OCR and LLM-assisted extraction of food items and quantities.
- An editable shopping list with manual confirmation and correction.
- Selection of ingredients and quantities needed for cooking.
- Ingredient-inventory management within the receipt-to-recipe flow; detailed inventory rules remain to be defined.
- **Pantry (proposed elaboration, for review):** an editable inventory of previously bought ingredients that the user adds to, edits, and removes manually, including items kept from confirmed receipts.
- **Basic staples (proposed elaboration, for review):** a predefined list of common household items (for example oil, salt, condiments) that the user can toggle on or off as available at home.
- Matching validated ingredients, the pantry, and enabled staples to viable recipe recommendations; the user can choose which sources a recommendation uses.
- Selecting a recipe and viewing its macronutrient breakdown.
- Persistent storage of receipts and generated recipes.
- Data models for ingredients, recipes, and user profiles; profile fields and account behavior remain open.
- Responsive interaction across mobile and web interfaces

### Out of scope

- Expiry tracking and automatic stock consumption for the pantry: pantry maintenance is manual in the MVP.
- Weekly meal planning, budget tracking, and dietary-restriction support in the initial application. The proposal identifies them as possible later extensions.
- Treating unreviewed AI suggestions as final ingredient data or using AI-generated numbers as the nutritional calculation itself

## Notes

The first version will be basic and focus on a few core parts of the project. The rest will be left open and worked on later, using Git and issues to track the work. The evaluation should show the main flow working: fixing uncertain fields, getting the same result when a calculation is repeated, and keeping the saved data. The test data, participants, and numeric goals are still to be decided. The technical idea is simple: OCR and AI only suggest the receipt contents, the user confirms them, and the nutrition numbers are calculated with fixed rules, not by the AI. No technology stack has been chosen yet.

Later planning still has to decide where the recipe and nutrition data will come from, how ingredients and quantities will be named, how the pantry and the default staples list will work, how data and user profiles will be stored, and how accuracy will be measured.