# Node.js VM Summit Agenda

## Participation

1001 E. Hillsdale Blvd, Foster City CA
Suite 400 (Fourth floor)

### Notes

Google Docs: https://docs.google.com/document/d/1lTYV31RLQd0Rr8tZUbmH0qy3JRLFwJvAh44uFhffw5c

### Remote Participants

There will be two options: A limited Google Hangout (max 8-participants) and a Conference Call Dial In.

~~The Google Hangout participation link: https://plus.google.com/hangouts/_/3pbsdj25ljarzecugvcupghlbee~~

On Air: https://plus.google.com/hangouts/_/hoaevent/AP36tYc59g1dJapPjToruLo5u5Sqa5uYmIhBUrKWGd-6FPbF0S1EYg?hl=en&authuser=0


The conference call number will be emailed out directly.

## 2016-04-05

There are two primary objectives for Day One:

* To ensure we all have a shared understanding of the issues and use cases that are being considered, and
* To clearly identify the boundary between Node.js and the underlying VM.

The secondary objectives include:
* To ensure a shared understanding of the similarities and differences between Chakra and v8
* To ensure a shared understanding of what the Microsoft Chakra team is looking to accomplish
* To level set on where things are currently at with regards to v8 in core

This is a rough agenda. We can alter the details as we go depending on how the conversation proceeds.

* 8:00am - 8:30am - Arrival and Getting Settled. 
* 8:30am - 9:00am - Introductions and Getting Started
* 9:00am - 11:30am - Framing the Issues and Use Cases
  * At the end of this period, we should have a clear shared understanding of the fundamental issues being considered
* 11:30am - 12:30pm - Lunch
* 12:30pm - 1:30pm - Node.js and Chakra, Discussing what the Microsoft team has done, their goals, etc
* 1:30pm - 2:30pm - Node.js and v8, Discussing where things are at with v8, ABI stability, current plans, goals, etc
* 2:30pm - 4:30p - General discussion of what issues a multi-VM Node.js would have to address

## 2016-04-06

The Day Two Agenda will be driven by the issues and discussions from Day 1. The primary objective of Day Two is to work through as many of the issues identified on Day One as possible. By the end of Day Two, we should have a clear shared understanding of the specific next steps.

* 8:00am - 8:30am - Arrival and Getting Settled
* 8:30am - 9:00am - Agenda Bashing - Prioritize discussion items from previous afternoon
* 9:00am - 11:30am - Discussion
* 11:30am - 12:30pm - Lunch
* 12:30pm - 4:30pm - Discussion


## High Level Discussion Points

* Should Node.js be VM-neutral?
  * What is the advantage to the ecosystem?
  * What are the key challenges?
* Definining the scope of the issue
  * Mapping out Node.js' own dependencies on the VM layer.
    * Mapping the non-obvious dependencies (behaviors implemented by v8 that are assumed by Node core)
    * Mapping the EcmaScript language requirements (what ES features are implemented by v8 that are assumed by Node core)
  * Mapping out the ecosystem use-cases / problems, including:
    * need to updated / recompile for new V8 versions
    * tight bindings to Node.js executable
  * Do we want to separate Node.js' use of the VM from the ecosystems? Or do we consider these together?
  * Defining the differences / similarities between v8 and chakra from an embedders point of view
    * Does v8 provide anything that chakra does not? (vice versa)
    * Does v8 require anything that chakra does not? (vice versa)
    * What are the language and build level differences?
* Mapping out module/capability/subsystem specific issues
  * Buffer
  * Error Handling
  * VM
  * Promises / EcmaScript Language Features
  * Intl
  * Debugging
  * Post Mortem
  * Native Modules / APIs / ABIs
* Map out the way forward
  * Additional community input
  * Which problems will be addressed (Node core/modules) - update, recompile, binding
  * API definition
    * c/c++ ?
    * plugable ?
    * Freeze current V8 hosting APIs as neutral APIs ?
    * Evolve the current v8 API into a stable, multi-engine API ?
    * Design and Build neutral VM hosting APIs for Node ?
  * Performance
  * Compatibility
    * Discuss creation of a Node.js VM Compatibility Certification that defines the limits for what a VM must do to be compatible with Node.
    * Testing VM Compatibility (VM test suite in CI)
* Identify actions and assign owners

## Arunesh Chandra's proposal (Chakra)

### PART A: Core technical things we should discuss and make progress on

* Design VM Neutral APIs for Node
  * Discuss strategies and tactics to achieve VM Neutrality for Node.
  * Design discussion
  * Identify owners and next steps
  * Following are some of the possible strategies that have been discussed in the community. Post the design discussions, identified owners could prototype some of these or other strategies the group comes up with, and provide a recommendation back to CTC within an agreed upon timeframe
    * Freeze current V8 hosting APIs as neutral APIs
    * Evolve the current v8 API into a stable, multi-engine API
    * Design and Build neutral VM hosting APIs for Node
* Design Native Module abstraction
  * Discuss the strategy and tactics related to Native module abstraction, as it relates to #1 above
  * Design discussions
* Improve interoperability and reduce incompatibilities between versions by creating a Node compatibility suite
  * This could be thought of as a standardized way to bring in more VMs to achieve interoperability across versions of Node. The focus here could be on:
  * Functional tests
  * Module compatibility (JS Modules, Native Modules)
  * Perf Goals

### PART B: Not sure if the VM Summit would be the appropriate forum to discuss these, but listing them just in case you think we should discuss this during the summit itself.

* Next steps for getting ChakraCore to Node mainline
  * This includes identifying the criteria and work to be done so that we can assign owners to make progress.
* Close on VM Neutrality discussion thread - Potential items
  * Establish and communicate value prop for VM Neutrality for Node.js
  * Discuss community concerns and find solutions / mitigations for the same
    * Maintenance cost
    * Code velocity
    * CI requirements
    * Zero-Day notification and patching process
* Chakra Team participation: Identify avenues for Chakra teams participation in the Foundation as a VM implementer
  * CTC/TSC/WGs
  * Standardization
