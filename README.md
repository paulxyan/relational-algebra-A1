# Relational Algebra — Pharmacy Database (CSC343 A1)

This repo contains my relational algebra work for a database assignment based on a pharmacy domain.
The goal was to interpret a new schema + integrity constraints and write correct relational algebra
queries and constraints using the course's allowed operators.

## What to look at (employer-friendly)
1. **Context first**: [`docs/problem-context.md`](docs/problem-context.md)
   - Schema summary (relations + what they represent)
   - Short plain-English descriptions of each question

2. **Final answers**: [`submission/A1.pdf`](submission/A1.pdf)
   - The compiled, readable submission

3. **Source (all RA expressions)**: [`submission/A1.tex`](submission/A1.tex)
   - Includes intermediate relations, renaming, set operations, and constraints

## Skills demonstrated
- Reading and using a relational schema + constraints
- Writing non-trivial relational algebra queries (joins, renaming, set difference/union/intersection, self-joins)
- Expressing integrity constraints as RA expressions of the form R = ∅
- Working within formal limitations (no division operator; set semantics)

## Building the PDF locally (optional)
If you want to compile the LaTeX:
```bash
cd submission
latexmk -pdf A1.tex
