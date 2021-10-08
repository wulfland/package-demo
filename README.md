# Creating and publishing an npm package

1. Create a new repository and select `Node` as your `.gitignore` template:

<img width="500" alt="001_create-repo" src="https://user-images.githubusercontent.com/5276337/136522864-04c1a503-d647-41f9-bd7e-1670170cdf2b.png">

2. Clone the repository to your local machine or open it in code spaces.

<img width="400" alt="002_clone or open" src="https://user-images.githubusercontent.com/5276337/136523686-adeb6542-79c5-41d7-8012-6823e52dfec3.png">

```
$ git clone <URL>
```

3. Create a file `index.js` and add some simple JavaScript code, for example:

```js
alert("Hello, World!");
```

3. If you have `npm` installed on your machine, run `npm init` and initialize your package using the wizard. Leave the defaults for the other values. 

```
$ npm init
...
package name: @YOUR-USER/YOUR-REPO
...
test command: exit 0
...
```

If you don't have npm installed, just clone this repository and copy the files `package.json` and `package-lock.json` to you local repo. Adjust the URLs and package name to include your username and repo.

4. Commit and push your files:

```
$ git add index.js package.json package-lock.json
$ git commit -m "Initialize package"
$ git push
```

5. Create a file `.github/workflows/release-package.yml` and copde the following content to it:

```YAML
name: Node.js Package

on:
  release:
    types: [created]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: 12
      - run: npm ci
      - run: npm test

  publish-gpr:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: 12
          registry-url: https://npm.pkg.github.com/
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}
```

