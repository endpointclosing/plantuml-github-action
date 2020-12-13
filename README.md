# PlantUML GitHub Action

PlantUML Github Action - kept up to date with PlantUML releases.

## Usage

### Minimal

Action generating all `*.puml` diagrams as svg in `diagrams` folder on push:

```yaml
name: Generate PlantUML Diagrams
on:
  push:

jobs:
  plantuml:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v2

      - name: Generate SVG Diagrams
        uses: holowinski/plantuml-github-action@main
        with:
          args: -v -tsvg diagrams/*.puml
```

### Update changed diagrams

Action updating diagrams modified in last commit and pushing the change

```yaml
name: Generate PlantUML Diagrams
on:
  push:
    paths:
      - "**.puml"

jobs:
  plantuml:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v2

      - name: Get changed UML files
        id: getfile
        run: |
          echo "::set-output name=files::$(git diff --name-only ${{ github.sha }} | grep .puml | xargs)"

      - name: Generate SVG Diagrams
        uses: holowinski/plantuml-github-action@main
        with:
          args: -v -tsvg ${{steps.getfile.outputs.files}}

      - name: Push Local Changes
        uses: stefanzweifel/git-auto-commit-action@v4.1.2
        with:
          commit_message: "Generate SVG files for PlantUML diagrams"
          branch: ${{ github.head_ref }}
```
