# Problem Context (Employer-Friendly Summary)

This repository contains relational algebra work for a database assignment set in a **pharmacy / prescriptions** domain.
The original course handout is **not** included in this public repo. Instead, this file summarizes the schema, constraints,
and the intent of each question so the relational algebra solutions can be read without the handout.

---

## How to read this repo

- **Start here:** `docs/problem-context.md` (this file)
- **See the final results:** `submission/A1.pdf`
- **See the source:** `submission/A1.tex` (all relational algebra expressions and integrity constraints)

---

## Domain overview

The database models:
- **Brand-name drugs** and their **generic alternatives**
- **Active ingredients** used to formulate brand-name drugs
- **Ingredient interactions** (pairs of ingredients that should not be combined)
- **Patients**, **pharmacists**, and **prescriptions**
- Which prescriptions were **filled** (and which remain **unfilled**)

A key detail: **ingredients are only recorded for brand-name drugs**. Generics inherit the ingredients/form/route/schedule
from their associated brand-name drug.

---

## Schema summary (relations)

Below is a plain-English reference for each relation and its attributes.

### Drugs and pricing
- **Product(DIN, name, manufacturer, form, schedule, route)**  
  Brand-name drug product.
- **Generic(DIN, brand, name, manufacturer)**  
  Generic drug product. `brand` references the brand-name drug’s DIN.
- **Price(DIN, price)**  
  Price is recorded for some drugs (brand or generic).

### Ingredients and interactions
- **ActiveIngredient(name)**  
  The set of known active ingredients.
- **Contains(DIN, ingredient, strength, unit)**  
  Ingredient composition for a **brand-name** drug (`DIN` is a brand DIN).  
  `strength` and `unit` describe dosage strength, but many questions treat ingredient *names* as the important part.
- **Interaction(ingredient1, ingredient2)**  
  Ingredient interaction pairs (intended to be symmetric).

### People and prescriptions
- **Patient(OHIP, name, dob, phone, address)**  
  Patient demographics.
- **Pharmacist(OCP, name, registered)**  
  Pharmacist info, including their registration date (used to define seniority).
- **Prescription(RxID, date, patient, drug, doctor, dosage, note)**  
  A prescription event. `drug` is a DIN (brand or generic). `doctor` is a doctor ID (doctors are not otherwise stored).
- **Filled(RxID, date, pharmacist)**  
  A record that a prescription was filled on a specific date by a pharmacist.

---

## Key integrity constraints (assumptions the database must satisfy)

These constraints matter because your relational algebra must work for **any** valid instance.

### Referential / set constraints
- Brand and generic DINs are **disjoint** (a DIN is not both a brand and a generic).
- Every generic’s `brand` DIN must exist in **Product**.
- Any DIN appearing in **Price** must correspond to a **Product** or **Generic** DIN.
- Any DIN in **Contains** must exist in **Product** (ingredients are stored only for brands).
- Any DIN referenced by **Prescription.drug** must exist in **Product** or **Generic**.
- Prescription patient IDs reference **Patient**.
- Filled records reference existing **Prescription** and **Pharmacist** tuples.

### Domain / time constraints
- Product.schedule is restricted to a small fixed set of categories (e.g., prescription / narcotic / OTC / homeopathic).
- A prescription cannot be filled **before** it is written (fill date is on/after prescription date).
- Interaction is intended to be **symmetric** (A interacts with B implies B interacts with A).  
  (This is explicitly used as a stated assumption when writing Part 1 queries.)

---

## Relational algebra “dialect” and limits used in this assignment

- Relations are treated as **sets** (no duplicates).
- Allowed operators are the core ones used in class (selection, projection, renaming, join/product, and set operators),
  plus assignment to name intermediate results.
- Extended operators from textbook “extras” (notably **division**) are not allowed.
- If something is not expressible in this language, the expected response is to state **“cannot be expressed”**.

---

## Part 1 — Query prompts (brief intent)

These are the problem statements your `A1.tex` answers, summarized for fast understanding.

1. **Frugal doctors**  
   Find doctors who prescribe only:
   - the cheapest generic option for a brand (allowing ties), or
   - brand-name drugs with **no** generic alternative.  
   Consider only drugs with a recorded price, and exclude doctors who have prescribed fewer than two distinct DINs.

2. **Potential doctor shopping**  
   “Equivalent medications” include:
   - same DIN, or
   - brand vs its generic, or
   - two generics that map to the same brand.  
   Find patients who have received equivalent meds from **two different doctors**.

3. **Safest ingredient**  
   Find the ingredient(s) that interact with the **fewest** other ingredients (ties included).
   Ingredients with zero interactions should be included when applicable.

4. **Drug shortage**  
   Find drugs (brand or generic) with **more than two unfilled** prescriptions, and those unfilled prescriptions span
   at least **two different patients**. Return the DIN and manufacturer.

5. **Protecting drug patents**  
   Find pairs of **different brand-name drugs** that have the **exact same set** of active ingredient names
   (ignore strength/unit). Avoid reporting mirrored duplicates (A,B) and (B,A).

6. **Patients at risk**  
   Find doctors who, on the **same day**, prescribed a patient multiple drugs such that at least two of those drugs
   have an interaction **via their ingredients**. Return doctor ID and date; include all qualifying dates per doctor.

7. **Many generics**  
   Find the pharmacist(s) who filled the **most** prescriptions for **generic** drugs.
   Also report the date when they last filled a prescription (their most recent fill date).

8. **Lots of competition**  
   Find manufacturers who:
   1) make at least one brand-name drug,  
   2) also manufacture a generic alternative for each of their own brand-name drugs, and  
   3) for each of their brand-name drugs, there exists at least one generic alternative made by a **different** company.  
   Return the manufacturer name.

---

## Part 2 — Additional integrity constraints (brief intent)

Each constraint is expressed as an RA expression that must evaluate to the empty set, or stated as not expressible.

1. **Symmetry of Interaction**  
   Interaction pairs must be symmetric.

2. **Don’t surpass those with seniority**  
   In each year, a pharmacist cannot fill more prescriptions than a pharmacist who registered earlier (is senior).

3. **Brand-name first**  
   A doctor cannot prescribe a generic drug unless they have previously prescribed its corresponding brand-name drug
   on an earlier date.

---

## What this shows an employer

- Comfort reading an unfamiliar schema and reasoning about keys/foreign keys and constraints
- Translating real-world rules into formal relational algebra queries and constraint checks
- Handling tricky cases like ties, “for all” style logic without division, self-joins, and pseudo-duplicate elimination
- Clean, auditable query structure using intermediate named relations
