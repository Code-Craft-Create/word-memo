# Word Memo

An app for memorising Bible verses. It's a single page of plain HTML and JavaScript, with no build step. Verses can be shared between people and devices through a free Firebase project.

## Run it

Open `index.html` in a browser.

## Features

- A home screen shows what's due, your days in a row, the verses of your last set or session (with a button to continue), and your learning sets. A menu (top right) leads to Learn, All verses, Add a verse, and Settings & backup (account, sign out, export, import).
- Add a verse with an optional translation, a passage and the text. Pick the book from a list of all 66 books (type to filter, e.g. "Joh"), then enter chapter and verse ("3:16", "3,16" or "3:16-18"). You can also type a whole passage such as "Joh 3,16" into the chapter field. Passages are stored as book + chapter:verse, so they are shown in the language of each translation: "John 3:16" for the NIV, "Johannes 3,16" for the NGÜ. Verses saved before this are read automatically where their passage is recognised (English and German names and common abbreviations).
- Settings → Translations lists the translations you use and their language (English or German); NGÜ, SLT2000 and NIV are there to start with. Translations used on verses but not listed can be added with one click, and well-known abbreviations fill in their name and language. Unlisted translations are shown in English.
- "Look up on Bibleserver" opens the passage in that translation on bibleserver.com, so you can copy the text in (the app does not fetch copyrighted text itself). NGÜ, Schlachter 2000 (SLT, SLT2000, SCH2000) and NIV are recognised under their usual names; other abbreviations are passed on as typed.
- "Compare all your translations on Bibleserver" opens the passage once in every translation used so far (and any typed in the form), one after another.
- Add several translations of the same verse, and switch between them on the board.
- Star a favourite translation per verse (on the board card or in the form). It is shown first on the board and used for learning; during a session you can switch to another translation of the same verse with one tap.
- Tag verses with free-text tags. Earlier tags are suggested, and you can filter the board by tag.
- Highlight key words by selecting them, either on the board or in the form.
- Edit or delete verses. A deleted verse can be brought back with Undo for 10 seconds.
- Export verses, learning sets, translations and your own learning progress to JSON, and import them again. Older exports, including single-translation ones, still import.
- Learn in sessions: pick verses one by one, by tag, or all that are due. Choose a way of learning (flip cards, which verse?, first letters, fading words), turn each card around when ready and rate how well you knew it: Not yet, Shaky, Got it or Perfect. Or play "Match up": tap each verse and then its passage, in rounds of up to five; a first-try match counts as Got it, one miss as Shaky, more as Not yet. "Word order" (tap the pieces of the verse in order; they get smaller as you improve) rates each verse by its slips: none is Got it, one or two Shaky, more Not yet. "Build it up" is for new verses: one phrase at a time with earlier phrases as first letters, then the whole verse, then you rate it. "Lightning round" asks quick "which passage?" and "which word is missing?" questions for one minute and keeps your best score. Nothing needs typing.
- Save a selection of verses as a named learning set to focus on over several days. Sets are shared, and each set shows how many of its verses you have learned and how many are due. Saving under an existing name updates that set.
- Each rating moves the verse to a level (0 to 6, shown as dots) and schedules when it is due again, from the same day up to a month later. A verse can rise at most 2 levels a day, and the quick ways (Which verse?, Match up, Lightning round) only raise it up to level 2. Sessions give points, runs of known verses earn a bonus, and the app counts the days in a row you practised.

- The app speaks English or German (Settings → Language). It starts in the browser's language and remembers the choice: on the device, and in shared mode in your own data, so it follows you to your other devices. All texts live in `index.html`: they are written in English in the code (`t('…')`), with the German ones in the `DE` list near the top of the script. Another language is one more such list plus an entry in `UI_LANGS`.
- Share verses with other people: everyone signs in with Google and sees and edits the same verses, live. It also works offline and syncs later.
- Groups: the admin (one Google account named in the Firestore rules) adds people and puts them in groups under Settings → People & groups. Each group has its own verses, learning sets and translations; someone in several groups switches between them in the menu. Removing someone's access keeps their learning progress in case they come back.

## Storage

While `FIREBASE_CONFIG` at the top of the script in `index.html` is empty, verses are stored in the browser's `localStorage` only.

Once it is filled in, the data is stored in Cloud Firestore. Each group's verses, learning sets and translations list live under `groups/{groupId}/verses`, `…/sets` and `…/config/translations`; the group's name is in `groups/{groupId}`. Each person is a document `people/{email}` (lowercase email: name, groupIds, active, addedAt, lastSeen) that only the admin can change.

Locally, the list of translations is kept under `verseBoard.translations.v1` and learning sets under `verseBoard.sets.v1`.

Learning progress is personal. Locally it is kept in `localStorage` under `verseBoard.progress.v1`. In shared mode each person's progress is one document `progress/{uid}`, which only they can read or change. The group being shown is remembered per device under `verseBoard.group.v1`. The app language is kept per device under `verseBoard.lang.v1`, and in shared mode also as `lang` in the person's `progress/{uid}` document (no rules change needed).

## Set up sharing

Everything below fits in Firebase's free Spark plan.

1. **Create the Firebase project.** At [console.firebase.google.com](https://console.firebase.google.com), add a project (Google Analytics is not needed).
2. **Turn on Google sign-in.** Go to Build → Authentication → Get started → Sign-in method → Google → Enable, then save.
3. **Create the database.** Go to Build → Firestore Database → Create database. Pick a location near you and start in production mode.
4. **Restrict access.** In Firestore → Rules, paste the contents of [`firestore.rules`](firestore.rules). Replace `admin.person@gmail.com` with the Google account that manages people (written in lowercase). Then publish.
5. **Register the web app.** In Project settings → General → Your apps, add a Web app (`</>`); Firebase Hosting is not needed. Copy the `firebaseConfig` object it shows into `FIREBASE_CONFIG` in `index.html`. These values are not secret: access is controlled by the rules.
6. **Host the page.** In this repo's Settings → Pages, set the source to "Deploy from a branch", with branch `main` and folder `/ (root)`. GitHub Pages is free for public repositories; private repositories need a paid GitHub plan. The page will be at `https://<github-user>.github.io/word-memo/`.
7. **Allow the domain.** In Authentication → Settings → Authorized domains, add `<github-user>.github.io`.

To move verses you already have, open the old page and use **Export JSON**. Then sign in on the shared page and use **Import JSON**. Duplicates are skipped.

## Add people and groups

1. Sign in with the admin account and open Settings → People & groups.
2. The first time, create a group and add yourself under Add a person, with that group ticked. Until you are in a group, the app opens straight on this page.
3. Create a group for the new people, or use an existing one.
4. Under Add a person, type their Google address and name, tick their group(s) and press Add person.
5. Send them the app link. They sign in with Google and see their group.

No rules change is needed for this. Remove access takes effect at once; Give access back restores it.
