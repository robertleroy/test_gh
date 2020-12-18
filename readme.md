# [Deploy to gh-pages](https://robertleroy.github.io/test_gh)
  
* ## Create Project / Create Repository 
Push to Github
``` js
git add .
git commit -m 'first commit'
git remote add origin https://github.com/robertleroy/PROJECT_NAME.git
git push -u origin master
```

<br>  

* ## Set Base
``` json
/* vite.config.js */

module.exports = {
  // base: '/test_gh/'
  base: './'
}
```

<br>  

* ## Node script to build and deploy to gh-pages
``` js
/* scripts/gh-deploy.js */

/* eslint-disable no-console */
const execa = require("execa");
const fs = require("fs");
(async () => {
  try {
    await execa("git", ["checkout", "--orphan", "gh-pages"]);
    // eslint-disable-next-line no-console
    console.log("Building started...");
    await execa("npm", ["run", "build"]);
    // Understand if it's dist or build folder
    const folderName = fs.existsSync("dist") ? "dist" : "build";
    await execa("git", ["--work-tree", folderName, "add", "--all"]);
    await execa("git", ["--work-tree", folderName, "commit", "-m", "gh-pages"]);
    console.log("Pushing to gh-pages...");
    await execa("git", ["push", "origin", "HEAD:gh-pages", "--force"]);

    /* delete dist folder? */
    // await execa("rm", ["-r", folderName]); 
    await execa("git", ["checkout", "-f", "master"]);
    await execa("git", ["branch", "-D", "gh-pages"]);
    console.log("Successfully deployed, check your settings");
  } catch (e) {
    // eslint-disable-next-line no-console
    console.log(e.message);
    process.exit(1);
  }
})();
```

<br>  

* ## Add `.nojekyll` file to public folder  
    * *corrects for error retrieving files from `_assets` folder*

<br>  

* ## Install `execa`, edit package.json
  * *runs script to automate deployment*

``` js
npm install execa -D
```

``` json
/* packsge.json */

"scripts": {
  "deploy": "node scripts/gh-deploy.js"
}
```

<br>  

* ## add, commit, push, **deploy**
``` js 
git add . && git commit -m 'create deploy script'
git push

npm run deploy
```