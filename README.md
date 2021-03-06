# Using i18n-next with typescript

Was having some trouble to work with the library using Next/Typescript in an existing project, so I stepped back and maade a clean new next app to see how it works.

# Starting

First of all:

```powershell
npm install next-i18next
```

There're four important steps:

- Create a next-i18next.config.js
- Create a next.config.js
- Create a loales file
- Use the translation hook

## next-i18next.config.js

This file is created in the root directory. It's responsible for the configuration off the i18next library.
Inside of this file you write:

```javascript
module.exports = {
  i18n: {
    defaultLocale: "en",
    locales: ["en", "pt"],
  },
};
```

The `defaultLocale` is the language that is aplied in the root route ("localhost:3000/"), all other languages appear in the URL ("localhost:3000/pt").

The `locales` array determines all the languages the Next App supports. The i18n also searchs for the repositories with the names in the array for translation files.

## next.config.js

This file is also created in the root directory and just exports the next-i18n.config. Basically, makes the configurations visible for the Next App.

```javascript
const { i18n } = require("./next-i18next.config");

module.exports = {
  i18n,
};
```

## locales

In the public/locales goes all the translation folders where the translation files are. They're just classic jsons.


## _app

In the _app it's necessary to encapsulate the MyApp function with the appWithTranslation function when exporting it.

```javascript

import "../styles/globals.css";
import type { AppProps } from "next/app";
import { appWithTranslation } from "next-i18next";

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
export default appWithTranslation(MyApp);

```

## Using the translation hook

In the files of the pages that will be translated, in the server side, the translations that will be used are loaded and the hook useTranslation is used to specify the translation files.

As an example, the index.tsx file is:

```javascript
import Head from "next/head";
import styles from "../styles/Home.module.css";
import { serverSideTranslations } from "next-i18next/serverSideTranslations";
import { useTranslation } from "next-i18next";

export default function Home() {
  const { t } = useTranslation("common");

  return (
    <div className={styles.container}>
      <Head>
        <title>{t("title")}</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main className={styles.main}>
        <h1 className={styles.title}>{t("helloWorld")}</h1>
      </main>
    </div>
  );
}

export async function getStaticProps({ locale }: any) {
  return {
    props: {
      ...(await serverSideTranslations(locale, ["common"])),
      // Will be passed to the page component as props
    },
  };
}
```
