# Changelog

## v0.5.0-fix1 (fork, not an upstream release)

Package version `0.5.0+fix1`. Built on the upstream v0.5.0 release (`0ca5ccc`). The public API is unchanged from 0.5.0: `decode_beams` still returns tuples.

### Fixed

- End-of-utterance language model scoring no longer scores an empty final word as `<unk>`
  when a beam ends on the word delimiter. Before this fix the same text received a
  different score (about -12 nats lower with the default weights) depending on whether its
  beam ended on a letter or on a space, which could change the ranking of the output.
  An empty transcript now scores `alpha * ln P(</s> | <s>)`.
  ([#120](https://github.com/kensho-technologies/pyctcdecode/issues/120),
  upstream PR [#121](https://github.com/kensho-technologies/pyctcdecode/pull/121))
- With a SentencePiece-style vocabulary, the lone `▁` piece no longer forces a word break
  on an unrelated beam (for example `▁the` + `re` decoded as `the re` instead of `there`,
  depending on the order of the vocabulary). Tokens bounded on both sides such as `▁⁇▁`
  still use the old frame-level flag; a per-beam fix is a follow-up.
  ([#122](https://github.com/kensho-technologies/pyctcdecode/issues/122),
  upstream PR [#123](https://github.com/kensho-technologies/pyctcdecode/pull/123))

Both changes come with regression tests that fail without the fix.
