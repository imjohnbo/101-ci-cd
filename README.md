# Open Source 101: CI/CD

## 🎯 Goal

Develop [Continuous Integration / Continuous Delivery workflows](https://help.github.com/en/actions/building-and-testing-code-with-continuous-integration/about-continuous-integration) using GitHub Actions.

## 💻 Steps

Discuss CI/CD using [Visualize Git](https://git-school.github.io/visualizing-git/).

Using what you learned from the [previous section](https://github.com/imjohnbo/101-first-workflow)...

#### 1. CI: Build, test, and audit this app across the LTS and Current version of Node.js upon every push.

<details><summary>Hints</summary>

1. Uh oh, are the tests broken? :smile:

2. `npm` can do quite a bit, including auditing software for known vulnerabilities.

3. Explore [workflow](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow) `strategy`.

</details>

<details><summary>Solution</summary>

1. Start with the "Node.js CI" [template workflow](https://github.com/actions/starter-workflows/blob/master/ci/node.js.yml), target the right versions of node, add `npm audit`:

```
name: Continuous Integration

on: [push]

jobs:
  ci:

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
    - run: npm install
    - run: npm audit
    - run: npm run build --if-present
    - run: npm test
      env:
        CI: true

```

2. `npm audit fix`
2. Fix the broken test.

</details>

#### 2. CD: Publish this library to [GitHub Packages](https://github.com/features/packages) upon push to the `master` branch.

<details><summary>Hints</summary>

1. Is there another starter template workflow that can help?

2. Can you combine CI and CD into one workflow?

</details>

<details><summary>Solution</summary>

```
name: Continuous Integration

on: [push]

jobs:
  ci:

    runs-on: [ubuntu-latest]

    strategy:
      matrix:
        node-version: [12.x, 13.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm install
    - run: npm audit
    - run: npm run build --if-present
    - run: npm test
      env:
        CI: true
  cd:
    
    needs: ci
    if: github.ref == 'refs/heads/master'
    runs-on: [ubuntu-latest]
    
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v1
        with:
          node-version: 12
          registry-url: https://npm.pkg.github.com/
          scope: '@gorchlevlok'
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}
    
```

</details>

## 🏅 Extra credit

* Hook one of your own projects up to GitHub Actions CI/CD