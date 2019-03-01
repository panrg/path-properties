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
This information is expressed as properties of paths between two endpoints.

--- middle

# Introduction

Because the current Internet provides an IP-based best-effort bit pipe, endpoints have little information about paths to other endpoints.
A Path Aware Network exposes information about one or multiple paths through the network to endpoints,
so that endpoints can use this information.

Such path properties may be relatively dynamic, e.g. current Round Trip Time, close to the origin, e.g. nature of the access technology on the first hop, or far from the origin, e.g. list of ASes traversed.

Usefulness over time is fundamentally different for dynamic and non-dynamic properties.
The merit of a momentary measurement of a dynamic path property diminishes greatly as time goes on, e.g. the merit of an RTT measurement from a few seconds ago is quite small, while a non-dynamic path property might stay relevant, e.g. a NAT can be assumed to stay on a path during the lifetime of a connection, as the removal of the NAT would break the connection.

Non-dynamic properties are further separated into (local) domain properties related to the first few hops of the connection, and backbone properties related to the remaining hops.
Domain properties expose a high amount of information to endpoints and strongly influence the connection behavior while there is little influence and information about backbone properties.

Dynamic properties are not separated into domain and backbone properties, since most of these properties are defined for a complete path and it is difficult and seldom useful to define them on part of the path.
There are exceptions such as dynamic wireless access properties, but these do not justify separation into different categories.

This document addresses the first of the questions
in Path-Aware Networking {{I-D.irtf-panrg-questions}}, which is a product of the PANRG in the IRTF.


# Domain Properties

Domain path properties usually relate to the access network within the first hop or the first few hops.
Endpoints can influence domain properties for example by switching from a WiFi to a cellular interface, changing their data plan to increase throughput, or moving closer to a wireless access point which increases the signal strength.

A large amount of information about domain properties exists.
Properties related to configuration can be queried using provisioning domains (PvDs).
A PvD is a consistent set of network configuration information as defined in {{RFC7556}}, e.g., relating to a local network interface.
This may include source IP address prefixes, IP addresses of DNS servers, name of an HTTP proxy server, DNS suffixes associated with the network, or default gateway IP address.
As one PvD is not restricted to one local network interface, a PvD may also apply to multiple paths.

Access Technology present on the path:
: The lower layer technology on the first hop, for example, WiFi, Wired Ethernet, or Cellular. This can also be more detailed, e.g., further specifying the Cellular as 2G, 3G, 4G, or 5G technology, or the WiFi as 802.11a, b, g, n, or ac. These are just examples, this list is not exhaustive, and there is no common index of identifiers here. Note that access technologies further along the path may also be relevant, e.g., a cellular backbone is not only the first hop, and there may be a DSL line behind the WiFi.

Monetary Cost:
: This is information related to billing, data caps, etc. It could be the allowed monthly data cap, the start and end of a billing period, the monetary cost per Megabyte sent or received, etc.


# Backbone Properties

Backbone path properties relate to non-dynamic path properties that are not within the endpoint's domain.
They are likely to stay constant within the lifetime of a connection, since Internet "backbone" routes change infrequently.
These properties usually change on the timescale of seconds, minutes, or hours, when the route changes.

Even if these properties change, endpoints can neither specify which backbone nodes to use, nor verify data was sent over these nodes.
An endpoint can for example choose its access provider, but cannot choose the backbone path to a given destination since the access provider will make their own policy-based routing decision.

Presence of certain device on the path:
: Could be the presence of a certain kind of middlebox, e.g., a proxy, a firewall, a NAT.

Presence of a packet forwarding node or specific Autonomous System on a path:
: Indicates that traffic goes through a certain node or AS, which might be relevant for deciding the level of trust this path provides.

Disjointness:
: How disjoint a path is from another path.

Path MTU:
: The maximum size, in octets, of an IP packet that can be transmitted without fragmentation on a path segment or path.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over this path. An endpoint may know this because it has cached whether it could successfully establish, e.g., a QUIC connection, or an MPTCP subflow.

Protocol Features available:
: Whether a specific feature within a protocol is known to work over this path, e.g., ECN, or TCP Fast Open.


# Dynamic Properties

Dynamic Path Properties are expected to change on the timescale of milliseconds.
They usually relate to the state of the path, such as the current end-to-end latency.
Some of these properties may depend only on the first hop or on the access network, some may depend on the entire path.

Typically, Dynamic Properties can only be approximated and sampled, and might be made available in an aggregated form, such as averages or minimums.
Dynamic Path Properties can be measured by the endpoint itself or somethere in the network.
See {{ANRW18-Metrics}} for a discussion of how to measure some dynamic path properties at the endpoint.


These properties may be symmetric or asymmetric. For example, an asymmetric property may be different in the upstream direction and in the downstream direction from the point of view of a particular host.


Maximum Data Rate (Transmit/Receive):
: The theoretical maximum data rate, in bits per second, that can be achieved on a link, path segment, or path, for receiving or transmitting traffic.

Current Data Rate (Transmit/Receive):
: The data rate, in bits per second, at which a link is currently receiving or transmitting traffic.

Round Trip Time:
: Time from sending a packet to receiving a response from the remote endpoint.

Round Trip Time variation:
: Disparity of Round Trip Time values either over time or among multiple concurrent connections. A high RTT variation often indicates congestion.

Packet Loss:
: Percentage of sent packets that are not received on the other end.

Congestion:
: Whether there is any indication of congestion on the path.


# Security Considerations

Some of these properties may have security implications for endpoints.
For example, a corporate policy might require to have a firewall on the path.

For properties provided by the network, their authenticity and correctness may need to be verified by an endpoint.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

Thanks to Paul Hoffman for feedback and editorial changes.
