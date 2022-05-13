# scheduler
how to use scheduler:
* instantiate scheduler with factory;
* start: begin to schedule;
* stop: end to schedule;
* scheduler listeners: perform actions based on scheduler events;
* thread pool: provides a set of threads for Quartz to use when executing jobs;

scheduler implemtations:
* stardard scheduler factory: easy to use;
* direct scheduler factory: create their scheduler instance in a more programmatic way;

# job
job concepts:
* job identity: group + job name;
* job detail: informs various attributes to job;
* job builder: used to build job detail;
* job listener: perform actions based on job events;

job implemtations:
* must implement method `public void execute(JobExecutionContext context)`, and parameter context has job/trigger data map;

job store is used to keep track of all the "work data" that you give to the scheduler: jobs, triggers, calendars, etc:
* RAM job store: keeps all of its data in RAM;
* JDBC job store: keeps all of its data in a database via JDBC;
* Terracotta job store: the data is stored in the Terracotta server;

# trigger
trigger conceptsz:
* trigger identity: group + trigger name;
* trigger builder: used to build trigger;
* trigger listener: perform actions based on trigger events;

common trigger attributes:
* trigger key: tracking identity;
* job key:  indicates the identity of the job;
* start time;
* end time;
* priority;
* misfire instructions: instructions if a persistent trigger "misses" its firing time;
* calendars: excludes blocks of time from the the trigger's firing schedule;

trigger implementations:
* simple trigger: execute exactly once , or repeats at a specific interval;
* cron trigger: recurs based on calendar-like notions;

# advanced features
quartz support some advanced features:
* clustering: works with the JDBC-Jobstore (JobStoreTX or JobStoreCMT) and the TerracottaJobStore.
* JTA transactions;

# extensions
quartz support some advanced extensions:
* plug-ins: Quartz ships with some plug-ins, such as auto-scheduling of jobs, logging a history of job and trigger events, and ensuring that the scheduler shuts down cleanly;
* job factory: to accomplish things such as having your application's IoC or DI container produce/initialize the job instance;
* 'factory-shipped' jobs: Quartz provides a number of utility jobs for doing things like sending e-mails and invoking EJBs;

# reference
* [code](https://github.com/quartz-scheduler/quartz)
* [tutorials](https://github.com/quartz-scheduler/quartz/tree/master/docs/tutorials)
