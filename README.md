# Node.js VM Summit Agenda
Start of an agenda for the Node.js VM Summit

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
