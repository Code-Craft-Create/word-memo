# word-memo

A mockup of an app for memorising Bible verses. It's a single page of plain HTML and JavaScript, with no build step and no backend.

## Run it

Open `index.html` in a browser.

## Features

- Add a verse with a passage, an optional translation and the text. Earlier translations are suggested.
- Add several translations of the same verse, and switch between them on the board.
- Tag verses with free-text tags. Earlier tags are suggested, and you can filter the board by tag.
- Highlight key words by selecting them, either on the board or in the form.
- Edit or delete verses.
- Export all verses to JSON and import them again. Older single-translation exports still import.

Verses are stored in the browser's `localStorage`.
