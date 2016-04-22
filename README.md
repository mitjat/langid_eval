# Language-annotated Tweets

This is a collection of professionally language-annotated Tweets, intended for measuring performance of automated language identifiers. Data files:

* `uniformly_sampled.tsv` - a uniform sample of all Twitter data; approximately 120k rows. Useful for measuring precision or recall of large languages, and for estimating the language distribution.
* `recall_oriented.tsv` - about 1500 Tweets per true language. Useful for for measuring recall regardless of language size.
* `precision_oriented.tsv` - about 1000 Tweets per language as determined by an old version of Twitter’s internal language identifier. A biased but still somewhat dataset for estimating the precision for small languages.

All files contain one Tweet per line, formatted as `<true language code><TAB><tweet ID>`. In addition, `precision_oriented.tsv` contains language codes like `not_en`, which indicates this tweet is *not* English, though we don't know its actual language.

For details on how the Tweets were collected, see our [blog post on the Twitter Engineering Blog](https://blog.twitter.com/2015/evaluating-language-identification-performance) or its [local copy](doc/blog_post.md).

All Tweets are from July 2014 and cover 70 languages: `am` (Amharic), `ar` (Arabic), `bg`(Bulgarian), `bn`(Bengali), `bo`(Tibetan), `bs`(Bosnian), `ca`(Catalan), ckb (Sorani Kurdish), `cs`(Czech), `cy`(Welsh), `da`(Danish), `de`(German), `dv`(Maldivian), `el`(Greek), `en`(English), `es`(Spanish), `et`(Estonian), `eu`(Basque), `fa`(Persian), `fi`(Finnish), `fr`(French), `gu`(Gujarati), `he`(Hebrew), `hi`(Hindi), `hi-Latn` (Latinized Hindi), `hr`(Croatian), `ht`(Haitian Creole), `hu`(Hungarian), `hy`(Armenian), `id`(Indonesian), `is`(Icelandic), `it`(Italian), `ja`(Japanese), `ka`(Georgian), `km`(Khmer), `kn`(Kannada), `ko`(Korean), `lo`(Lao), `lt`(Lithuanian), `lv`(Latvian), `ml`(Malayalam), `mr`(Marathi), `ms`(Malay), `my`(Burmese), `ne`(Nepali), `nl`(Dutch), `no`(Norwegian), `pa`(Panjabi), `pl`(Polish), `ps`(Pashto), `pt`(Portuguese), `ro`(Romanian), `ru`(Russian), `sd`(Sindhi), `si`(Sinhala), `sk`(Slovak), `sl`(Slovenian), `sr`(Serbian), `sv`(Swedish), `ta`(Tamil), `te`(Telugu), `th`(Thai), `tl`(Tagalog), `tr`(Turkish), `ug`(Uyghur), `uk`(Ukrainian), `ur`(Urdu), `vi`(Vietnamese), `zh-CN` (Simplified Chinese), `zh-TW` (Traditional Chinese). There is a smattering of other language codes present in the data as an artifact of our labeling process, but Tweets in those languages were not collected systematically.

### Downloading

Because of privacy concerns, we aren't able to provide a snapshot of Tweets' text. To retrieve the text, use the Twitter API. An example of efficiently fetching the Tweets 100 at a time using the [statuses/lookup](https://dev.twitter.com/rest/reference/get/statuses/lookup) API endpoint, the [twurl](https://github.com/twitter/twurl) command-line utility, and [jq](https://stedolan.github.io/jq/) for JSON parsing:

```sh
cat >/tmp/fetch.sh <<'EOF'
#!/bin/bash
sleep 5
twurl "/1.1/statuses/lookup.json?id=$(echo $@ | tr ' ' ,)&trim_user=true" | jq -c ".[]|[.id_str, .text]"
EOF

cat uniformly_sampled.tsv | cut -f2 | xargs -n100 /tmp/fetch.sh > hydrated.json
```

Make sure you’ve completed the [OAuth setup](https://github.com/twitter/twurl) for twurl (see “Getting started” on their github page) before running the above commands. The code above observes the [API rate limits](https://dev.twitter.com/rest/reference/get/statuses/lookup) by sleeping between requests. It silently skips Tweets that have been removed, and you will need to join the `hydrated.json` file with original language labels in `uniformly_sampled.tsv`.

