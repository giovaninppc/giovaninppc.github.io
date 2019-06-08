---
layout: post
title: "Dealing with asian characters"
featured-img: Events/Altconf/sticker
categories: [Event,altconf]
---

# Dealing with asian characters
### Based on the speech of Tomoki Yamaguchi, from LINE @ altconf 2019

Localizing an app in an Asian language, can be more difficult than you expect. Not only because its a totally different language, and the characters, _letters_ are different from what you are used to, but interacting, typing and reading is different too.

With that in mind, Tomoki Yamaguchi made a lightning speech on the Altconf 2019 to give a few advices if your a looking into making an app available at an asian country.

#### Remember there are multiple languages

If you are looking to the asian market, you will step into a bunch of different idioms: Korean, Chinese, Japanese, Thai...
And each of these languages have their particularities.

Some of them are left-to-right writings, and even vertical. To this, *auto-layout* already has a lot of tools to automatically change the position of your views, based on *leading* and *trailing* constraints.

One particularity of the Thai language is that the font exceeds the frame boxes of the UILabels, so setting `clipToBounds` to `true` will hide pieces of the labels.

![Thai label example](../assets/img/posts/Events/Altconf/thai.png)

#### Font size

Different of the characters we are used to, which can hav different widths, and usually a character is *half-width*, most of the asian characters are always *full-width*, it means they occupy more space horizontally (similar to a emoji size when compared to a single letter).


#### Character counting

Let's suppose you want to get the number of characters someone typed, to do any kind of validation. Characters can be counted differently depending on your counting strategy.

For example, a single korean character `었` can be more than one character on a different encoding (UTF-8, for example). The same will happen when you count string sizes with emojis. I had a problem with this when creating the fieldbook app, because we needed to split strings, and when counting with emojis it simply didn't work. I had to change the count for the number of characters in UTF-16.

Probably the results from `.count` are likely to be what your are looking for, but its always nice to keep in mind that maybe... it doesn't.

Because of that, you cannot block an input by the number of characters, just validate it after the input.

#### Replacing characters

Since some of the asian characters are actually a combination of multiple characters that form a single one, using `replacingCharacters` or `replacingOccurencesOf` may have unexpected results on these languages. Keep that in mind next time you use these methods.


### Resume
So, we can resume the lessons in here in 4 points:
- Be careful for characters width and height
- Don't clip to bounds a label
- Be careful for counting strategy
- Don't block input while editing
