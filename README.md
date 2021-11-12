# update-helm-chart-values
A github action to update a Helm chart values file (or any other yaml file) in a given repository.

## Typical Usage
```
- name: Update helm chart to use new docker image
  uses: ebot7/update-helm-chart-values@master
  if: github.ref == 'refs/heads/master'
  with:
    repository: ebot7/core-helm-charts
    file_path: helm-charts/my-app*/values.yaml
    key: '.*.image.tag'
    value: ${{ steps.image_builder.outputs.image_tag }}
    token: ${{ secrets.HELM_CHART_UPDATE_TOKEN }}
```

## Options

- `repository`: The repository to update.
- `file_path`: the path to the file relative to the repository root. This can contain bash globbing patterns to update multiple files.
- `key`: the yaml path of the key to be updated inside of the file. See the [yq documentation](https://mikefarah.gitbook.io/yq/) for the syntax.
- `value`: the value to assign to the given key.
- `token`: a [Github personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with at least the `read:discussion`, `read:org` and `repo` permissions.

## How it works
- Checkout the target repository
- Use `yq` to modify the yaml file.
- Commit changes to a dedicated branch.
- Create a pull request.
- Merge the pull request if checks are passing, otherwise set it to [auto-merge](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request).
