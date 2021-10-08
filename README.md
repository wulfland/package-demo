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

6. Create a file `.npmrc` in the root of your repository with the following content:

```
@YOUR-USER:registry=https://npm.pkg.github.com
```

7. Commit and push the new files:

```
$ git add .github/workflows/release-package.yml .npmrc
$ git commit -m "Add workflow to pusblish package"
$ git push
```

8. Create a release. Clock on `Releases` under `Code` and click `Draft new Release`. Create a new tag and enter a title. If you create the release, the workflow will automatically run.

<img width="500" alt="003_create-release" src="https://user-images.githubusercontent.com/5276337/136526942-2b6ebb28-bbe8-46df-84b3-ba02f76cbeb1.png">

9. If the workflow has completed, you can find you package under `Code`.

<img width="500" alt="004_see-package" src="https://user-images.githubusercontent.com/5276337/136528558-b9283c83-4e9a-4320-b7b0-2bc7601174a6.png">

10. Click on the package to see the details:

<img width="500" alt="005_package-details" src="https://user-images.githubusercontent.com/5276337/136528589-a25eed4f-6381-4b6d-b4c7-8e28ebc05a08.png">

> **Note:**  
> If you want to create new releases, you have to update the version in `package.json`. Otherwise the build will fail.
