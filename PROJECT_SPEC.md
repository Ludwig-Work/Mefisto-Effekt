> [!CAUTION]
>
> Before continuing, please read through the rules, the list of projects and an example of a specification here:
>
> <https://github.com/se-tuebingen-epe/course/blob/main/projects/README.md>
>
> It's okay if this specification is a bit vague, I'll ask you more questions in the Feedback PR.
> However, do **not** start working on your project unless the specification is accepted!
>
> _By writing here, you acknowledge that you read and understood the rules in the link above._

---

# Mefisto

The game of Mastermind, except it is unfair:
The computer does not commit to a specific secret code beforehand.
Instead, no matter what the user guesses, the computer chooses a different combination,
for as long as possible, without violating its own previous answers.

## Must-have

Features that you 100% promise to be done

- Game of Mefisto playable in the Terminal
- User can input a combination and receive the answer, repeatedly, until the right combination is found.
- Colorful representation of guesses, and the computer's answer
- An AI solver, efficiently solving the game (from a given starting position)
- The computer always gives the answer that leaves the largest possible solution space
- Announcement that the user won the game

## Can-have

Features that might or might not be done

Here I mostly see some configurable settings, which I don't know yet how hard they will be:
- Switch "Mefisto" on or off, so you can also play regular Mastermind against the computer
  - In this case, you may also _lose_ the game if you take more than `n` (config'able) turns
- How many spots for the secret code
- How many different values for the secret code
- Can the values repeat or not

At first glance, these config options all seem easy, but I need to figure out if some make the search space explode,
or if they require vast differences in the search algorithm.

- Announcing statistics to the user about their guess:
  - if their entered guess was possibly the correct answer or not
  - by what percentage the search space was cut by this guess

## Will-not-have

Features that you definitely won't have

- GUI
- Sound
- Multiplayer (how would that even work?)
- Saving the game state / high score / whether you accepted all cookies across program calls

## Effects and Handlers

What effects and handlers are you planning to use and how?
(There should be some fun/interesting ones, the language is called _Effekt_ after all...)

I find it a bit hard to come up with exact effects before starting on the project... 😅

Currently I'm thinking of two main interfaces.

1. For the game input handling, with effects such as:
    - Making a Guess
    - Winning the Game
    - Losing the Game (if applicable)

2. For "Mefisto" vs. "Regular Mastermind":
    - Evaluating a guess (How many correct, how many in the wrong place?)
    - (More, if I find there are more differences that need to be handled separately between the two modes)

With their corresponding handlers:

1. "User" handler (via STDIN) and "AI" handler

2. "Mefisto" handler (being evil about the combination) and "Mastermind" handler (choosing one combination in advance).
   Probably also a "deterministic" handler (a specific combination is chosen every time) for debugging / testing.

These two interfaces should allow me to write a game logic that is agnostic of such special conditions.

A third interface might be:

3. Config
    - Which game is being played (regular Mastermind or Mefisto)
    - How many spots for the secret code
    - How many different values for the secret code
    - Can the values repeat or not

It could have different handlers for pulling the answers from:
- CLI arguments (with default values)
- Reading from a config file
- Asking the user sequentially (optionally writing the answers back to a config file)
- Use specific (default) values (for automated tests)

## FFI and Libraries

<!--
rule "Keep FFI surface minimal" applies:
  implement as much as possible in Effekt directly and keep FFI bindings small.
-->

What FFI/libraries will you need? What is their current status?
(This is so that we both can estimate how much time you'll have to spend on writing glue code.)

I don't think any FFI / libraries will be required for this? I may be wrong here, correct me please!
