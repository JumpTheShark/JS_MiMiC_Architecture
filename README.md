# JS MiMiC Architecture

JavaScript *Module-Mediator-Controller* architecture (or *MiMiC*, *MMC*) is a way to create JS back-end programs in a specific ellegant style.

The architecture is inspired by the Eddie Osmani pattern.

### How it works on paper?

As realized by architecture name, the program is separated on three logical parts.

1. ***Module*** - an architecture unit, which implements the concrete task, formally named *executive task*. These and only these units form all the technical logic part of the program. Moreover, according to the architecture, modules are the only objects that changes between different programs, so the programmer needs to use only them to create his project - other code part is crearly defined and automated.

2. ***Mediator*** (or ***Core***) - an architecture unit, which implements the execution of tasks handled by modules. Cores can be compared with processors in a computer that manages tasks and their completion. Mediators have their own task queue. These elements finalize the technical task part.

3. ***Control unit*** (***CU***, or ***Controller***) - an architecture unit, which implements the cores' manager system. It is also the communicator between business logic and technical implementation. The controller is only one for the project, against the potentially unlimited number of mediatros and modules. As it is the input point for program users, controller accepts human-written tasks, formally named *business tasks*. It consists of under-controlled core collection for executing the given tasks and mapping { business task : executive tasks }. This element finalizes the business task part.

___

Now we describe the operating cycle of a program based on MiMiC architecture.

* Firstly, any user requests a business task to be handled. The task with necessary arguments is served in the program's Control Unit. The last one knows about the connection of business tasks and special structures that describe business tasks technically, basing on executive tasks (formally the structure is named *task package*). The CU selects appropriate mediator to execute the business task and gives to it respective task package with already hardcoded business arguments into the executive tasks.

* Secondly, mediator to be chosen receives task package and put all executives tasks into the queue. As the time comes, executive tasks begin to be handled in order they are described. Tasks are able to share their output as argument for each other, within one task package.

* The execution of a single executive task is followed by several notices:
 - Core is searching for mapping { executive task : module } to find the module for completing the task.
 - Module receives arguments that described for it. Argument can be hardcoded from CU or during the output return of another task.
 - During code execution, module is able to request some data without itself, by calling another executive task with arguments. Note that it is the only way to get something from outside the module code, *libraries direct using is not recommended and may be forbidden in future*.
 - When module requests an outsource data, its execution is frozed until the data be got. Called executive task is put in the task queue.
 - When completed, module can return only one result (or error), meaning that it can not be separated on two different datas as arguments.

* When all of the tasks with taskpackage are completed, the final result (provided by the last task) is returned to the CU, which in turn will be sent to the user.

### How it works practically?

As we talk about the JavaScript back-end language, let us show how to write modules with *Node.js* and *ECMAScript 6*.

##### First steps

Modules in JavaScript representation are perfectly implemented by JavaScript's *generators*.

```javascript
const module1 = function *() {
	const args = yield;
	return "Hello, world!!";
},
```

The module above demonstrates how to implement a simple task returning "Hello, world!!" message. You can see there `const args = yield;` line - this is how the module receives the ininial arguments. Note that every module **must** request input arguments before all other outsource requests in any case, even if there is no module arguments at all. In case of no arguments there is an option to write `yield;` instead:


```javascript
const module1 = function *() {
	yield;
	return "Hello, world!! Now with no saved arguments ;)";
},
```

##### Outsource data request 

When there is a need to get something from outside the module logic, the request mechanism is used:

```javascript
const module2 = function *() {
	yield;
	
	const message = yield {
		str  : "get hello-world message",
		args : {}
	};
	
	return `${message} ${message}`;
},
```

This example calls internally the executive task named `"get hello-world message"` which we define in `str` property, and returns the twice repeated hello-world message.
Note that request is not attached to the module, but to the task by its formal name. The executive task name related to the module is binded to it during the Control Unit configuration.

##### Throwing errors

***Coming soon***

##### task package define

As we noticed, user can only call business tasks to handle. Business tasks binded to the task packages which represents an array of executive tasks with some parameters.

```javascript
const exTask = {
	str  : "get doubled hello-world message",
	args : {}
};
```

In general it is similair to how you describe it inside the module request.
We can specify the result name indentificator to use it for pasting the result as an argument into another executive task:

```javascript
const exTask = {
	str        : "get doubled hello-world message",
	args       : {},
	returnName : "msg"
};
```

If we want to declare any argument to be put in the input from user (as an input argument), then we should write argument this way:

```javascript
testArg : {
	mustBePut : true,
	argId     : "arg1" /* any name */
}
```

If we want to declare any argument to be put from another executive task within one task package, then we should write argument this way:

```javascript
testArg : {
	mustBeGot : true,
	argId     : "resArg" /* any name from another executive task returnName value */
}
```

Note that ```mustBePut``` and ```mustBeGot``` properties in argument definition are keywords and can not be used for other reasons.

***TODo continue***
