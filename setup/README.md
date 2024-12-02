<h1>
  <span class="headline">Unit Testing in JavaScript</span>
  <span class="subhead">Setup</span>
</h1>

Open your Terminal application and navigate to your `~/code/ga/lectures` directory:

```bash
cd ~/code/ga/lectures
```

## Starter Code

This lecture uses the [`Unit Testing in JavaScript Starter Code`](https://git.generalassemb.ly/modular-curriculum-all-courses/unit-testing-in-javascript-starter-code).

Navigate to [Unit Testing in JavaScript Starter Code](https://git.generalassemb.ly/modular-curriculum-all-courses/unit-testing-in-javascript-starter-code) and clone the repository to your machine:

```bash
git clone https://git.generalassemb.ly/modular-curriculum-all-courses/unit-testing-in-javascript-starter-code.git unit-testing-in-javascript
```

Note by adding the `unit-testing-in-javascript` argument we're cloning the specified repo into a directory called `unit-testing-in-javascript` on our machines.

Next, `cd` into the new directory:

```bash
cd unit-testing-in-javascript
```

Finally, remove the existing `.git` information from this template:

```bash
rm -rf .git
```

> 🚨 Removing the `.git` info is important as this is just a starter template provided by GA. You do not need the existing git history for this project.

## GitHub setup

To add this project to GitHub, initialize a new Git repository:

```bash
git init
git add .
git commit -m "init commit"
```

Make a new repository on [GitHub](https://github.com/) named `unit-testing-in-javascript`.

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/unit-testing-in-javascript.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.



## Install dependencies

Next, you will want to install all of the packages listed in `package.json`

```bash
npm i
```

Open the project's folder in your code editor:

```bash
code .
```

Now you're all set to start working on Unit Testing in JavaScript!
