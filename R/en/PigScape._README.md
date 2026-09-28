# PigScape.

<p align="center"><img src="../assets/kr/PigScape/cover.png" alt="PigScape. cover image" width="100%"></p>

> A team project that planned 「돼탈출」 ("Pig Escape"), a diet helper website bundling a basal metabolic rate calculator, a diet facts quiz and a personal records page, which I rebuilt three times on the tech side

---

## 1. Project Overview

| Item | Details |
|---|---|
| Project | PigScape. (「돼탈출」 🐷) |
| One-line Summary | Planned a diet service that tackles obesity and stress together, and built a 6-page web prototype made up of a BMR calculator, a quiz, a My Page and a membership introduction |
| Period | 2025.08 ~ 2025.12 (based on file dates. Flask v1 2025.08–09 → Flask rework 2025.09 → static web ReReWork 2025.12) |
| Team | 6 people (Team 「뒷동네」 ("Back Street"): 3 tech · 3 operations) + 1 mentor |
| My Role | Tech team, "Tech & Production." Wrote the requirement specs for each page, built the website, rebuilt it three times |
| Contribution | (to be confirmed) |
| Context | TMD activity, second semester of first year (to be confirmed) |

The team pitched a service that helps people diet "continuously and systematically." The tech team took on a web prototype to make that service visible. I wrote a spec for each page covering layout, copy, colors, animation and edge-case behavior, and built the pages from those specs. The specs followed one principle: "every component fits snugly within a single screen."

There is no repository. This report is based on the source code of the three versions and the planning notes left on my machine.

## 2. Tech Stack

| Category | Technology | Why |
|---|---|---|
| v1 · v2 (Flask) | Python Flask 3.0.2, Jinja templates, gunicorn 21.2.0 + Procfile | Split the pages into routes, with server deployment in mind |
| v3 (ReReWork) | HTML · CSS · Vanilla JavaScript (6 pages, about 1,480 lines) | The server wasn't doing any work, so I moved to static files (§5-③) |
| Charts | Chart.js (CDN) | The BMI trend line chart on My Page |
| Design | Self-hosted Paperlogy font, pastel burgundy (#D64E52) and pink + Toss-style gray background | The palette was fixed at the planning stage as "four light pastel shades of red" |

## 3. Key Features & Contributions

<p align="center"><img src="../assets/kr/PigScape/home.png" width="80%" alt="Pig Escape home"></p>
<p align="center"><sub>Captured locally (v3 ReReWork). The three BMI range cards flip over on hover or tap to show exercise tips for each range</sub></p>

| Page | Features |
|---|---|
| Home | Logo interaction, tip cards for each BMI range (overweight 23–25 · obese 25–30 · severely obese 30–35), a premium info modal when you press 👑 |
| Pig Escape? | An intro page that moves one screen at a time with scroll snap: the problem → values (Continuous · Systematic) → key features → team · mentor → copyright |
| BMR calculator | Popup for sex, age, height and weight → BMR calculation → "Me" and "age-group average" arrows on a sex-specific range bar → analysis · diet guide · fun facts → recommended workout videos |
| 「돼지니어스」 ("Pigenius") | A 3-choice diet facts quiz. Tapping an answer shows right or wrong by color, with a per-question explanation and a score at the end |
| My Page | Dashboard after login: BMI trend graph, health indicators, today's goal progress, achievement badges |
| Premium | 3 subscription plans and an idea for challenges that pay out part of the subscription fees as prize money |

<p align="center"><img src="../assets/kr/PigScape/bmr.png" width="80%" alt="BMR calculation result"></p>
<p align="center"><sub>BMR result (sample input: male · 17 · 175 cm · 72 kg). The three bar segments and two arrows show the gap from the age-group average</sub></p>

### How BMR Is Calculated

- The formula is the revised Harris-Benedict equation. Male `88.362 + 13.397W + 4.799H − 5.677A`, female `447.593 + 9.247W + 3.098H − 4.330A`.
- The age-group average comes from a table by sex × 5-year bracket (e.g. male under 25: 1,720 kcal), and anything within ±100 kcal of it counts as "about average."
- The diet guide suggests BMR × 1.5 for maintenance and BMR × 1.2 for weight loss. The fun facts convert BMR into hourly and yearly energy burned and a weekly fat equivalent (7,700 kcal = 1 kg).

### Main Tasks

- Wrote requirement specs for all 6 pages (layout, copy, colors, transition animations, edge-case behavior)
- Flask v1: home, intro and BMR calculator (the quiz and My Page showed an "in development" notice)
- Flask rework: completed all 6 pages and set up `Procfile` and `requirements.txt` for deployment
- Static ReReWork: cleaned up the design, fixed the verdict logic, switched to a structure that opens without a server

## 4. Results & Metrics

### Quantitative Results

| Item | Result |
|---|---|
| Pages | 6 (home · intro · BMR calculator · quiz · My Page · premium) |
| Versions | Rebuilt 3 times (Flask v1 with 3 of 5 pages implemented → rework 6/6 → static ReReWork 6/6) |
| Quiz | 3 questions, each with an explanation |
| Presentation · evaluation results | (to be confirmed) |
| Real users · live URL | (to be confirmed) |

### Qualitative Results

- **Practiced turning plans into specs.** For every screen I defined the layout and hierarchy in writing first, like "logo on the left half, two topic/subtopic pairs on the right, topics bold and subtopics light," and only then built it.
- **Rebuilt the same service three times.** Each new version found and fixed one problem, in page transitions, verdict logic or file structure (§5).

### Known Limitations

- **My Page is a mockup.** Login is checked against a single account hard-coded in the source, and the graph, indicators and badges are all fixed values. The BMR you enter is never saved or turned into a record.
- **The source of the age-group average BMR table wasn't noted in the code.** (to be confirmed)
- **BMI ranges are described differently from page to page.** The home cards call 23–25 overweight and 25–30 obese, but the quiz explanation says "25 or above is overweight."
- **The recommended workout video always opens the first file only.** The originals are iPhone `.MOV` files, so some browsers may not play them. (to be confirmed)
- **The premium plans are only a concept.** The payment button does nothing.
- I added a script that blocks right-click, copying and developer tools, but it runs in the browser, so it offers no real protection.

## 5. Troubleshooting

### ① The intro page only advanced with the mouse wheel

**Problem** The v1 intro page moved one screen at a time. Rolling the wheel moved to the next section, and on reaching the team page it jumped to the next page automatically after 2 seconds.

**Cause** It listened for the `wheel` event, moved the container with `translateY(-N×100vh)` and applied a 0.8-second lock. Since it only handled wheel events, touch scrolling and the keyboard couldn't advance the page. With inputs that fire events in rapid succession, like trackpads, the number of screens it skipped depended on the lock timing.

**Solution** From the rework on, switched to CSS `scroll-snap-type: y mandatory`. Each section gets `scroll-snap-align: start`, and the scrolling itself is left to the browser. In v3, I removed the in-between screen that held only the team/mentor title so the team intro appears directly, cutting the sections from six to five.

**Result** Wheel, trackpad, touch and keyboard all advance one screen at a time in the same way. With the transition script gone, the only JS left on the intro page is the navigation effect and the security script.

### ② "Below average" wasn't actually compared with the average

**Problem** The v1 and v2 BMR calculators showed "Below average / Average / Above average" under the result. But for the same BMR, changing the age didn't change the verdict.

**Cause** The verdict came from fixed ranges per sex (male 1,600 · 1,900 kcal, female 1,300 · 1,700 kcal), not from the age-group average. The sex × age-group average table was only used to place the arrow.

**Solution** In v3, the verdict is based on the average for the user's sex and age group. Within ±100 kcal of the average it says "That's about average"; if lower or higher, it says so. The bar shows the "Me" and "Average" arrows side by side.

**Result** The text and the arrows now point to the same reference. A 17-year-old male at 72 kg · 175 cm gets 1,796 kcal, within +100 of the 1,720 kcal average, so he's judged "about average."

### ③ The server had nothing to do

**Problem** The Flask rework even had a `Procfile` for deployment, but opening a page always meant starting a Python server.

**Cause** All 6 routes simply returned `render_template`. Calculations, the quiz and login all ran in browser JS. Meanwhile, the links (`/info`, `/bmr`) and resource paths (`/static/...`) were written relative to the server root.

**Solution** In v3, I moved the templates to static HTML and changed links to file names like `info_pigscape.html` and resources to relative `static/...` paths.

**Result** It can now be opened straight from the folder without a server, or uploaded as-is to any static host.

## 6. Links & Deliverables

| Category | Link |
|---|---|
| GitHub Repository | None |
| Live URL | (to be confirmed) |
| Deliverables | 6-page static website (ReReWork), 2 Flask versions, logo and workout videos (8), planning specs for each page |

<p align="center"><img src="../assets/kr/PigScape/quiz.png" width="48%" alt="Pigenius results"> <img src="../assets/kr/PigScape/mypage.png" width="48%" alt="My Page"></p>
<p align="center"><sub>Left: Pigenius results (answers and explanations per question). Right: My Page dashboard (fixed mockup data; the name was changed to "User" for the capture)</sub></p>

---

+++

## Customer Journey Map

The user is someone about to start managing their weight.

| Stage | User action | User thought | Service response |
|---|---|---|---|
| Arrive | Opens the home page | "What should I do with a BMI like mine?" | Flipping the three range cards reveals a first step suited to each range, like walking, strength training or protecting the joints |
| Understand | Scrolls through the Pig Escape? page | "What kind of service is this?" | Shows the problem → values → features → the people who made it, one screen at a time |
| Measure | Fills in the BMR calculator | "Do I burn more than average?" | Places my value and the age-group average on one bar and gives an intake guide |
| Learn | Takes the Pigenius quiz | "Can't I just skip meals?" | On a wrong answer, it colors the correct one too and sets things straight with an explanation |
| Keep going | Opens My Page | "Am I doing okay?" | Shows progress with a trend graph, today's goals and badges (mockup) |

## Gesturing

- Tip cards flip on mouse hover, on tap on touch devices, and with Enter · Space on the keyboard (`tabindex="0"`).
- The 🐷 in the logo and the letters of the title bounce when pressed, and 👑 opens the premium modal.
- Hovering over a navigation item dims the others and shows a one-line description under the hovered item.

## Motion Design

- Cards rise into place one after another with `fadeUp`, 0.1 seconds apart. Flipping uses a `rotateY(180deg)` transition.
- After a choice, the quiz shows the right/wrong colors for 1.2 seconds before moving on to the next question.
- A failed login makes the card shake from side to side.

## DREAD Risk Assessment

| Threat | D | R | E | A | D | Score | Mitigation |
|---|---|---|---|---|---|---|---|
| Login account stored in plain text in client code | 1 | 3 | 3 | 1 | 3 | 2.2 | For now it guards only a mockup dashboard, so the damage is small. Before attaching real records, server-side authentication and hashed storage have to come first |
| Mistaking dev tools blocking for real protection | 1 | 3 | 3 | 1 | 2 | 2.0 | Assume the code can always be read, and keep no sensitive values on the client |

<sub>D·R·E·A·D = Damage · Reproducibility · Exploitability · Affected users · Discoverability (1~3). The score is the average.</sub>
