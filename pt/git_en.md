# Git

The project may be opensource, but that doesn't mean it's a free-for-all!

## Open pull requests

**Especially** if you're contributing for the first time, we ask that you open a pull request so that the LanguageTool community can have a look at your work.

New pull requests must, to the extent it is possible...
- ... be self-contained – i.e., avoid pull requests with tonnes of unrelated changes;
- ... be described and documented **in English**, even if you are working on another language;
- ... have a title starting with a [tag](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes), enclosed in square brackets, corresponding to the language you're working on, e.g. `[en] Add grammar rule DO_THING`, `[nl] Fix bug 9234 with messages`;
- ... be labelled with the language you're working on;
- ... contain a brief explanation, in the description, of your changes.

For your pull request to be considered acceptable, it must:
- receive at least **one** accepting review;
- pass all the Circle CI tests.

Once approved, you can merge your PR yourself. If your PR contains more than one commit, prefer the [merge with squash](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github#squashing-your-merge-commits) option so as to keep our Git history as clean as possible.


## Branching policy

If possible, we ask that the names of your branches follow the format `$language_code/$domain/$feature`, where:
- `$language_code` is the two-letter [code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) of your language, e.g. `de` for German, `ja` for Japanese;
    - if you want to make it clear you are editing a specific dialect, please add the Alpha-2 [country code](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes), e.g. `de-CH`, `en-US`, `pt-BR`;
- `$domain` refers to, broadly speaking, the type of content your PR affects – `grammar` for grammar rules, `style` for style rules, `dict` for spelling, etc
    - this is not a closed category and there are no strict rules dictating what names are or aren't allowed, just use your best judgement;
- `$feature` should describe, very succinctly, the content of your change; it can be the name of a new rule, a short description (e.g. `fix_professional_messages`), the number of the GitHub issue you're fixing, etc.

Please keep `$domain` in English, but the name of the `$feature` component of the branch name might make sense to name in your vernacular. If you do use languages other than English, please avoid non-ASCII characters.

Some good examples of branch names might `pt/grammar/subjuntivo_sem_conjuncao`, `nl/style/SENT_FINAL_PLEASE`, `de/dict/fix_9123`.

The following information can be extracted from commit metadata and **must not** be present in your branch name:
- your name (a branch may be committed to by many people);
- the date (a branch may be committed to on different days).

Some bad examples might be:
- `en/grammar` – missing the `$feature` component;
- `sarah_april_22` – contains name and date;
- `rules` – very uninformative;
- `JE_NE_SAIS_QUOI` – rule ID only, without locale or domain.


## [Commit messages](https://xkcd.com/1296/)

Use [these guidelines](https://cbea.ms/git-commit/) to the extent they apply.

As much as it is reasonable to do so, please keep your commits well-described:
- start all of your commit messages with the locale code (e.g. `[en]`, `[de-CH]`), much like the title of a PR;
- write your messages in English;
- the first line of your message must not be longer than 50 characters;
- unless you are making changes that apply across the board to many different rules, try to add the name of the rule to your commit message.

The adage 'commit early, commit often' only applies to **local** work. Ideally, your pull requests should be composed of a few, relatively self-contained commits. If you have loads of commits with messages like `[en] Fix error` and `[de] Add antipattern`, prefer squashing them.
