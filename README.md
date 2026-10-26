# mobilize-sweep
Command-line scripts comparing Mobilize event lists to a local calendar.

These scripts are intended for maintainers of calendars which draw
from multiple sources including Mobilize events.  It can be tedious to
notice when new events are created by an organization in Mobilize, or
when a Mobilize event is cancelled.  The scripts in this repository
allow the calendar maintainers to automate comparing a local calendar
file to the event lists of designated organizations on Mobilize.  The
main script will then report discrepancies which may require the
maintainers' attention.

## The `sweep-mobilize` script

This script is the primary tool of this repository.  It compares a
single text-based representation of an organization's calendar to the
events specified by one or more Mobilize organizations, and reports
both

1. Any events on an organization's Mobilize which is not included on
   the local calendar, and
   
2. Any events on the local calendar which are not included on one of
   the designated organization's Mobilize list.
   
### Required command-line arguments

- `--source=PATH`

  Path to a local text file holding a calendar where we look for
  Mobilize URLs.  Exactly one value must be provided with this option.

- `--mobilize SLUG=ID`

  Specifies a pair of values for each Mobilize organization whose
  events are to be tracked.  This argument may appear multiple times
  (but unless at least one instance is given, the script will do no
  useful work).

### Optional command-line arguments

- `--mobilize-call-pause=VALUE`

  Minimum number of seconds between calls to the Mobilize API.
  Mobilize does implement rate limits, and the script will fail if you
  hit them.  The script uses millisecond time via, so
  `--mobilize-call-pause` expects a floating-point value.  The default
  value is `1.0`.

- `--ignore-event=ID`

  Specifies the numeric Mobilize ID of an event which should not
  trigger a report when it appears in an organization's event list,
  but not in the local calendar.  This argument may be given multiple
  times.

- `--allow-calendar-event=ID`

  Specifies the numeric Mobilize ID of an event which should not
  trigger a report when it appears in the local calendar, but not in
  any organization's event list.  This argument may be given multiple
  times.

- `--default-section-header=TEXT`

  This script will attempt to identify the section of the calendar
  file in which links appear.  In particular, it cites the section
  title when reporting IDs which appear in the calendar file but not a
  Mobilize list.  This argument specifies the text which will be used
  if such a link is found prior to any section header.  The default
  value is `(no header)`.

- `--mobilize-link-regex=REGEX`

  This argument specifies a (suitably quoted) Perl regular expression
  for finding links to Mobilize events in the local calendar file.
  This regex should use the Perl named substring capture
  `(?<NAME>PATTERN)` to match and name two components of a Mobilize
  event URL,
  
   1. An organization's slug, given the name `org`, and
   
   2. The numeric ID of the event, given the name `id`.

  The default value for `--mobilize-link-regex` is 
  
    ```
	href=\"https://(www\\.)?mobilize\\.us/(?<org>[^/]+)/event/(?<id>[0-9]+)/?\"
	```

- `--section-header-regex=REGEX`

  This argument specifies a (suitably quoted) Perl regular expression
  for finding section headers in the local calendar file.  This regex
  should use the Perl named substring capture `(?<NAME>PATTERN)` to
  match the text of the section header, and should name it `header`.
  The default value for `--mobilize-link-regex` is
  
    ```
	^(?<header>[A-Za-z]+day,.*)\$
	```

- `--calendar-event-title-regex=REGEX`

  This argument specifies a (suitably quoted) Perl regular expression
  for finding event titles in the local calendar file.  When found,
  the event title will be reported when an unreported Mobilize link is
  referenced from a calendar event.  By default this argument is not
  defined.

- `--verbose=NUM`

  Set the level of detail in the program's output.  `NUM` should be an
  integer value of zero or higher.
  - The default value is `1`.
  - A value of `0` is useful for utilities such as `cron` which can be
    configured to generate emails or other alerts for non-empty
    program output.  So verbosity `0` suppresses progress messages and
    other non-error output, showing only issues requiring attention.
  - Values of `4` or higher include information typically associated
    with debugging.

### Example

```
sweep-mobilize \
	--source=cal.org \
	--mobilize dfl=617 \
	--mobilize ilhanomar=2754 \
	--mobilize keithellison=13798 \
	--mobilize mnforklobuchar=38826 \
	--mobilize peggyflanaganforminnesota=4113 \
	--ignore-event=1025420 --ignore-event=1073034 \
	--ignore-event=1055205 --ignore-event=1053514 \
	--ignore-event=1073750
```

### Plans and wishes

- Might be nice to have it scan a Google calendar rather than a text
  file.

Bug reports and PRs gratefully received!

## The `find-org-slugs` script

This script finds the ID numbers corresponding to Mobilize slugs.
This search is helpful for setting up a call to `sweep-mobilize`, and
accepts the following arguments:

- `--slug=NAME`

  Gives the name of the slug of a Mobilize organization whose numeric
  ID we need.  This argument may be given multiple times.

- `--mobilize-call-pause=VALUE`

  See the documentation above of this argument for `sweep-mobilize`

- `--no-comments`

  Suppresses some of the output which might be considered extraneous.

## Notes

- Mobilize API docs: https://github.com/mobilizeamerica/api
