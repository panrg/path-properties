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

    I-D.ietf-tcpm-converters:

    RFC8175:

    RFC5693:

    RFC5136:

    RFC7679:

    RFC3393:

    RFC7680:

    RFC3357:

    RFC6534:

    ANRW18-Metrics: DOI.10.1145/3232755.3232764

--- abstract

This document defines and categorizes
information about Internet paths that an entity, such as a host, might have or want to have.
This information is expressed as properties of paths between two hosts.

--- middle

# Introduction

In the current Internet architecture, hosts generally do not have information about forwarding paths through the network and about services associated with these paths.
A path-aware network, as introduced in {{I-D.irtf-panrg-questions}}, exposes information about paths to hosts or to other entities.
This document defines such information as path properties, addressing the first of the questions in path-aware networking {{I-D.irtf-panrg-questions}}.

As terms related to paths have different meanings in different areas of networking, first, this document provides a common terminology to define paths, path elements, and path properties.
Then, this document provides some examples for use cases for path properties.
Further, this document classifies path properties according to different criteria, e.g., how dynamic they are and to what path elements they apply.
Finally, the document lists several path properties that may be useful for the mentioned use cases.

# Terminology

Node:
: An entity which processes packets, e.g., sends, receives, forwards, or modifies them.

Host:
: A node that processes packets that are explicitly addressed to itself.

Router:
: A node that processes packets that are not explicitly addressed to itself.

Link:
: A medium or communication facility that connects two or more nodes with each other and enables them to exchange packets. A link can be physical, e.g., a WiFi network which connects an Access Point to stations, or virtual, e.g., a virtual switch which connects two virtual machines hosted on the same physical machine.

Path element:
: Either a node or a link.

Path:
: A sequence of adjacent path elements, alternating between nodes and links, starting and ending with a host. A path can be viewed as an abstraction on a specific layer, omitting lower layer path elements. For example, a router implementing IPv6 may be a path element on a path when considering the network layer. If this router does not implement transport layer functionality, it is hidden when a higher layer, such as the transport or application layer, is considered. In the case of multicast or broadcast, a single packet may be sent over multiple paths at once -- one path for each combination of sending and receiving host.

Subpath:
: Given a path, a subpath is a sequence of adjacent path elements of this path, starting and ending with a node.

Flow:
: One or multiple packets which are traversing the same subpath or path. For example, a flow can consist of all packets sent within a TCP session with the same five-tuple between two hosts, or it can consist of all packets sent on the same physical link.

Property:
: A trait of one or a sequence of path elements, or a trait of a flow with respect to one or a sequence of path elements. An example of a link property is the maximum data rate that can be sent over the link. An example of a node property is the administrative domain that the node belongs to. An example of a property of a flow with respect to a subpath is the aggregated one-way delay of the flow being sent from one node to another node over a subpath.
A property is thus described by a tuple containing the sequence of path elements, the flow or an empty set if no packets are relevant for the property, the name of the property (e.g., maximum data rate), and the value of the property (e.g., 1Gbps).

Aggregated property:
: A collection of multiple values of a property into a single value, according to a function. A property can be aggregated over multiple path elements (i.e., a path), e.g., the MTU of a path as the minimum MTU of all links on the path, over multiple packets (i.e., a flow), e.g., the median one-way latency of all packets between two nodes, or over both, e.g., the mean of the queueing delays of a flow on all nodes along a path.
The aggregation function can be numerical, e.g., median, sum, minimum, it can be logical, e.g., "true if all are true", "true if at least 50\% of values are true", or an arbitrary function which maps multiple input values to an output value.

Measured property:
: A property that is observed for a specific path element or path, e.g., using measurements. For example, the one-way delay of a specific packet can be measured.

Estimated property:
: An approximate calculation or judgment of the value of a property. For example, an estimated property may describe the expected median one-way latency of packets sent on a path within the next second. An estimated property includes the reliability of the estimate. The notion of reliability depends on the property. For example, it may be the confidence level and interval for numerical properties or the likelihood that a property holds for non-numerical properties.

# Use Cases for Path Properties

When a path-aware network exposes path properties to hosts or other entities,
these entities may use this information to achieve different goals.
This section lists several use cases for path properties.
Note that this is not an exhaustive list, as with every new technology and protocol, novel use cases may emerge, and new path properties may become relevant.

## Performance Monitoring and Enhancement

Network operators can observe path properties (e.g., measured by on-path devices), to monitor Quality of Service (QoS) characteristics of recent end-user traffic on a path or subpath through their network. Such properties may help identify potential performance problems or trigger countermeasures to enhance performance.

## Path Selection

Entities can choose what traffic to send over which path or subset of paths.
Entities may select their paths to fulfill a specific goal, e.g., related to security or performance.
As an example of security-related path selection, an entity may allow or disallow sending traffic over paths involving specific networks or nodes to enforce traffic policies. In an enterprise network where all traffic has to go through a specific firewall, a path-aware host can implement this policy using path selection, in which case the host needs to be aware of paths involving that firewall.
As an example of performance-related path selection,
an entity may prefer paths with performance properties that best match its traffic, e.g., retrieving a small webpage as quickly as possible over a path with short One-Way Delays in both directions, or retrieving a large file over a path with high Link Capacities on all links.
Note, there may be trade-offs between path properties (e.g., One-Way Delay and Link Capacity), and entities may influence these trade-offs with their choices.
As a baseline, a path selection algorithm should aim to not perform worse than the default case most of the time.

Path selection can be done both by hosts and by entities within the network:
A network (e.g., an AS) can adjust its path selection for internal or external routing based on the path properties.
In BGP, the Multi Exit Discriminator (MED) attribute decides which path to choose if other attributes are equal; in a path aware network, instead of using this single MED value, other properties such as maximum or available/expected data rate could additionally be used to improve load balancing.
A host might be able to select between a set of paths, either if there are several paths to the same destination (e.g., if the host is a mobile device with two wireless interfaces, both providing a path), or if there are several destinations, and thus several paths, providing the same service (e.g., Application-Layer Traffic Optimization (ALTO) {{RFC5693}}, an application layer peer-to-peer protocol allowing hosts a better-than-random peer selection).
Care needs to be taken when selecting paths based on path properties, as path properties that were previously measured may have become outdated and, thus, useless to predict the path properties of packets sent now.

## Traffic Configuration

When sending traffic over a specific path, entities can adjust this traffic based on the properties of the path.
For example, an entity may select an appropriate protocol depending on the capabilities of the on-path devices,
or adjust protocol parameters to an existing path.
An example of traffic configuration is a video streaming application choosing an (initial) video quality based on the achievable data rate, or the monetary cost to send data across a network, eventually on a given path, using a volume-based or flat-rate cost model.

Conversely, the selection of a protocol may influence the devices that will be involved in a path.
For example, a 0-RTT Transport Converter {{I-D.ietf-tcpm-converters}} will be involved in a path only when invoked by a host; such invocation will lead to the use of MPTCP or TCPinc capabilities while such use is not supported via the default forwarding path. Another example of traffic policies is a connection which may be composed of multiple streams; each stream with specific service requirements. A host may decide to invoke a given service function (e.g., transcoding) only for some streams while others are not processed by that service function.


# Path Property Classification

Path properties may be relatively dynamic, e.g. median one-way delay of packets sent over a specific path within the last second.
Path properties may also be non-dynamic, i.e., change less frequently, i.e., on a timescale of seconds or more.
Usefulness over time is fundamentally different for dynamic and non-dynamic properties.
The merit of a momentary measurement of a dynamic path property diminishes greatly as time goes on, e.g. the merit of an RTT measurement from a few seconds ago is quite small, while a non-dynamic path property might stay relevant for a longer period of time, e.g. a NAT typically stays on a specific path during the lifetime of a connection involving packets sent over this path.

Moreover, path properties may relate to path elements close to the origin, e.g. nature of the access technology on the first hop, or extend to path elements far from the origin, e.g. list of ASes traversed.

\[TODO: Remove split between domain and backbone properties? Or at least remove some of those claims? \]

Non-dynamic properties are further separated into (local) domain properties related to the first few hops of the connection, and backbone properties related to the remaining hops.
Domain properties expose a high amount of information to hosts and strongly influence the connection behavior while there is little influence and information about backbone properties.

Dynamic properties are not separated into domain and backbone properties, since most of these properties are defined for a complete path and it is difficult and seldom useful to define them on part of the path.
There are exceptions such as dynamic wireless access properties, but these do not justify separation into different categories.


# Domain Properties

Domain path properties relate to path elements within the first hop or the first few hops, which are usually in the same administrative domain as a host considering them.

Due to the potential physical proximity and pre-existing trust or contractual relationships between hosts and path elements within the same domain, domain properties may be more accessible to the host than other properties.

Furthermore, hosts may be able to influence both which domain they are in and which path elements in this domain to connect to, and they may be able to influence the properties of path elements within this domain.
For example, a user might select between multiple potential adjacent links by selecting between multiple available WiFi Access Points. Or when connected to an Access Point, the user may move closer to enable their device to use a different access technology, potentially increasing the data rate available to the device.
Another example is a user changing their data plan to reduce the Monetary Cost to transmit a given amount of data across a network.


Access Technology:
: The physical or link layer technology used for transmitting or receiving a flow on one or multiple path elements in the same domain. The Access Technology may be given in an abstract way, e.g., as a WiFi, Wired Ethernet, or Cellular link. It may also be given as a specific technology, e.g., as a 2G, 3G, 4G, or 5G cellular link, or an 802.11a, b, g, n, or ac WiFi link. Other path elements relevant to the access technology may include on-path devices, such as elements of a cellular backbone network. Note that there is no common registry of possible values for this property.

Monetary Cost:
: The price to be paid to transmit a specific flow across a subpath.


# Backbone Properties

Backbone path properties relate to path elements not within the same domain as a host considering them, thus, in the backbone from the host's point of view.

Typically, backbone properties are less accessible to a host than domain properties, due to the potential increased distance and the lack of pre-existing trust or contractual relationship.

Additionally, hosts are less likely to be able to influence which path elements form their path in the backbone, as well as their properties.

Some path properties relate to the entire path or to subpaths, part of which often lies outside of a host's domain. Thus, such properties are listed as Backbone Properties.

Presence of a certain network function on the path:
: Indicates that a node performs a certain network function on a flow, e.g., whether the node acts as a proxy, as a firewall, or performs Network Address Translation (NAT). This node may be either in the same domain as the host or in a different domain, i.e., the backbone.

Administrative Entity:
: The administrative entity, e.g., the AS, to which a path element or subpath belongs.

Disjointness:
: For a set of two paths, the number of shared path elements can be a measure of intersection (e.g., Jaccard coefficient, which is the number of shared elements divided by the total number of elements). Conversely, the number of non-shared path elements can be a measure of disjointness (e.g., 1 - Jaccard coefficient). A multipath protocol might use disjointness of paths as a metric to reduce the number of single points of failure.

Path MTU:
: The maximum size, in octets, of an IP packet that can be transmitted without fragmentation on a subpath.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over a path or subpath. A host may cache its knowledge about recent successfully established connections using specific protocols, e.g., a QUIC connection, or an MPTCP subflow.

Protocol Features available:
: Whether a specific protocol feature is available over a path or subpath, e.g., Explicit Congestion Notification (ECN), or TCP Fast Open.


# Dynamic Properties

Dynamic path properties relate to the transmission of an individual packet or of a flow over a subpath.
Properties related to a path element which constitutes a single layer 2 domain are abstracted from the used physical and link layer technology, similar to {{RFC8175}}.

Typically, Dynamic Properties can be measured or approximated, and might be made available in an aggregated form, such as averages or minimums.
Dynamic Path Properties can be measured by the host itself or by a different entity.
See {{ANRW18-Metrics}} for a discussion of how to measure some dynamic path properties at the host.

Some dynamic properties are defined in different directions for the same path element, e.g., for transmitting and receiving packets.


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

Congestion:
: Whether a protocol feature such as ECN has provided information that there currently is congestion on a path.


# Security Considerations

If nodes are basing policy or path selection decisions on path properties, they need to rely on the accuracy of path properties that other devices communicate to them.
In order to be able to trust such path properties, nodes may need to establish a trust relationship or be able to verify the authenticity, integrity, and correctness of path properties received from another node.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

Thanks to the Path-Aware Networking Research Group for the discussion and feedback. Thanks to Mohamed Boudacair for the detailed review and text suggestions, and thanks to Adrian Perrig and Matthias Rost for the feedback. Thanks to Paul Hoffman for the editorial changes.
