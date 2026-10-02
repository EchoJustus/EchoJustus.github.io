# `i18n/` — UI string overrides

This directory is empty on purpose. It is where you override the **theme's own
chrome strings** — the words the theme puts on the page that do not come from
your Markdown: "Language", "On this page", "Previous", "Next", the `::: tip`
container labels, and so on.

It has nothing to do with translating your content. A translated article is a
sibling file with a language suffix (`01.welcome.zh-Hans.md`); see the
repository `README.md`.

## How it works

Add one EDN file per language, named after the language code exactly as it
appears under `:langs :locales` in `site.edn`:

```
i18n/en.edn
i18n/zh-Hans.edn
i18n/zh-Hant.edn
i18n/ms.edn
i18n/ta.edn
```

Each file is a map of keyword → string. It is **deep-merged over the theme's
defaults for that language, and your value wins**, so you only write the keys
you want to change. An absent file means "use the theme's defaults", which is
why this directory can stay empty.

```clojure
;; i18n/en.edn — override two strings, inherit the rest.
{:index/updates "Latest"
 :page/toc       "Contents"}
```

Any other file in this directory is ignored — the generator looks up
`i18n/<lang>.edn` by exact name, which is why this README is harmless here, and
why a file for a language that is not configured (`fr.edn`) is never opened.

A file that *does* match a configured language but is not valid EDN is a
different matter: it aborts the build with a raw stack trace rather than the
tidy "could not parse" message a broken `site.edn` gets. If a build fails with
a Clojure exception right after you edited a translation, check the brackets in
that file first.

## The keys

These are the theme's chrome strings and their English defaults, in the
order the generator's `en.edn` lists them. `{{...}}` is an interpolation slot;
keep it in your translation or the value it carries disappears. The `:search/…`
keys use Pagefind's own placeholders instead, `[SEARCH_TERM]`, `[COUNT]` and
`[DIFFERENT_TERM]`; keep those too.

The `:search/…` strings label the search dialog. Pagefind ships its own
strings for every configured language except Malay, so those keys are used
for `ms` pages, and for any language whose `i18n/<lang>.edn` overrides at
least one `:search/…` key. The `:banner/…` strings make up the "also
available" note, and are always shown in the language the reader chose, not
the language of the page.

| Key | English default |
|---|---|
| `:lang/name` | `English` |
| `:nav/search` | `Search` |
| `:nav/language` | `Language` |
| `:nav/home` | `Home` |
| `:page/last-updated` | `Last updated {{date}}` |
| `:page/prev` | `Previous` |
| `:page/next` | `Next` |
| `:page/edit` | `Edit this page` |
| `:page/toc` | `On this page` |
| `:page/also-available` | `Also available in {{lang}}` |
| `:page/fallback-notice` | `Shown in {{lang}} — not yet translated` |
| `:index/categories` | `Categories` |
| `:index/tags` | `Tags` |
| `:index/archives` | `Archive` |
| `:index/count` | `Articles: {{n}}` |
| `:container/tip` | `TIP` |
| `:container/warning` | `WARNING` |
| `:container/danger` | `DANGER` |
| `:container/note` | `NOTE` |
| `:container/details` | `Details` |
| `:comments/title` | `Comments` |
| `:index/all` | `All` |
| `:index/page` | `Page {{n}} of {{total}}` |
| `:index/prev-page` | `Previous page` |
| `:index/next-page` | `Next page` |
| `:index/empty` | `No articles yet.` |
| `:index/sticky` | `Pinned` |
| `:index/read-more` | `Read more` |
| `:index/updates` | `Recently updated` |
| `:index/more` | `More` |
| `:index/category-title` | `Category: {{name}}` |
| `:index/tag-title` | `Tag: {{name}}` |
| `:page/breadcrumbs` | `You are here` |
| `:page/author` | `Author` |
| `:page/date` | `Date` |
| `:page/categories` | `Categories` |
| `:page/tags` | `Tags` |
| `:page/sidebar` | `Sidebar` |
| `:container/theorem` | `Theorem` |
| `:search/placeholder` | `Search` |
| `:search/clear-search` | `Clear` |
| `:search/load-more` | `Load more results` |
| `:search/search-label` | `Search this site` |
| `:search/filters-label` | `Filters` |
| `:search/zero-results` | `No results for [SEARCH_TERM]` |
| `:search/many-results` | `[COUNT] results for [SEARCH_TERM]` |
| `:search/one-result` | `[COUNT] result for [SEARCH_TERM]` |
| `:search/total-zero-results` | `No results` |
| `:search/total-one-result` | `[COUNT] result` |
| `:search/total-many-results` | `[COUNT] results` |
| `:search/alt-search` | `No results for [SEARCH_TERM]. Showing results for [DIFFERENT_TERM] instead` |
| `:search/search-suggestion` | `No results for [SEARCH_TERM]. Try one of the following searches:` |
| `:search/searching` | `Searching for [SEARCH_TERM]...` |
| `:search/results-label` | `Search results` |
| `:search/keyboard-navigate` | `navigate` |
| `:search/keyboard-select` | `select` |
| `:search/keyboard-clear` | `clear` |
| `:search/keyboard-close` | `close` |
| `:search/keyboard-search` | `search` |
| `:search/error-search` | `Search failed` |
| `:search/filter-selected-one` | `[COUNT] selected` |
| `:search/filter-selected-many` | `[COUNT] selected` |
| `:search/input-hint` | `Results will appear as you type` |
| `:search/loading` | `Loading` |
| `:banner/available` | `This page is also available in {{lang}}.` |
| `:banner/read` | `Read in {{lang}} →` |
| `:banner/dismiss` | `Dismiss` |

The authoritative list is the generator's own
`src/clogem/theme/resources/i18n/<lang>.edn`; the theme ships a full set for all
five languages, so overriding is optional everywhere.

## Resolution order

For a requested language, a string is looked up in this order and the first hit
wins:

1. your `i18n/<requested-lang>.edn`, then the theme's defaults for that language
2. the same, for each language in `:i18n :fallback`, which `site.edn` leaves
   at its default `[:site-default :en]`: `:langs :default` (English), then `en`
3. the key itself

A missing key is reported as a warning while `:i18n :missing-key` is `:warn` in
`site.edn`.
