# Project architecture

Beyond Earth is a self-contained, English-language space quiz. `index.html`
contains the markup, inline CSS, inline SVG artwork, question data, and vanilla
JavaScript. Keep the app usable directly from a `file://` URL without external
assets, dependencies, or a server.

The head script sets `html[data-theme]` before rendering. The script at the end
of the body owns the quiz state and DOM event handlers:

- `questions` contains objects with `category`, `question`, `answers`, a
  zero-based `correct` answer index, and `explanation`.
- `current`, `score`, `answered`, and `history` track the current question,
  correct-answer count, answer lock, and ordered boolean results.
- `renderQuestion()` creates answer buttons and resets per-question UI.
  `selectAnswer()` locks the answer, records its result, updates feedback and
  progress, and enables advancement. `showResults()` switches to the summary.
  The restart handler resets state and returns to the first question.

# Running and testing

Open `index.html` directly in a browser. In the integrated browser canvas, use
`file:///Users/cyz/Desktop/space-quiz/index.html` for this checkout; derive the
absolute path from the workspace in other checkouts.

The project smoke-test procedure is
`.github/skills/teste-quiz/SKILL.md`. Invoke the `teste-quiz` skill when asked to
test the quiz in the browser. For a single targeted check, follow the relevant
numbered scenario in that file from a freshly reloaded page.

Exercise the real UI using button clicks and keyboard events, not direct calls
to internal functions or mutations of quiz state. Derive expected answers from
the question data without changing the answer key. Distinguish synthetic
keyboard events from native keyboard navigation and ARIA inspection from
screen-reader testing. Wait for transitions before asserting final colors.
Restore temporary DOM/theme changes and finish with the page reloaded.

The full smoke procedure covers initial state, keyboard shortcuts and focus,
answer locking, score/progress/feedback, both themes, mixed/perfect/zero-score
runs, results, and restart. Do not introduce dependencies, a server, or
persistent test files just to run this procedure.

# Codebase conventions

- Use the existing `--cp-*` theme tokens for colors, surfaces, borders, and
  accents, including inline SVG. Light tokens live in `:root`; dark overrides
  live in `html[data-theme="dark"]`. Answer and feedback colors are derived
  from `--cp-success` and `--cp-danger` with `color-mix()`.
- `?scoutTheme=light` and `?scoutTheme=dark` override the OS preference.
  Without an override, the app follows `prefers-color-scheme`, including changes
  while open. Keep the early theme script and the later media listener aligned.
- Use `$()` for element lookup by ID. Dynamic answer text and explanations use
  `textContent`; answer buttons and result markers are built with DOM APIs and
  replaced with `replaceChildren()`.
- Progress counts answered questions, not the displayed question index. Each
  correct answer earns one point, and each question can be answered only once.
  Result tiers are 10, 7-9, 4-6, and 0-3 correct answers.
- The quiz assumes ten questions with four answers each. If changing that
  shape, update the introductory count, status text, progress ARIA maximum,
  final-score denominator, completion text, percentage calculation, A-D
  labels, and 1-4 shortcuts together.
- Preserve native answer buttons, disabled states, feedback live-region
  announcements, accessible answer/result labels, and `hidden` visibility.
  After answering, focus moves to Next; after advancement or restart, to the
  question heading; after completion, to the result heading. These headings
  use `tabindex="-1"` for programmatic focus.
- Answer shortcuts ignore Alt/Ctrl/Meta modifiers and repeated keydown events,
  and cannot submit after an answer is locked or results are displayed.
- Preserve the small-screen layout at 480px and the
  `prefers-reduced-motion` rule that disables animations and transitions.
