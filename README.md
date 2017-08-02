
@trasukg/state-machine
======================

Description  
-----------

This is a fairly simple finite state machine utility.  It supports transitions
from state to state, functions executed on transitions, and entry and exit
functions.

Usage
-----

    var StateMachine=require('@trasukg/state-machine');  
    var states={
      A: {
        go: 'B',
        takeArgs: ['A', function(val) {
          calledValue=val;
        }],
        stay: function() {
          stayWasCalled=true;
        },
        value: ['B', function() { return 1; }]
      },
      B: {
        go: ['A', function() { trB(); }],
        value: ['A', function() { return 2; }],
        onEntry: function() {
          entryCalled=true;
        },
        onExit: function() {
          exitCalled=true;
        }  
      }  
    };

    var machine=new StateMachine(states,'A');  
    machine.go();
    var c=machine.value();
    machine.takeArgs(12);

- First, import the state machine class: 
- Then, create a descriptor for the states.  This is a JavaScript object that
contains a key/value pair for each state.  The key is the name of the state,
and the value an object that defines the events the state responds to.
- Instantiate a state machine, by calling the StateMachine constructor, with
the state descriptor, and the name of the initial state of the machine:    
- Now, you can call events on the state machine.  

What happens when you call the event depends on the state definitions.  To see
the possibilities, lets' take a look at the definition for State 'A' above:

    A: {
      go: 'B',
      takeArgs: ['A', function(val) {
        calledValue=val;
      }],
      stay: function() {
        stayWasCalled=true;
      },
      value: ['B', function() { return 1; }]
    },

You can probably figure out that in this object, the key is the name of the
event function that we can call.  The value defines what happens when we call that
event function while in that state.

- A simple string value, like the value of 'go' above, indicates that the machine
will transition to the named state.  So if we're in state 'A' and we call
'machine.go()', the machine transitions to state 'B'.  
- If we want to execute a function on transition, then we provide an array that
contains the next state and the function, as in the 'takeArgs' event above.  The
function will be executed and the machine will transition to the named state.  The
function can take arguments, so for example in the case above, we could call
'machine.takeArgs(12)', and the arguments are passed on to the transition function.
The return value from the transition function is returned to the caller.  
- If we want to execute a function, but then remain in the same state, we can just
provide the function, as in the 'stay' event shown above.  

In addition to the transition functions, we can also provide entry and exit functions
for a state.  Simply define events named 'onEntry' and/or 'onExit', and these
functions will be executed when the machine enters or exits the state.  The entry
and exit functions are called without arguments.

    B: {
      go: ['A', function() { trB(); }],
      value: ['A', function() { return 2; }],
      onEntry: function() {
        entryCalled=true;
      },
      onExit: function() {
        exitCalled=true;
      }
    }

The transition functions and entry/exit functions are executed in the context of the
state machine object (i.e. this==machine).

For more demonstrations of the usage, have a look at 'specs/StateMachineSpec.js'.

License
-------

Apache Software License 2.0
