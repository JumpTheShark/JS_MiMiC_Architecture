# JS MiMiC Architecture

JavaScript *Module-Mediator-Controller* architecture (or *MiMiC*, *MMC*) is a way to create JS back-end programs in a specific ellegant style.

### How it works on paper?

As realized by architecture name, it separates the program on three logical parts.

1. ***Module*** (or ***TODO***) - an architecture unit, which implements the concrete task, formally named *executive task*. These and only these units form all the technical logic part of the program. Moreover, according to the architecture, modules are the only objects that changes between different programs, so the programmer needs to use only them to create his project - other code part is crearly defined and automated.

2. ***Mediator*** (or ***Core***) - an architecture unit, which implements the execution of tasks handled by modules. Cores can be compared with processors in a computer that manages tasks and their completion. Mediators have their own task queue (more precisely, 'sustained segment' queue, what is explained further). These elements finalize the technical task part.

3. ***Control unit*** (or ***Controller***) - an architecture unit, which implements the cores' manager system. It is also the communicator between business logic and technical implementation. The controller is only one for the project, against the potentially unlimited number of mediatros and modules. As it is the input point for program users, controller accepts human-written tasks, formally named *business tasks*. It consists of under-controlled core collectio which executes the given tasks and mapping { business task : executive task**s** }. This element finalizes the business task part.

***TODO***
