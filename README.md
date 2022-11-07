This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `pages/index.js`. The page auto-updates as you edit the file.

[API routes](https://nextjs.org/docs/api-routes/introduction) can be accessed on [http://localhost:3000/api/hello](http://localhost:3000/api/hello). This endpoint can be edited in `pages/api/hello.js`.

The `pages/api` directory is mapped to `/api/*`. Files in this directory are treated as [API routes](https://nextjs.org/docs/api-routes/introduction) instead of React pages.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js/) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/deployment) for more details.

## Development

# Create Project

`yarn create next-app my-husky-project`

# Add husky

`yarn add husky`

# Add below code in package.json

```
"husky": {
    "hooks": {
      "applypatch-msg": "echo \"[Husky] applypatch-msg\"",
      "pre-applypatch": "echo \"[Husky] pre-applypatch\"",
      "post-applypatch": "echo \"[Husky] post-applypatch\"",
      "pre-commit": "echo \"[Husky] pre-commit\""
    }
  },
```

Restart app to make it work

## From Real Time Docs

Automatic (recommended)
husky-init is a one-time command to quickly initialize a project with husky.

npx husky-init && npm install # npm
npx husky-init && yarn # Yarn 1
yarn dlx husky-init --yarn2 && yarn # Yarn 2+
pnpm dlx husky-init && pnpm install # pnpm
It will setup husky, modify package.json and create a sample pre-commit hook that you can edit. By default, it will run npm test when you commit.

To add another hook use husky add.

For example:

npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
For Windows users, if you see the help message when running npx husky add ..., try node node_modules/husky/lib/bin add ... instead. This isn't an issue with husky code.

Manual
Install
Install husky
npm install husky --save-dev
Enable Git hooks
npx husky install
To automatically have Git hooks enabled after install, edit package.json
npm pkg set scripts.prepare="husky install"
You should have:

// package.json
{
"scripts": {
"prepare": "husky install"
}
}
Yarn 2+ doesn't support prepare lifecycle script, so husky needs to be installed differently (this doesn't apply to Yarn 1 though). See Yarn 2+ install.

Create a hook
To add a command to a hook or create a new one, use husky add <file> [cmd] (don't forget to run husky install before).

npx husky add .husky/pre-commit "npm test"
git add .husky/pre-commit
Try to make a commit

git commit -m "Keep calm and commit"
If npm test command fails, your commit will be automatically aborted.

Using Yarn to run commands? There's an issue on Windows with Git Bash, see Yarn on Windows.

For Windows users, if you see the help message when running npx husky add ..., try node node_modules/.bin/husky add ... instead. This isn't an issue with husky code and is fixed in recent versions of npm 8.

Uninstall
npm uninstall husky && git config --unset core.hooksPath
Yarn 2
Install
Install husky
yarn add husky --dev
yarn add pinst --dev # ONLY if your package is not private
Enable Git hooks
yarn husky install
To automatically have Git hooks enabled after install, edit package.json
// package.json
{
"private": true, // ← your package is private, you only need postinstall
"scripts": {
"postinstall": "husky install"
}
}
if your package is not private and you're publishing it on a registry like npmjs.com, you need to disable postinstall script using pinst. Otherwise, postinstall will run when someone installs your package and result in an error.

// package.json
{
"private": false, // ← your package is public
"scripts": {
"postinstall": "husky install",
"prepack": "pinst --disable",
"postpack": "pinst --enable"
}
}
Uninstall
Remove "postinstall": "husky install" from package.json and run:

yarn remove husky && git config --unset core.hooksPath
Recipes
Monorepo
It's recommended to add husky in root package.json. You can use tools like lerna and filters to only run scripts in packages that have been changed.

Custom directory
If you want to install husky in another directory, for example .config, you can pass it to install command. For example:

// package.json
{
"scripts": {
"prepare": "husky install .config/husky"
}
}
Another case you may be in is if your package.json file and .git directory are not at the same level. For example, project/.git and project/front/package.json.

By design, husky install must be run in the same directory as .git, but you can change directory during prepare script and pass a subdirectory:

// package.json
{
"scripts": {
"prepare": "cd .. && husky install front/.husky"
}
}
In your hooks, you'll also need to change directory:

# .husky/pre-commit

# ...

cd front
npm test
Bypass hooks
You can bypass pre-commit and commit-msg hooks using Git -n/--no-verify option:

git commit -m "yolo!" --no-verify
For Git commands that don't have a --no-verify option, you can use HUSKY environment variable:

HUSKY=0 git push # yolo!
Disable husky in CI/Docker/Prod
There's no right or wrong way to disable husky in CI/Docker/Prod context and is highly dependent on your use-case.

With npm
If you want to prevent husky from installing completely

npm ci --omit=dev --ignore-scripts
Alternatively, you can specifically disable prepare script with

npm set-script prepare ""
npm ci --omit=dev
With a custom script
You can create a custom JS script and conditionally require husky and install hooks.

"prepare": "node ./prepare.js"
// prepare.js
const isCi = process.env.CI !== undefined
if (!isCi) {
require('husky').install()
}
Or make prepare script fail silently if husky is not installed:

"prepare": "node -e \"try { require('husky').install() } catch (e) {if (e.code !== 'MODULE_NOT_FOUND') throw e}\""
With env variables
You can set HUSKY environment variable to 0 in your CI config file, to disable hooks installation.

Alternatively, most Continuous Integration Servers set a CI environment variable. You can use it in your hooks to detect if it's running in a CI.

# .husky/pre-commit

# ...

[ -n "$CI" ] && exit 0
With is-ci
You can also use is-ci in your prepare script to conditionally install husky

npm install is-ci --save-dev
// package.json
{
"scripts": {
"prepare": "is-ci || husky install"
}
}
Test hooks
If you want to test a hook, you can add exit 1 at the end of the script to abort git command.

# .husky/pre-commit

# ...

exit 1 # Commit will be aborted
Git-flow
If using git-flow you need to ensure your git-flow hooks directory is set to use Husky's (.husky by default).

git config gitflow.path.hooks .husky
Note:

If you are configuring git-flow after you have installed husky, the git-flow setup process will correctly suggest the .husky directory.
If you have set a custom directory for husky you need to specify that (ex. git config gitflow.path.hooks .config/husky)
To revert the git-flow hooks directory back to its default you need to reset the config to point to the default Git hooks directory.

git config gitflow.path.hooks .git/hooks
