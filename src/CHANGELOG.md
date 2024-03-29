# CHANGELOG

## 1.3.2 (2019-06-26)

* To help debug [alda-client-java issue
  #38](https://github.com/alda-lang/alda-client-java/issues/38), we will now
  print the exit code baked into an AldaException whenever one is thrown.

## 1.3.1 (2019-05-05)

* Tabs can now be used as whitespace in an Alda score.

* Fixed a bug where attempting to play an empty score (e.g. `c d e`, which
  results in an empty score because there are no instruments) results in a
  NullPointerException.

  The problem was that there were no instruments, so the MIDI system wasn't
  being set up. However, we expect the MIDI system to be set up even if there
  are no instruments and no notes to play, because we use the MIDI Sequencer to
  schedule notes.

## 1.3.0 (2019-04-14)

* New CLI command: `alda export` and REPL command: `:export`

  This command exports an Alda score to another format. Currently, the only
  supported format is MIDI. MIDI files are exported at a resolution of 128
  pulses per quarter note (PPQ).

  The score is provided in the same way as it is for `alda play` and `alda
  parse`: by specifying either a `-f / --file`, a string of `-c / --code`, or
  piping in the code via STDIN.

  ```
  alda export -f my-score.alda -F midi -o my-score.alda
  alda export -c 'piano: c8 d e f' -o piano-notes.mid
  echo 'piano: c8 d e f' | alda export -o piano-notes.mid
  ```

  The `:export` REPL command takes a single argument, the output filename:

  ```
  > :export /path/to/desired-filename.mid
  ```

* The `alda parse` JSON output has a couple of new keys: `tempo/values` and
  `tempo/role`.  These values are used internally to schedule MIDI notes in a
  way that is exportable.

## 1.2.0 (2019-01-19)

* New CLI command: `alda instruments` and REPL command: `:instruments`

  This new command lists all available instruments, which is currently the 128
  instruments in the General MIDI spec, as well as `midi-percussion`.

  NB: Alda does have a number of aliases for these instruments, e.g. `piano` is
  recognized as `midi-acoustic-grand-piano`. These aliases are not included in
  the list.

* Made adjustments to the command that prints for Windows users when running
  `alda update` to update the Alda client.

  For details, see issue
  [#35](https://github.com/alda-lang/alda-client-java/issues/35).

## 1.1.0 (2018-12-01)

New feature alert!

An ["alternate
ending"](http://dictionary.onmusic.org/terms/4798-second_ending_735)-like
feature has been added to the Alda language. The syntax looks like this:

```
piano:
  [ c8 d e f
    [g f e4]'1-3
    [g a b > c4.]'4
  ]*4
```

This allows you to have repeated phrases that can differ on each iteration. In
the example above, each repeat starts with `c8 d e f`; on times 1 through 3
through the repeated phrase, the phrase ends with `g f e4`, whereas on the 4th
time through, the phrase ends with `g a b > c4.`.

Note that these "adjustments" can occur anywhere within the repeated phrase, not
necessarily at the end, making this feature of Alda more flexible than the
"alternate endings" notation seen in sheet music. To illustrate this, here is
another example where the phrase has what you might describe as an "alternate
beginning" and an "alternate middle":

```
piano:
  [ [c8 d e]'1,3 [e8 d c]'2,4
    f
    [g f e]'1-3 [g a b]'4
    > c <
  ]*4
```

Thanks to [elyisgreat] for the initial idea/discussion and [pzxwang] for
implementing the feature! :balloon:

## 1.0.1 (2018-11-24)

Whoops! It turns out that the mechanism we had in place in the Alda client for
determining whether or not your version of Alda is up-to-date had a flaw that
prevents one from updating from Alda 1.0.0-rcXX to Alda 1.0.0!

That's fixed in this version. Also, your client will be able to update to the
latest version now because now the latest version is 1.0.1 instead of 1.0.0. :)

## 1.0.0 (2018-11-24)

This release is functionally equivalent to the previous release, 1.0.0-rc85.
After 3 years of working on a series of 1.0.0 release candidates, the focus has
shifted toward re-architecting Alda in a way that will allow us to make some
substantial improvements. The result of this restructuring will become a new
major version of Alda, version 2.0.0.

The work has only just started on Alda 2.0.0, and it will probably be a long
while before the initial release in the 2.0.0 series. We will continue to
maintain the 1.0.0 series in the meantime. As such, it feels like now is as good
a time as any to release Alda 1.0.0!

## 1.0.0-rc85 (2018-10-28)

* Made internal improvements to the Alda sound engine. Specifically, the sound
  engine now uses a Java MIDI Sequencer to schedule and play MIDI notes, whereas
  before we were using a third party library called JSyn.

  You shouldn't notice any difference in note scheduling, but if you do notice
  any problems, please file an issue in the
  [alda-sound-engine-clj][sound-engine] repo so that we can address them!

* The `schedule` function has been removed from alda.lisp. Scheduled functions
  haven't been very useful since we moved to a client/server architecture, and
  we haven't implemented a way for them to work with the new changes to the
  sound engine. So, we are removing the feature for now. We may re-implement the
  scheduled functions feature in the future, in a way that works with both the
  client/server architecture and the current implementation of the sound engine.

  For more context on this, see
  [alda-core#65](https://github.com/alda-lang/alda-core/pull/65).

## 1.0.0-rc84 (2018-08-25)

* Handled an edge case re: what to display when playing extremely short scores.

* Improved the help text descriptions of the `:info` and `:save` REPL commands.

## 1.0.0-rc83 (2018-08-25)

* Implemented a `--no-color` CLI option, which suppresses ANSI color codes.

## 1.0.0-rc82 (2018-07-01)

* Upgraded JLine dependency to 2.14.6 to fix the platform-specific "Failed to
  construct terminal" part of [this
  issue](https://github.com/alda-lang/alda-sound-engine-clj/issues/13).

## 1.0.0-rc81 (2018-03-08)

* Added an `:info` command to the Alda REPL. It prints some user-readable
  information about the current score, for example:

  ```
  p> :info
  Instruments: piano-sFz6g
  Current instruments: piano-sFz6g
  Events: 6
  Markers: start, one, two, three
  ```

  Thanks, [TBuc], for implementing this new feature!

## 1.0.0-rc80 (2018-02-28)

* Fixed [a minor bug](https://github.com/alda-lang/alda-core/issues/64) in the
  parser: there was an edge case where a "get variable" event wasn't being
  disambiguated from its earlier, less-specific "name" form if the "get
  variable" event happened to be the last thing in the definition of another
  variable.

  Thanks to [elyisgreat] for spotting the bug!

## 1.0.0-rc79 (2018-02-16)

* Fixed a minor bug in the parser that is only evident when using the `alda
  parse` command. The bug was that in certain situations where the code is
  syntactically valid, but there is an error at score build time (e.g. `piano:
  undefined` where a variable is referenced that has not been defined), the
  parser was returning nil instead of throwing an error.

## 1.0.0-rc78 (2018-02-05)

* Fixed [a bug](https://github.com/alda-lang/alda-core/issues/55) where the
  parser did not correctly parse nested events in some situations, for example a
  set-variable expression containing a CRAM expression containing a chord.

  Thanks to [elyisgreat] for reporting this issue!

## 1.0.0-rc77 (2018-02-03)

* On Windows, Alda processes would show up in Task Manager as "A music
  programming language for musicians" due to confusion on my part (as a
  non-Windows user) about the properties of a Windows executable. Now they ought
  to show up as "Alda" so you can easily identify them.

* Fixed a bug in the Alda REPL where the `:play from` and `to` options were
  being completely ignored.

* Fixed a separate bug where the `--from` command-line option to `alda play` was
  being ignored.

* Fixed issues where `!` is not escaped properly in the Alda REPL.

  This is something that had been fixed previously in the Clojure version of the
  Alda REPL, but not ported over when we rewrote the REPL as part of the Java
  client.

  For context, see [this issue](https://github.com/alda-lang/alda/issues/125).

* Enabled persistent history for the Alda REPL. For example, if you start a REPL
  session and enter a bunch of lines of input, then close the session and start
  a new one, you can access the lines you typed in the previous session by
  pressing the Up arrow.

  History is stored in a file at `~/.alda-repl-history`.

## 1.0.0-rc76 (2017-10-30)

* Fixed a bug in the way the program path is determined when a server starts
  workers. (That code lives in alda.util, in this repo.) The bug was showing
  itself when the path to the `alda` (or `alda.exe`) executable contained spaces
  or other special characters. This was causing the worker processes not to
  start in environments where the path to `alda` (or `alda.exe`) contains
  spaces.

  Thanks to [Hemaolle] for the detective work and PR to fix this issue!

## 1.0.0-rc75 (2017-10-17)

* Added a `reference-pitch` (alias: `tuning-constant`) attribute, which will
  have an affect on the pitch of each note in Hz. This number is the desired
  pitch of A4 (the note A in the 4th octave). The default value is 440 Hz.

  However, please note that this value is not currently used. We are still
  figuring out how to tune MIDI notes in Java -- it is more difficult that one
  might expect. If you're interested in helping with this, please let us know!

* Added a `transposition` (alias: `transpose`) attribute, which moves all notes
  (either per-instrument, or globally, depending on whether you are using
  `transpose` or `transpose!`) up or down by a desired number of semitones.
  Positive numbers represent increasing semitones, and negative numbers
  represent decreasing semitones.

  This attribute can be used to make writing parts for [transposing
  instruments](https://en.wikipedia.org/wiki/Transposing_instrument) more
  convenient. To see `transpose` in use, see [this example
  score](https://github.com/alda-lang/alda-core/blob/master/examples/jimenez-divertimento.alda),
  a transcription of a saxophone quartet by Juan Santiago Jiménez.

  Saxophones are transposing instruments; soprano and tenor saxophones are
  considered "Bb" instruments, and alto and baritone saxophones are considered
  "Eb" instruments.  This means that an instrument part written for a baritone
  saxophone, for example, might appear to be written in C major, but when read
  and performed by a baritone saxophonist, it will sound like Eb major, the
  intended key.

Thanks, [pzxwang], for implementing these new features!

## 1.0.0-rc74 (2017-10-14)

* Minor improvement to the new `tempo` function overload and `metric-modulation`
  function: the supplied note-length can be a string representing multiple note
  lengths tied together, e.g.:

  ```
  (tempo "4~16" 120)
  ```

  Thanks to [elyisgreat] for the issue and [pzxwang] for the pull request!

* Fixed a strange issue in the client where, after updating Alda successfully, a
  `java.lang.NoClassDefFoundError` would be printed.

## 1.0.0-rc73 (2017-10-12)

* Improved the timing of waiting for the server to stop before starting a new
  one when running the `alda downup` command.

  Before, this was just guesswork, and often times, the server wouldn't be down
  yet, so when a new server tried to start, it would fail with the message:

  ```
  There is already a server trying to start on this port. Please be patient -- this can take a while.
  ```

  Now, we're actually checking to see when the server stops responding, and
  waiting until that's the case before we try to start a new server.

  Unfortunately, there is still a bit of guesswork here because the message
  above is triggered by (assuming you're running OS X or Linux) a check to see
  if there is an Alda server process in your OS that was started on the same
  port you are trying to use. There is still a brief window of time between when
  the server stops responding to requests and when the process has terminated.

  As such, I think there is room for improvement in the future, and you might
  still see the message above from time to time. But, with this release, things
  should at least be better than they were before.

* Expanded the scope of the `-t` / `--timeout` option to include how long (in
  seconds) the Alda command-line client should wait, after running `alda down`
  or `alda downup`, for confirmation that the server has gone down. The default
  value is still 30 seconds, which should be more than enough time on most
  systems.

## 1.0.0-rc72 (2017-10-09)

* Added an overload of `tempo` that allows you to specify the tempo in terms of
  a note value other than (the default) a quarter note.

  For example, "♩. = 150" can be expressed as:

  ```
  (tempo! "4." 150)
  ```

  (NB: the note value can be either a number or a string containing a
  number followed by dots.)

  It is still OK to leave out the note value; the default behavior is to set the
  tempo relative to a quarter note. "♩ = 60" can still be expressed as:

  ```
  (tempo! 60)
  ```

* Added a new function, `metric-modulation`, which sets the tempo based on a
  [metric modulation](https://en.wikipedia.org/wiki/Metric_modulation), i.e.
  shifting from one meter to another.

  Say, for example, that you're writing a score that starts in 9/8 -- three
  beats per measure, where each beat is a dotted quarter note.

  At a certain point in the piece, you want to transition into a 3/2 section --
  still three beats per measure, but now each beat is a half note. You want the
  "pulse" to stay the same, but now each beat is subdivided into 4 half notes
  instead of 3. How do you do it?

  In traditional notation, it is common to see annotations like "♩. = 𝅗𝅥 " at the
  moment in the score where the time signature changes. This signifies that at
  that moment, the pulse stays the same, but the amount of time that used to
  represent a dotted quarter note now represents a half note. When the orchestra
  arrives at this point in the score, the conductor continues to conduct at the
  same "speed," but each musician mentally adjusts his/her perception of how to
  read his/her part, mentally subdividing each beat into 4 eighth notes instead
  of 3 eighth notes.

  In Alda, you can now express a metric modulation like "♩. = 𝅗𝅥 " as:

  ```
  (metric-modulation! "4." 2)
  ```

Thanks, [pzxwang], for the PR to add these new features!

## 1.0.0-rc71 (2017-10-07)

* Added an `--output` (`-o`) option to the `alda parse` command that allows you
  to specify what should be output. Valid values are:

  * `data` (default) is the map of score data that includes instruments, events,
    etc.

  * `events` is the sequence of events parsed from the score.

  For example (using [`jq`][jq] to pretty-print the JSON for readability):

  ```
  # score data output
  $ alda parse -c 'piano: c d e' | jq .

  {
    "chord-mode": false,
    "current-instruments": [
      "piano-cnLzW"
    ],
    "events": [
      {
        "offset": 500,
        "instrument": "piano-cnLzW",
        "volume": 1,
        "track-volume": 0.7874015748031497,
        "panning": 0.5,
        "midi-note": 62,
        "pitch": 293.6647679174076,
        "duration": 450,
        "voice": null
      },
      {
        "offset": 0,
        "instrument": "piano-cnLzW",
        "volume": 1,
        "track-volume": 0.7874015748031497,
        "panning": 0.5,
        "midi-note": 60,
        "pitch": 261.6255653005986,
        "duration": 450,
        "voice": null
      },
      {
        "offset": 1000,
        "instrument": "piano-cnLzW",
        "volume": 1,
        "track-volume": 0.7874015748031497,
        "panning": 0.5,
        "midi-note": 64,
        "pitch": 329.6275569128699,
        "duration": 450,
        "voice": null
      }
    ],
    "beats-tally": null,
    "instruments": {
      "piano-cnLzW": {
        "octave": 4,
        "current-offset": {
          "offset": 1500
        },
        "key-signature": {},
        "config": {
          "type": "midi",
          "patch": 1
        },
        "duration": {
          "beats": 1,
          "ms": null
        },
        "min-duration": null,
        "volume": 1,
        "last-offset": {
          "offset": 1000
        },
        "id": "piano-cnLzW",
        "quantization": 0.9,
        "duration-inside-cram": null,
        "tempo": 120,
        "panning": 0.5,
        "current-marker": "start",
        "time-scaling": 1,
        "stock": "midi-acoustic-grand-piano",
        "track-volume": 0.7874015748031497
      }
    },
    "markers": {
      "start": 0
    },
    "cram-level": 0,
    "global-attributes": {},
    "nicknames": {},
    "beats-tally-default": null
  }
  ```

  ```
  # events output
  $ alda parse -c 'piano: c d e' -o events | jq .

  [
    {
      "event-type": "part",
      "instrument-call": {
        "names": [
          "piano"
        ]
      },
      "events": null
    },
    {
      "event-type": "note",
      "letter": "c",
      "accidentals": [],
      "beats": null,
      "ms": null,
      "slur?": null
    },
    {
      "event-type": "note",
      "letter": "d",
      "accidentals": [],
      "beats": null,
      "ms": null,
      "slur?": null
    },
    {
      "event-type": "note",
      "letter": "e",
      "accidentals": [],
      "beats": null,
      "ms": null,
      "slur?": null
    }
  ]
  ```

The new "events" output mode can be useful for debugging Alda code that isn't
behaving as expected.

## 1.0.0-rc70 (2017-10-05)

* A behind-the-scenes improvement:

  Increased the duration that a worker will wait if it hasn't heard from the server in a while, before it times out and shuts down.

  This duration used to be 10 seconds, and is now 30 seconds.

  This should help in environments (slow processor, low resources, poor
  networking situation, etc.) where it takes the server longer than 10 seconds
  before it sends a worker its first heartbeat.

  The trade-off is that stale worker processes will hang around for 30 seconds
  instead of 10 seconds, which hopefully won't be much of an inconvenience.

## 1.0.0-rc69 (2017-10-02)

* Added the following modes:

  * `:ionian`
  * `:dorian`
  * `:phrygian`
  * `:lydian`
  * `:mixolydian`
  * `:aeolian`
  * `:locrian`

  These can be used as an alternative to `:major` and `:minor` when specifying a
  key signature.

  For example:

  ```
  piano:
    (key-sig [:d :locrian])
    d8 e f g a b > c d8~1
  ```

  Thanks, [iggar], for this contribution!

## 1.0.0-rc68 (2017-09-29)

* Fixed a parser bug where a rest `r` followed by a newline inside of a variable
  definition would not be considered part of the variable definition.

  Thanks, [elyisgreat], for reporting this issue!

## 1.0.0-rc67 (2017-09-28)

Thanks, [pzxwang] for contributing the changes in this release [this PR](https://github.com/alda-lang/alda-core/pull/50)!

* Non-integer decimal note lengths are now accepted. For example, `c0.5` (or a
  double whole note, in Western classical notation) is twice the length of `c1`
  (a whole note).

* Added a convenient `set-note-length` function to alda.lisp.

  This is an alternative to `set-duration`, which, somewhat unintuitively, sets
  the current duration to its argument as a number of beats.

  To set the note length to a quarter note (1 beat), for example, you can now
  use either `(set-duration 1)` or `(set-note-length 4)`.

## 1.0.0-rc66 (2017-07-28)

* Added a `--history-file` (`-I`) option. It's like `--history`, except that it
  takes the name of a file containing Alda code to be used as history.

## 1.0.0-rc65 (2017-07-16)

* Bugfix: reversed logic in the `alda update` command so that the Windows update
  logic happens if you HAVE Windows, not if you don't have it. Whoops.

## 1.0.0-rc64 (2017-07-16)

* Fixed a bug where the `--history` option to the `alda play` command was being
  ignored if the `--file` option was used or code was being piped into STDIN.

## 1.0.0-rc63 (2017-07-16)

* Prior to this release, the client would almost always exit with an exit code
  of 0, even if there was an error of some kind.

  Starting with this release, Alda has a handful of meaningful [exit codes](https://github.com/alda-lang/alda-client-java/blob/master/src/alda/error/ExitCode.java). Crucially, there is now a distinction between 0 (success) and non-0 (error/failure).

* Minor improvements to a handful of error messages.

* Running `alda update` has been known not to work on Windows because of
  limitations of the OS: Windows apparently will not let you download a new
  version of a program to replace the program while it is running.

  As a workaround, if your OS is Windows and you run `alda update`, we now print
  detailed instructions with a command to run in your terminal that will update
  alda.exe. (Thanks, [jgkamat]!)

## 1.0.0-rc61 (2017-06-22)

* Fixed a bug where running `alda stop` (or `:stop` in the Alda REPL) would not
  stop sound on every channel.

* Fixed a bug where if an error occurs while trying to download a new version of
  Alda, the client would incorrectly report that the update was successful.

* Fixed a bug where, when reading Alda code from STDIN, newlines were omitted.
  This could break scores in some cases, e.g. scores containing single-line
  variable definitions.

* Minor improvements to error handling. In some cases, parse errors were being
  swallowed.

## 1.0.0-rc60 (2017-06-11)

Now, at long last, you can stop playback without restarting the server!

Simply run `alda stop` at the command line or `:stop` in the Alda REPL.

"Pause / resume" functionality is a TODO item, as it will be a little tricky to
implement. It's coming, though! For now, running `:play` after you run `:stop`
will start playback from the beginning of the score again.

## 1.0.0-rc59 (2017-06-05)

This release adds a bunch of features and improvements to the new, faster Alda
REPL. Thanks to [jgkamat] for helping me implement all this stuff!

* Added 6 new REPL commands, each equivalent to its command-line counterpart:

  * `:status` - display the status of the server
  * `:list` - list running Alda processes
  * `:version` - display Alda client + server versions
  * `:down` - stop the server
  * `:up` - start the server
  * `:downup` - restart the server

* When starting the Alda REPL, we now check to see if there is an Alda server
  running. If there is not, then we offer to start one for you.

  We also offer to start the server *after* the REPL has started, in the event
  that the server fails to respond, e.g. if it gets shut down from outside of
  the REPL.

* Added a confirmation prompt when running the `:new`, `:load` or `:quit`
  commands in the REPL. This is to avoid accidentally losing unsaved changes to
  your score.

* Fixed miscellaneous bugs in the REPL related to sending requests to the server
  and receiving responses. This behavior is more reliable now.

* Fixed a bug where if something goes wrong when trying to start a server, the
  program does not exit, but instead waits forever for workers to start. Now,
  the program prints the error and exits.

## 1.0.0-rc58 (2017-05-31)

* Fixed [a bug](https://github.com/alda-lang/alda-core/issues/41), where `r`
  followed by e.g. `]` would trigger a parser error.

## 1.0.0-rc57 (2017-05-30)

This is a big release, packed with exciting features. Things are rapidly
improving, and we're getting ever closer to a 1.0.0 release!

### New Features

* The Alda REPL has been rewritten from the ground-up as a client-side
  application that talks to the Alda server.

  Prior to this release, the Alda REPL was a [standalone application written in
  Clojure](https://github.com/alda-lang/alda-repl-clj). Unfortunately, the
  Clojure runtime has an unpleasantly long start-up time, making it not
  well-suited for command-line applications.

  We've solved this problem in Alda by architecting it as a client/server
  system. The server is a Clojure process that runs in the background and does
  all of the hard work of parsing, building, and playing musical scores written
  in the Alda language. Meanwhile, the Alda client is a lightweight Java program
  that is quick to start. This is nothing new; Alda has had this architecture
  since December 2015. It is what allows you to play an Alda score almost
  immediately from the command-line. One goal of Alda is to be snappy -- to let
  you type some code and hear it within seconds, allowing you to compose music
  spontaneously in your text editor.

  Until now, though, the Alda REPL has been cumbersome to start because it did
  not participate in the client/server system. After running `alda repl`, you
  would have to wait a good while (just long enough to be annoying) as the
  Clojure runtime finished loading and the program set up the audio environment.
  This felt less than snappy, and snappiness is what we strive for in a
  command-line application like Alda.

  The new REPL is a faithful reproduction of the old one, but rewritten as a
  Java frontend to the Clojure backend. The REPL is quick to start, being a
  simple Java program, and provided that you have an Alda server running in the
  background (via the `alda up` command), you can start typing Alda code right
  away and hearing each line played back immediately.

  If you aren't already in the habit of having an Alda server running in the
  background, now might be a good time to start. With our new REPL
  implementation and its fast startup time, it is refreshing to be able to type
  `alda repl` anytime you want and immediately start writing music in Alda in an
  interactive environment.

  The new Alda REPL is, admittedly, still a little bit rough around the edges.
  Please bear with us! We are working to make the experience more comfortable as
  rapidly as we can. As always, feel free to [join us in Slack][slack] and let
  us know what you think of the new REPL and how we can improve it.

* Alda's parser has also been rewritten from the ground up with a special focus
  on performance and the ability to parse scores in an asynchronous, streaming
  fashion.

  You should already notice a big improvement in the time it takes to parse
  larger scores, and thanks to the ability to parse scores asynchronously, we
  may be able to speed up parse times even more in the future.

  Because this is a complete rewrite of the parser, it's likely that there are a
  number of subtle bugs or, at least, things that work differently than before.
  If you encounter any problems, please let us know so that we can fix them!

* The `alda play` command now takes an optional `--history` option, which can be
  used to provide a string of Alda code to be used as context for the string or
  file that you are currently playing.

  For example:

  ```
  $ alda play --history "trumpet: (tempo 200) c8 d e" --code "f g a b > c"
  ```

  The command above will result in the notes f g a b > c being played as eighth notes, on a trumpet, at 200 bpm.

  This feature was developed out of necessity for the new Alda REPL, and it may
  not be immediately useful for most Alda composers, but we are exposing as a
  convenience for anyone who is interested in writing an Alda editor plugin or
  another type of frontend for Alda.

### Bug Fixes

* Rewriting the parser also enabled us to fix [longstanding
  issues](https://github.com/alda-lang/alda-core/issues/12) where if a score
  failed to parse, the error messages would sometimes be misleading and/or
  include incorrect line and column numbers.

  Error messages are now easier to understand, and they include correct line and
  column numbers that point you to the exact location in your score where there
  was a syntax error or other issue.

* You are now less likely to run into a weird bug that can happen when you have
  an Alda server running and you suspend your system (e.g. close the laptop lid)
  and then resume it. The bug causes MIDI playback to be delayed by a number of
  seconds; [this is a bug in
  Java](https://bugs.openjdk.java.net/browse/JDK-8164300), which makes it
  difficult for us to fix it at the source.

  While it is still possible to encounter this strange behavior, we now have a
  good workaround where the server is regularly replacing the processes that
  perform the scores with fresh, new ones. It is now the case that if your
  computer has been out of suspend mode for 20 minutes or more, you should not
  experience the delayed playback bug.

### Breaking Changes

* Because of the parser rewrite, we do not currently have a way to generate
  `alda.lisp` code. This used to be the default behavior of the `alda parse`
  command, which now returns the score JSON by default.

  Generating `alda.lisp` code is now a deprecated feature.

* The Alda client now requires Java 8 or higher. Chances are, if you have a
  fairly recent operating system, you already have a new enough version of Java.
  You can verify your version of Java by running `java -version`. If your
  version of Java is earlier than 1.8 (i.e. Java 7 or below), you can [download
  the latest version of Java](https://www.java.com/en/download).

## 1.0.0-rc56 (2017-01-14)

* Fixed 2 bugs re: `alda list` output:
  * It didn't work correctly on non-OS X systems like Ubuntu due to differences in the `ps` command across Unix distributions.
  * Fixed buggy output when running multiple Alda servers ([#4](https://github.com/alda-lang/alda-client-java/issues/4)).

Major thanks to [tobiasriedling] for both fixes!

## 1.0.0-rc55 (2016-12-07)

* Fixed a regression introduced in 1.0.0-rc53. There was a bug causing worker processes not to cycle after suspending the process, e.g. closing and later re-opening your laptop's lid.

## 1.0.0-rc54 (2016-12-05)

* Fixed [alda-lang/alda-core#27](https://github.com/alda-lang/alda-core/issues/27), a bug where, when using note durations specified in seconds/milliseconds, the subsequent "default" note duration was not being set.

  Thanks to [damiendevienne] for reporting this bug!

## 1.0.0-rc53 (2016-12-03)

* Refactored server code to use [ezzmq](https://github.com/daveyarwood/ezzmq) instead of JeroMQ. The server should still function exactly the same.

* Minor bugfix in server shutdown code: in some cases if the timing was just right, a java.util.concurrent.RejectedExecutionException was being thrown, resulting in the server not shutting down cleanly.

## 1.0.0-rc52 (2016-11-20)

### Minor breaking change

* Removed the `voices` (voice group) event, as [bbqbaron] and I figured out that
  it's not necessary. It turns out that each `voice` event manages its voice
  group implicitly. For more discussion, see
  [alda-lang/alda#286](https://github.com/alda-lang/alda/pull/286).

  This change should not be noticeable, unless you have scores with inline
  Clojure code that use the `voices` event. Now you can simplify code like that
  by just writing `voice` events, without having to group them inside of a
  `voices` event.

## 1.0.0-rc51 (2016-11-19)

This release is technically no different than 1.0.0-rc50, but it's the first release under the new, multi-repository organization of the Alda project. See [#186](https://github.com/alda-lang/alda/issues/186) for more context.

This release was built using the new system where each Alda "component" (e.g. the core library, the client, the server, etc.) is an isolated dependency. This is a more organized system that will make life easier for the developers working on Alda. The end user should not notice any difference.

As a bonus, this new system of organization is a step towards allowing developers to write their own custom Alda components, swap them with the existing ones, and make custom builds.

## 1.0.0-rc50 (2016-11-16)

* Fixed [#255](https://github.com/alda-lang/alda/issues/255). Voices can now be used inside of event sequences and variable definitions.

  Thanks, [bbqbaron], for your work on fixing this issue!

## 1.0.0-rc49 (2016-11-12)

* Fixed a small bug where sometimes, using an undefined variable within a variable definition would not result in an error.

## 1.0.0-rc48 (2016-11-12)

* Fixed [#242](https://github.com/alda-lang/alda/issues/242). In rare cases, error messages were sometimes hidden due to the way we were formatting them inside of the function that displays the error message.

  Thanks to [bbqbaron] for the PR to fix this issue!

## 1.0.0-rc47 (2016-11-09)

* Fixed [#284](https://github.com/alda-lang/alda/issues/284), bugs related to comment spacing. Thanks [elyisgreat] for reporting!

## 1.0.0-rc46 (2016-11-02)

This release includes improvements to the system that assigns instrument parts and groups in an Alda score ([#249](https://github.com/alda-lang/alda/issues/249)). Thanks, [elyisgreat] and [jimcheetham], for helping to talk me through all the different scenarios!

[The documentation](doc/scores-and-parts.md) has also been updated and improved. We hope this will make the rules of instrument part and group assignment easier to understand and remember from here on out.

* Errors are now thrown in the following scenarios, which prior to this release would lead to undefined or confusing behavior:

  * Calls like `foo "bar"` where `foo` is a previously defined nickname (e.g. `piano "foo"`), not a stock instrument.

  * Mixing and matching unnamed vs. named instances of the same instrument in a score.

  * Mixing and matching stock instruments and named instances in the same group. Groups are now explicitly only for grouping named (existing) instances or creating new ones.

  There is a [list of simple rules with examples](doc/scores-and-parts.md#how-instances-are-assigned) in the documentation, which I think is the best way to understand valid vs. invalid usage of parts in an Alda score.

  For the curious, there is also a [comprehensive explanation](doc/instance-and-group-assignment.md) of what happens in different scenarios.

* There is a new dot (`.`) operator for accessing members of groups:

  ```
  violin/viola/cello "strings": c8 d e f
  strings.cello: g1
  ```

## 1.0.0-rc45 (2016-10-15)

This release includes a handful of behind-the-scenes improvements that will make it easier for us to develop Alda.

* The output of `alda status` now includes the server's backend port number. This is the port that the server uses to communicate with its worker processes.

* Added automated integration tests. These tests can be run alone with `boot test --integration`, or along with the existing unit tests with `boot test --all`.

* Both the `alda.server` and `alda.worker` namespaces now include a `*no-system-exit*` dynamic var that is `false` by default, but can be bound to `true` in situations where you're running a server or worker within a Clojure program and you don't necessarily want the entire process to exit when the server or worker does. This makes it possible to run the suite of integration tests without being interrupted by a `System.exit` from a server or worker thread.

* The server now recognizes a new message, `DONE`, that can be sent by a worker process to indicate that it is done working and should not be considered available. When the server receives a `DONE` message from a worker, it removes that worker from its queue, thus ensuring that it will not use that worker to handle any subsequent client requests.

  This message is not currently used during the lifetime of a "production" Alda worker process, but it ended up being useful for the integration test suite.

## 1.0.0-rc44 (2016-10-08)

* The `-v` / `--verbose` option will now provide debug output when starting a `server` or `worker` process in the foreground:

  ```
  $ alda -v -p 27714 server
  16-Oct-08 16:43:24 skeggox.local INFO [alda.server] - Binding frontend socket on port 27714...
  16-Oct-08 16:43:24 skeggox.local INFO [alda.server] - Binding backend socket on port 56885...
  16-Oct-08 16:43:24 skeggox.local INFO [alda.server] - Spawning 2 workers...
  16-Oct-08 16:43:45 skeggox.local DEBUG [alda.server] - Receiving message from frontend...
  16-Oct-08 16:43:45 skeggox.local DEBUG [alda.server] - Forwarding message to worker 00B757D1FC...
  16-Oct-08 16:43:45 skeggox.local DEBUG [alda.server] - Forwarding backend response to frontend...
  ```

  ```
  $ alda -v -p 56885 worker
  Oct 08, 2016 4:45:40 PM com.jsyn.engine.SynthesisEngine start
  INFO: Pure Java JSyn from www.softsynth.com, rate = 44100, RT, V16.7.3 (build 457, 2014-12-25)
  16-Oct-08 16:45:41 skeggox.local INFO [alda.worker] - Worker reporting for duty!
  16-Oct-08 16:45:41 skeggox.local INFO [alda.worker] - Connecting to socket on port 50292...
  16-Oct-08 16:45:41 skeggox.local INFO [alda.worker] - Sending READY signal.
  16-Oct-08 16:45:42 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:43 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:44 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:45 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:46 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:46 skeggox.local DEBUG [alda.worker] - Processing message...
  16-Oct-08 16:45:46 skeggox.local ERROR [alda.worker] - java.lang.Exception: Parse error at line 1, column 19:
  (throw (Exception.
                    ^
  Expected one of:
  ")"
  "("
  "\\"
  "\""
#".|\n|\r"

                   alda.Main.main        Main.java: 230
             alda.AldaWorker.upFg  AldaWorker.java:  12
          alda.Util.callClojureFn        Util.java: 263
                              ...
        alda.worker/start-worker!       worker.clj: 136
        alda.worker/start-worker!       worker.clj: 151
     alda.worker/start-worker!/fn       worker.clj: 188
  alda.worker/start-worker!/fn/fn       worker.clj: 191
                              ...
          alda.worker/eval8377/fn       worker.clj:  95
    alda.worker/handle-code-parse       worker.clj:  72
          alda.parser/parse-input       parser.clj: 284
      alda.parser/remove-comments       parser.clj: 109
    alda.parser/check-for-failure       parser.clj:  54
  java.lang.Exception: Parse error at line 1, column 19:
                       (throw (Exception.
                                         ^
                       Expected one of:
                       ")"
                       "("
                       "\\"
                       "\""
                       #".|\n|\r"


  16-Oct-08 16:45:46 skeggox.local DEBUG [alda.worker] - Sending response...
  16-Oct-08 16:45:46 skeggox.local DEBUG [alda.worker] - Response sent.
  16-Oct-08 16:45:47 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  16-Oct-08 16:45:48 skeggox.local DEBUG [alda.worker] - Got HEARTBEAT from server.
  ```

## 1.0.0-rc43 (2016-09-25)

* Tuned JVM performance to use less unnecessary memory and CPU. See issue [#269](https://github.com/alda-lang/alda/issues/269) for more context, but the TL;DR version is that prior to this release, the Alda server and worker processes were each provisioned with more memory and CPU settings than they needed, causing them to wastefully use up memory/CPU resources that your computer could otherwise be using.

  Big thanks to [feldoh] for this contribution!

## 1.0.0-rc42 (2016-09-24)

* Implemented an internal "worker status" system so that the Alda client has better visibility into the status of the worker process handling a request to play a score. This only affects the `alda play` command.

  `alda play` requests that take longer than 3000 milliseconds will no longer time out and result in an incorrect error about the server being "down." The way it works now is more asynchronous:

  - The client makes a request to play a large score.
  - A worker gets the request and responds immediately. The server includes a note about which worker it is so that the client can follow up with the worker for status.
  - The client repeatedly sends requests for the status of that worker, and the worker asynchronously responds to let the client know if it is parsing, playing, done, or if there was some error.
  - As the client receives updates, it prints them to the console:

  ```bash
  $ alda play -c 'bassoon: (Thread/sleep 5000) o2 d1~1~1~1~1'
  [27713] Parsing/evaluating... # immediate response
  [27713] Playing... # 5 seconds later
  ```

### Breaking Changes

* To accomplish the above, I had to make a couple of minor adjustments to the
  ZeroMQ message structure. If you are writing your own Alda client, server, or
  worker, you may need to adjust the way messages are handled slightly. The Alda
  ZeroMQ Architecture doc has been updated to reflect these changes to the
  message structure.

## 1.0.0-rc41 (2016-09-18)

The focus of this release is to use less CPU when starting an Alda server and worker processes. Thanks to [0atman] for reporting [this issue](https://github.com/alda-lang/alda/issues/266)!

* Prior to this release, when you started up and Alda server, it would simultaneously start all of its worker processes, which was heavy on the CPU. It turns out that waiting 10 seconds between starting each worker decreases CPU usage significantly, so that's what we're doing now.

* Because it can take a little longer now for all workers to start (add an additional 10 seconds for each worker after 1), the server will now check on the current number of workers every 60 seconds instead of every 30 seconds.

* The default number of workers is now 2 instead of 4. This uses significantly less CPU, and should be adequate for most Alda users. Remember that if you desire more workers, you can use the `--workers` option when starting the server, e.g. `alda --workers 4 up` for 4 workers.

* Removed the temporary `--cycle-workers` option introduced in 1.0.0-rc39. Now that CPU usage will not be as much of a problem, it is safer to cycle workers by default.

## 1.0.0-rc40 (2016-09-17)

* Bugfix: prior to this release, if evaluating an Alda score resulted in any error, the client would report `ERROR Invalid Alda syntax.`. Now it will report the error message. (This may have been working at some point and then regressed in a recent release. Sorry about that!)

## 1.0.0-rc39 (2016-09-17)

* There is [a CPU usage issue](https://github.com/alda-lang/alda/issues/266) that can cause poor performance when cycling out workers, a feature that was implemented in the last release.

  The real solution is going to be to fix the CPU usage problem, but in the meantime, this release makes the worker-cycling behavior introduced in the last release an opt-in feature.

  To opt in, include the `--cycle-workers` option when starting the server:

  ```
  alda --cycle-workers up
  ```

  Note that even when _not_ using this option, the worker cycling will still eventually happen (within 30 seconds of your computer coming out of suspended state). When it has been 10+ seconds since the worker was last active (e.g. if your computer was in suspended state), each worker will detect this and shut itself down. Every 30 seconds, the server checks to make sure it has the correct number of workers, so it will replace the workers that went down.

  The change with this release is that the workers will not immediately be replaced upon coming out of suspended state. It would be better if they were immediately replaced, but we will have to fix the CPU usage problem first. (If you're good at profiling / determining the cause of high CPU usage, we could use your help!)

## 1.0.0-rc38 (2016-09-14)

* Fixed issue [#160](https://github.com/alda-lang/alda/issues/160), re: MIDI audio being delayed after suspending and bringing back Alda processes, e.g. after closing and re-opening your laptop lid.

  Now that we have a server/workers architecture, the solution to this is for the server and workers to each detect when the system has been suspended, and act accordingly:

  - Each existing worker will shut down.
  - The server will clear out its worker queue and start up new workers.

  This is not a perfect solution in that if you close your laptop and then reopen it in under 10 seconds, the suspension may not be properly detected, so you might still have the same "bad" workers as before with delayed audio. If you ever observe this behavior, you can always fix it by restarting the server and workers via `alda downup`.

* Fixed a minor bug where workers may be sending too many heartbeats, potentially resulting in poor performance. Now they should only send heartbeats once per second.

## 1.0.0-rc37 (2016-09-08)

* Continuing from the `ulimit -n`-related `alda up` issues noted in the previous release, this makes it so that we check the number of available workers every 250 ms instead of 100 ms. This does not solve the "Too many open files" issue, but as a workaround it appears to stop the error from happening when your `ulimit -n` is 256 and you're starting the default number (4) of workers.

  See issue [#261](https://github.com/alda-lang/alda/issues/261) for further discussion.

## 1.0.0-rc36 (2016-09-05)

This release makes a handful of minor improvements to the way we're managing socket connections. The goal is avoid the `java.io.IOException: Too many open files` error, (issue [#261](https://github.com/alda-lang/alda/issues/261)) which can occur when Alda tries to open more socket connections than your system allows.

* Updated to JeroMQ 0.3.5, which includes some behind-the-scenes resource management improvements.

* Ensured that any time Alda forks a background process, it closes its input, output, and error streams in order to free up those resources that we aren't using.

* When you run `alda up`, the client repeatedly sends requests to get an update on when the server is up so that it can notify you, then it does the same thing with the worker processes. The worker processes can take a while to spin up, so the client ends up sending a lot of requests to the server for the status of the worker processes. Prior to this release, each of these requests created a new connection, causing the "open files" limit to be quickly met. As of this release, a single Alda CLI command (like `alda up`) will only create a single connection and reuse it.

I tested this release by setting my `ulimit -n` to 256 and saw some definite improvement, but still occasionally saw the "Too many open files" error happening, especially when trying to use 4 Alda worker processes. I'll continue to tinker with this and see if there are other improvements to be made.

> If you have a low `ulimit -n` (256 is quite low; I would recommend 10240 at least) and would like to get better performance from Alda, you can set it higher for your current terminal session by running `ulimit -n 10240`. However, this setting will go away once you close your terminal window, so if you're looking for a more permanent solution, [the Riak docs](https://docs.basho.com/riak/kv/2.1.4/using/performance/open-files-limit/) happen to have an excellent description of how to set your open file limit more permanently.

## 1.0.0-rc35 (2016-09-04)

> Quick TL;DR of what you should do when updating to this release:
>
> - _Before_ updating, run `alda down` to stop your Alda server if you have one running. If you don't do this before updating, you may need to find and kill the process manually.
> - Run `alda update` to update.
> - `alda start`, `alda stop` and `alda restart` have been renamed. From now on, you will need to use the commands `alda up`, `alda down` and `alda downup` or `alda start-server`, `alda stop-server` and `alda restart-server` instead. See below for more details about this change.

* This release adds more internal architecture improvements, further leveraging ZeroMQ to break out the functionality of Alda into separate, specialized background processes.

  Prior to this release, Alda was a two-process system:

  - a **server** that runs in the background and plays scores on demand, and
  - a command-line **client** used to make requests to the server

  To help with performance issues related to playing multiple scores at once, each request is now handled by a separate **worker** process. The **client**/**server** relationship is still exactly the same; as an Alda user, you never need to communicate directly with the workers or worry about them in any way; they are solely the responsibility of the server.

  When starting Alda with `alda up`, you will now see the server come up much faster than before, followed by a more typical wait for the first worker to become available:

  ```bash
  $ alda up
  [27713] Starting Alda server...
  # short wait
  [27713] Server up ✓
  [27713] Starting worker processes...
  # longer wait
  [27713] Ready ✓
  ```

  When you see the "Ready" line, you will be able to use Alda the same as before, e.g.:

  ```bash
  $ alda play -c 'bassoon: o2 d8 e (quant 33) f+ g (quant 90) a2'
  [27713] Playing...
  ```

  You can safely leave the server running in the background and it will manage its own supply of workers, replacing any that become unresponsive. Similarly, a worker process will shut itself down if the server becomes unresponsive for any reason.

  By default, an Alda server maintains a pool of 4 workers. If you try to play more than 4 scores at the same time, the server will respond that there are no workers available and ask you to wait until the next worker becomes available:

  ```bash
  $ alda play -c 'bassoon: o2 d1~1~1~1~1'
  [27713] Playing...

  $ alda play -c 'bassoon: o2 d1~1~1~1~1'
  [27713] Playing...

  $ alda play -c 'bassoon: o2 d1~1~1~1~1'
  [27713] Playing...

  $ alda play -c 'bassoon: o2 d1~1~1~1~1'
  [27713] Playing...

  $ alda play -c 'bassoon: o2 d1~1~1~1~1'
  [27713] ERROR All worker processes are currently busy. Please wait until playback is complete and try again.
  ```

  If you desire more or fewer workers, you can use the `-w`/`--workers` option when starting the server:

  ```bash
  $ alda -w 6 up
  [27713] Starting Alda server...
  [27713] Server up ✓
  [27713] Starting worker processes...
  [27713] Ready ✓
  ```

  Be aware, however, that running a larger amount of servers uses more of your CPU. I think the default of 4 workers should be comfortable for most users.

* Running `alda status` will now show you the number of workers currently available:

  ```bash
  $ alda status
  [27713] Server up (4/4 workers available)

  $ alda play -f examples/hello_world.alda
  [27713] Playing...

  # one worker is busy playing hello_world.alda
  $ alda status
  [27713] Server up (3/4 workers available)

  # after playback completes, the worker is available again
  $ alda status
  [27713] Server up (4/4 workers available)
  ```

* Running `alda list` will now list all Alda processes, including servers and workers.

  > This command is currently only available on Unix/Linux systems.
  >
  > Please let us know if you can help us implement the same functionality for Windows!

  ```bash
  $ alda list
  [27713] Server up (4/4 workers available)
  [58678] Worker (pid: 85804)
  [58678] Worker (pid: 85805)
  [58678] Worker (pid: 85806)
  [58678] Worker (pid: 85807)
  ```

* If an Alda worker process ever encounters an error, it logs the error to `~/.alda/logs/error.log`. The logs in this folder are rotated weekly, making cleanup easy if you do not want to keep old error logs.

* Fixed [issue #243](https://github.com/alda-lang/alda/issues/243). Prior to the infrastructure improvements made in this release, the server was a single process trying to play multiple scores at once, which caused the sound to break up. Having each worker be a separate process that only handles one score at a time results in more reliable playback. Thanks to [goog] for reporting this issue!

* Fixed [issue #258](https://github.com/alda-lang/alda/issues/258). This issue was also related to the previous single-server/worker system. While busy handling one request, the server was not able to handle the next right away, causing the request to be re-submitted and handled more than once, resulting in scores playing more than once with unexpected timing. Now that the server's sole responsibility is to maintain worker processes and forward them requests, it can handle requests a lot faster, and the client no longer needs to submit them more than once. Thanks to [elyisgreat] for reporting this issue!

### Breaking Changes

* In order to enable the new server/workers architecture, we had to remove a handful of commands that relied on "stateful server" behavior. An Alda server no longer has a notion of the "current score" you are working with. Instead, the state of your score should be maintained in the form of Alda code in a file; the functionality of the removed commands is better left up to your file system and text editor.

  The following commands have been removed:

  - `alda play` (without a `--file` or `--code` argument, this command used to play the "current score" in memory on the server)
  - `alda play --append` (this used to play a file or string of code and append it to the "current score").
  - `alda append` / (same as `alda play --append`, but just appended to the "current score" without playing the new code)
  - `alda score` (used to display the "current score")
  - `alda info` (used to display information about the "current score")
  - `alda new` (used to delete the "current score" and start a new one)
  - `alda load` (used to replace the "current score" with the contents of a file or string of code)
  - `alda save` (used to save the "current score" to a file)
  - `alda edit` (used to open the "current score" in your text editor of choice)

* In anticipation of a future `alda stop` command that will [stop playback](https://github.com/alda-lang/alda/issues/69) instead of stopping the server, the previous `alda stop` command (which would stop the server) has been renamed `alda stop-server`. By analogy, `alda start` has been renamed to `alda start-server` and `alda restart` has been renamed to `alda restart-server`. I recommend using the shorter aliases `alda up`, `alda down` and `alda downup` to start, stop, and restart the server.

## 1.0.0-rc34 (2016-08-23)

* This release adds a few safeguards against inadvertently starting more than one server process for the same port and ending up in a situation where you have potentially many Alda server processes hanging around, only one of which will be able to serve on that port. Thanks to [elyisgreat] for reporting this issue ([#258](https://github.com/alda-lang/alda/issues/258)).

  Safeguards include:

  * Increasing the "server startup timeout" back to 30 seconds. It turns out that I accidentally lowered it to 15 seconds in 1.0.0-rc32, and on some computers an Alda server can take longer than 15 seconds to start. 30 seconds seems like a better default.

    This timeout is the number of seconds, after running `alda up`, before the client gives up on waiting for the server to start, assumes something went wrong, and tells you that the server is down.

  * If your computer is particularly slow and it takes longer than 30 seconds to start an Alda server, you can increase the timeout by supplying a new global option `-t` / `--timeout`:

    ```bash
    alda --timeout 45 up # wait 45 seconds before giving up
    ```

    This should only be necessary on the slowest of computers, but the option is there if you need it.

  * If you do experience a timeout and you try to start the server again, a helpful message is now displayed, letting you know that there is already a server trying to start on that port:

    ```
    [27713] There is already a server trying to start on this port. Please be patient -- this can take a while.
    ```

    ...and the client does not attempt to start a duplicate server for that port.

    If you wait long enough, the existing server should be up and ready to play scores. You can check the status of the server by running `alda status`.

## 1.0.0-rc33 (2016-08-20)

* Made the server a little more resilient to failure. There are a handful of tasks that are handled in parallel, like setting up the audio systems (e.g. MIDI) for a score. This involves running the tasks in parallel background threads and waiting for them to complete. This works fine when the tasks are successful, but if they fail, then the server ends up waiting forever and needs to be restarted in order to serve any more requests.

  Starting from this release, we are able to notice when the background tasks fail and re-throw the error so that the server can report the error back to the client and will be ready to handle any subsequent requests.

  One example of a background task that can fail is if you try to play an Alda score with multiple MIDI percussion instruments. There is only one MIDI percussion channel available, so this will throw a "Ran out of MIDI channels! :(" error. on the background thread that loads the instruments. Before this release, the server would just lock up when you tried to play such a score; now it will report the error back to the client.

* Added more debug logging when running a server in debug mode.

## 1.0.0-rc32 (2016-08-18)

* The major change in this release is that we replaced the internal implementation of the server, previously a resource-intensive HTTP server, with a more lightweight [ZeroMQ](http://zeromq.org) REQ/RES socket. This means lower overhead for the server, which should translate to better performance.

  > We are using [the pure Java implementation of ZeroMQ](https://github.com/zeromq/jeromq), which means no native dependencies to install. You can update Alda and it'll just work™.

  This is a transparent change; you should not notice any difference in your usage of Alda via the `alda` command-line client, besides seeing better performance from the server.

* Bugfix: I just realized that the `alda play` command's options `--from` and `--to` were not actually hooked up so that they did anything useful. Oops! Sorry about that. As of this release, you can use them to specify start and end points for playing back the score, either as marker names or minute/second markings. For real, this time.

* I discovered that the `-y/--yes` auto-confirm option for the `alda play` and `alda parse` commands was never being used, so I removed the option from both commands.

  When I first wrote the Alda client, the `play` and `parse` commands would overwrite whatever score you were currently working on in memory. So, if you had unsaved changes, the client would warn you that you were about to overwrite the in-memory score and lose your unsaved changes, and ask you for confirmation. The `-y` flag was there as a convenience so that if you didn't care about the score in memory and you just wanted to play a score file, you could include the flag and skip the unsaved changes warning and prompt.

  A while back, we improved the experience so that the default behavior of `alda play` and `alda parse` is to play or parse a score separately without affecting the working score you might have in-memory. The client never needs to prompt you for confirmation anymore when using these two commands, so the `-y` flag is no longer necessary.

  > Note: The `-y` flag is still available for other commands that _can_ affect the score in memory, such as `alda load` and `alda down`.

### Breaking Changes

* If you happened to be using the HTTP server directly (i.e. via `curl` instead of the Alda client), you will find that this will no longer work, since the server does not respond to HTTP requests anymore.

  > If you still wish to have lower-level access to an Alda server, you can use ZeroMQ to make a REQ socket connection to `tcp://*:27713` (assuming 27713 is the port on which the Alda server is running), send a request with a JSON payload, and receive the response. For more information about ZeroMQ, read [the ZeroMQ guide](http://zguide.zeromq.org/page:all), it's excellent!

* Before, if you ran a command that requires the Alda server to be up, for example `alda play -f somefile.alda`, and the Alda server is not up, the client would go ahead and start it for you. We have removed this behavior; now, you will get an error message notifying you that the server is down and explaining that you can start the server by running `alda up`. We are working toward making the server failsafe enough that you can leave it running in the background forever and forget about it (making it more of a daemon), so hopefully you will not need to run `alda down` or `alda up` very often.

* When running `alda score -m` (to show the data representation of the current score) or `alda parse -m -f somefile.alda` (to show the data representation of the score in a file), the output is now JSON instead of [EDN](https://github.com/edn-format/edn).

  The reason for this is that JSON is more widely supported and there are a variety of useful command-line tools like [`jq`][jq] for working with JSON on the command line.

  For example, if you have `jq` installed, you can now run this command to pretty-print an Alda score map:

  ```
  $ alda score -m | jq .
  {
    "chord-mode": false,
    "current-instruments": [
      "flute-nUVei"
    ],
    "score-text": "(tempo! 90)\n(quant! 95)\n\npiano:\n  o5 g- > g- g-/f > e- d-4. < b-8 d-2 | c-4 e- d- d- <b-1/>g-\n\nflute:\n  r2 g-4 a- b-2. > d-32~ e-16.~8 < b-2 a- g-1\n",
    "events": [
      {
  ...

  ```

  Or you can output the offsets of every event in the score:

  ```
  $ alda score -m | jq '.events[] .offset'
  666.666687117188
  2000.0000610351562
  5333.333465576172
  1333.3333740234375
  1333.3333740234375
  4666.666748046875
  4750.00008392334
  ```

## 1.0.0-rc31 (2016-08-12)

* Fixed a bug where the Alda server was spinning its wheels for a long time trying to parse certain scores when played via the command-line.

## 1.0.0-rc30 (2016-08-11)

* Removed the `--pre-buffer` and `--post-buffer` options, as I realized they weren't necessary. For details, see [this issue comment](https://github.com/alda-lang/alda/issues/26#issuecomment-239345440).

* Improved the timing of the behind-the-scenes clean-up that occurs after a score is finished playing.

* Fixed a bug where in some cases, that clean-up might not have been happening.

* Implemented a minor optimization to the way events are scheduled; earlier events are now given priority scheduling, making it less likely for events to be skipped during playback.

## 1.0.0-rc29 (2016-07-26)

* Fixed a bug where using chords with implicit note duration within a CRAM expression would result in incorrect timing.

## 1.0.0-rc28 (2016-07-25)

More variable-related bugfixes in this release:

* Setting a variable that includes a repeated event sequence, e.g. `foo = [c c]*3` no longer throws an error.

* Setting a variable that includes an empty Clojure expression, e.g. `foo = () c d e f` no longer throws an error.

Shout-out to [elyisgreat] for finding all these bugs!

## 1.0.0-rc27 (2016-07-24)

* Behind the scenes change: simplified the code that handles getting and setting variables. See [ebf2a4](https://github.com/alda-lang/alda/commit/ebf2a42c78e5be3ef1cbedc4c2579a7bd72d08bb) for more details.

  TL;DR: when you use a variable inside the definition of another variable, now the value of the variable is retrieved right away, instead of waiting until you try to get the value of the outer variable.

  For the most part, you should not notice any changes to the way that variables work, aside from the Alda score map (i.e. the result of using `alda parse -m`, or `:map` in the Alda REPL.) no longer containing the key `:variables` and having a greatly simplified `:env` key that is no longer a nested map, but a simple lookup of variables to their values.

* Trying to define a variable where the value includes an undefined variable now throws an error immediately, instead of waiting until you get the value of the variable. For example, the following score will now fail immediately because `bar` isn't defined:

  ```
  foo = bar
  ```

  Whereas before this release, the "undefined variable" error would not be thrown until you tried to use `foo` in an instrument part.

## 1.0.0-rc26 (2016-07-24)

* Minor bugfix: in some situations, undefined variables were not being appropriately caught. Now, an error will always be thrown if you try to use a variable that hasn't been defined.

* Empty event sequences, e.g. `[]` are now supported. Hey, why not? ㄟ( ･ө･ )ㄏ

* Defining a previously undefined variable as itself, e.g. `foo = foo` used to trigger a stack overflow. Now it throws an "undefined variable: foo" error, which is more helpful.

## 1.0.0-rc25 (2016-07-23)

### Breaking Changes

* There was a breaking change in one of the last few versions that may not have been documented, where it is no longer acceptable to place barlines in between notes instead of whitespace, like this:

  ```
  c|d|e
  ```

  To improve parsing flexibility, make the parser easier to develop, avoid unexpected bugs in future releases, and enforce a convention that will make Alda scores easier to read, whitespace is now required between all events, including not only notes, chords, event sequences, etc., but also barlines.

* As of this release, it is no longer valid to end a note duration with a barline, e.g.:

  ```
  c1| d
  ```

  The correct way to write the above is:

  ```
  c1 | d
  ```

  The reasoning for this is the same as the point above -- events (including barlines) must be separated by whitespace.

* Note that there is one situation that is a minor exception where it is acceptable for a barline to not be preceded/succeeded by whitespace:

  ```
  c1~|1~|1 d
  ```

  In this case, there are technically two "events" -- the C note and the D note. Alda's parser reads the C note as a single event where the pitch is C and the duration is 3 whole notes tied across 2 bar lines.

  If this is confusing, note that it is also acceptable to put spaces in between the barlines:

  ```
  c1 | ~1 | ~1 d
  ```

  The last two examples are equivalent.

## 1.0.0-rc24 (2016-07-21)

* Refines the behavior of variables when used within the definitions of other variables. The "scope" of a variable is now tracked when it is defined. This makes it possible to do things like this:

  ```
  foo = c d e
  foo = foo f g

  piano: foo  # expands to "c d e f g"
  ```

  It also makes it so that you won't run into unexpected bugs when redefining a variable that another variable depends on, as in this example:

  ```
  foo = c d e
  bar = foo f g

  foo = c

  piano: bar  # still "c d e f g," because that's what it was when it was defined
  ```

### Breaking Changes

* Prior to this release, trying to use a variable that wasn't defined would result in that variable being ignored and the rest of the score processed as usual.

  Now, trying to use a variable that hasn't been defined results in an error being thrown. This will help score writers catch unintentional bugs caused by misspelling variable names, etc.

## 1.0.0-rc23 (2016-07-18)

* Fixes another bug related to `>` and `<` being used back-to-back without spaces in between.

* An Alda score containing only Clojure code (i.e. no instrument parts) is now considered a valid score. For example, the following is a valid Alda score:

  ```
  (part "bassoon"
    (for [x (map (comp keyword str) "cdefgab")]
      (note (pitch x) (ms 100))))
  ```

## 1.0.0-rc22 (2016-07-18)

* The previous release inadvertently made it invalid for a note to be followed immediately (no spaces) by an octave up/down operator, e.g. `c<`. This release makes it acceptable again to do that.

  (It's also still acceptable to follow an octave up/down operator immediately with a note, e.g. `>c`, and to sandwich a note between octave up/down operators, e.g. `>c<`.)

### Breaking Changes

* Because the `=` sign is used to define variables, the natural sign, which used to be `=`, has been changed to `_` to avoid confusion. If you have any scores using naturals, make sure you change the `=`'s to `_`'s to avoid parse errors.

## 1.0.0-rc21 (2016-07-18)

* Variables implemented! This simple, but powerful feature allows you to define named sequences of musical events and refer to them by name. You can even define variables that refer to other variables, giving you the means to build a score out of modular parts. For details, see [the docs](doc/variables.md).

* Minor parsing performance improvements.

### Breaking Changes

* In order to avoid conflicts between variable names and multiple notes squished together (e.g. "abcdef"), the rules are now more rigid about spaces between notes. Multiple letters back-to-back without spaces in between is now read as a variable name. Multiple letters with spaces between each one (e.g. "a b c d e f") is read as multiple notes.

  Note that this does not only apply to single letters, but to anything else that the parser considers a "note" -- this includes notes that include a duration (e.g. "a4"), notes that include multiple durations tied together (e.g. "a4~4~4"), and notes that end in a final tie/slur, indicating that the note is to be played legato (e.g. "a~").

  A couple of Alda example scores contained examples that broke the "mandatory space between notes" rule, and had to be changed. For example, `awobmolg.alda` contained the following snippet representing 5 notes under a slur:

  ```
  b4.~b16~a~g~a
  ```

  The parser was trying to read this as the (legato/slurred) note `b4.~` followed immediately by another note, `b16`. This is now explicitly not allowed. The example was changed to the following (valid) syntax:

  ```
  b4.~ b16~ a~ g~ a
  ```

## 1.0.0-rc20 (2016-06-20)

* Fixed a regression caused by 1.0.0-rc19, which was causing scores not to parse correctly.

## 1.0.0-rc19 (2016-06-19)

* Parsing/playing Alda scores is now significantly faster, thanks to some optimizations to the parser. (Many thanks to [aengelberg] for your help with this!)

* Fixed [#235](https://github.com/alda-lang/alda/issues/235) -- when trying to parse (as a `--map`) or play a very large score, a "Method code too large!" error was occurring because of the way that scores were parsed into Clojure code as an intermediate form and then `eval`'d. Now, the parser transforms an Alda score into the score map (i.e. the output of `alda parse --map`) directly.

  Even though parsing and playing scores no longer does so by generating Clojure code, it is still possible to generate the Clojure code, if desired, by using `alda parse --lisp`.

  This should be a transparent change; both ways of parsing should still work the same as before.

### Breaking Changes

* Part of the process of optimizing the Alda parser was removing cases of ambiguity. A consequence of doing this is that the `duration` grammar rule no longer includes a `barline` or `slur` at the end. Instead, a `barline` must stand on its own (after the `note` containing the `duration`), and a `slur` must be part of a `note` instead of its `duration`.

  In other words, when writing alda.lisp code, whereas it used to be possible to do things like this:

  ```
  (note (pitch :c)
        (duration (note-length 4)
                  (barline)))

  (note (pitch :c)
        (duration (note-length 4)
                  :slur))
  ```

  Now you can only do it like this:

  ```
  (note (pitch :c)
        (duration (note-length 4)))
  (barline)

  (note (pitch :c)
        (duration (note-length 4))
        :slur)
  ```

  This is a trivial change, but I thought I'd mention it just in case anyone runs into it.

## 1.0.0-rc18 (2016-05-28)

* Fixes a bug related to the fix introduced in 1.0.0-rc17. For more details, see [issue #231](https://github.com/alda-lang/alda/issues/231).

## 1.0.0-rc17 (2016-05-21)

* Fixed issue #27. Setting note quantization to 100 or higher no longer causes issues with notes stopping other notes that have the same MIDI note number.

  Better-sounding slurs can now be achieved by setting quant to 100:

      bassoon: o2 (quant 100) a8 b > c+2.

## 1.0.0-rc16 (2016-05-18)

* Fixed issue #228. There was a bug where repeated calls to the same voice were being treated as if they were separate voices. Hat tip to [elyisgreat] for catching this!

## 1.0.0-rc15 (2016-05-15)

* This release includes numerous improvements to the Alda codebase. The primary goal was to make the code easier to understand and more predictable, which will make it possible to improve Alda and add new features at a much faster pace.

  To summarize the changes in programmer-speak: before this release, Alda evaluated a score by storing state in top-level, mutable vars, updating their values as it worked its way through the score. This code has been rewritten from the ground up to adhere much more to the functional programming philosophy. For a better explanation, read below about the breaking changes to the way scores are managed in a Clojure REPL.

* Alda score evaluation is now a self-contained process, and an Alda server (or a Clojure program using Alda as a library) can now handle multiple scores at a time without them affecting each other.

* Fixed issue #170. There was a 5-second socket timeout, causing the client to return "ERROR Read timed out" if the server took longer than 5 seconds to parse/evaluate the score. In this release, we've removed the timeout, so the client will wait until the server has parsed/evaluated the score and started playing it.

* Fixed issue #199. Local (per-instrument) attributes occurring at the same time as global attributes will now override the global attribute for the instrument(s) to which they apply.

* Using `@markerName` before `%markerName` is placed in a score now results in a explicit error, instead of throwing a different error that was difficult to understand. It turns out that this never worked to begin with. I do think it would be nice if it were possible to "forward declare" markers like this, but for the time being, I will leave this as something that (still) doesn't work, but that we could make possible in the future if there is demand for it.

### Breaking Changes

* The default behavior of `alda play -f score.alda` / `alda play -c 'piano: c d e'` is no longer to append to the current score in memory. Now, running these commands will play the Alda score file or string of code as a one-off score, not considering or affecting the current score in memory in any way. The previous behavior of appending to the current score is still possible via a new `alda play` option, `-a/--append`.

* Creating scores in a Clojure REPL now involves working with immutable data structures instead of mutating top-level dynamic vars. Whereas before, Alda event functions like `score`, `part` and `note` relied on side effects to modify the state of your score environment, now you create a new score via `score` (or the slightly lower-level `new-score`) and update it using the `continue` function. To better illustrate this, this is how you used to do it **before**:

  ```
  (score*)
  (part* "piano")
  (note (pitch :c))
  (chord (note (pitch :e)) (note (pitch :g)))
  ```

  Evaluating each S-expression would modify the top-level score environment. Evaluating `(score*)` again (or a full score wrapped in `(score ...)`) would blow away whatever score-in-progress you may have been working on.

  Here are a few different ways you can do this **now**:

  ```clojure
  ; a complete score, as a single S-expression
  (def my-score
    (score
      (part "piano"
        (note (pitch :c))
        (chord
          (note (pitch :e))
          (note (pitch :g))))))

  ; start a new score and continue it
  ; note that the original (empty) score is not modified
  (def my-score (new-score))

  (def my-score-cont
    (continue my-score
      (part "piano"
        (note (pitch :c)))))

  (def my-score-cont-cont
    (continue my-score-cont
      (chord
        (note (pitch :e))
        (note (pitch :g)))))

  ; store your score in an atom and update it atomically
  (def my-score (atom (score)))

  (continue! my-score
    (part "piano"
      (note (pitch :c))))

  (continue! my-score
    (chord
      (note (pitch :e))
      (note (pitch :g))))
  ```

  Because no shared state is being stored in top-level vars, multiple scores can now exist side-by-side in a single Alda process or Clojure REPL.

* Top-level score evaluation context vars like `*instruments*` and `*events*` no longer exist. If you were previously relying on inspecting that data, everything has now moved into keys like `:instruments` and `:events` on each separate score map.

* `(duration <number>)` no longer works as a way of manually setting the duration. To do this, use `(set-duration <number>)`, where `<number>` is a number of beats.

* The `$` syntax in alda.lisp (e.g. `($volume)`) for getting the current value of an attribute for the current instrument is no longer supported due to the way the code has been rewritten. We could probably find a way to add this feature back if there is a demand for it, but its use case is probably pretty obscure.

* Because Alda event functions no longer work via side effects, inline Clojure
  code works a bit differently. Basically, you'll just write code that returns
  one or more Alda events, instead of code that produces side effects (modifying
  the score) and returns nil. See
  [entropy.alda](https://github.com/alda-lang/alda-core/blob/master/examples/entropy.alda)
  for an example of the way inline Clojure code works starting with this
  release.

## 1.0.0-rc14 (2016-04-01)

* Command-specific help text is now available when using the Alda command-line client. ([jgerman])

  To see a description of a command and its options, run the command with the `-h` or `--help` option.

  Example:

      $ alda play --help

      Evaluate and play Alda code
      Usage: play [options]
        Options:
          -c, --code
             Supply Alda code as a string
          -f, --file
             Read Alda code from a file
          -F, --from
             A time marking or marker from which to start playback
          -r, --replace
             Replace the existing score with new code
             Default: false
          -T, --to
             A time marking or marker at which to end playback
          -y, --yes
             Auto-respond 'y' to confirm e.g. score replacement
             Default: false

## 1.0.0-rc13 (2016-03-10)

* Setting quantization to 0 now makes notes silent as expected. (#205, thanks to [elyisgreat] for reporting)

## 1.0.0-rc12 (2016-03-10)

* Improve validation of attribute values to avoid buggy behavior when using invalid values like negative tempos, non-integer octaves, etc. (#195, thanks to [elyisgreat] for reporting and [jgkamat] for fixing)

## 1.0.0-rc11 (2016-03-08)

* Fix parsing bugs related to ending a voice in a voice group with a certain type of event (e.g. Clojure expressions, barlines) followed by whitespace. (#196, #197 - thanks to [elyisgreat] for reporting!)

## 1.0.0-rc10 (2016-02-28)

* Fix parsing bug re: placing an octave change before the slash in a chord instead of after it, e.g. `b>/d/f` (#192 - thanks to [elyisgreat] for reporting!)

## 1.0.0-rc9 (2016-02-21)

* Fix parsing bug re: starting an event sequence with an event sequence. (#187 - Thanks to [heikkil] for reporting!)
* Fix similar parsing bug re: starting a cram expression with a cram expression.

## 1.0.0-rc8 (2016-02-16)

* You can now update to the latest version of Alda from the command line by running `alda update`. ([jgkamat])

* This will be the last update you have to install manually :)

## 1.0.0-rc7 (2016-02-12)

* Fixed a bug that was happening when using a cram expression inside of a voice. (#184 -- thanks to [jgkamat] for reporting!)

## 1.0.0-rc6 (2016-01-27)

* Fixed a bug where voices were not being parsed correctly in some cases ([#177](https://github.com/alda-lang/alda/pull/177)).

## 1.0.0-rc5 (2016-01-24)

* Added `midi-percussion` instrument. See [the docs](doc/list-of-instruments.md#percussion) for more info.

## 1.0.0-rc4 (2016-01-21)

* Upgraded to the newly released Clojure 1.8.0 and adjusted the way we compile Alda so that we can utilize the new Clojure 1.8.0 feature [direct linking](https://github.com/clojure/clojure/blob/master/changes.md#11-direct-linking). This improves both performance and startup speed significantly.

## 1.0.0-rc3 (2016-01-13)

* Support added for running Alda on systems with Java 7, whereas before it was Java 8 only.

## 1.0.0-rc2 (2016-01-02)

* Alda now uses [JSyn](http://www.softsynth.com/jsyn) for higher precision of note-scheduling by doing it in realtime. This solves a handful of issues, such as [#134][issue133], [#144][issue144], and [#160][issue160]. Performance is probably noticeably better now.
* Running `alda new` now asks you for confirmation if there are unsaved changes to the score you're about to delete in order to start one.
* A heaping handful of new Alda client commands:
  * `alda info` prints useful information about a running Alda server
  * `alda list` (currently Mac/Linux only) lists Alda servers currently running on your system
  * `alda load` loads a score from a file (without playing it)
    * prompts you for confirmation if there are unsaved changes to the current score
  * `alda save` saves the current score to a file
    * prompts you for confirmation if you're saving a new score to an existing file
    * `alda new` will call this function implicitly if you give it a filename, e.g. `alda new -f my-new-score.alda`
  * `alda edit` opens the current score file in your `$EDITOR`
    * `alda edit -e <editor-command-here>` opens the score in a different editor

[issue133]: https://github.com/alda-lang/alda/issues/134
[issue144]: https://github.com/alda-lang/alda/issues/144
[issue160]: https://github.com/alda-lang/alda/issues/160


## 1.0.0-rc1 (2015-12-25) :christmas_tree:

* Server/client relationship allows you to run Alda servers in the background and interact with them via a much more lightweight CLI, implemented in Java. Everything is packaged into a single uberjar containing both the server and the client. The client is able to manage/start/stop servers as well as interact with them by handing them Alda code to play, etc.
* This solves start-up time issues, making your Alda CLI experience much more lightweight and responsive. It still takes a while to start up an Alda server, but now you only have to do it once, and then you can leave the server running in the background, where it will be ready to parse/play code whenever you want, at a moment's notice.
* Re-implementing the Alda REPL on the client side is a TODO item. In the meantime, you can still access the existing Alda REPL by typing `alda repl`. This is just as slow to start as it was before, as it still has to start the Clojure run-time, load the MIDI system and initialize a score when you start the REPL. In the near future, however, the Alda REPL will be much more lightweight, as it will be re-implemented in Java, and instead of starting an Alda server every time you use it, you'll be interacting with Alda servers you already have running.
* Starting with this release, we'll be releasing Unix and Windows executables on GitHub. These are standalone programs; all you need to run them is Java. [Boot](http://boot-clj.com) is no longer a dependency to run Alda, just something we use to build it and create releases. For development builds, running `boot build -o directory_name` will generate `alda.jar`, `alda`, and `alda.exe` files which can be run directly.
* In light of the above, the `bin/alda` Boot script that we were previously using as an entrypoint to the application is no longer needed, and has been removed.
* Now that we are packaging everything together and not using Boot as a dependency, it is no longer feasible to include a MIDI soundfont with Alda. It is easy to install the FluidR3 soundfont into your Java Virtual Machine, and this is what we recommend doing. We've made this even easier (for Mac & Linux users, at least) by including a script (`scripts/install-fluid-r3`). Running it will download FluidR3 and replace `~/.gervill/soundbank-emg.sf2` (your JVM's default soundfont) with it. (If you're a Windows user and you know how to install a MIDI soundfont on a Windows system, please let us know!)

---

## Earlier Versions

* [0.1.0 - 0.14.2](CHANGELOG-0.X.X.md)

[jgkamat]: https://github.com/jgkamat
[heikkil]: https://github.com/heikkil
[elyisgreat]: https://github.com/elyisgreat
[jgerman]: https://github.com/jgerman
[aengelberg]: https://github.com/aengelberg
[goog]: https://github.com/goog
[0atman]: https://github.com/0atman
[feldoh]: https://github.com/feldoh
[jimcheetham]: https://github.com/jimcheetham
[bbqbaron]: https://github.com/bbqbaron
[damiendevienne]: https://github.com/damiendevienne
[tobiasriedling]: https://github.com/tobiasriedling
[iggar]: https://github.com/iggar
[pzxwang]: https://github.com/pzxwang
[Hemaolle]: https://github.com/Hemaolle
[TBuc]: https://github.com/TBuc

[slack]: http://slack.alda.io
[jq]: https://stedolan.github.io/jq/
[sound-engine]: https://github.com/alda-lang/alda-sound-engine-clj
