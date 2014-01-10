# The Spaghetti Problem of Low Coverage Features in Industrial Web Applications

Large applications deployed globally face an engineering complexity problem that directly stems from their size. As they become older, bigger, and go through more iterations, they tend to accumulate legacy code that eventually leads to paralysis. The initial owners left, 7000 people worked on it, and nobody has its overall architecture in mind anymore. 

Lately, we at Yahoo Search decided to experiment ([bucket test](http://en.wikipedia.org/wiki/A/B_testing)) at all levels of the app, much more rapidly and nimbly than before (think every day with a team of 6 developers). Each bit/feature of the page may be served in a different flavor for a small subset of the users, which lets us see how users react to a change in our app and retain only the winning changes. Since each user sees many of those page bits/features, each user is served with a _combination_ of experiments. Where then, should we write those flavors so they can be combined dynamically, yet have no dependance to one another, be easily versionable, testable, maintainable and reusable? Depending on the architecture, it can be easy to just stitch-up a feature (think if-statement) on top of the big bowl of spaghetti rather than thinking of a modular, reusable, scalable way of writing it. Here at Yahoo, we use [Mojito](http://developer.yahoo.com/cocktails/mojito/) and the answer lies in how a mojito app is structured.

## The tree that masks the forest
A [mojito](http://developer.yahoo.com/cocktails/mojito/) app is roughly a node package with a config file (in json or yaml) plus a set of directories, each corresponding to an independant widget - called "mojit". Each mojit has resources (files) for each: model, view, controller, client-side assets.

```yaml
search-apps-news
|-- mojits
|   |-- SearchBox/
|   |   |-- models/
|   |   |-- views/
|   |   |    `-- index.html
|   |   |-- controller.js
|   |   |-- assets/
|   |   ...
|   |-- SearchResult/
|   |   |-- models/
|   |   |-- views/
|   |   |-- controller.js
|   |   |-- assets/
|   |   ...
|   ...
|-- package.json
|-- ...
// the configuration file
`-- application.json
```

That's the base application, that's the forest. Now, say you want to try changing the view of the search box to add a button for some users to see how they react. You will want to change `search-apps-news/mojits/SearchBox/views/index.html`. Right? 

## Well no.
This is a recipe for a spaghetti code disaster when you have 40 experiments on that search box. Besides, the logic that decides what user should get what view should be reusable and in the app framework (not your app). If you believe that, then [mojito-dimensions-base](https://github.com/yahoo/mojito-dimensions-base) just became your best friend. By including that package in the package containing your experiments, you can then create "mask packages" that mimic the structure of your app _only for those files that you want to override for that experiment_.

So to come back to experimenting on that extra button for some users, make a node package for your searchbox experiments that looks like this:

```yaml
mojito-dimensions-experiment_searchbox
 |-- extrabutton
 |   |-- mojits/
 |   |   `-- SearchBox/
 |   |      `-- views/
 |   |          `-- index.html
 |   `-- application.yaml
 ...
 |-- otherExperiment/
 ...
 |-- node_modules
 |   `-- mojito-dimensions-base/
 `-- package.json
```
Done. As you can see, the `extrabutton/` directory structure mirrors your base app but only replaces one file: `mojits/SearchBox/views/index.html`.  An experiment can involve many file substitutions, but in this case, we only need to change a single file. `application.yaml` is the config that tells your app when to trigger that "mask" (= who are the "some users").

Et voila! If you now define `mojito-dimensions-experiment_searchbox` as a dependency of you app, the file loaded when your request matches the `extrabutton` configuration will be the one from the `extrabutton` package, _not_ the baseline!
You can now easily develop experiment packages that you can test and maintain outside your baseline app, activate and deactivate at will, and merge easily when you determine you have a winner.

*Jacques Arnoux (arnoux [at] yahoo-inc [dot] com) & David Gomez (dgomez [at] yahoo-inc [dot] com) for Yahoo! Search Front-End Platform*
