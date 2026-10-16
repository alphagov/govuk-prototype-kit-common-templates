# Releasing a new version

Releases are automated using GitHub Actions workflows, in the same way as the GOV.UK Prototype Kit and GOV.UK Frontend repos:

1. Run the "[Release] Build release for review" workflow to update the changelog and version numbers and open a pull request.
2. Once that's merged, run the "[Release] Publish package to npm" workflow.
3. Run the "[Release] Publish to GitHub releases" workflow.

All releases and pre-releases are public, but we distinguish between:

- `internal` pre-releases aimed at internal users
- `beta` pre-releases aimed at external users

See the [documentation on support branches](https://team-playbook.design-system.service.gov.uk/guides/version-control/support-branches/#support-branches) if you need to:

- publish a new release of previous major versions of the common templates
- release fixes that cannot wait until the next planned release of the common templates without including other unreleased changes on the `main` branch

Developers must pair on releases, so do not publish on your own. You should create a calendar event for the release so other team members can also be present.

## Build the release

1. Before running the "[Release] Build release for review" workflow, make sure the [changelog](../../CHANGELOG.md) is up to date with the latest release notes under the **Unreleased** heading - if it is not up to date, update it in a separate pull request before proceeding.

2. Decide on the release type by looking at the changelog entries:

   - when you fix a bug and your code stays backwards compatible, use `patch`
   - when you add functionality and your code stays backwards compatible, use `minor`
   - when you implement changes and your code becomes backwards incompatible, use `major`
   - for pre-releases, use `premajor`, `preminor`, `prepatch` or `prerelease` with a prerelease label, for example `beta`

3. Open the [**Actions** tab](https://github.com/alphagov/govuk-prototype-kit-common-templates/actions) on the `alphagov/govuk-prototype-kit-common-templates` repo.

4. Select the ["[Release] Build release for review" workflow](https://github.com/alphagov/govuk-prototype-kit-common-templates/actions/workflows/build-release.yml) and run the workflow:
   1. Set the **Use workflow from** select field to `main` or the `support/*` branch you're releasing from.
   2. Set the **Release Type** select field to the version change, for example `major`.
   3. For pre-releases, set the **Prerelease label** input field, for example `beta`.
   4. Select **Run workflow**.

   The workflow updates the version number in [package.json](../../package.json) and [package-lock.json](../../package-lock.json), adds a new version heading to the changelog, and opens a pull request with the release notes generated from the changelog.

5. Ask a second developer to review the pull request, checking that the version numbers and release notes are correct.

6. Once a reviewer approves the pull request, merge it.

## Publish a release to npm

1. Once you’ve merged the release pull request, open the **Actions** tab on the `alphagov/govuk-prototype-kit-common-templates` repo.

2. Select the ["[Release] Publish package to npm" workflow](https://github.com/alphagov/govuk-prototype-kit-common-templates/actions/workflows/publish-to-npm.yaml) and run the workflow:
   1. Set the **Use workflow from** select field to `main` or the `support/*` branch you're releasing from.
   2. Set the **Environment** select field to `production`.
   3. Select **Run workflow**.

   You can set the **Environment** select field to `test` to do a dry run - this packages the release and uploads it as a workflow artifact without publishing it to npm.

3. Ask a second developer to [review and approve the workflow run](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/review-deployments).

4. Verify the presence of the release and its tag on [npm](https://www.npmjs.com/package/@govuk-prototype-kit/common-templates?activeTab=versions) - refresh npm if you do not see the published release there once the Action is complete.

5. Compare the diff of the new package with the previous one:

   ```txt
   https://npmdiff.dev/@govuk-prototype-kit/common-templates/<PREVIOUS_VERSION_NUMBER>/<RELEASED_VERSION_NUMBER>
   ```

## Create a release on GitHub

1. Open the ["[Release] Publish to GitHub releases" workflow](https://github.com/alphagov/govuk-prototype-kit-common-templates/actions/workflows/publish-to-github.yaml) and run the workflow:
   1. Set the **Use workflow from** select field to `main` or the `support/*` branch you're releasing from.
   2. Set the **Environment** select field to `production`.
   3. Select **Run workflow**.

   This will generate a GitHub tag and release. You can set the **Environment** select field to `test` to do a dry run - this creates the tag locally and prints the release notes without pushing anything.

   If the workflow fails after creating a tag, or after creating a release, you'll need to manually delete these using [the GitHub tags UI](https://github.com/alphagov/govuk-prototype-kit-common-templates/tags) before running the workflow again.

2. [Verify the release](https://github.com/alphagov/govuk-prototype-kit-common-templates/releases) and check that its description looks correct.

If you're publishing a pre-release, make sure the pre-release label is set correctly on the release page.

## Let the community know about the release

Write a brief summary with highlights from the release then send it to the following slack channels:

- X-GOV #govuk-design-system
- X-GOV #prototype-kit
- GDS #govuk-design-system

Make sure to send a link to the 'create a new prototype' page rather than the GitHub release page: https://prototype-kit.service.gov.uk/docs/create-a-new-prototype.

## Update the sprint board

On the sprint board, move everything that's been released from the Ready for release column to the Done column.
