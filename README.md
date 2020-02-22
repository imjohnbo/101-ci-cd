# Open Source 101: CI/CD

## Purpose

Develop CI/CD workflows using GitHub Actions.

## Steps

1. Using what you learned from the [previous section](https://github.com/imjohnbo/101-first-workflow), use GitHub Actions to build, test, and security audit this app across the LTS and Current version of Node.js.

<details><summary>Hints</summary>

1. Uh oh, are the tests broken? :-)

1. `npm` can do quite a bit, including auditing software for known vulnerabilities.

1. Explore [workflow](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow) `strategy`.

</details>

<details><summary>Solution</summary>

1. Start with the "Node.js CI" [template workflows](https://github.com/actions/starter-workflows/blob/master/ci/node.js.yml) and add `npm audit`.

```
name: Node.js CI

on: [push]

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

2. Deploy this app to GitHub Packages.

<details><summary>Hints</summary></details>

<details><summary>Solution</summary></details>