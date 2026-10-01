# Create review sessions from CI

Source: https://lyba.io/docs/rx/ci

`npx @lyba/cli session create` must run after the preview deployment exists. It reads the agency CI key from `LYBA_API_KEY` and auto-detects the preview URL, commit SHA, branch, provider, repo (`owner/repo`) and PR number from Vercel, Netlify, Cloudflare Pages and GitHub Actions variables. When a value can't be detected, pass it: `--preview-url`, `--sha`, `--ref`, `--provider`, `--pr`, `--project owner/repo`.

Pass GitHub event values to the shell through `env:`, never by `${{ }}` interpolation inside `run:` — branch names can contain shell metacharacters.

## Vercel (GitHub Actions on `deployment_status`)

Vercel's GitHub integration reports each preview as a deployment. Create the session when it succeeds:

```yaml
name: Lyba review session
on:
  deployment_status:

permissions:
  contents: read

jobs:
  session:
    if: >-
      github.event.deployment_status.state == 'success' &&
      github.event.deployment.creator.login == 'vercel[bot]' &&
      github.event.deployment.environment != 'Production'
    runs-on: ubuntu-latest
    steps:
      - name: Create Lyba review session
        id: lyba
        env:
          LYBA_API_KEY: ${{ secrets.LYBA_API_KEY }}
          PREVIEW_URL: ${{ github.event.deployment_status.environment_url }}
          SHA: ${{ github.event.deployment.sha }}
          REPO: ${{ github.repository }}
        run: npx @lyba/cli session create --preview-url "$PREVIEW_URL" --sha "$SHA" --project "$REPO" --provider vercel
```

Vercel sets the deployment's `ref` to the commit SHA, not the branch. If you want sessions linked by branch or PR, look the branch up first (`gh api repos/$REPO/commits/$SHA/pulls`) and pass `--ref` / `--pr`.

## GitHub Actions (your own deploy step)

```yaml
- name: Create Lyba review session
  id: lyba
  env:
    LYBA_API_KEY: ${{ secrets.LYBA_API_KEY }}
    PREVIEW_URL: ${{ steps.deploy.outputs.url }}
  run: npx @lyba/cli session create --preview-url "$PREVIEW_URL"

- name: Comment the review link on the PR
  if: steps.lyba.outputs.review-url
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    PR: ${{ github.event.number }}
    REVIEW_URL: ${{ steps.lyba.outputs.review-url }}
  run: gh pr comment "$PR" --body "Review this preview in Lyba: $REVIEW_URL"
```

The step sets `review-url` and `session-id` outputs. Replace `steps.deploy.outputs.url` with whatever your deploy step outputs.

## Netlify / Cloudflare Pages

Run the CLI in a step that runs after the deploy. It reads `DEPLOY_PRIME_URL`, `COMMIT_REF` and `BRANCH` on Netlify, and `CF_PAGES_URL`, `CF_PAGES_COMMIT_SHA` and `CF_PAGES_BRANCH` on Cloudflare Pages.

## Reviewer emails

Pass `--client-emails "dana@client.com"` to have Lyba email the review invite. Otherwise share the printed review link yourself.

## Secret

The user adds the secret: copy the CI key from **Lyba → Settings → CI key** into the CI provider's secrets as `LYBA_API_KEY`. Never commit it or expose it to browser code.
