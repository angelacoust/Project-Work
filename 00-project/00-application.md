---
name: Ángela Costa Trigo
neptun: IE2DPE
---
# Nutritional Receipt Scanner and Recipe Generator

## My interpretation of the brief
The project is an adaptation of 2026_SD_4. My idea is a mobile and web application that uses OCR and an LLM to extract editable food item lists from supermarket receipts. Instead of splitting expenses, the system will use the extracted ingredients to generate viable recipe recommendations and calculate accurate macronutrient profiles. The application must provide responsive interaction, manual correction of AI suggestions, and persistent storage of receipts and generated recipes.

## Why I am a good fit for this project
I am highly interested in the intersection of artificial intelligence and database management. While the original brief (id: 2026_SD_4) focused on financial allocation, adapting the OCR and AI extraction pipeline to a nutritional context maintains the exact technical complexity required (handling uncertain fields, deterministic calculations, and persistent state) while solving a complex daily planning problem. 

## Relevant experience and background
I have background in computer engineering, including logic design, data modeling, and structuring complex databases. These skills can be applied to designing the different user flows required for scanning receipts, verifying data, managing ingredient inventories, and calculating precise nutritional information.

## Proposed approach
I would first define the main user flow, starting from a user uploading a photo of a receipt or a ticket to finally selecting a recipe and viewing its nutritional breakdown. I would then implement the data models for ingredients, recipes, and user profiles.

The platform would support a seamless process where the user acts as the final validator. The system will suggest the extracted items from the receipt, the user will confirm or edit the quantities (for example, saving only the ingredients needed for cooking), and the application will match those ingredients to provide meal recommendations

## Initial plan
Define the main user flows for ingredients and recipes.
Design the receipt uploading and item extraction process.
Build the user interface for reviewing and editing the shopping list

## Additional information
I plan to maintain a Git repository from the start and use issues to track development. The application will be designed with a focus on simplicity, usability, and accuracy, with the possibility of adding more advanced features like weekly meal planning, budget tracking, and dietary restrictions later.
