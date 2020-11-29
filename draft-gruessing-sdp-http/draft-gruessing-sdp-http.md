---
title: "SDP Mapping into HTTP structured headers"
abbrev: "SDP Mapping into HTTP structured headers"
docname: draft-gruessing-sdp-http-latest
date: {DATE}

author:
    -
      ins: J. Gruessing
      name: James Gruessing
      email: james.ietf@gmail.com

ipr: trust200902
category: std
area: Applications and Real-Time
workgroup: Multiparty Multimedia Session Control
keyword: Internet-Draft
stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, subcompact, comments, inline]

normative:
    RFC2119:
    RFC3864:
    RFC3986:
    RFC5322:
    RFC4566:
    RFC8174:
    I-D.ietf-httpbis-header-structure:
    "E.164":
        title: "The international public telecommunication numbering plan"
        target: "https://www.itu.int/rec/dologin_pub.asp?lang=e&id=T-REC-E.164-201011-I!!PDF-E&type=items"
        date: November 2010

--- abstract

This document specifies a HTTP header based representation of the Session
Description Protocol which can be used in describing media being negotiated or
delivered via HTTP.

--- note_Note_to_Readers

*RFC Editor: please remove this section before publication*

Source code and issues for this draft can be found at
<https://github.com/fiestajetsam/I-D/tree/main/draft-gruessing-sdp-http>.

--- middle

# Introduction

Services either negotiating or offering media over HTTP may want to express a
greater amount of information beyond a MIME type of content and its preference.

The Session Description Protocol {{RFC4566}} describes multimedia sessions for
the purpose of session announcement and initiation.

The Session-Description and Session-Media headers may be used for either a HTTP
request or response and may be included as part of any HTTP method.

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# The Session-Description Header

The Session-Description header field conveys the entire session description
information, using {{!I-D.ietf-httpbis-header-structure}} to describe the
structure. Its value MUST be a dictionary containing containing only the
following keys, receivers MUST ignore all other values. Dictionary keys MUST be
ordered in the order presented in this document, but MAY be omitted where they
are explicitly declared as OPTIONAL.

### Session Description

v
 : The version, represented as an sh-integer that MUST be set 0.

o
 : The originator of the session, whose value is an sh-list. The order of values
   present in the list MUST map to the order specified in Section 5.2 of
   {{RFC4566}}.

s
 : The name of the SDP, whose values is a string and MUST NOT be empty.

i
 : An OPTIONAL description of the session, whose value is a string.

u
 : An OPTIONAL URI reference containing additional information about the
   session, whose value is a string and SHOULD be represented as {{RFC3986}}.

e
 : An OPTIONAL email address, whose value is a string and SHOULD be represented
   using {{RFC5322}} address semantics.

p
 : An OPTIONAL phone number whose value is a string, which SHOULD be represented
   as {{E.164}}.

c
 : An OPTIONAL field containing connection data, whose value is an inner-list, and
   whose elements are each a string type matching the order of elements as
   defined in Section 5.7 of {{RFC4566}}. This field MUST be set if no media
   entities in the description contain connection information.

b
 : An OPTIONAL field containing the proposed bandwidth to be used by the
   session, whose value is an inner-list with only two elements, the first being a
   string type whose value corresponds to a `bwtype` as listed in the IANA
   registry, or a string prefixed "X-" to denote an experiemental value. The
   second element is an integer type and MUST NOT be negative.

k
 : An OPTIONAL field containing encryption key information, whose field is an
   inner-list containing up to two elements. The first element

### Time Description

All values within time description fields that represent wall time MUST be
values shown as integers which represent NTP timestamps with second resolution.
To facilitate ease of parsing, fields that are used to represet a time duration
or offset as described in Section 5.10 of {{RFC4566}} MUST NOT use the compact
version e.g "1h" instead of "3600".

t
 : An OPTIONAL field containing the start and end times of the session, whose
   value is an inner-list containing two elements - the first being the wall time
   start time, and the latter being the stopping time.

r
 : An OPTIONAL field containing the repeat times, whose value is an inner-list
   containing three elements. The first element contains the repeat interval
   whose value is an integer, and the second element containing the active
   duration whose value is an integer. The third element of the offsets from
   start-time whose value is an inner-list containing two elements containing
   integer values representing the offsets.

z
 : An OPTIONAL field containing time zone adjustment information, whose value is
   an inner-list containing elements where each is an inner-list with two elements;
   the first element being an integer representing the wall time which the
   adjustment should take place, and the second element whose value is an
   integer representing the offset.

a
 : An OPTIONAL field containing session-level attributes, whose value is an
   inner-list. Each element may either be a sh-dictionary when representing a value
   attribute, or a string where it is a property attribute. For value
   attributes, the contents MUST be a single name/value pair with the name being
   the attribute name, and the value as a string.

## The Session-Media Header

The Session-Media header describes each media element within the session, and at
the top level is an sh-list.

Each media representation may additionally contain a media title (i), connection
information (c), bandwidth (b), or encryption key (k).

## Implementation Considerations

The Session-Description header MAY be sent at the same time in a response with a
HTTP body that also contains the SDP payload for backwards compatibility. In
such case the values of the header MUST be identical in semantic meaning to the
body payload and not include additional information or redaction. It may also,
dependant on implementation be sent in response to a HEAD request - in such
cases the body MUST be omitted but the server MUST also send the
"application/sdp" `Content-Type` HTTP header.

### Character set usage

TODO: Cover character sets

# Examples

TODO: Examples

# IANA Considerations

This specification registers the following entry in the Permanent Message Header
Field Names registry established by {{!RFC3864}}:

   o  Header field name: Session-Description

   o  Applicable protocol: http

   o  Status: standard

   o  Author/Change Controller: IETF

   o  Specification document(s): \[this document\]

   o  Related information:



   o  Header field name: Session-Media

   o  Applicable protocol: http

   o  Status: standard

   o  Author/Change Controller: IETF

   o  Specification document(s): \[this document\]

   o  Related information:


# Security Considerations

TODO: Incorporate things like secure transport (HTTPS), in addition to
considerations raised in the structured header draft.

--back

# Acknowledgements

TODO
