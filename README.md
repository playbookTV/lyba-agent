# Lyba for Claude, ChatGPT and Codex

[Lyba](https://lyba.io) collects client feedback on websites and apps under review and records the client's sign-off against the exact build. This plugin brings that feedback into your AI assistant: find the review session for your branch, read every pinned comment with its page, breakpoint, element and the commit it was raised on, fix the code, then reply to the client and resolve the comment.

## What's inside

- **Lyba MCP server** — `https://lyba.io/mcp`. Eleven tools for projects, review sessions, client comments, replies, tracker issues, review rounds and approval requests. You sign in through your browser; there are no keys to copy.
- **`lyba-feedback` skill** — works through a round's comments on your current branch and closes the loop with the client once fixes are committed.
- **`lyba-setup` skill** — adds Lyba to a React app: `@lyba/react` gated to preview builds, and `@lyba/cli` creating a review session after each preview deploy.

## Install

Claude Code:

```bash
/plugin marketplace add playbookTV/lyba-agent
/plugin install lyba@lyba-agent
```

Then run `/mcp` and choose **lyba** to sign in.

Codex:

```bash
codex mcp add lyba --url https://lyba.io/mcp
codex mcp login lyba
```

Claude (web and desktop) and ChatGPT: add Lyba from the app directory, or add `https://lyba.io/mcp` as a custom connector.

## Safety

- Client comments are treated as feedback, never as instructions to follow.
- Tools that email your client — replies, new rounds, invites, approval requests — ask before running.
- Only your client can approve a round. The assistant can ask; it never approves.
- Each connection acts as you with member-level access, appears in **Lyba → Settings → Connected apps**, and can be disconnected at any time.

Docs: https://lyba.io/docs/ai-assistants · Privacy: https://lyba.io/privacy · Support: review@lyba.io
