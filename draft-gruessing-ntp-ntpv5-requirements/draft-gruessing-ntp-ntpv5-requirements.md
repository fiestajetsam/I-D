---
title: "NTPv5 use cases and requirements"
abbrev: "NTPv5 use cases and requirements"
docname: draft-gruessing-ntp-ntpv5-requirements-01
date: {DATE}

author:
    -
      ins: J. Gruessing
      name: James Gruessing
      email: james.ietf@gmail.com

ipr: trust200902
category: info
area: Internet
workgroup: Network Time Protocol
keyword: Internet-Draft
stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, subcompact, comments, inline]

normative:
    RFC2119:
    RFC5905:
    RFC7808:
    RFC8174:
    I-D.ietf-ntp-roughtime:

informative:
    ntp-misuse:
        title: "NTP server misuse and abuse"
        target: https://en.wikipedia.org/wiki/NTP_server_misuse_and_abuse
    ntppool:
        title: "pool.ntp.org: the internet cluster of ntp servers"
        target: "https://www.ntppool.org"
    IEEE-1588-2008:
        title: "IEEE Standard for a Precision Clock Synchronization Protocol for Networked Measurement and Control Systems"
        doi: "10.1109/IEEESTD.2008.4579760"

--- abstract

This document describes the use cases, requirements, and considerations that
should be factored in the design of a successor protocol to supercede version 4
of the NTP protocol {{!RFC5905}} presently referred to as NTP version 5
("NTPv5"). This document is non-exhaustive and does not in its current version
represent working group consensus.

--- note_Note_to_Readers

*RFC Editor: please remove this section before publication*

Source code and issues for this draft can be found at
<https://github.com/fiestajetsam/I-D/tree/main/draft-gruessing-ntp-ntpv5-requirements>.

--- middle

# Introduction

NTP version 4 {{!RFC5905}} has seen active use for over a decade, and within
this time period the protocol has not only been extended to support new
requirements but also fallen victim to vulnerabilities that have made it used
for distributed denial of service (DDoS) amplification attacks.

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Use cases and existing deployments of NTP

There are several common scenarios for exxisting NTPv4 deployments; publicly
accessible NTP services such as the NTP Pool {{ntppool}} are used to offer clock
synchronisation for end users and embedded devices, ISP provided servers to
synchronise devices such as customer-premesis equipment where reduced accuracy
may be tollerable. Depending on the network and path these deployments may be
affected by variable latency as well as throttling or blocking by providers.

Data centres and cloud computing providers also have deployed and offer NTP
services both for internal use and for customers, particularly where the network
is unable to offer or does not require PTP {{IEEE-1588-2008}}. As these
deployments are less likely to be constrained by network latency or power the
potential for higher levels of accuracy and precision within the bounds of the
protocol are possible.

# Requirements

At a high level, NTPv5 should be a protocol that is capable of operating in both
local networks and also over public internet connections where packet loss,
delay, and even filtering may occur.

## Resource mamangement

Historically there have been many documented instances of NTP servers taking a
large increase in unauthorised traffic {{ntp-misuse}} and the design of NTPv5
must ensure the risk of these can be minimised to the fullest extent.

Servers SHOULD have a new identifier that peers use as reference, this SHOULD
NOT be a FQDN, an IP address or identifier tied to a public certificate. Servers
SHOULD be able to migrate and change their identifiers as stratum topologies or
network configuration changes occur.

The specification MUST have support for servers to notify clients that the
service is unavailable, and clients MUST have clearly defined behaviours
honouring this signalling. In addition to this servers SHOULD be able to
communicate to clients that they should reduce their query interval rate when
the server is under high bandwidth or has reduced capacity.

Clients SHOULD re-establish connections with servers at an interval to prevent
attempting to maintain connectivity to a dead host and give network operators
the ability to move traffic away from hosts in a timely manner.

## Algorithms

Algorithms describing functions such as clock filtering, selection and
clustering SHOULD be omitted from the protocol specification; the specification
should instead only provide what is necessary to describe protocol semantics and
normative behaviours.

The working group should consider creating a separate informational document to
describe an algorithm to assist with implementation, and to consider adopting
future documents which describe new algorithms as they are developed. Specifying
client algorithms separately from the protocol allows will allow NTPv5 to meet
the needs of applications with a variety of network properties and performance
requirements. It also allows for innovation in implementations without
sacrificing basic interoperability.

## Timescales

The protocol SHOULD adopt a linear, monotonic timescale as the basis for
communicating time. The format should meet sufficient scale and precision with
resolution either meeting or exceeding NTPv4, and have a rollover date
sufficiently far enough into the future that the protocol's complete
obsolescence is most likely to occur first. The timescale in addition to any
other time sensitive information must be sufficient to calculate representations
of both UTC and TAI. Through extensions the protocol SHOULD support additional
timescale representations outside of the main specification, and all
transmissions of time data SHALL indicate the timescale in use.

## Leap seconds

Support for UTC leap second information MUST be included in the protocol
specification in order for clients to generate a UTC representation but must be
transmitted as separate information to the timescale. The specification SHOULD
also be capable of transmitting upcoming leap seconds greater than 1 calendar
day in advance.

Leap second smearing SHOULD NOT be part of the wire specification, however this
should not prevent implementors from applying leap second smearing between the
client and any clock it is training but MUST NOT be applied to downstream
clients.

## Backwards compatibility to NTS and NTPv4

The support for compatibility with other protocols should not prevent addressing
issues that have previous caused issues in deployments or cause ossification of
the protocol.

Protocol ossification MUST be addressed to prevent existing NTPv4
deployments which incorrectly respond to clients posing as NTPv5 from causing
issues. Forward prevention of ossification (for a potential NTPv6 protocol in
the future) should also be taken into consideration.

The model for backward compatibility is servers that support mutliple versions
NTP and send a response in the same version as the request. This does not
preclude servers from acting as a client in one version of NTP and
a server in another.

## Extensibility

The protocol MUST have the capability to be extended, and that implementations
MUST ignore unknown extensions. Unknown extensions received by a server from a
lower stratum server SHALL not be added to response messages sent by the server
receiving these extensions.

# IANA Considerations

Considerations should be made about the future of the existing IANA registry
for NTPv4 parameters. If NTPv5 becomes incompatible with these parameters a new
registry SHOULD be created.

# Security Considerations

Encryption and authentication MUST be provided by the protocol specification as
a default and MUST be resistent to downgrade attacks. The encryption used must
have agility, allowing for the protocol to update as more secure cryptography
becomes known and vulnerabilities are discovered.

The specification MAY consider leaving room for middleboxes which may
deliberately modify packets in flight for legitimate purposes. Thought must be
given around how this will be incorporated into any applicable trust model.
Downgrading attacks that could lead to an adversary disabling or removing
encryption or authentication MUST NOT be possible in the design of the protocol.

Detection and reporting of server malfeasence SHOULD remain out of scope of this
specification as {{!I-D.ietf-ntp-roughtime}} already provides this capability as
a core functionality of the protocol.

--- back

# Acknowledgements

The author would like to thank Doug Arnold and Hal Murray for contributions to
this document, and would like to acknowledge Daniel Franke, Watson Ladd,
Miroslav Lichvar for their existing documents and ideas. The author would also
like to thank Angelo Moriondo, Franz Karl Achard, and Malcom McLean for
providing the author with motivation.
