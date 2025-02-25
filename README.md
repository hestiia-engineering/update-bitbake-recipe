# Update BitBake Recipe GitHub Action

**Update BitBake Recipe** is a composite GitHub Action that automatically updates a BitBake recipe in a Yocto meta repository based on a Rust project. The action extracts package information from your `Cargo.toml` using `cargo read-manifest` and `jq`, generates a BitBake file with `cargo bitbake`, and creates a pull request with the updated recipe.

## Usage

```yaml
name: Update BitBake Recipe

on:
  release:
    types: [published]

jobs:
  update_recipe:
    runs-on: ubuntu-latest
    steps:
      - name: Update BitBake Recipe
        uses: hestiia-engineering/update-bitbake-recipe@v1
        with:
          git_token: ${{ secrets.GIT_TOKEN }}
          yocto_repository: "hestiia-os"
          yocto_branch: "develop"
          organization: "hestiia-engineering"
          recipe_path: "meta-myeko/meta-myeko-pro/recipes-myeko-pro"
```

## Inputs

| Input              | Description                                                                            | Required | Default               |
|--------------------|----------------------------------------------------------------------------------------|----------|-----------------------|
| `git_token`        | Git Token with write permissions to the Yocto meta repository.                         | Yes      |                       |
| `yocto_repository` | The URL (or repository name) of the Yocto meta repository.                             | Yes      |                       |
| `yocto_branch`     | The branch of the Yocto meta repository to update.                                     | Yes      |                       |
| `organization`     | The organization of the Yocto meta repository.                                         | Yes      | `hestiia-engineering` |
| `recipe_path`      | The path to the folder in the Yocto meta repository where the recipe is located.       | Yes      |                       |

## How It Works

This action performs the following steps:

1. **Checkout Application:**  
   Checks out the Rust application code.

2. **Setup Rust Toolchain:**  
   Configures the stable Rust toolchain.

3. **Extract Package Information:**  
   Extract the package name and version from the `Cargo.toml` file. Then it converts the package name (e.g. `resistive-core-regulator`) into a human-friendly title (e.g. `Resistive Core Regulator`).

4. **Generate BitBake File:**  
   Installs `cargo-bitbake` and generates the BitBake file.

5. **Checkout Yocto Meta Repository:**  
   Uses the provided `git_token` and repository details to check out the Yocto meta repository.

6. **Copy Generated BitBake File:**  
   Removes any existing BitBake recipe in the target directory and copies the newly generated file.

7. **Create Pull Request:**  
   Utilizes the [peter-evans/create-pull-request](https://github.com/peter-evans/create-pull-request) action to create a pull request with a commit message and title based on the package name and version.

## Requirements

- A valid `Cargo.toml` must exist at the root of your Rust repository.
- The Yocto meta repository must be accessible using the provided `git_token`.
- `jq` must be available.
