# Run Flow

## program: CommanderCommand
[main.tsx](https://github.com/dj707chen/not-clawd-code/blob/8a488eefc0bca641865f987d087ca7a73c16cc60/src/main.tsx#L904)
```typescript
  import { Command as CommanderCommand, Option } from '@commander-js/extra-typings';
  const program: CommanderCommand = new CommanderCommand()
  program.name('claude')

  // action_handler_start
  // [main.tsx](https://github.com/dj707chen/not-clawd-code/blob/8a488eefc0bca641865f987d087ca7a73c16cc60/src/main.tsx#L1075)
  program.action(async (prompt, options) => { ...
    ......
  }
  # [main.tsx](https://github.com/dj707chen/not-clawd-code/blob/8a488eefc0bca641865f987d087ca7a73c16cc60/src/main.tsx#L4542)
  return program
```

## Main loop
Q: where is main loop in this CLI program?

There are two distinct main loops depending on the mode:

1. Interactive mode (default) — launchRepl()

First call is around main.tsx:3203, with 7 total call sites covering different startup scenarios
(fresh session, --resume, --continue, remote session, etc.).

launchRepl() renders <App><REPL/></App> via Ink (React for terminals). 
The main loop here is React's render cycle — Ink keeps the terminal UI alive, re-rendering on state changes, 
and processing each user message as it's submitted.

2. Non-interactive (--print) mode — runHeadless()

Around main.tsx:2895:

## Repl

[replLauncher.tsx](https://github.com/dj707chen/not-clawd-code/blob/8a488eefc0bca641865f987d087ca7a73c16cc60/src/replLauncher.tsx#L12)

## renderAndRun


## This CLI app uses React, how can a CLI app use a HTML framework?
It uses React, but not for HTML — it uses Ink, a library that swaps React's DOM renderer for a terminal renderer.

Here's how it works:

Normal React:

Ink (this app):

Ink replaces ReactDOM with its own renderer that writes to stdout using ANSI escape codes (the control sequences terminals use for cursor movement, colors, clearing lines, etc.).

So instead of <div>, <span>, <p>, Ink provides terminal-native components:

. <Box> — layout container (uses Flexbox-like layout via the yoga-layout engine)
. <Text> — renders text, supports color, bold, italic
. <Newline>, <Spacer> — spacing primitives

You can see this in the repo — look at any component file using JSX and you'll find:

    import { Box, Text } from '../ink.js'

### Why use React for a CLI?

React's component model, state (useState), effects (useEffect), and context work identically. The benefit is being able to build complex, stateful terminal UIs (like the REPL screen with its message list, input box, status bar, etc.) using the same declarative patterns as web UI — without manually tracking which terminal lines to redraw.
