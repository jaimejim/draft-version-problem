---
title: Problem Details For Versioning in IoT
abbrev: Versioning Problems
docname: draft-jimenez-version-problem-latest
category: std

ipr: trust200902
area: IPSO
workgroup: IPSO Working Group
keyword: Version, Problem Details

stand_alone: yes
pi: [toc, sortrefs, symrefs, iprnotified]

author:
 -
    ins: J. Jimenez
    name: Jaime Jimenez
    organization: Ericsson
    phone: "+358-442-992-827"
    email: jaime@iki.fi

normative:

informative:
  SEMVER:
   target: http://semver.org
   title: Semantic Versioning
   date: false
  DATMOD:
   target: https://github.com/t2trg/wishi/blob/master/slides/2019-11-07-versions.pdf
   title: Data Model Versioning
   date: false
  SCHEMA:
   target: http://www.openmobilealliance.org/tech/profiles/LWM2M.xsd
   title: LwM2M Schema
   date: false
  OMNA:
   target: http://www.openmobilealliance.org/wp/OMNA/LwM2M/LwM2MRegistry.html
   title: OMNA Registry
   date:  false
  LWM2MSPEC:
   target: http://www.openmobilealliance.org/release/LightweightM2M/V1_1_1-20190617-A/HTML-Version/OMA-TS-LightweightM2M_Core-V1_1_1-20190617-A.html#7-2-0-72-Object-Versioning
   title: LwM2M Specification
   date: false
  VERSION:
   target: https://github.com/OpenMobileAlliance/LwM2M/issues/350
   title: Versioning Discussion
   date: false

--- abstract
This document provides some problem details when doing versioning on IoT devices, it illustrates common problems faced by SDOs.

--- middle

# Introduction

As software is improved over time, new releases are provided with new features or with fixes to known problems. To distinguish between releases you have specific version numbers that identify the release. They operate on the assumption that development runs in a linear progression with little complexity.

However, as systems grow and become interdependent it is more likely to enter into dependency and backwards compatibility issues. IoT is no different in this regard.

## Requirements Language

{::boilerplate bcp14}

# Types of Changes

Changes can be additions or extensions to existing software, bug fixes or complete reinterpretations of existing interactions.

Changes need to be *backwards compatible* with legacy devices and systems without breaking existing APIs and/or format assumptions, so that your evolved code can work with the legacy system. They need also to be able to be *forwards compatible* towards future extensions added to them, they need to tolerate different input without breaking up.

## Semantic Versioning

On most software systems and Web APIs there is the concept of "Semantic Versioning" {{SEMVER}}, which provides a categorization as follows:  

~~~
   Given a version number MAJOR.MINOR.PATCH, increment the:
   MAJOR version when you make incompatible API changes,
   MINOR version when backwards compatible functions are added,
   PATCH version when you make backwards compatible bug fixes.
~~~

## Software Evolution

In scenarios where interactions are between two endpoints (e.g., producer/consumer, client/server, ...) there is a distinction on how software changes are expected to happen.

When the data sender can enforce changes on the consumer, versioning is indicated and the consumer has no say on the new version.

When both sides have a say they may negotiate what changes can be applied and the producer also needs to adapt to the requirements of the consumer, this is common in backwards compatibility breaking changes.  

RFC791 defines the "robustness principle" which places the burden of handling backwards compatibility on the consumer/server.

~~~
   an implementation must be conservative in its sending
   behavior, and liberal in its receiving behavior.
~~~

Of course, in IoT the client/server roles may change depending on the protocol used and other considerations like computing power, memory or connectivity might be more important.

With most software there is a disincentive to roll-up feature bundles into new versions as it creates flag day between producers and consumers.

## Data Model Versioning

TBD

# Versioning Examples

While {{SEMVER}} formatting works fine for versioning of interfaces and protocols it might be different for *versioning of data formats*. Here we see some example protocols and data models and how they have dealt with versioning.

## HTTP Versioning

The Hypertext Transfer Protocol (HTTP) also includes a protocol versioning scheme to indicate which version of the protocol is used {{?RFC2145}}. This pattern is common in many other protocols.

Versions of the protocol is indicated using the *major.minor* numbering. The *minor* number is incremented when the changes made to the protocol add features which do not change the general message parsing algorithm, but which may add to the message semantics and imply additional capabilities of the sender. The *major* number is incremented when the format of a message within the protocol is changed.

HTTP since v1.0 had multiple modifications but somehow the existing feature set never triggered a major revisions until HTTP v1.1. HTTP v2 completely replace the protocol layer.

## IPSO Versioning

IPSO Smart Objects are resources stored on a CoAP server. IPSO provides an {{SCHEMA}} which is the same used by LwM2M. IPSO Objects are defined in such a way that they do not depend on the use of CoAP, any RESTful protocol is sufficient. Nevertheless, to develop a complete and interoperable solution the object model is based on the Open Mobile Alliance Lightweight Specification (OMA LWM2M) and comes with an object registry Each object is stored in a repository, the {{OMNA}} Registry.

In IPSO the versioning problem is manyfold; Object Versioning, Repository Versioning, Enabler Version, Schema Version.

In scope of this document is the problem of Object Versioning. IPSO uses a 16 bit number space to identify objects (e.g., 3303 identifies the object for "temperature"). IPSO uses a versioning similar to that of {{SEMVER}}, using the concept of MINOR and MAJOR revisions, and it is defined in the {{LWM2MSPEC}} (section 7.2. Object Versioning). Explain Object, Explain Resource.

Object versioning aims at identifying the LwM2M Object modifications which can be categorized in 2 types defined below:

- Evolution of type I: representing non-backward-compatible ("breaking") evolutions, namely:
   - a Resource characteristic (optional vs mandatory, data type, supported operations) is changed in the Object
   - a mandatory Resource is added to or removed from the Object.
   - a feature which is only defined by a new LwM2M Enabler, is added to the Object.

- Evolution of type II: representing backward-compatible ("non-breaking") evolutions

   - an optional Resource is added or removed in the Object

Compatibility is guaranteed between client and server when they interact.

Currently IPSO creates a new reusable resource whenever a reusable resource changes. Any object that wants to make use of the new reusable resource has to be updated resulting in a new version of the object. The discussion is around deprecation of versioning altogether {{VERSION}}.

## YANG versioning

On the IETF the topic of Semantic Versioning has appeared multiple times, specially when versioning YANG modules.

Some of the items defined in YANG {{?RFC7950}} require the use of a unique identifier.  In both NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}, these identifiers are implemented using names.  

On IoT, they have come up with compact unique identifiers called Schema Item iDentifiers (SID) {{?I-D.ietf-core-sid}} which are themselves registered on IANA. Every time a company has a new YANG module, they may register it with IANA, which will automatically assign them a SID or a SID range.

To track the addition of new features and specially to integrate the specification work with the way developers do version control (e.g., Git, SVN...) they have come up with a mechanism to also version drafts and assign them to specific GIT commits {{?I-D.claise-semver}}.

# Security Considerations

TBD.

# IANA Considerations

None

--- back

# Acknowledgments
{: numbered="no"}

This document relies heavily on Carsten Bormann's input on Data model versioning {{DATMOD}} and on WISHI discussions.