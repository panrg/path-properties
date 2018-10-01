---
title: A Vocabulary of Path Properties
abbrev: Path Properties
docname: draft-enghardt-panrg-path-properties-latest
date:
category: info

ipr: trust200902
area: IRTF
workgroup: PANRG
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: T. Enghardt
    name: Theresa Enghardt
    organization: TU Berlin
    email: theresa@inet.tu-berlin.de
 -
    ins: C. Krähenbühl
    name: Cyrill Krähenbühl
    organization: ETH Zürich
    email: cyrill.kraehenbuehl@inf.ethz.ch

informative:

    I-D.irtf-panrg-questions:

    RFC7556:

    ANRW18-Metrics: DOI.10.1145/3232755.3232764

--- abstract

This document defines and categorizes
information about Internet paths that an endpoint might have or want to have.
This information is expressed as properties of the path between two endpoints.

--- middle

# Introduction

Because the current Internet provides a best-effort bit pipe, endpoints do not know much about the path to other endpoints.
A Path Aware Network exposes information about one or multiple paths through the network to endpoints,
so that endpoints can use this information.

Such path properties may be relatively static,
e.g., the nature of the access technology on the first hop,
or relatively dynamic,
e.g., current Round Trip Time.

This document addresses the first of the questions
in Path-Aware Networking {{I-D.irtf-panrg-questions}}, which is a product of the PANRG in the IRTF.


# Static Properties

Static Path Properties are not expected to change very often: they stay constant within the lifetime of a connection to another endpoint.
They usually relate to the access network within the first hop or the first few hops.



Access Technology present on the path:
: The lower layer technology on the first hop, for example, WiFi, Wired Ethernet, or Cellular. This can also be more detailed, e.g., further specifying the Cellular as 2G, 3G, 4G, or 5G technology, or the WiFi as 802.11a, b, g, n, or ac. These are just examples, this list is not exhaustive, and there is no common index of identifiers here. Note that access technologies further along the path may also be relevant, e.g., a cellular backbone is not only the first hop, and there may be a DSL line behind the WiFi.

Monetary Cost:
: This is information related to billing, data caps, etc. It could be the allowed monthly data cap, the start and end of a billing period, the monetary cost per Megabyte sent or received, etc.




# Topology Properties

\[FIXME: Can we come up with a better name?]

Topology Properties can change within the lifetime of a connection to a another endpoint, but usually on the timescale of seconds, minutes, or hours, e.g., when the routes change.



Presence of certain device on the path:
: Could be the presence of a certain kind of middlebox, e.g., a proxy, a firewall, a NAT.

Presence of a specific Autonomous System (AS) on a path:
: Indicates that traffic goes through a specific AS.

Disjointness:
: How disjoint a path is from another path.

Path MTU
: The end-to-end maximum transmission unit in one packet.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over this path. An endpoint may know this because it has cached whether it could successfully establish, e.g., a QUIC connection, or an MPTCP subflow.

Protocol Features available:
: Whether a specific feature within a protocol is known to work over this path, e.g., ECN, or TCP Fast Open.




\[Idea: Something related to trust level -- Does traffic stay within the Intranet or not?]


# Dynamic Properties

Dynamic Path Properties are expected to change on the timescale of milliseconds.
They usually relate to the state of the path, such as the currently available end-to-end bandwidth.
Some of these properties may depend only on the first hop or on the access network, some may depend on the entire path.

Typically, Dynamic Properties can only be approximated and sampled, and might be made available in an aggregated form, such as averages or minimums.
Dynamic Path Properties can be measured by the endpoint itself or somethere in the network.
See {{ANRW18-Metrics}} for a discussion of how to measure some dynamic path properties at the endpoint.


These properties may be symmetric or asymmetric. For example, an asymmetric property may be different in the upstream direction and in the downstream direction from the point of view of a particular host.



Available bandwidth:
: Maximum number of bytes per second that can be sent or received over this path. This depends on the available bandwidth at the bottleneck, and on crosstraffic.

Round Trip Time:
: Time from sending a packet to receiving a response from the remote endpoint.

Round Trip Time variation:
: Disparity of Round Trip Time values either over time or among multiple concurrent connections. A high RTT variation often indicates congestion.

Packet Loss:
: Percentage of sent packets that are not received on the other end.

Congestion:
: Whether there is any indication of congestion on the path.

Wireless Signal strength:
: Power level of the wireless signal being received. Lower signal strength, relative to the same noise floor, is correlated with higher bit error rates and lower modulation rates.

Wireless Modulation Rate:
: Modulation bitrate of the wireless signal. The modulation rate determines how many bytes are transmitted within a symbol on the wireless channel. A high modulation rate leads to a higher possible bitrate, given sufficient signal strength.

Wireless Channel utilization:
: Percentage of time during which there is a transmission on the wireless medium. A high channel utilization indicates a congested wireless network.



# Technologies to disseminate Path Properties


\[TODO: Is this in scope for this draft?]

Provisioning Domain:
: A consistent set of network configuration information as defined in {{RFC7556}}, e.g., relating to a local network interface. This may include source IP address prefixes, IP addresses of DNS servers, name of an HTTP proxy server, DNS suffixes associated with the network, or default gateway IP address. As one PvD is not restricted to one local network interface, a PvD may also apply to multiple paths.



# Security Considerations

Some of these properties may have security implications for endpoints.
For example, a corporate policy might require to have a firewall on the path.

If those properties are provided by the network, there is the question of whether an endpoint can trust them.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

Thanks to Paul Hoffman for feedback and editorial changes.
