# Bugs found

Add one section per issue. Bug 1 is filled in to show the format — fix it, then write what you changed. Copy the blank template for the rest.

Keep this file in the repo and **commit it** with your fixes.

---

## Bug 1

**How to reproduce:** Open the app. The expense list says “Newest first”. The first row is Wine (7 Mar). Board game (15 Mar) is further down.

**What is wrong:** The list is showing oldest expenses first. Newest should be at the top.

**What I changed:** Fixed the sort order in `src/components/ExpenseList.jsx` line 56 from `dateValue(a.date) - dateValue(b.date)` to `dateValue(b.date) - dateValue(a.date)` to sort in descending order (newest first).

---

## Bug 2

**How to reproduce:** Add an expense where the payer is not in the split (e.g., Diya pays for a cab that only Aisha and Ben use). Check the Balances panel and Settle up list.

**What is wrong:** When the payer is not included in the split, the balance calculation incorrectly subtracts their share from what they paid. For example, Diya paid $60 for Aisha and Ben to share ($30 each), but the balance showed Diya as owing instead of being owed. Diya should be owed $60 (the full amount), not $30.

**What I changed:** Removed the incorrect special case logic in `src/lib/balances.js` (lines 18-21) that was subtracting the payer's share when they were not in the split. The correct behavior is: when someone pays for others, they are owed the full amount they paid if they're not in the split. The share subtraction only applies to people in the split.

---

## Bug 3

**How to reproduce:** Open the app, verify the expenses sort correctly. Reload the page. The expenses will no longer sort correctly.

**What is wrong:** When the app loads state from localStorage, the date objects are not being hydrated (converted from strings to Date objects). The sorting relies on Date objects, so when dates are strings like "2026-03-12", the sorting fails.

**What I changed:** Modified `src/state/store.js` in the `loadState` function to hydrate the parsed data from localStorage. Changed `return JSON.parse(raw);` to `return hydrate(JSON.parse(raw));` to ensure dates are properly converted to Date objects.

---

## Bug 4

**How to reproduce:** Add a new expense. Then delete it. Observe which expense gets deleted. Try deleting an old expense from the list. Notice that the wrong expense gets deleted.

**What is wrong:** The expense list receives a filtered array of expenses from App.jsx, but when deleting or updating an expense, it was passing the array index from the filtered/sorted list. This index doesn't correspond to the index in the original state.expenses array. For example, if you filter by category and add a new expense, then try to delete it, the app would delete a different expense from the full list at that same index.

**What I changed:** Modified the delete/update callbacks in `src/App.jsx` to find the actual index in state.expenses by matching the expense ID. Modified `src/components/ExpenseList.jsx` to pass the expense.id instead of the array index when calling onDelete and onUpdateAt. This ensures that the correct expense is always deleted or updated, regardless of filtering or sorting.

---
