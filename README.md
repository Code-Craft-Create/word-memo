# word-memo

An app for memorising Bible verses. It's a single page of plain HTML and JavaScript, with no build step. Verses can be shared between people and devices through a free Firebase project.

## Run it

Open `index.html` in a browser.

## Features

- Add a verse with a passage, an optional translation and the text. Earlier translations are suggested.
- Add several translations of the same verse, and switch between them on the board.
- Tag verses with free-text tags. Earlier tags are suggested, and you can filter the board by tag.
- Highlight key words by selecting them, either on the board or in the form.
- Edit or delete verses.
- Export all verses to JSON and import them again. Older single-translation exports still import.

- Share one collection of verses with other people: everyone signs in with Google and sees and edits the same verses, live. It also works offline and syncs later.

## Storage

While `FIREBASE_CONFIG` at the top of the script in `index.html` is empty, verses are stored in the browser's `localStorage` only.

Once it is filled in, verses are stored in Cloud Firestore in the `verses` collection (one document per verse, keyed by its id), and only the Google accounts listed in the Firestore rules can read or change them.

## Set up sharing

Everything below fits in Firebase's free Spark plan.

1. **Create the Firebase project.** At [console.firebase.google.com](https://console.firebase.google.com), add a project (Google Analytics is not needed).
2. **Turn on Google sign-in.** Go to Build → Authentication → Get started → Sign-in method → Google → Enable, then save.
3. **Create the database.** Go to Build → Firestore Database → Create database. Pick a location near you and start in production mode.
4. **Restrict access.** In Firestore → Rules, paste the contents of [`firestore.rules`](firestore.rules). Replace the two example addresses with the Google accounts that should have access, written in lowercase. Then publish.
5. **Register the web app.** In Project settings → General → Your apps, add a Web app (`</>`); Firebase Hosting is not needed. Copy the `firebaseConfig` object it shows into `FIREBASE_CONFIG` in `index.html`. These values are not secret: access is controlled by the rules.
6. **Host the page.** In this repo's Settings → Pages, set the source to "Deploy from a branch", with branch `main` and folder `/ (root)`. GitHub Pages is free for public repositories; private repositories need a paid GitHub plan. The page will be at `https://<github-user>.github.io/word-memo/`.
7. **Allow the domain.** In Authentication → Settings → Authorized domains, add `<github-user>.github.io`.

To move verses you already have, open the old page and use **Export JSON**. Then sign in on the shared page and use **Import JSON**. Duplicates are skipped.
