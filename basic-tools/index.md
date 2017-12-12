# Basic tools

There are three main things a basic application needs to be able to do
easily in order to be successful.  They are:

1. Setting up new API endpoints
2. Moving tasks out of the request/response cycle with a queue
3. Easy to set up recurring tasks that push jobs to the work queue

Without any one of these pieces developers will be forced to make
unfortunate tradeoffs.  Note that having difficult to use versions of
these pieces while preferable to nothing should not be considered
acceptable and will tend to hurt team velocity especially as the
application grows.

The main goal of installing these pieces early is to make the easiest
and simplest course of action when developing new features also the
correct one.  Ultimately that sets up the right incentives for
developers and helps lead to successful projects.

## API

The API is the core way to communicate data either to a client you
control (app or frontend) or to a third party depending.  Proper API
design is about consistency, stability, and documentation.  See the
[REST API design](../rest-apis/index.md) section for more detail on
this.  It is important to invest in the tools you need to be successful
early on in the development.  These are often simple pieces like proper
structuring for errors that can make a big difference in usability.  Do
not be afraid to spend some time thinking aobut how things like errors,
serialization, and url structure will work across your API at the
beginning of the project.  Doing so will save a lot of time in the long
run.

## Work queues

The work queue is the core feature for asynchronous processing.  In most
new applications the scale does not need to be particularly high so a
lot of options exist for a queue.  The important things to get right
when doing background processing are the following:

- Idempotence: Whenever possible your jobs should be
  [idempotent](https://en.wikipedia.org/wiki/Idempotence), meaning
  that they have the same effect whether they are run one time or
  multiple times.
- Retry-able: Jobs should be able to have retry logic where needed which
  makes it easy to trust the queue to get the work done.  This is
  particularly easy to deal with when the job itself is idempotent.
- Error handling: Errors should be logged and reported in a structured
  way so that any issues that come up cannot fly under the radar.
- Logging: Not all queues provide an easy way to debug what is happening
  with jobs.  As such it makes sense to provide robust logging for the
  inputs and outputs of the job along with retry counts and delays for
  errors.  This makes debugging significantly easier.

## Scheduled tasks

The scheduled task is another core building block and useful for things
that need to happen outside of the request/response context that are not
triggered by a particular user action. Examples of this might be
updating a cache or updating data that only needs to be eventually
consistent.

Important things to get right with it:

- Do the minimal possible work in the code that handles scheduling
  itself.  Prefer to just have scheduling code enqueue a job in the work
  queue rather than do anything itself.  Do not be afraid of having a
  job that determines work that needs to be done and then enqueues
  other jobs to complete that work.
- Avoid infinite retries for parts that fail by designing your jobs that
  are time based to only look a fixed distance in the past.  This
  distance should be two to three times as long as the period between
  jobs.  So a job running every five minutes would look 10 to 15 minutes
  into the past for items to operate on.
- Make sure jobs take less time than the period they run in.  Otherwise
  you can get a buildup of jobs that keeps growing since not everything
  is finished by the time the next batch starts.
- Provide logging about what is happening so you have an easy way to
  debug issues.
- Strive for idempotence with periodic jobs the same way you would for a
  one off job.
