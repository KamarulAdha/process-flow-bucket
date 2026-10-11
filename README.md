# process-flow-bucket

Flow files for the process flow canvas. Each file describes one business process: the teams involved, the steps they take, and what each step hands to the next. The canvas reads this repository directly, so it works like a static file bucket.

Everything here is public. Keep it generic: no names of people, clients or companies, and nothing sensitive.

## Layout

```
index.yaml                 the list the canvas reads
<domain id>/
  <process id>.yaml        one flow
```

A process file always lives at `<domain id>/<process id>.yaml`. Ids use only `a-z`, `0-9` and `-`.

## Add a process

1. Write the flow file in its domain folder, for example `finance/expense-claims.yaml`.
2. Add the process to that domain in `index.yaml`, with its `id` and a short `name`.
3. Check the library: open the canvas with `?check` added to its address. Every process should say OK.
4. Commit and push.

To add a domain, create its folder and add an entry under `domains` in `index.yaml`, with an `id`, a `name` and its `processes`. Domains and processes appear in the canvas in the order they are listed.

Changes show on the site within about 5 minutes, because GitHub caches files for that long.

## Check before you push

Run a local copy of this repository through the canvas: serve the folder (for example `python3 -m http.server 8000`), then open the canvas with `?check&bucket=http://127.0.0.1:8000/`. It lists every process as OK, not found, invalid YAML (with the line), or the problems the slide found.

## The flow file

Lines starting with `#` are comments. Quote any text that contains `": "`.

```yaml
title: "How a purchase moves from request to payment"   # slide heading
eyebrow: "Finance and procurement · Procure to pay"      # small line above it
team_heading: "Team"                                     # optional heading above the team names
footer:
  bold: "Opening words in bold."
  text: "The rest of the footer line."

teams:            # top to bottom; steps refer to a team by its id
  - id: requester
    name: "Requester"
    note: "Raises the request"          # optional, shown under the name

steps:            # run in this order
  - team: requester                     # required: a team id
    phase: "Request"                    # column heading; leave out to stay in the previous phase
    title: "Raise request"              # required; short titles fit best
    hands_over: "Purchase request"      # required: what this step passes on
    to: "Budget holder"                 # optional: who receives it; defaults to the next step's team
    what: "What happens in this step."  # shown on the slip
    accepted_when: "The receiving team's acceptance test."
    decision: "APPROVED"                # decision points only: the stamp word
    list:                               # optional extra list on the slip
      title: "The request holds"
      items:
        - "Item and quantity"

loops:            # dashed return arrows; step numbers count from 1
  - from: 5
    to: 3
    label: "Rejected: back to the request"
    show: "end"                         # optional: only on the closing view

intro:            # the opening view, before step 1
  heading: "Each team hands named work to the next"
  paragraphs:
    - "One paragraph per item."

summary:          # the closing view, after the last step
  heading: "One line that sums up the flow"
  points:
    - "One point per item."
  note: "A closing note."
```

### How the slide places steps

A step starts a new column when its phase is different from the step before it, when it cannot drop straight down or up to its team in the current column, or when it has `new_column: true`. A phase can run across several columns, and its heading stretches to cover them.

The slide holds 7 columns and 9 teams. Columns get narrower as you add them; at 6 or 7 columns, titles wrap to two shorter lines.

Files saved from the canvas editor place every step by hand instead: they list `columns` (each with a `phase`) and give every step a `column` number, counting from 1.
