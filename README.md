# ArtCraft release sync

Checks the latest stable ArtCraft release for seven `allsagetech` forks every hour, at minute 17. GitHub may delay scheduled runs during busy periods.

Covered forks: `wordcraft`, `gridcraft`, `deckcraft`, `cadcraft`, `pdfcraft`, `photocraft`, and `filmcraft`. Each maps to the repository with the same name under `storytold`.

The workflow preserves the upstream release tag object, copies the original release notes with an upstream link, and transfers all published downloads. Every download and uploaded file must match the upstream size and SHA-256 digest. New releases stay in draft until their complete asset set is verified. Repeated checks reuse matching files, and an interrupted draft can resume on the next run. Conflicting existing tags or files stop that fork's job for review. Only the current latest stable release is considered on each check.

## Finish setup

1. While signed in as `allsagetech`, create a fine-grained GitHub personal access token with resource owner `allsagetech`.
2. Choose **Only select repositories**, selecting the seven forks listed above.
3. Grant **Contents: Read and write** and **Workflows: Read and write**. GitHub needs workflow permission to preserve release tags whose commits include workflow changes. Choose an expiration you can renew before it lapses.
4. In this controller repository, open **Settings → Secrets and variables → Actions → New repository secret**. Save the token as **`ARTCRAFT_MIRROR_TOKEN`**. Enter the token directly into GitHub's secret field, never into a file or chat.
5. Open **Actions → Sync newest ArtCraft releases → Run workflow**. Leave the validation checkbox unchecked for normal copying. The seven mirror jobs should succeed.

The controller's built-in `GITHUB_TOKEN` cannot write across the seven forks, so the scoped secret is required for release copying. The token is passed only to the mirroring step. The workflow uses Python's standard library, downloads no third-party actions, and never executes upstream software.

## Operation

- **Validate:** manually run the workflow with **Validate all seven mirrors without changing releases** checked. This works before adding the secret.
- **Check status:** inspect the latest Actions run and its seven job summaries. GitHub's normal failed-workflow notifications apply according to your account notification settings.
- **Pause:** use the workflow's menu in Actions and choose **Disable workflow**.
- **Renew access:** replace `ARTCRAFT_MIRROR_TOKEN` before its token expires or if access is revoked.
- **Keep scheduling active:** a separate job writes a small status file in this helper repository at most once every 28 days, preventing GitHub's public-repository inactivity cutoff from silently disabling the schedule.

The schedule runs on standard GitHub-hosted Linux runners in this public helper repository. The fork source branches are managed independently of release mirroring. Future mirrored releases appear on each fork's normal Releases page.

GitHub references: [scheduled workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule), [release API permissions](https://docs.github.com/en/rest/releases/releases#create-a-release), [fine-grained tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).
