---
title: "HTTP Live Streaming Manifest Template Extension"
abbrev: "HTTP Live Streaming Template Extension"
docname: draft-gruessing-hls-template-latest
date: {DATE}
author:
    -
      ins: J. Gruessing
      name: James Gruessing
      email: james.ietf@gmail.com

ipr: trust200902
category: info 
keyword: Internet-Draft
stand_alone: yes
pi: [toc, sortrefs, symrefs]

normative:
    RFC2119:
    RFC8174:
    RFC8216:

informative:
    DASH:
        name: Guidelines for Implementation: DASH-IF Interoperability Points
        target: https://dashif.org/docs/DASH-IF-IOP-v4.2-clean.htm
        date: April 9th 2018

--- abstract

This document describes extensions to the HTTP Live Streaming Protocol
({{!RFC8216}}) that define additional playlist tags to enable a client to
calculate the segment URLs from a provided template, instead of an explicit
listing of segment file names.

--- middle

# Introduction

HTTP Live Streaming {{RFC8216}}} defines a protocol for streaming of media
such as audio or video. However, when clients are performing requests for
Variant Streams they are required to make both requests for the media but also
for the manifest to be advised of changes to new segments being published. This
behaviour comes with a few notable issues; firstly the manifests themselves are
not cache efficient as they can be updated every few seconds in the duration of
a live stream, secondly the number of requests being made by the client is
increased. If the event or window of continuous live streaming is sufficiently
long, even with compression the size of a manifest may exceed that of the
segment being requested, bringing with it a notable inefficiency.

To address this, other streaming protocols like {{DASH}} have functionality
to define the URL structure for segments instead of an absolute reference to the
segments themselves, allowing the segment requests to be predictable in nature
given a few variables of information which can be used to calculate the
necessary URL.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Segment naming convention

Segments MUST have a continuous, predictable sequence to URL of which they may
be requested. This SHOULD either be a representation of current time, or as
another incrementing, unique value which can be computing entirely from other
metadata that is nominally supplied as part of the manifest.

# Manifest Request Flow

## Time Acquisition

As client clocks may be inaccurate or deviate from the media encoder,
clients MUST acquire current UTC time from an external source.

TODO: Servers sending a template MUST also send a Date response, and client
implementations MUST be able to see the headers We could also stipulate
additional means of requesting it?

TODO: How to handle `EXT-X-DISCONTINUITY` - solving this *may* address the
issues that multi-period DASH have faced if done right.

# Media Segment Tags

This document defines several additional segment types present.

## EXT-X-VERSION

The version declared by manifests MUST be at least 9.

## EXT-X-BASE-MEDIA-URL

The base media URL contains either a relative or absolute URL that serves as a
template to describe how segment URLs are to be composed.

TODO: Consider this being a dictionary instead, having a largely identical
layout to EXT-X-MAP which can have a BYTERANGE as well as URI.

## EXT-X-BUFFERDEPTH

The buffer depth refers to the total non-negative whole number of addressable
segments that can be fetched by a client from the present into the past. When
an explicit list of segments is not provided, this tag MUST be provided.

## EXT-X-UPDATE-INTERVAL

This field is used for clients to update the template manifest.

# Examples

    #EXTM3U
    #EXT-X-VERSION:3
    #EXT-X-INDEPENDENT-SEGMENTS
    #EXT-X-TARGETDURATION:2
    #EXT-X-BASE-MEDIA-URL:"https://playlist.example/{}.ts"
    #EXT-X-BUFFER-DEPTH:50

# Security Considerations

TODO: Discuss URI precomputation as a risk to conditional access

# IANA Considerations

There are no IANA considerations for this document.

--- back

# Acknowledgments
{:numbered="false"}
TODO
