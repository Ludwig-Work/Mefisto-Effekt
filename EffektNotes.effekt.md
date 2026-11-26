# Effekt Notes while creating the project

Currently I'm working on parsing the user input to be a valid guess.
For this I rely on `String.toInt` with return type `Int / fail`.
Two notes on this:
- Maybe this is just me, but I cannot understand why this would produce a `fail` instead of an `Exception`.
  I don't see any advantage in having both of these in the standard library at all. A `fail` seems just
  like a downgraded `Exception` without the possibility of attaching a `msg: String` to it, and without
  being able to use the various `on` handlers.
- In `partial.effekt`, there are several handlers for `fail`, including `attempt` and `onFail` (which do
  the same thing, just accept their arguments in different orders, which seems redundant?), `optionally`,
  `exhaustively` and more. I think functionality like my `retryUntilSuccess` might also be appropriate there:
  ```effekt
  def retryUntilSuccess[A] { code: => A / fail }: A = {
    try {
      code()
    } with fail {
      retryUntilSuccess { code }
    }
  }
  ```

For now, I put generic functions, some of which I could imagine as stdlib functions, into `nonstdlib.effekt`.



Something confusing I figured out today: If a handler takes a block that has effects which are *not* handled by the handler,
you cannot just pass a function's name in the block, but you need to call it explicitly with brackets.
Otherwise, you get the error that the non-handled effect is unexpected in this context.
I expect the non-handled effect to just bubble through, which is precisely the behavior you get when you put the brackets.

```
effect a(): Unit
effect b(): Unit

def handleA { program: => Unit / a }: Unit =
  try { program() } with a { () }

def useA(): Unit / a =
  do a()

def useAAndB(): Unit / { a, b } = {
  do a()
  do b()
}

def problemDemonstration(): Unit / b = {
  handleA { useA } // <-- perfectly valid without brackets
  handleA { useAAndB() } // <-- Here, the brackets are required, otherwise (unclear, at least to me) error!
}
```
