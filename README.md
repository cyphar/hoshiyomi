## hoshiyomi ##

**NOTE**: This project is still a work-in-progress.

A tool to convert [Yomichan][yomichan] dictionaries directly to the
[StarDict format][stardict], with support for creating a `syn`-based
psuedo-deinflection table for devices which do not natively support Japanese
deinflection on lookup (which is most StarDict-based programs).

[yomichan]: https://github.com/FooSoft/yomichan
[stardict]: https://github.com/huzheng001/stardict-3/blob/master/dict/doc/StarDictFileFormat

### Details ###

#### Why Create a New Tool? ####

There already exist tools to convert Yomichan dictionaries to StarDict, but
unfortunately those tools drop more information than necessary (notably they
don't use StarDict's native furigana system) because they convert the
dictionary into intermediary formats (StarDict's tab format usually) which
drops loads of useful information.

Each individual tool is also maintained separately meaning you need to install
several tools just to convert a single dictionary. In addition, I was unable to
find a tool which created a synonym-based deinflection table automatically.

#### Synonym-based Definlection ####

StarDict does not support Japanese deinflection, and the dictionary format does
not have a built-in system to define deinflection rules. As such, looking up
Japanese verbs (or words in any other agglutinative language) with StarDict is
incredibly painful if not impossible if you don't know how to type the word
you're looking up.

This is made worse by the fact that StarDict's fuzzy search system is not
remotely useful for Japanese because -- in contrast to most European langauges
-- grammatical inflection in Japanese often takes up more chracters of a word
than the dictionary form of the word itself. In the inflection 喋られない for
instance, the actual verb is 喋る but the fuzzy search system will instead
throw away the 喋 and start looking for words that match られない. StarDict's
fuzzy search is only really suited for verb cases and other European
grammatical concepts where the word is only slightly modified when being
inflected.

Thus it is necessary to abuse the synonym system to implement Japanese
deinflection. The idea is that every inflectable word is given a synonym for
the first okurigana character for every inflection:

 * Godan (五段/u-verbs) have 5 verb stems plus the た・て stem. For example,
   読む is given the synonyms 読ま, 読み, 読め, 読も, 読ん.

 * Ichidan (一段/ru-verbs) do not need to have synonyms for every inflection
   because they are inflected by removing the る entirely. Instead they are
   simply given a synonym for the verb with okurigana removed. For example 見る
   is given the synonym 見, and 食べる gets 食べ.

 * The three exception verbs (来る, 行く, する) are each given their own
   special treatment.

 * Adjectival verbs (形容詞/i-adjectives) have several different inflections,
   each of which is given the same treatment as with 五段 verbs. For example
   安い is given the synonyms 安く, 安か, 安け, 安さ.

The above transformations are also done on the kana form of the word too to
create synonyms for cases where words are written purely in kana.

With the above inflections in place, it is possible to look up any
grammatically correct inflection of any word by searching the word by only
selecting the first okurigana character. For instance, if you see the word
喋られない, select "喋ら" and search for that.

The reason for not creating a synonym for every inflection is simply that the
synonym table would become far too large because every synonym has to be
defined for every word individually, meaning that creating 5 times as many
synonyms will make the synonym table 5 times larger. If you want better
deinflection support, it's necessary for the reader you're using to implement
native Japanese deinflection (most likely with some kind of text segmentation
too so you can look up words without having to accurately select the whole
inflection) and then look up words using their dictionary form directly.

### License ###

hoshiyomi is licensed under the GNU General Public License, version 3 or later.

```
hoshiyomi: convert Yomichan dictionaries to StarDict
Copyright (C) 2021 Aleksa Sarai <cyphar@cyphar.com>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
```
