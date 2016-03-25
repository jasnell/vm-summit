# Node.js VM Summit Agenda
Start of an agenda for the Node.js VM Summit

## High Level Agenda

* Introduction
* Defining the scope of the issue
  * Enumerate the ecosystem uses-cases / problems we are trying to address as part of this discussion. Starting point:
    * need to update (Node core/modules) for new version of V8
    * need to recompile (Node core/modules) for new version of V8
    * tight binding to Node executable
    * ...
* How would we address these problems? How might that lead to Node work towards VM neutrality?
* Figure out we are going to split up the rest of the 2 days to work on these problems.

## Agenda proposal from IBM

* Introduction
* Defining the scope of the issue
  * Node core versus module usage/requirements
    * are we going to cover both, may be different solution for each
  * Key problems
    * need to update (Node core/modules) for new version
    * need to recompile (Node core/modules) for new version
    * tight binding to Node executable
* Identify Minimal surface area
  * Mapping the surface area of v8's use in Node core and ecosystem
    * Mapping the non-obvious dependencies (behaviors implemented by v8 that are assumed by Node core)
    * Mapping the EcmaScript language requirements (what EcmaScript features are implemented by v8 that are assumed by Node core)
  * Defining the differences / similarities between v8 and chakra from an embedders point of view
    * Does v8 provide anything that chakra does not? (vice versa)
    * Does v8 require anything that chakra does not? (vice versa)
    * What are the language and build level differences?
  * Brainstorm approaches for addressing the differences and requirements
  * Use cases walkthough
    * Buffer
    * Error
    * VM
    * Promises
    * Intl
* Map out the way forward
  * What should philosophy be for core
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
