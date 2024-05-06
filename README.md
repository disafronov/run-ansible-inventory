# run-ansible-inventory

GitHub Action to run SOPS-enabled Ansible with inventory directory.

Example:

```yaml
name: Ansible

"on":
  pull_request:
    branches: [main]
    paths:
      - "inventory/**/*"
  push:
    branches: [main]
    paths:
      - "inventory/**/*"

jobs:

  find-out-changed-inventories:
    name: Find out changed inventories
    runs-on: ubuntu-latest
    outputs:
      directories: ${{ steps.changed-files.outputs.directories }}
    steps:
      - name: Get Ansible changed inventories directories
        uses: dmitriysafronov/get-ansible-changed-inventories@v1.0.2
        id: changed-files

  run-ansible-on-changed-inventories:
    name: Ansible
    runs-on: ubuntu-latest
    strategy:
      matrix:
        directory: ${{ fromJson(needs.find-out-changed-inventories.outputs.directories) }}
    needs:
      - find-out-changed-inventories
    steps:
      - name: Run SOPS-enabled Ansible with inventory directory
        uses: dmitriysafronov/run-ansible-inventory@v1.0.2
        with:
          directory: ${{ matrix.directory }}
          ssh-private-key: ${{ secrets.ANSIBLE_SSH_PRIVATE_KEY }}
          sops-age-private-key: ${{ secrets.ANSIBLE_SOPS_AGE_KEY }}
```
