+++
title = "How to cheat at Wordle"
date = "2022-05-18T21:59:28-04:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["wordle", "regex"]
keywords = ["wordle", "regex", "cheat"]
description = ""
showFullContent = false
+++

Imagine this: you fire up [Wordle](https://www.nytimes.com/games/wordle/index.html) and, struck by inspiration, enter this fantastic starting word:

!["SPLAY" with "S" green and the other letters gray](/img/wordle1.png)

Well that's nice. What next? Scamp? Scold? It is surprisingly hard to think of a word starting with "S" and not using the letters that were disqualified. After much soul-searching, you arrive at this:

!["SOUTH" with "S" green, "OU" yellow, and the rest gray](/img/wordle2.png)

"This is too hard!" you exclaim. "If only there were a better way!" Well now there is, with regular expressions! All you need is a list of words and some software that lets you perform regex searches on it. For this purpose, I'm going to use this [Online Regex Dictionary](https://www.visca.com/regexdict/).

Here are the basic facts about the word we are seeking: we know it is five letters long, it starts with "S", and it does not have the letters in "PLAYTH". So here is how we format our search:

```
^s[^playth]{4}$
```

Broken down:
- The `^` at the beginning means the start of the string. So `^s` means what we're searching for has to start with "s". Without the `^`, the search would pay attention to whenever it sees an "s" anywhere in a word.
- The brackets `[]` normally mean that the letter has to match one of the things inside them. However, with the `^` starting inside the brackets, the meaning is the opposite: the letter can be anything *BUT* what's inside the brackets.
- The curly braces with the number `{4}` mean four of the preceeding thing (so, four letters that are not in `playth`).
- The `$` means the end of the string. In this case, the search is constrained to 5-letter words.

Putting this into the dictionary linked above gets 98 results. Well, that's not so bad, except for that fact that we only have four more guesses. We can do better, because we haven't used the fact we know there's an "o" and a "u" yet. We can do this with "lookaheads", which is a bit of syntax telling the search to check for something without giving it a fixed position in the match.

```
(?=\w*o)(?=\w*u)^s[^playth]{4}$
```

- `(?=)` means we're doing a lookahead to make sure something is present in the string.
- `\w` means any word character.
- The `*` means zero or more of the preceeding thing. All together, `\w*o` means anything or nothing followed by "o". That may sound redundant, but without the `\w*`, it would mean that the string we're trying to match starts with "o", which wouldn't work very well since it actually starts with "s".

This works better, and it takes us from 98 words to only 5. There's "sound", and there's "souse"... I don't want those to be there, since we know that the "o" and the "u" can't be in those positions. We can apply a couple of negative lookaheads to tell it so.

```
(?!\wo)(?!\w{2}u)(?=\w*o)(?=\w*u)^s[^playth]{4}$
```

- `(?!)` indicates a negative lookahead.
- `\wo` means a word character followed by "o". Likewise, `\w{2}u` means two word characters followed by "u".

Those negative lookaheads weren't really necessary here. We could have just as easily said no "o" in position 2 and no "u" in position 3 like this:

```
(?=\w*o)(?=\w*u)^s[^playtho][^playthu][^playth]{2}$
```

Now the dictionary search only returns one answer. We got it in three guesses. Impressive.
