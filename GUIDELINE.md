# DEV GUIDELINES

## 1. PROCESS: Think → Data → Code

- **Document First:** Never write code without clear requirements. Must have a Flow or Wireframe first.
- **Database Centric:** Data is the heart. Design the Schema (table structure) correctly first, because fixing the DB is 10 times more painful than fixing Code.
- **Scaffolding First:** Build the skeleton, directory structure, and connections before adding the meat (detailed logic).
- **Pragmatic Testing:** Test smartly, not mechanically. Prioritize Unit Tests for important logic functions (calculations, data conversion). UI does not need to be tested too heavily.

## 2. PRINCIPLES: Simple & Clean

- **Vertical Slice Architecture:** Split code by **Feature** instead of by technical Layer. Keep features self-contained.
- **KISS (Keep It Simple, Stupid):** "Dumb" readable code > "Smart" brain-damaging code. The simplest solution is usually the best solution.
- **DRY (Don't Repeat Yourself):** If you copy-paste twice, split it into a function. Business logic should only exist in one place.
- **SOLID (Flexible):** Follow to keep code flexible, but forbid Over-engineering (creating 10 interfaces for 1 simple function).

## 3. MINDSET: Lean & Human

- **LEAN:** Eliminate waste. Only do what creates immediate value.
- **YAGNI (You Aren't Gonna Need It):** Forbid anticipatory coding. Don't worry about 2 years from now. Only code what is needed for today.
- **Code for Humans:** Code is for others to read. Name variables clearly, comment fully on complex logic.

## 4. NAMING CONVENTIONS

- **camelCase:** Javascript/Typescript, Java, JSON keys.
- **kebab-case:** HTML tags/attributes, CSS classes, URLs, Filenames.
- **PascalCase:** Class, Component, Interface/Type.
- **SCREAMING_SNAKE:** Constants, Config.
- **snake_case:** Database (Table/Column) and everything else (Backend variables/functions).

## Error Handling

- **Never fail silently:** Always handle errors gracefully.
- **Async:** Wrap async calls in try/catch (or Result pattern).
- **User Feedback:** On UI, always show Feedback (Toast, Alert) for user actions.
- **Logging:** Use specific Error types, not generic `Error`. Log clearly for debugging.

## Documentation

- **Docstrings:** Add JSDoc/Docstrings for complex functions explaining inputs, outputs, and edge cases.
- **Comments:** Only comment "WHY", not "WHAT". (e.g., "Using Set for O(1) lookup" instead of "Creating a Set").
