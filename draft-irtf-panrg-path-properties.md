---
title: A Vocabulary of Path Properties
abbrev: Path Properties
docname: draft-irtf-panrg-path-properties-latest
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
    email: ietf@tenghardt.net
 -
    ins: C. Krähenbühl
    name: Cyrill Krähenbühl
    organization: ETH Zürich
    email: cyrill.kraehenbuehl@inf.ethz.ch

informative:

    I-D.irtf-panrg-questions:

    I-D.ietf-tcpm-converters:

    I-D.ietf-quic-transport:

    I-D.ietf-idr-performance-routing:

    RFC3357:

    RFC3393:

    RFC4271:

    RFC5136:

    RFC5693:

    RFC6534:

    RFC7665:

    RFC7679:

    RFC7680:

    RFC8175:

    ANRW18-Metrics: DOI.10.1145/3232755.3232764

--- abstract

Path properties express information about paths across a network and the services provided via such paths.
In a path-aware network, path properties may be fully or partially available to entities such as hosts.
This document defines and categorizes path properties.
Furthermore, the document specifies several path properties which might be useful to hosts or other entities,
e.g., for selecting between paths or for invoking some of the provided services.

--- middle

# Introduction

In the current Internet architecture, hosts generally do not have information about forwarding paths through the network and about services associated with these paths.
A path-aware network, as introduced in {{I-D.irtf-panrg-questions}}, exposes information about paths to hosts or to other entities.
This document defines such information as path properties, addressing the first of the questions in path-aware networking {{I-D.irtf-panrg-questions}}.

As terms related to paths have different meanings in different areas of networking, first, this document provides a common terminology to define paths, path elements, and path properties.
Then, this document provides some examples for use cases for path properties.
Finally, the document lists several path properties that may be useful for the mentioned use cases.

Note that this document does not assume that any of the listed path properties are actually available to any entity. The question of how entities can discover and distribute path properties in a trustworthy way is out of scope for this document.

# Terminology

Node:
: An entity which processes packets, e.g., sends, receives, forwards, or modifies them. A node may be physical or virtual, e.g., a physical device or a service function provided as a virtual element. A node may also be the collection of multiple entities which, as a collection, processes packets, e.g., an entire Autonomous System (AS).

Host:
: A node that generally executes application programs on behalf of user(s), employing network and/or Internet communication services in support of this function, as defined in {{?RFC1122}}.

Link:
: A medium or communication facility that connects two or more nodes with each other. A link enables a node to send packets to other nodes.
Links can be physical, e.g., a Wi-Fi network which connects an Access Point to stations, or virtual, e.g., a virtual switch which connects two virtual machines hosted on the same physical machine. A link is unidirectional. As such, bidirectional communication can be modeled as two links between the same nodes in opposite directions.

Path element:
: Either a node or a link.

Path:
: A sequence of adjacent path elements over which a packet can be transmitted, starting and ending with a node. A path is unidirectional. Paths are time-dependent, i.e., the sequence of path elements over which packets are sent from one node to another may change. A path is defined between two nodes. For multicast or broadcast, a packet may be sent by one node and received by multiple nodes. In this case, the packet is sent over multiple paths at once, one path for each combination of sending and receiving node; these paths do not have to be disjoint. Note that an entity may have only partial visibility of the path elements that comprise a path and visibility may change over time. Different entities may have different visibility of a path and/or treat path elements at different levels of abstraction. For example, a path may be given as a sequence of physical nodes and the links connecting these nodes, or it may be given as a sequence of logical nodes such as a sequence of ASes or an Explicit Route Object (ERO). Similarly, the representation of a path and its properties, as it is known to a specific entity, may be more complex and include details about the physical layer technology, or it may be more abstract and only consist of a specific source and destination which is known to be reachable from that source.

Reverse Path:
: The path that is used by a remote node in the context of bidirectional communication.

Subpath:
: Given a path, a subpath is a sequence of adjacent path elements of this path.

Flow:
: An entity made of packets to which the traits of a path or set of subpaths may be applied in a functional sense. For example, a flow can consist of all packets sent within a TCP session with the same five-tuple between two hosts, or it can consist of all packets sent on the same physical link.

Property:
: A trait of one or a sequence of path elements, or a trait of a flow with respect to one or a sequence of path elements. An example of a link property is the maximum data rate that can be sent over the link. An example of a node property is the administrative domain that the node belongs to. An example of a property of a flow with respect to a subpath is the aggregated one-way delay of the flow being sent from one node to another node over this subpath.
A property is thus described by a tuple containing the path element(s), the flow or an empty set if no packets are relevant for the property, the name of the property (e.g., maximum data rate), and the value of the property (e.g., 1Gbps).

Aggregated property:
: A collection of multiple values of a property into a single value, according to a function. A property can be aggregated over multiple path elements (i.e., a subpath), e.g., the MTU of a path as the minimum MTU of all links on the path, over multiple packets (i.e., a flow), e.g., the median one-way latency of all packets between two nodes, or over both, e.g., the mean of the queueing delays of a flow on all nodes along a path.
The aggregation function can be numerical, e.g., median, sum, minimum, it can be logical, e.g., "true if all are true", "true if at least 50\% of values are true", or an arbitrary function which maps multiple input values to an output value.

Observed property:
: A property that is observed for a specific path element, subpath, or path, e.g., using measurements. For example, the one-way delay of a specific packet transmitted from one node to another node can be measured.

Assessed property:
: An approximate calculation or assessment of the value of a property. An assessed property includes the reliability of the calculation or assessment. The notion of reliability depends on the property.
For example, a path property based on an approximate calculation may describe the expected median one-way latency of packets sent on a path within the next second, including the confidence level and interval. A non-numerical assessment may instead include the likelihood that the property holds.

# Use Cases for Path Properties {#use-cases}

When a path-aware network exposes path properties to hosts or other entities,
these entities may use this information to achieve different goals.
This section lists several use cases for path properties.
Note that this is not an exhaustive list, as with every new technology and protocol, novel use cases may emerge, and new path properties may become relevant.

## Path Selection

Entities can choose what traffic to send over which path or subset of paths.
A node might be able to select between a set of paths, either if there are several paths to the same destination (e.g., in case of a mobile device with two wireless interfaces, both providing a path), or if there are several destinations, and thus several paths, providing the same service (e.g., Application-Layer Traffic Optimization (ALTO) {{RFC5693}}, an application layer peer-to-peer protocol allowing hosts a better-than-random peer selection).
Care needs to be taken when selecting paths based on path properties, as path properties that were previously measured may not be helpful in predicting current or future path properties and such path selection may lead to unintended feedback loops.

Entities may select their paths to fulfill a specific goal, e.g., related to security or performance.
As an example of security-related path selection, an entity may allow or disallow sending traffic over paths involving specific networks or nodes to enforce traffic policies. In an enterprise network where all traffic has to go through a specific firewall, a path-aware host can implement this policy using path selection, in which case the host needs to be aware of paths involving that firewall.
As an example of performance-related path selection,
an entity may prefer paths with performance properties that best match its traffic requirements.
For example, for sending a small delay sensitive query, a host may select a path with a short One-Way Delay,
while for retrieving a large file, it may select a path with high Link Capacities on all links.
Note, there may be trade-offs between path properties (e.g., One-Way Delay and Link Capacity), and entities may influence these trade-offs with their choices.
As a baseline, a path selection algorithm should aim to not perform worse than the default case most of the time.

Path selection can be done both by hosts and by entities within the network:
A network (e.g., an AS) can adjust its path selection for internal or external routing based on path properties.
In BGP, the Multi Exit Discriminator (MED) attribute is used in the decision-making process to select which path to choose among those having the same AS PATH length and origin {{RFC4271}}; in a path aware network, instead of using this single MED value, other properties such as Link Capacity or Link Usage could additionally be used to improve load balancing or performance {{I-D.ietf-idr-performance-routing}}.

## Protocol Selection

When sending traffic over a specific path, an entity may select an appropriate protocol or configure protocol parameters depending on path properties.
A host may cache state on whether a path allows the use of QUIC {{I-D.ietf-quic-transport}} and if so, first attempt to connect using QUIC before falling back to another protocol when connecting over this path again.
A video streaming application may choose an (initial) video quality based on the achievable data rate or the monetary cost of sending data (e.g., volume-base or flat-rate cost model).

## Service Invocation

Conversely to path or protocol selection, in addition to selecting a protocol to use over a specific adjacent path element, an entity may choose to invoke additional functions influencing the nodes to be involved in the path.
For example, a 0-RTT Transport Converter {{I-D.ietf-tcpm-converters}} will be involved in a path only when invoked by a host; such invocation will lead to the use of MPTCP or TCPinc capabilities while such use is not supported via the default forwarding path.
Another example is a connection which is composed of multiple streams where each stream has specific service requirements. A host may decide to invoke a given service function (e.g., transcoding) only for some streams while others are not processed by that service function.

# Examples of Path Properties

This Section gives some examples of path properties which may be useful, e.g., for the use cases described in {{use-cases}}.

Path properties may be relatively dynamic, e.g., the one-way delay of a packet sent over a specific path, or non-dynamic, e.g., the MTU of an Ethernet link which only changes infrequently.
Usefulness over time differs depending on how dynamic a property is:
The merit of a momentary measurement of a dynamic path property diminishes greatly as time goes on, e.g. the merit of an RTT measurement from a few seconds ago is quite small, while a non-dynamic path property might stay relevant for a longer period of time, e.g. a NAT typically stays on a specific path during the lifetime of a connection involving packets sent over this path.

From the point of view of a host, path properties may relate to path elements close to the host, i.e., within the first few hops, or they may include path elements far from the host, e.g., list of ASes traversed.
The visibility of path properties to a specific entity may depend on factors such as the physical or network distance or the existence of trust or contractual relationships between the entity and the path element(s).

Furthermore, entities may or may not be able to influence the path elements on their path and their path properties.
For example, a user might select between multiple potential adjacent links by selecting between multiple available Wi-Fi Access Points. Or when connected to an Access Point, the user may move closer to enable their device to use a different access technology, potentially increasing the data rate available to the device.
Another example is a user changing their data plan to reduce the Monetary Cost to transmit or receive a given amount of data across a network.


Access Technology:
: The physical or link layer technology used for transmitting or receiving a flow on one or multiple path elements. If known, the Access Technology may be given as an abstract link type, e.g., as Wi-Fi, Wired Ethernet, or Cellular. It may also be given as a specific technology used on a link, e.g., 2G, 3G, 4G, or 5G cellular, or 802.11a, b, g, n, or ac Wi-Fi. Other path elements relevant to the access technology may include nodes related to processing packets on the physical or link layer, such as elements of a cellular backbone network. Note that there is no common registry of possible values for this property.

Monetary Cost:
: The price to be paid to transmit or receive a specific flow across a network to which one or multiple path elements belong.

Service function:
: A service function that a path element applies to a flow, see {{RFC7665}}. Examples of abstract service functions include firewalls, Network Address Translation (NAT), and TCP optimizers. Some stateful service functions, such as NAT, require the same instance to be involved in both directions, i.e., on the path and the reverse path.

Transparency:
: A node is transparent with respect to a protocol if it does not modify headers of this protocol and it processes packets independently of protocol-specific meta-information.
An IP router could be transparent for transport protocols such as TCP and UDP, in contrast to a NAT that actively modifies TCP and UDP header information.

Administrative Domain:
: The administrative domain, e.g., the IGP area, AS, or Service provider network to which a path element belongs.

Disjointness:
: For a set of two paths or subpaths, the number of shared path elements can be a measure of intersection (e.g., Jaccard coefficient, which is the number of shared elements divided by the total number of elements). Conversely, the number of non-shared path elements can be a measure of disjointness (e.g., 1 - Jaccard coefficient). A multipath protocol might use disjointness as a metric to reduce the number of single points of failure.

Symmetric Path:
: Two paths are symmetric if a path and its reverse path consist of the same path elements, but in reverse order, i.e., the paths have a Jaccard coefficient of one.

Path MTU:
: The maximum size, in octets, of an IP packet that can be transmitted without fragmentation.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over a path or subpath, e.g., whether the path is QUIC-capable or MPTCP-capable, based on cached knowledge.

Protocol Features available:
: Whether a specific protocol feature is available over a path or subpath, e.g., Explicit Congestion Notification (ECN), or TCP Fast Open.


Some path properties express the performance of the transmission of a packet or flow over a link or subpath.
Such transmission performance properties can be measured or approximated, e.g., by hosts or by path elements on the path. They might be made available in an aggregated form, such as averages or minimums.
See {{ANRW18-Metrics}} for a discussion of how to measure some transmission performance properties at the host.
Properties related to a path element which constitutes a single layer 2 domain are abstracted from the used physical and link layer technology, similar to {{RFC8175}}.


Link Capacity:
: The link capacity is the maximum data rate at which data that was sent over a link can correctly be received at the node adjacent to the link.
This property is analogous to the link capacity defined in {{RFC5136}} but not restricted to IP-layer traffic.

Link Usage:
: The link usage is the actual data rate at which data that was sent over a link is correctly received at the node adjacent to the link.
This property is analogous to the link usage defined in {{RFC5136}} but not restricted to IP-layer traffic.

One-Way Delay:
: The one-way delay is the delay between a node sending a packet and another node on the same path receiving the packet.
This property is analogous to the one-way delay defined in {{RFC7679}} but not restricted to IP-layer traffic.

One-Way Delay Variation:
: The variation of the one-way delays within a flow.
This property is similar to the one-way delay variation defined in {{RFC3393}} but not restricted to IP-layer traffic and defined for packets on the same flow instead of packets sent between a source and destination IP address.

One-Way Packet Loss:
: Packets sent by a node but not received by another node on the same path after a certain time interval are considered lost.
This property is analogous to the one-way loss defined in {{RFC7680}} but not restricted to IP-layer traffic.
Metrics such as loss patterns {{RFC3357}} and loss episodes {{RFC6534}} can be expressed as aggregated properties.


# Security Considerations

If nodes are basing policy or path selection decisions on path properties, they need to rely on the accuracy of path properties that other devices communicate to them.
In order to be able to trust such path properties, nodes may need to establish a trust relationship or be able to verify the authenticity, integrity, and correctness of path properties received from another node.

Security related properties such as confidentiality and integrity protection of payloads are difficult to characterize since they are only meaningful with respect to a threat model which depends on the use case, application, environment, and other factors.
Likewise, properties for trust relations between nodes cannot be meaningfully defined without a concrete threat model, and defining a threat model is out of scope for this draft.
Properties related to confidentiality, integrity, and trust are orthogonal to the path terminology and path properties defined in this document.
Such properties are tied to the communicating entities and protocols used (e.g., client and server using HTTPS, or client and remote network node using VPN) while the path is typically oblivious to them.
Intuitively, the path describes what function the network applies to packets, while confidentiality, integrity, and trust describe what function the communicating parties apply to packets.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

Thanks to the Path-Aware Networking Research Group for the discussion and feedback. Specifically, thanks to Mohamed Boudacair for the detailed review and various text suggestions, thanks to Brian Trammell for suggesting the flow definition, and thanks to Adrian Perrig and Matthias Rost for the detailed feedback. Thanks to Paul Hoffman for the editorial changes.
