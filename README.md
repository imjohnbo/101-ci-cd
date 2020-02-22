# Open Source 101: CI/CD

## Goal

Develop CI/CD workflows using GitHub Actions.

## Steps

#### 1. Using what you learned from the [previous section](https://github.com/imjohnbo/101-first-workflow), use GitHub Actions to build, test, and security audit this app across the LTS and Current version of Node.js upon every pull request.

<details><summary>Hints</summary>

1. Uh oh, are the tests broken? :smile:

1. `npm` can do quite a bit, including auditing software for known vulnerabilities.

1. Explore [workflow](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow) `strategy`.

</details>

<details><summary>Solution</summary>

1. Start with the "Node.js CI" [template workflows](https://github.com/actions/starter-workflows/blob/master/ci/node.js.yml), target the right versions of node, add `npm audit`, and trigger the workflow on the correct event:

```
name: Node.js CI

on: [pull_request]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [12.x, 13.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm audit
    - run: npm install
    - run: npm run build --if-present
    - run: npm test
      env:
        CI: true

```

1. Fix the broken test.

</details>

#### 2. On a push to the master branch, publish this library to [GitHub Packages](https://github.com/features/packages).

<details><summary>Hints</summary>

1. Is there another starter template workflow that can help?

</details>

<details><summary>Solution</summary>

```
name: Publish Node.js Package

on:
  push:
    branches: master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: 12
      - run: npm ci
      - run: npm test

  publish-package:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: 12
          registry-url: https://npm.pkg.github.com/
          scope: '@your-github-username'
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}

```

</details>