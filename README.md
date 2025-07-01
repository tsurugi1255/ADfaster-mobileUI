# Antimatter Dimensions

This is a modification of Wyxkk's Antimatter Fast Dimensions mod, slightly redesigned to be playable on a mobile browser. Please note that the changes are only applied to the **Modern UI**.

## Credits
Credits go to the following:
- [Ivark](https://github.com/IvarK/AntimatterDimensionsSourceCode): for the AD source code
- [Wyxkk](https://github.com/WYXkk/ADfaster): for the AD Fast Dimensions mods
- [Asanned](https://github.com/Asanned/AD_MWGP): for the code to enable glyph selection on mobile
- [realjman](https://github.com/realjman/ADfasterer): for the code to convert offline IP gen to online

## Run

To run the game locally, you will need to install
[Node.js](https://nodejs.org/) (LTS suggested).

First, run the following command in your terminal (or command line) while being
inside the checked out repository:

```
npm ci
```

After all the packages are installed, start up the game:

```
npm run serve
```

This will make the game served via your localhost, and the playable link will
be displayed in your terminal. The server **doesn't** need to be restarted
after you've made changes - just reload the page. The server **can**
occasionally crash, so check your terminal from time to time and run `serve`
again if needed.
