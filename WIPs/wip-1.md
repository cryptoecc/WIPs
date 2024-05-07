# WIP-1: WIP Purpose and Guidelines

<pre>
  Title: WIP Purpose and Guidelines
  Status: Final
  Type: Informational
  Author: Heungno Lee<@lincolnkerry>, Gyeongdeok Maeng <@siddharth0a>
  Created: 2023-05-03
  License: GNU Lesser General Public License v3.0
</pre>

## What is a WIP?

WIP stands for Worldland Improvement Proposal. A WIP is a design document that either describes a new feature for the Worldland network or its processes, or provides information to the Worldland community. The WIP should concisely explain the proposed improvement and the rationale behind it.

WIPs are intended to be the primary medium for proposing new features, collecting community feedback, and documenting design decisions made for the Worldland network. The WIP author is responsible for building consensus within the community and documenting dissenting opinions.

As WIPs are maintained in a versioned repository as text files, their revision history is the historical record of the proposed improvement.

## WIP Workflow

The WIP process begins with an idea for improving the Worldland network. The WIP author drafts a proposal in accordance with the guidelines described in this document, and submits it to the WIPs repository as a pull request. The author must build community consensus around the proposal and document dissenting opinions.

Once the draft is complete, the pull request will be merged and the WIP will receive a number and status of "Draft." From this point, the author should seek wider community review and feedback. When the author believes the WIP is mature enough and has sufficiently addressed feedback, they can request to change the status to "Proposed."

For a WIP to be "Accepted," it must demonstrate sufficient community support and a clear intention to implement the proposal. Once a WIP is implemented and adopted by the community, its status will be changed to "Final."

## WIP Format

Each WIP should have the following parts:

* Preamble -- RFC 822 style headers containing metadata about the WIP, including the WIP number, a short descriptive title (limited to a maximum of 44 characters), the names, and optionally the contact info for each author, etc.

* Abstract -- A short (~200 word) description of the issue being addressed.

* Copyright -- Each WIP must be explicitly released under the Creative Commons CC0 1.0 Universal license.

* Specification -- The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow interoperable implementations on the Worldland platform.

* Motivation -- The motivation is critical for WIPs that propose changes to the Worldland network or protocol. It should clearly explain why the existing specification is inadequate to address the problem that the WIP solves. WIP submissions without sufficient motivation may be rejected outright.

* Rationale -- The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g., how the feature is supported in other systems.

* The rationale should provide evidence of consensus within the community and discuss important objections or concerns raised during discussion.

* Backwards Compatibility -- All WIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The WIP must explain how the author proposes to deal with these incompatibilities. WIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

* Reference Implementation -- The reference implementation must be completed before any WIP is given "Final" status, but it need not be completed before the WIP is accepted. It is better to finish the specification and rationale first and reach consensus on it before writing code.

* The final implementation must include test code and documentation appropriate for the Worldland network.

## WIP Licensing

All WIPs must be released under the GNU Lesser General Public License v3.0, also included in our repository in the COPYING.LESSER file.