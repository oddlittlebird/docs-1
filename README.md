Welcome to the Macrometa API Documentation. 

If you would like to suggest an edit or addition, please create an issue or fork this repo and submit a pull request.

## Deployed Site

https://macrometa.dev

## How to build docs

To get all relevant packages:

```bash
pip3 install -r requirements.txt
```

To build docs:

```bash
mkdocs build

or

python3 -m mkdocs build
```

To serve docs:

```bash
mkdocs serve

or

python3 -m mkdocs serve
```

After that connect to http://localhost:8080

> Note: Any changes to the docs once saved will get automatically built and visible in browser gui

To deploy docs:

```bash
mkdocs gh-deploy

or

python3 -m mkdocs gh-deploy
```

That's it! Behind the scenes, MkDocs will build your docs and use the `ghp-import` tool to commit them to the `gh-pages` branch and push the `gh-pages` branch to GitHub.

Use `mkdocs gh-deploy --help` to get a full list of options available for the `gh-deploy` command.

> Warning: You should never edit files in your pages repository by hand if you're using the gh-deploy command because you will lose your work the next time you run the command.

## Advanced

To build theme,

```bash
    npm install
    npm run build
```
