# Sauce intake: instructions for the coding agent

You are running inside a SaaS founder's project folder. Follow every part below in order.
Do not skip ahead. Do not improvise steps that are not written here.

## 1. Role and the one rule

You are collecting information about this product for a content team (Sauce). You change nothing.

Allowed writes: only inside a new folder `./sauce-intake/` at the project root. Create it first, empty.

Two things are also allowed, because they are reads in effect: running the project's own dev script
for the screenshots (part 5), which may write into its own ignored cache folders such as
`node_modules/.vite/`, `.next/` or `.cache/`, and nothing else; and the one Playwright download of
part 5 into the npm cache, after the founder said yes.

Forbidden, with no exception:

- editing, creating, deleting, renaming or moving any file or folder outside `./sauce-intake/`;
- any install, build, migration, lint fix, formatter or code generator (`npm install`, `pnpm i`,
  `yarn`, `pip install`, `prisma migrate`, `eslint --fix`, `prettier --write` and the like);
- `git add`, `git commit`, `git push`, `git stash`, `git checkout`, `git reset`, `git restore`,
  or any other git command that writes;
- writing to `.gitignore` or to any other config file;
- sending anything to any network address yourself (no upload, no POST, no webhook, no API call).
  What the app's own pages load when a browser opens them (fonts, analytics) is the app's doing,
  not yours, and is fine. Never submit a form on those pages;
- opening or reading `.env*` files, keys, tokens, credentials, or any database. Checking that an
  env file exists by name (`ls`) is fine; opening it is not.

If a step needs any of these, skip that step and write in `REPORT.md` which step you skipped and why.

## 2. Proof of no changes

Right after creating the empty folder, run these two commands and save their exact output to
`sauce-intake/PROOF-no-changes.txt` under the heading `BEFORE`:

```
git status --porcelain
git stash list
```

At the very end (part 7) run them again and append the output under the heading `AFTER`.
BEFORE already holds the line `?? sauce-intake/`, because the folder exists. AFTER must be identical
to BEFORE, line for line. If anything differs, say so plainly in `REPORT.md` and in the chat.

If this folder is not a git repository, write `Not a git repository, proof skipped.` in the file
and continue.

## 3. Read the repo (read-only)

Read, do not run. Look for:

- `README*` and any docs folder: what the product is and who it is for.
- Landing or marketing pages, the pricing page, the onboarding screens.
- i18n or copy files (`locales/`, `messages/`, `*.json` with UI strings): the product's own words.
- `package.json` (or the matching manifest): the name and the scripts. Do not run the scripts here.
- The UI theme for colours and fonts: `tailwind.config.*`, CSS variables in global CSS files,
  theme files, design tokens. Record each brand colour as hex and the role it plays (primary,
  accent, background, text).
- `public/`, `assets/`, `static/` and similar folders for logo files (svg, png, ico).
- The routes or screens list (`app/`, `pages/`, `routes/`, the router file): the main screens.
- Feature names and whether each one is wired up, half built, or only planned.

Keep a running list of every fact with the file path it came from. Every fact you later write into
`customer.json` must have a matching entry in `sources`.

## 4. Interview the founder

Ask in the chat. At most 9 questions in total. Ask only for what the code could not tell you.
Ask one question at a time and wait for the answer. Short answers are fine. Use these exact
questions (the app's own wording), dropping any the code already answered:

1. Who is the product for? (Their role, situation, and the moment they realize they need help.)
2. What problem keeps coming back? (Describe a frustrating moment in their own words.)
3. What do they do without you? (Competitors, spreadsheets, manual work, or simply living with the problem.)
4. What evidence can we use? (Verified results, approved testimonials, demos, or product facts. "No proof yet" is a useful answer.)
5. What is the next step for an interested person? (Try the product, book a demo, join a waitlist, or visit a specific page.)
6. How should your brand sound? (Examples help: direct and practical, friendly and witty, thoughtful and expert.)
7. Anything we should know or avoid? (Brand colors, words to avoid, claims you cannot make, or sensitive topics.)
8. What should your content do first: reach new people, become the helpful one, or turn interest into action?

9. Which features work today, and what is not ready yet?

Always ask 8 and 9. Drop any of 1 to 7 the code already answered in full. Never ask more than 9.
Questions 1 to 7 are the app's own onboarding wording; question 8 is its three purpose choices.

## 5. Screenshots

The screens to capture, 5 to 8 in all: home or login, the main screen, the one screen where the
product's value is seen, settings or pricing, the empty state, and one to three more that show a
feature from part 3. If a cookie or consent banner covers the page, say so in `REPORT.md`.

Climb this ladder and stop at the first rung that works. A screenshot must end up as a file
inside `sauce-intake/screenshots/`; a picture you only see in your own browser tool does not count.

a. Get the app running locally without changing anything: the local code is what the founder is
   building, the public site may be older. If a local server is already running, use it. Else,
   only if `node_modules` (or the equivalent) exists, and, when the project has a `.env.example`,
   a matching `.env` or `.env.local` exists too (checked by name only), start it with its existing
   dev script in the background. You may add a port flag to avoid a clash. Do not wait for the
   log, which may stay empty: request the page with `curl` every few seconds until it answers.
   Never install anything and never create an env file. If it cannot run this way, use the public
   URL only if the repo itself names one (README, package.json, a deploy config), else go to rung c.

b. Capture each screen with Playwright driving the Chrome that is already on this computer. Ask
   the founder once before the first run, then:
   `npx --yes playwright@latest screenshot --channel chrome --full-page --viewport-size "1440,900" <url> sauce-intake/screenshots/<file>`
   This downloads only the small Playwright package into the npm cache, not into the repo, and
   needs no browser download. If there is no Chrome, try `--channel msedge`. If the command asks
   to install browsers or anything else, do not install: go to rung c.

   Screens behind a login: never ask for, read or type a password. Instead open a browser window
   for the founder and let him log in himself, once:
   `npx --yes playwright@latest open --channel chrome --save-storage "$TMPDIR/sauce-intake-auth.json" <login url>`
   Tell him: log in with a demo or test account if there is one, then close the window. The file
   saves his logged-in session, like a browser that stays signed in; it lives outside the project
   and is never copied into `sauce-intake/`. Then capture every inner screen with the same
   screenshot command plus `--load-storage "$TMPDIR/sauce-intake-auth.json"`. If he does not want
   to log in, those screens go to rung c.

c. Write `sauce-intake/SCREENSHOTS-TODO.md` with the screens listed at the top of this part, one
   line each, leaving out any screen the app does not have. Tell the founder in the chat to capture them and drop them into
   `sauce-intake/screenshots/`.

When you are done, stop any dev server you started and delete `$TMPDIR/sauce-intake-auth.json` if
it exists. Check that no file named `*auth*` sits inside `sauce-intake/`.

Rules for every screenshot:

- Demo or seed data only. If a screen shows real people's names, emails, phone numbers or money,
  do not capture it. Write in `REPORT.md` which screen you skipped and why.
- Name the files `01-home.png`, `02-<screen>.png`, `03-<screen>.png` and so on, in lower case,
  with words joined by hyphens.
- Capture the screens listed at the top of this part, 5 to 8 in all.

## 6. Write the output folder

Write exactly this inside `./sauce-intake/`:

```
sauce-intake/
  PROOF-no-changes.txt
  customer.json
  REPORT.md
  brand/          copies of the logo files found, untouched
  screenshots/    the captured screens
  SCREENSHOTS-TODO.md   only if rung c was used
```

### customer.json

English only. Keep the 13 top-level fields with these exact names. Write in plain sentences.
`purpose` is one of `reach`, `value`, `conversion`, taken from the founder's answer to question 8;
never leave it empty and never guess it from the page. `website` is the public `https://` address or
an empty string when there is no public site yet. Required: `name`, `whatItDoes`, `audience`,
`problem`, `differentiation`. If a field is unknown, write an empty string and list it under open questions
in `REPORT.md`. Never invent a fact.

Every fact in `proof` carries its source in the same sentence: a file path, a URL, or
"the founder said, <YYYY-MM-DD>". If there is no proof, write "No proof yet.", and you may follow it
with sourced product facts. In `sources`, a founder's answer is written the same way:
`"where": "the founder said, <YYYY-MM-DD>"`.

`feature.status` is one of `works`, `partial`, `planned`. A screen that is only a design mock-up,
not wired to anything, is `planned`. `feature.screenshot` is the file name in
`screenshots/` or an empty string. `customer.brandFile` is the main logo's path inside `brand/`, or
an empty string when the repo holds no logo file (a wordmark drawn in CSS is not a file; say so in
`REPORT.md` and leave `brand/` empty): the mark or wordmark the product
shows in its header or as its favicon. A mascot picture or a social share image is not the main
logo; put such files in `customer.logos` if the content may need them.

Use this exact shape:

```json
{
  "name": "",
  "website": "",
  "whatItDoes": "",
  "audience": "",
  "problem": "",
  "differentiation": "",
  "alternatives": "",
  "proof": "",
  "pricing": "",
  "desiredAction": "",
  "voice": "",
  "constraints": "",
  "purpose": "",
  "customer": {
    "colours": [
      { "hex": "#000000", "name": "", "role": "" }
    ],
    "brandFile": "brand/<logo file>",
    "logos": [],
    "fonts": []
  },
  "product": {
    "features": [
      { "name": "", "whatItDoes": "", "status": "works", "screenshot": "" }
    ],
    "mainFlow": "",
    "screens": []
  },
  "funnel": {
    "whatTheAudienceSearches": [],
    "objections": [],
    "offer": "",
    "freeThing": ""
  },
  "sources": [
    { "fact": "", "where": "" }
  ]
}
```

Notes on the blocks:

- `customer.logos`: other logos the content may show (integrations, partners), as file paths in
  `brand/`, at most 5. Empty if none.
- `customer.fonts`: font family names found in the theme.
- `product.mainFlow`: the path a new user walks from sign-up to the first result, in one or two
  sentences.
- `product.screens`: the screen names from the routes list.
- `funnel.whatTheAudienceSearches`: what the audience types into Google or asks a chat model
  before they know the product.
- `funnel.objections`: why someone would hesitate to try it.
- `funnel.offer`: the offer as stated on the site or by the founder.
- `funnel.freeThing`: anything free the product gives (a trial, a template, a tool), or empty.

### brand/

Copy every logo file you found (svg, png, ico) into `sauce-intake/brand/`, byte for byte. Do not
convert, resize or edit them. Copying is a read of the original and a write inside
`sauce-intake/`, which is allowed.

### REPORT.md

Short, for a person to read in a few minutes, in this order:

1. What the product is, in three lines.
2. The brand colours as hex, one per line with name and role.
3. What was found in the code, with the file path for each fact.
4. What the founder said.
5. Open questions: what could not be found.
6. The screenshot list, and any screen skipped and why.
7. Any step skipped because of the rule in part 1, and why.

## 7. Finish line

1. Scan `sauce-intake/` for anything that must not leave this computer: strings that look like
   keys or tokens (`sk-`, `pk_`, `ghp_`, `eyJ`, `AKIA`, long random strings), passwords, real
   email addresses, phone numbers, and any file named like `*auth*` or `.env*`. Remove every hit
   and note it in `REPORT.md`. Only the founder's own public contact details may stay.
2. Run the two proof commands again and append them under `AFTER` (part 2).
3. Print the folder tree of `sauce-intake/`.
4. Print the BEFORE and AFTER proof and say whether they are identical.
5. End with this one sentence to the founder:
   "Zip the `sauce-intake/` folder and send it to Sauce, then delete the folder."
