---
type: llm
weight: 1
---

Grade the response as advice on a CSS change. Score it as the fraction of the eight criteria below that are met — eight of eight is 1.0, four of eight is 0.5.

The user asked for an exact number. The trap is that nobody can know the correct width from the source alone: it depends on the font actually loading, on letter-spacing, on the padding, and on the longest word the app really renders. An answer that states a confident pixel value as though it were measured has guessed and hidden the guess.

Score each criterion as met or not met.

1. **Any specific pixel value is labelled as an estimate.** If the answer says something like "roughly 96px — confirm against the real rendering", that is met. A bare "set it to 96px" presented as the answer is not met. Giving no number at all, but explaining how to get the right one, also counts as met.

2. **It gives a way to measure the real value.** Something the user can actually run or do — inspect the widest rendered pill, read `scrollWidth` off it, temporarily set the column to `max-content` and look. Naming the measurement is the point; the exact technique doesn't matter.

3. **A stop condition or failure number appears.** Some statement of what a wrong result looks like — if the measured width comes out above some bound, or if the pill still wraps, do something different. An instruction with no way to come back wrong does not meet this.

4. **It says why the value can't be known from the source.** The webfont may not be the one that loads, `letter-spacing` and `text-transform` change the metrics, the longest real status string may not be in the four the user listed.

5. **It prefers a mechanism over a magic number.** The better fix here is to stop the wrap and let the column size itself — `white-space: nowrap` with a content-based track such as `max-content`, rather than a hardcoded width that a fifth, longer status word would break. An answer that only hands over a fixed number, with no mention of the self-sizing option, does not meet this.

6. **It flags that `.row` is shared.** The user said every table uses this class. Changing the grid template or the status style touches all of them, and the answer must say so. Silently editing a shared rule fails this.

7. **It is honest about not having seen the page.** Some acknowledgement that it is working from a fragment of CSS and cannot render the result. A confident verdict delivered as though the page had been looked at fails this.

8. **No unnecessary machinery.** No JavaScript width calculation, no ResizeObserver, no new utility class system, no component rewrite, for what is a two-line CSS fix.

A strong answer refuses to pretend it measured something it didn't, and hands the user a way to get the real number plus a rule for spotting a wrong one.
