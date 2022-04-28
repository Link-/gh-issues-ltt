# gh-issues-ltt

> Extract action items from multiple issues and aggregate them into 1

## Demo

![gh-issues-ltt Demo GIF](./assets/gh-issues-ltt-demo.gif)

## Usage

### Pre-requisites

1. Create an empty **Issue** in the same repository that will be the container for action items
2. Create a **label** with any name to mark the aggregate issue. Example: `aggregate_issue`
3. Add the label create in step 2 to the **Issue** you just created
4. Add the label name to the workflow file on the repository you're setting up the action for
5. Create another **label** with any name to mark the issues you want to sync with the aggregate issue. Example: `sync_issue`

### Action Items Structure

In order for your action items list to be recognized and copied to the aggregate issue it should have the following format:

```markdown
...(whatever)

### (whatever) Action Items (whatever)
- [ ] What you want your action item body to be
- [ ] This is another action item example
- [ ] This is a 3rd expample with a reference to another issue #1
- [ ] This is a 4th example with a date: 20 October 2021
- [ ] Action item with a user mention @Link-

...(whatever)
```

**(whatever)** represents anything. It could be an introduction section or anything else markdown supported.

### Workflow setup

In your repository create the folders `.github/workflows` if they don't exist already. Inside `.github/workflows` create a new workflow file and name it whatever you like.

Copy and paste the workflow below to your workflow file.

**Make sure to:**

1. Use the latest version of `link-/gh-issues-ltt`
2. Update the label used to identify issue you want to sync `<SYNC_LABEL>` with a label name of choice
3. Update the **aggregateIssueLabel** `<IDENTIFYING_LABEL>` with the name of the identifying label you created in the pre-requisites
4. If you are using this action for repositories owned by an organization, make sure to specify the organization name in the `owner` input

```yaml
name: Synchronize Action Items
on:
  issues:
    types: [labeled]


jobs:
  Sync-Action-Items:
    runs-on: ubuntu-latest
    steps:
      - name: "Sync Ation Items"
        id: sync_action_items
        uses: link-/gh-issues-ltt@v0.1.1-beta
        if: ${{ contains(github.event.issue.labels.*.name, '<SYNC_LABEL>') }}
        with:
          owner: "${{ github.actor }}"
          repo: "${{ github.event.repository.name }}"
          issueNumber: "${{ github.event.issue.number }}"
          aggregateIssueLabel: "<IDENTIFYING_LABEL>"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Limitations

- If the issue title has been modified, it'll be assumed as a new issue and a new block will be created.
- The aggregate issue and the label will not be created automatically for you. If they don't exist the action will fail.
- Using an aggregate issue in another repository is not possible.

## Troubleshooting

### Error: FATAL: Could not sync aggregate issue: Cannot read property 'body' of undefined

This could be due to multiple problems. Revisit the `pre-requisites` and `workflow setup` sections.

## Changelog

- v0.1.5-beta
  - Support for repositories owned by organizations (public / private)
- v0.1.3-beta
  - Variable aggregate issue label
  - Workflow is triggered by adding a sync label instead of open issue open / edit

## License

- [MIT License](./LICENSE)
