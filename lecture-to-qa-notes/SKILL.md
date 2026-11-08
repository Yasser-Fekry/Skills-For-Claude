---
name: lecture-to-qa-notes
description: Turn a lecture PDF or slide deck into short, exam-friendly study notes in Q&A markdown (.md), using the student's fixed structure (## Q.1 headings, bold key terms, comparison tables, analogies, worked examples with answers, key takeaways). Use this skill whenever the user uploads or mentions a PDF, lecture, slides, or "lec" and wants notes, a summary, a study sheet, "make it like the other file", or just says "make this also" or "send it as md", even if they do not name the skill. Output is always a downloadable .md file.
---

# Lecture PDF → Q&A Study Notes (.md)

The student sends a lecture PDF (usually slides, often engineering or CS courses) and wants notes in one fixed style. Follow this skill every time, without asking the user to repeat the format.

## Workflow

1. **Read the whole PDF first.** Use the content already in context. If the file content is not in context, read it from `/mnt/user-data/uploads/` (see the file-reading and pdf-reading skills). Check every page, including slides that are only images (diagrams, tables, worked examples).
2. **List the topics** in lecture order. Turn each topic into a question: "What is…?", "How does… work?", "What is the difference between… and…?", "What are the types of…?".
3. **Write the notes** using the structure below.
4. **Save** as a `.md` file in `/mnt/user-data/outputs/`, then call `present_files`. Name it after the course and lecture, with underscores, for example `Embedded_Systems_Lec2_Microcontroller_Features.md` or `DIP_Lec5_Spatial_Filtering.md`.
5. **Reply briefly** (a few lines): what is in the file, what you added beyond the slides, and any mistakes you found in the slides. Do not paste the whole notes in the chat.

If the PDF is not a lecture (for example a research paper or a form), still use the same style, but say so in one line.

## Structure rules

**Headings**
- Each topic is `## Q.N. Question?`, numbered in lecture order.
- Separate questions with `---`.
- Use `###` and `####` for sub-parts: "How does it work?", "Types", "Advantages", "Example", "Comparison".
- No title page, no table of contents, no intro paragraph. The file starts directly with `## Q.1.`.

**Inside each question, in this order when it applies**
1. **Definition first:** 3–4 short bullets. Key term in **bold**, then a colon and a one-line meaning.
2. **How it works:** a numbered list for any process or steps (INPUT → CONTROLLER → OUTPUT, Fetch → Decode → Execute).
3. **Types / categories:** a table.
4. **Comparison table** whenever two or more things are contrasted. Use centered columns (`:---:`). Add a "Feature" column when comparing.
5. **Analogy** when the slides give one, or when it makes the idea easier (chef vs food truck, doorbell vs opening the door).
6. **Worked example:** show the numbers step by step and end with the bold result.
7. **Examples list** at the end of a topic when useful.

**Style**
- Simple, direct English. The minimum words needed. Short bullets, not paragraphs.
- Expand acronyms on first use: `PCB = Printed Circuit Board`.
- Attach a concrete example to every abstract idea.
- Order: what is it → how it works → types → comparison → examples.
- Put side notes, tips and reminders in italics: `_Note: …_`.
- Mark figures the slides contain with an italic placeholder: `_(Diagram: …)_` or `_(Picture: …)_`. Describe what the figure shows in one line. Do not try to redraw complex figures.
- Math: write formulas in bold on their own line using plain Unicode (Σ, ∇², ∂, ×, ÷, ≈, →). Do not use LaTeX.
- Matrices and small grids: use markdown tables with centered columns. A table needs a header row, so use the first row as the header when there is no title.
- Keep the lecture's own numbering where the slides number things (for example "feature 10"), even if the Q order differs.

**Last sections (add when the lecture has them or when they help)**
- A "Which one is it?" / quick-check table, with answers filled in.
- **Review questions with short answers**, if the slides list review questions.
- Student projects / extra ideas, as one table, if the slides have them.
- `## Q.N. Key takeaways`: 5–8 numbered one-line points.

## Accuracy rules (important)

- **Stay inside the slides.** Do not add new theory. You may add small clarifications (one italic line) that are standard and safe, such as "the weights sum to 1".
- **Compute missing answers.** If a slide poses an example or question without the answer, calculate it, show the steps, and add an italic note: `_These answers are calculated from the image on the slide._`
- **Check every calculation in the slides.** Redo the arithmetic. If the slide is wrong, keep the slide's wording where it matters, write the correct value, and add an italic note, for example: `_The slide writes 154 − 14 = 130. The correct sum is 140._`
- **Flag other slide errors** the same way (wrong titles, repeated symbols, labels that differ from standard textbooks). Never silently change the lecturer's definition. In the final chat reply, list these points so the student can check them with the lecturer.
- **Garbled or reversed text** (for example text that reads backwards in the PDF): reconstruct the sentence from the context and the figure next to it.
- **Handwritten marks, circles and highlights** in the PDF show what the lecturer stressed. Make those terms and sentences **bold** and make sure they are in the notes. Do not mention the marks themselves.
- If something is unreadable or unclear, say so in an italic note instead of guessing.

## Template

```markdown
## Q.1. What is X?

### X

- **Term:** short meaning.
- Second key point.

### How does X work?

1. **Step one:** what happens.
2. **Step two:** what happens.

_Note: one short reminder or tip._

_(Diagram: what the figure shows.)_

---

## Q.2. What is the difference between A and B?

|  Feature  |   A   |   B   |
| :-------: | :---: | :---: |
| Purpose   | …     | …     |
| Speed     | …     | …     |

### Examples

1. Example one: A
2. Example two: B

---

## Q.N. Key takeaways

1. …
```

## Final reply format

Keep it short. Example:

> Your Lecture N notes are ready as `File_Name.md`, in the same Q&A style.
>
> I added: (1–3 short items, such as calculated answers or extra tables).
>
> Check these points in the slides: (list of slide mistakes, if any).

Language of the reply: the language the student writes in. The notes themselves stay in English, as the slides are in English.
