---
toc: true
layout: post
description: A very powerful and customizable template for meal planning powered by Airtable, with a back-end that is integrated across Recipes, Inventory, and Shopping Lists, with Meal board and Recipe Boards and much more!
categories: [markdown]
title: Meal Planner Database Using AirTable
tags: [database, airtable, worksheets]
---


## What is this? 🤔
---
An all-in-one meal planner, recipe board, and shopping list manager powered by [AirTable](https://airtable.com/). All three components work together using the same backend data to simplify meal planning needs.

- Meal Planner: Manage weekly meal plans.
- Recipe Board: Manage and curate your favorite recipes.
- Shopping List: Automatically generate shopping list based on recipes in the meal planner.

## How to get it? 🖐️
---
Use this link to copy the template to your AirTable account.
[AirTable Meal Planner Template by Rohith Palakirti](https://airtable.com/universe/exppHKsrkZZ191JNn/meal-planner-template)
---

If the above link doesn't work, try this:

1. Use this [link]((https://airtable.com/shrHIUJeQz37TY0ww)
) to copy the template to your AirTable account.

2. When you open this share link, you’ll see something like this:
![](https://support.airtable.com/hc/article_attachments/360000712568/Screen_Shot_2018-02-19_at_3.11.00_PM.png)

3. Select **Copy base** in the right-top corner to copy the base into your account.
![](https://support.airtable.com/hc/article_attachments/360000723607/Screen_Shot_2018-02-19_at_3_11_00_PM.png)


## How does this work? ⚙
---
There are four back-end tables that power the meal planner. These are:

- **Grocery Items:** List of grocery items.
- **Recipes:** List of recipes.
- **Ingredients:** Links recipes to grocery items with quantities needed.
- **Meal Plan:** Links days and meal times to recipes.

## How to use? 👩‍🍳
---

The linked database views below show four common meal planner needs:

- Meal planning view to organize the days.
- Recipe Kanban view for collection and inspiration.
- Inventory view to assess shopping needs.
- Shopping list view to identify needs at grocery store.

### Meal Planner Board View
Linked database of "Meals Table" arranged by day and meal time.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrqWQmXzOlaITR6B?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Recipe Kanban View
---
Linked database of "Recipes Table" in Board format arranged by meal type.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrLAsMPZnBZ97qNv?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Inventory List View
---
Linked database list "Grocery Table" with "Total Needed" > 0, ordered by home storage location.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrLfhwBVHtPnWYOf?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Shopping List View
---
Linked database list "Grocery Table" with "Total Needed" > 0, not in stock, ordered by grocery aisle.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrfPsx37Ebh63PPh?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Ingredient List View
Linked database list "Ingredients Table" with name, quantity, recipe, size, and Total Needed > 0.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrWpfyjvkAqb82fS?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

## The Meal Planner Backend Explained
---
### Groceries Table
**Fields**

- **Grocery:** Grocery title and icon.
- **Aisle:** The shopping aisle items if usually found in.
- **Storage:** The location it is stored at home.
- **Stocked?:** Flag whether item is available at home.

**Relations**

- **Ingredients:** Connects grocery to recipes via an ingredients list.

**Rollups**

- **Total Needed:** Total qty. needed based on meals this grocery appears in.
- **Shopping Qty:** Human readable quantity. Doesn't rollup across recipes so you'll see multiple quantities like in butter.

<iframe class="airtable-embed" src="https://airtable.com/embed/shr7HGi0bFZFz92uo?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Recipes Table
---
**Fields**

- **Name:** Recipe name, icon and cover.
- **Recipe Type:** Category field to group recipes.

**Relations**

- **Ingredients:** Connects recipes to groceries via an ingredients list.
- **Planned meals:** List of meals this recipe will be used in.

<iframe class="airtable-embed" src="https://airtable.com/embed/shr4jJZ4j4fMDUJ0H?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Ingredients Table
---
**Fields**

- **Ingredient:** Ingredient identifier. Currently using a reference to the grocery page.
- **Quantity:** Quantity needed of grocery for recipe.
- **Size:** Text label identifying quantity measure/size.
- **Total Needed:** Times planned times quantity needed for recipe.
- **Shopping Quantity:** Human readable quantity needed for week based on recipe days planned.

**Relations**

- **Grocery:** The grocery used for this ingredient.
- **Recipe:** The recipe this ingredient belongs to.

**Roll-ups**

- **Times Planned:** Number of times this recipe is included in Planned Meals.

<iframe class="airtable-embed" src="https://airtable.com/embed/shrp292rlJm8Pbcus?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

### Meals Table
---

**Fields**

- **Name:** Meal day and time label.
- **Day:** Day of week for board view.

**Relations**

- **Recipes:** The recipes included in this mealtime.

<iframe class="airtable-embed" src="https://airtable.com/embed/shruzCVcdMp7y7rKC?backgroundColor=gray&viewControls=on" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

Customize this backend as you like.