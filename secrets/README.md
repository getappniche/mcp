# `secrets/` — local operational credentials

**This README is the only file in here that is tracked by git.** Everything else
is gitignored (`secrets/*` + `!secrets/README.md` in the root `.gitignore`) and
lives only on this machine. Never `git add -f` anything from this folder.

Why it exists: a few credentials aren't application config — they're keys a
human or agent needs *occasionally*, from a laptop, to do an infrastructure
task. Keeping them next to the project beats losing them in `~/Downloads`.

## What's in here

| File | What it is | Losing it means |
|---|---|---|
| `getappniche-mcp-registry-key.pem` | Ed25519 **private** key that signs publishes to the official MCP Registry for the `com.getappniche/*` namespace. Its public half is served over **HTTP** (not DNS) at `https://getappniche.com/.well-known/mcp-registry-auth` — there is deliberately no apex TXT record on this domain. Login: `mcp-publisher login http --domain getappniche.com --private-key <hex>`. | Recoverable, but manual: generate a new keypair, **replace** the contents of the `.well-known/mcp-registry-auth` file with the new public half (a stale proof is tried first and fails with a generic signature error), then `mcp-publisher login http` again. |

## ⚠️ The `.well-known/mcp-registry-auth` file is permanent

```
v=MCPv1; k=ed25519; p=<public key>
```

Served at `https://getappniche.com/.well-known/mcp-registry-auth`, it is
**ongoing proof** that we own the `com.getappniche/*` MCP namespace — not a
one-time challenge that can be cleaned up after verification. Removing it means
nobody can publish new versions under our name. Leave it in place.

## Adding something here

Drop the file in and add a row to the table above — the row is the point, since
a bare key file with no context is nearly as bad as a lost one. Say what it
authenticates against, what uses it, and what to do if it's lost or leaked.
