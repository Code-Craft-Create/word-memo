# word-memo

An app for memorising Bible verses. It's a single page of plain HTML and JavaScript, with no build step. Verses can be shared between people and devices through a free Firebase project.

## Run it

Open `index.html` in a browser.

## Features

- Add a verse with a passage, an optional translation and the text. Earlier translations are suggested.
- Add several translations of the same verse, and switch between them on the board.
- Star a favourite translation per verse (on the board card or in the form). It is shown first on the board and used for learning; during a session you can switch to another translation of the same verse with one tap.
- Tag verses with free-text tags. Earlier tags are suggested, and you can filter the board by tag.
- Highlight key words by selecting them, either on the board or in the form.
- Edit or delete verses.
- Export all verses to JSON and import them again. Older single-translation exports still import.
- Learn in sessions: pick verses one by one, by tag, or all that are due. Choose a way of learning (flip cards, which verse?, first letters, fading words), turn each card around when ready and rate how well you knew it: Not yet, Shaky, Got it or Perfect. Or play "Match up": tap each verse and then its passage, in rounds of up to five; a first-try match counts as Got it, one miss as Shaky, more as Not yet. Nothing needs typing.
- Save a selection of verses as a named learning set to focus on over several days. Sets are shared, and each set shows how many of its verses you have learned and how many are due. Saving under an existing name updates that set.
- Each rating moves the verse to a level (0 to 6, shown as dots) and schedules when it is due again, from the same day up to a month later. Sessions give points, runs of known verses earn a bonus, and the app counts the days in a row you practised.

- Share one collection of verses with other people: everyone signs in with Google and sees and edits the same verses, live. It also works offline and syncs later.

## Storage

While `FIREBASE_CONFIG` at the top of the script in `index.html` is empty, verses are stored in the browser's `localStorage` only.

Once it is filled in, verses are stored in Cloud Firestore in the `verses` collection (one document per verse, keyed by its id), and only the Google accounts listed in the Firestore rules can read or change them.

Learning sets are shared: in `localStorage` under `verseBoard.sets.v1`, or in the Firestore collection `sets` (one document per set: name and verse ids).

Learning progress is personal. Locally it is kept in `localStorage` under `verseBoard.progress.v1`. In shared mode each person's progress is one document `progress/{uid}`, which only they can read or change. If you set up sharing before learning sessions or learning sets existed, paste the current [`firestore.rules`](firestore.rules) into Firestore → Rules again (with your two addresses) and publish, or progress cannot be saved.

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
