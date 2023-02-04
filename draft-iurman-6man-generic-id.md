---
title: "Carrying an Identifier in IPv6 packets"
abbrev: "Carrying IPv6 Identifier"
category: std

docname: draft-iurman-6man-generic-id-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 0
area: "Internet"
workgroup: "IPv6 Maintenance"
keyword:
venue:
  group: "IPv6 Maintenance"
  type: "Working Group"
  mail: "ipv6@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ipv6/"
  github: "IurmanJ/draft-iurman-6man-generic-id"
  latest: "https://IurmanJ.github.io/draft-iurman-6man-generic-id/draft-iurman-6man-generic-id.html"

author:
 -
    ins: J. Iurman
    name: Justin Iurman
    organization: ULiege
    street:
    - Institut Montefiore B28
    - Allée de la Découverte 10
    code: 4000
    city: Liège
    country: Belgium
    email: justin.iurman@uliege.be

normative:

informative:


--- abstract

Some recent use cases have a need for carrying an identifier in IPv6 packets.
While those drafts might perfectly make sense on their own, each document
requires IANA to allocate a new code point for a new option, and so for very
similar situations, which could quickly exhaust the allocation space if similar
designs are proposed in the future. As an example, one might need an 8-bit ID,
while another one might need a 32-bit, 64-bit or 128-bit ID. Or, even worse, one
might need a 32-bit ID in a specific context, while someone else might also need
a 32-bit ID in another context. Therefore, allocating a new code point for each
similar option is probably not the way to go.

--- middle

# Introduction

Some recent use cases have a need for carrying an identifier in IPv6 packets.
Two examples are {{?I-D.draft-ietf-6man-enhanced-vpn-vtn-id}} and
{{?I-D.draft-li-6man-topology-id}}.
While those drafts might perfectly make sense on their own, each document
requires IANA to allocate a new code point for a new option, and so for very
similar situations, which could quickly exhaust the allocation space if similar
designs are proposed in the future. As an example, one might need an 8-bit ID,
while another one might need a 32-bit, 64-bit or 128-bit ID. Or, even worse, one
might need a 32-bit ID in a specific context, while someone else might also need
a 32-bit ID in another context. Therefore, allocating a new code point for each
similar option is probably not the way to go.

This document proposes two different solutions to carry an identifier in IPv6
packets, in order to avoid the aforementioned issue. Each solution defines a new
option in the Destination Options and Hop-by-Hop Options sub-registry. Both are
explained in this document.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Solution 1: Generic Identifier

For simple use cases such as a single identifier carried without additional
fields and without specific context, a new Option Type named "Generic
Identifier" is defined as follows:

                             1                   2                   3
         0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                        |  Option Type  |  Opt Data Len |
        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        |                                                               |
        ~                  Identifier (variable length)                 ~
        |                                                               |
        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

                       Figure 1. Generic Identifier Option

where:

- Option Type:  8-bit option type as defined in [](#generic-identifier-solution-1).

- Opt Data Len:  8-bit unsigned integer.  Length of the Identifier field, in
       octets.

- Identifier:  variable length field representing the carried identifier.

## Pros and Cons

(+) Totally generic solution, similar to an Identifier container.

(-) Too generic, as it could be used to carry all and nothing.\\
(-) No context for the carried identifier, which might disturb the receiver.\\
(-) No multiple identifiers, where some use cases might need to carry more than
one.\\
(-) No additional fields, where some use cases might need that.


# Solution 2: Identifier with Context

For use cases where one or several identifiers are carried with additional
fields, or when a context is required, a new Option Type named "Identifier with
Context" is defined as follows:

                             1                   2                   3
         0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
                                        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                                        |  Option Type  |  Opt Data Len |
        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        |          Context-ID           |           Reserved            |
        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
        |                                                               |
        ~                Context-Data (variable length)                 ~
        |                                                               |
        +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

                     Figure 2. Identifier with Context Option

where:

- Option Type:  8-bit option type as defined in [](#identifier-with-context-solution-2).

- Opt Data Len:  8-bit unsigned integer.  Length of this option, in octets, not
       including the first 2 octets.

- Context-ID:  16-bit field as defined in [](#context-id).

- Reserved:  16-bit field MUST be set to zero upon transmission and ignored
       upon reception.

- Context-Data:  variable length field representing a data structure which
       depends on the Context-ID field.

## Pros and Cons

(+) Allows for a context.\\
(+) Allows for multiple identifiers.\\
(+) Allows for additional fields.\\
(+) IETF review process for new Context-ID code points.

(-) Not hardware friendly.


# IANA Considerations

## Generic Identifier (Solution 1)

If the solution is adopted, this document requests IANA to allocate the
following IPv6 Option Type in the Destination Options and Hop-by-Hop Options
sub-registry of Internet Protocol Version 6 (IPv6) Parameters:

        Binary Value   Description                     Reference
        act chg rest
        --------------------------------------------------------------
        00   0  TBD    Generic Identifier Option       [This document]


## Identifier with Context (Solution 2)

If the solution is adopted, this document requests IANA to allocate the
following IPv6 Option Type in the Destination Options and Hop-by-Hop Options
sub-registry of Internet Protocol Version 6 (IPv6) Parameters:

        Binary Value   Description                     Reference
        act chg rest
        --------------------------------------------------------------
        00   0  TBD    Identifier with Context Option  [This document]


This document also requests IANA to define a registry group named "Identifier
with Context". The following subsections detail the registries therein contained.

### Context-ID

This registry defines 65536 code points for the Context-ID field, in order to
know the context. The following code points are defined in this document:


        Code point       Description             Reference
        +--------+--------------------------+-----------------+
        | 0x0000 |         Reserved         |  This document  |
        +--------+--------------------------+-----------------+
        | 0x0001 |                          |                 |
        |  ....  |        Unassigned        |                 |
        | 0xffef |                          |                 |
        +--------+--------------------------+-----------------+
        | 0xfff0 |                          |                 |
        |  ....  | RFC3692-style Experiment |    [RFC8126]    |
        | 0xfffe |                          |                 |
        +--------+--------------------------+-----------------+
        | 0xffff |         Reserved         |  This document  |
        +--------+--------------------------+-----------------+


Unassigned code points are available for assignment via the "IETF Review"
process, as per {{!RFC8126}}. For a new registration request to be accepted, its
main purpose MUST be to carry an identifier. The aforementioned identifier MUST
be the centerpiece of the new context.

New registration requests MUST use the following template:

- Code point:  requested code point for the new Context-ID.
- Description:  name of the new Context-ID.
- Reference:  reference to the document that defines the new Context-ID.


# Security Considerations

As this document describes new options for IPv6, these are similar to the
security considerations of {{!RFC8200}} and the weakness documented in
{{!RFC8250}}.

This document does not define security considerations for the Context-Data field
of the Identifier with Context Option, which varies based on the Context-ID.
These custom data structures will have security considerations in their own
documents that define the new formats.


--- back

# Acknowledgments
{:numbered="false"}

