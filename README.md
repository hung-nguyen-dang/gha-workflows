# gha-workflows

Reusable GitHub Actions workflows.

## `notify-slack.yml`

Sends a single Slack message via an incoming webhook. That is all it does.

### Usage

```yaml
jobs:
  deploy:
    # ... your build/deploy job ...

  notify:
    needs: deploy
    if: always()
    uses: hung-nguyen-dang/gha-workflows/.github/workflows/notify-slack.yml@v1
    with:
      status: ${{ needs.deploy.result }}
      title: deployment
      webhook-url: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Inputs

| Name          | Required | Default     | Description |
|---------------|----------|-------------|-------------|
| `status`      | yes      | —           | Result to report: `success`, `failure`, `cancelled`, `skipped`. Pass `${{ needs.<job>.result }}`. |
| `webhook-url` | yes      | —           | Slack incoming-webhook URL. Use a secret. |
| `app-name`    | no       | `owner/repo`| Name shown in the message. |
| `title`       | no       | `deployment`| Headline word, e.g. `release`, `smoke test`. |
| `message`     | no       | `''`        | Extra mrkdwn appended on its own line. |
| `show-commit` | no       | `true`      | Include branch + short SHA link + commit subject. |
| `run-url`     | no       | this run    | URL the **View run** button opens. |

### Message example

```
✅ neil-frontend deployment success
Branch: main | Commit: 9a045f0
> feature: added slack notification
```

### Notes

- The workflow runs in the **caller's** context: `github.token`, `github.sha`,
  and `github.repository` all refer to the calling repo.
- `show-commit` uses `gh api` to fetch the commit subject, which needs
  `contents: read` on the token (granted by the reusable workflow).
- Pin a tag (`@v1`) or a commit SHA, not `@main`.
