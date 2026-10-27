# The Discontinued Aisle

A fan-made board for voting on discontinued Trader Joe's products, with all-time and
30-day leaderboards and a complaint-letter kit for Trader Joe's feedback form.
Not affiliated with Trader Joe's.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole site |
| `firebase-config.js` | Your Firebase project's settings (you fill this in) |
| `firestore.rules` | Database security rules (paste into Firebase) |
| `images/` | Optional product photos (see Product pictures) |

Without a Firebase config the site still works, but votes are saved only in each
visitor's own browser.

## 1. Create the database (Firebase, free plan)

1. Go to https://console.firebase.google.com and click **Create a project**. Google Analytics is optional.
2. **Build → Firestore Database → Create database.** Pick a location near your visitors and start in **production mode**.
3. Open the **Rules** tab, replace everything with the contents of `firestore.rules`, and click **Publish**.
4. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable.**
   This gives each visitor an ID so they get one vote per product, with no sign-up.
5. **Project settings (gear icon) → General → Your apps → Web (`</>`).** Register an app
   (skip Firebase Hosting). Copy the `firebaseConfig` values into `firebase-config.js`.

## 2. Publish on GitHub Pages

1. Create a repository and upload `index.html`, `firebase-config.js` and `firestore.rules`.
2. **Settings → Pages → Build and deployment:** Source *Deploy from a branch*, branch `main`, folder `/ (root)`. Save.
3. Your site appears at `https://<your-username>.github.io/<repo-name>/` within a minute or two.
4. Back in Firebase: **Authentication → Settings → Authorized domains → Add domain** and add
   `<your-username>.github.io` (and your custom domain if you use one).

## Managing the board

- **Curated products** live in the `STARTER` list near the top of the script in `index.html`. Edit and commit.
- **Fan-reported products** are in the `submissions` collection, one doc per visitor. Delete or edit them in the Firestore console.
- **Hide a fan product without deleting it:** in Firestore, create collection `mod`, document `hidden`,
  with a map field `ids`. Add the product's id (e.g. `u-abc12345-x9y8z7`) as a key with value `true`.
- **Votes** are in `votes`, one doc per visitor: `picks` (product id → time voted), `sent` (letters), `skus` (reported SKUs).

## Product pictures

Every product shows a red line drawing:

- **Products with their own drawing:** the curated products each have one in the `ICONS` list in `index.html`, keyed by product id.
- **Everything else**, including new fan-reported products, shows its category's drawing from `CAT_ICONS`
  (a chip bag for Snacks & crackers, a freezer box for Frozen, and so on).
- **A custom drawing for a fan product:** add an entry to `ICONS` using its id (starts with `u-`; find it in
  the `submissions` collection in Firestore).

**Photos (optional):** to use a photo instead of a drawing, put it in `images/` and list it in the `IMAGES`
list near the top of the script, e.g. `"gorgonzola-crackers": "images/gorgonzola-crackers.jpg",`.
Photos are cropped to a square; about 400×400 pixels keeps the page fast. Use your own photos or ones you
have permission to use. Trader Joe's owns its product photography.

## Limits to know about

- **Ballot stuffing:** anonymous IDs reset if someone clears their browser data, so a determined person
  can vote more than once. If that becomes a problem, switch Anonymous to Google sign-in in Firebase
  and change `signInAnonymously` in `index.html` to a Google sign-in popup.
- **Free-plan reads:** every page view reads each voter's doc once to total the votes. The free plan
  allows 50,000 reads a day, which covers roughly (50,000 ÷ number of voters) page views a day.
  If the site grows past that, move the totals into a Cloud Function that keeps a single summary doc.
