Archived attempt at SPA for GitHub Pages, didn't work because this solution wasn't compatible with React atomize.

# Single Page Apps for GitHub Pages

[Live example][liveExample]  

This is a lightweight solution for deploying single page apps with [GitHub Pages][ghPagesOverview]. You can easily deploy a [React][react] single page app with [React Router][reactRouter] `<BrowserRouter />`, like the one in the [live example][liveExample], or a single page app built with any frontend library or framework.

#### Why it's necessary
GitHub Pages doesn't natively support single page apps. When there is a fresh page load for a url like `example.tld/foo`, where `/foo` is a frontend route, the GitHub Pages server returns 404 because it knows nothing of `/foo`.

#### How it works
When the GitHub Pages server gets a request for a path defined with frontend routes, e.g. `example.tld/foo`, it returns a custom `404.html` page. The [custom `404.html` page contains a script][404html] that takes the current url and converts the path and query string into just a query string, and then redirects the browser to the new url with only a query string and hash fragment. For example, `example.tld/one/two?a=b&c=d#qwe`, becomes `example.tld/?p=/one/two&q=a=b~and~c=d#qwe`.

The GitHub Pages server receives the new request, e.g. `example.tld?p=/...`, ignores the query string and hash fragment and returns the `index.html` file, which has a [script that checks for a redirect in the query string][indexHtmlScript] before the single page app is loaded. If a redirect is present it is converted back into the correct url and added to the browser's history with `window.history.replaceState(...)`, but the browser won't attempt to load the new url. When the [single page app is loaded][indexHtmlSPA] further down in the `index.html` file, the correct url will be waiting in the browser's history for the single page app to route accordingly. (Note that these redirects are only needed with fresh page loads, and not when navigating within the single page app once it's loaded).

A quick SEO note - while it's never good to have a 404 response, it appears based on [Search Engine Land's testing][seoLand] that Google's crawler will treat the JavaScript `window.location` redirect in the `404.html` file the same as a 301 redirect for its indexing. From my testing I can confirm that Google will index all pages without issue, the only caveat is that the redirect query is what Google indexes as the url. For example, the url `example.tld/about` will get indexed as `example.tld/?p=/about`. When the user clicks on the search result, the url will change back to `example.tld/about` once the site loads.


## Usage instructions
*For general information on using GitHub Pages please see [GitHub Pages Basics][ghPagesBasics], note that pages can be [User, Organization or Project Pages][ghPagesTypes]*  
&nbsp;

**Basic instructions** - there are two things you need from this repo for your single page app to run on GitHub Pages
  1. Copy over the [`404.html`][404html] file to your repo as is
      - Note that if you are setting up a Project Pages site and not using a [custom domain][customDomain] (i.e. your site's address is `username.github.io/repo-name`), then you need to set [`segmentCount` to `1` in the `404.html` file][segmentCount] in order to keep `/repo-name` in the path after the redirect.
  2. Copy the [redirect script][indexHtmlScript] in the `index.html` file and add it to your `index.html` file
      - Note that the redirect script must be placed *before* your single page app script in your `index.html` file
&nbsp;
