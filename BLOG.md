# The Dimensional Problem in Global Web Applications

From either side of the screen, the web is a ruthless place and a fierce jungle. Stubborn old, cookieless, scriptless and fresh new browsers roam alike in a wide diversity of locales, preferences and screen ratios, and it is not without a certain anxiety that web developers decide to release their prodigies in that worldwide bestiary. Modern webapps must be designed from the ground up to support that diversity, and depending on the architecture it can be easier to stitch-up a feature (think if-statement) on top of big bowl of spaghetti rather than thinking of a modular, reusable, scalable way of writing it.

## The key word here is "combination".
The dimensional problem stems from the ambition that Yahoo Search needs to be personalized for each request, and to take into account a wide variety of parameters, or "dimensions". There are `locale`, `device`, `browser`, user `preferences`, `vertical` (news, finance, websearch, auto, ...), `location`, `partners`, etc. But also lately we decided we needed to experiment at all levels of the app. That means each feature of the app may have a different flavor for a random subset of user each. Since each user sees many of those features, each user is served with a _combination_ of experiments and regular dimensions. Where then, should we write those flavors so they can be combined dynamically, yet have any interdependance with one another? The answer lies into how a mojito app is structured.

## The tree that masks the forest
A mojito app is roughly a json config file plus a set of directories, each roughly corresponding to an independant widget - called "mojit". Each mojit has resources (files) for each: model, view, controller, client-side assets. ([learn more about mojito here](http://developer.yahoo.com/cocktails/mojito/))

```yaml
search-apps-news
|-- mojits
|   |-- SearchBox/
|   |   |-- models/
|   |   |-- views/
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
|-- ...
// the configuration file
`-- application.json

```
