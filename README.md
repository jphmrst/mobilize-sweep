# mobilize-sweep
Command-line scripts comparing Mobilize event lists to a local calendar.

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

- `--source`

- `--mobilize`

### Optional command-line arguments

- `--mobilize-call-pause`

- `--ignore-event`

- `--allow-calendar-event`

- `--default-section-header`

- `--mobilize-link-regex`

- `--section-header-regex`

- `--cron-mode`

## The `find-org-slugs` script

This script finds the ID numbers corresponding to Mobilize slugs.
This search is helpful for setting up a call to `sweep-mobilize`, and
accepts the following arguments:

- `--slug`

- `--mobilize-call-pause`

- `--no-comments`
