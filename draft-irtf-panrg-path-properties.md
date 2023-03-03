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
    ins: R. Enghardt
    name: Reese Enghardt
    organization: Netflix
    email: ietf@tenghardt.net
 -
    ins: C. Krähenbühl
    name: Cyrill Krähenbühl
    organization: ETH Zürich
    email: cyrill.kraehenbuehl@inf.ethz.ch

informative:

    I-D.ietf-idr-performance-routing:

    I-D.ietf-alto-path-vector:

    I-D.ietf-alto-performance-metrics:

    I-D.ietf-teas-ietf-network-slices:

    RFC1930:

    RFC2616:

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

    RFC8547:

    RFC8548:

    RFC8558:

    RFC8684:

    RFC8803:

    RFC9000:

    RFC9097:

    RFC9217:

    RFC9298:

--- abstract

This document is a product of the Path Aware Networking Research Group (PANRG).
Path properties express information about paths across a network and the services provided via such paths.
In a path-aware network, path properties may be fully or partially available to entities such as endpoints.
This document defines and categorizes path properties.
Furthermore, the document identifies several path properties which might be useful to endpoints or other entities,
e.g., for selecting between paths or for invoking some of the provided services.

--- middle

# Introduction

The current Internet architecture does not explicitly support endpoint discovery of forwarding paths through the network nor the discovery of properties and services associated with these paths.
Path-aware networking, as defined in Section 1.1 of {{RFC9217}}, describes
"endpoint discovery of the properties of paths they use for communication across an internetwork, and endpoint reaction to these properties that affects routing and/or data transfer".
This document provides a generic definition of path properties, addressing the first of the questions in path-aware networking {{RFC9217}}.

As terms related to paths have been used with different meanings in different areas of networking, first, this document provides a common terminology to define paths, path elements, and flows. Based on these terms, the document defines path properties.
Then, this document provides some examples of use cases for path properties.
Finally, the document lists several path properties that may be useful for the mentioned use cases. This list is intended to be neither exhaustive nor definitive.

Note that this document does not assume that any of the listed path properties are actually available to any entity. The question of how entities can discover and distribute path properties in a trustworthy way is out of scope for this document.

This document represents the consensus of the Path Aware Networking Research Group (PANRG).

# Terminology

Entity:
: A physical or virtual device or function, or a collection of devices or functions, which plays a role related to path-aware networking for particular paths and flows.
An entity can be on-path or off-path: On the path, an entity may participate in forwarding the flow, i.e., what may be called data plane functionality.
On or off the path, an entity may influence aspects of how the flow is forwarded, i.e., what may be called control plane functionality, such as Path Selection or Service Invocation.
An entity influencing forwarding aspects is usually aware of path properties, e.g., by observing or measuring them or by learning them from another entity.

Node:
: An on-path entity which processes packets, e.g., sends, receives, forwards, or modifies them. A node may be physical or virtual, e.g., a physical device, a service function provided as a virtual element, or even a single queue within a switch. A node may also be an entity which consists of a collection of devices or functions, e.g., an entire Autonomous System (AS).

Link:
: A medium or communication facility that connects two or more nodes with each other. A link enables a node to send packets to other nodes.
Links can be physical, e.g., a Wi-Fi network which connects an Access Point to stations, or virtual, e.g., a virtual switch which connects two virtual machines hosted on the same physical machine. A link is unidirectional. As such, bidirectional communication can be modeled as two links between the same nodes in opposite directions.

Path element:
: Either a node or a link. For example, a path element can be an Abstract Network Element (ANE) as defined in {{I-D.ietf-alto-path-vector}}.

Path:
: A sequence of adjacent path elements over which a packet can be transmitted, starting and ending with a node.

  Paths are unidirectional and time-dependent, i.e., the sequence of path elements over which packets are sent from one node to another may change.

  The representation of a path and its properties may depend on the entity considering the path.
  On the one hand, the representation may differ due to entities having partial visibility of path elements comprising a path or their visibility changing over time.
  On the other hand, the representation may differ due to treating path elements at different levels of abstraction.
  For example, a path may be given as a sequence of physical nodes and the links connecting these nodes, a sequence of logical nodes such as a sequence of ASes or an Explicit Route Object (ERO), or only consist of a specific source and destination which is known to be reachable from that source.

  A multicast or broadcast setting, where a packet is sent by one node and received by multiple nodes, is described by multiple paths over which the packet is sent, one path for each combination of sending and receiving node; these paths do not have to be disjoint as defined by the Disjointness path property, see {{examples}}.

Endpoint:
: The endpoints of a path are the first and the last node on the path. For example, an endpoint can be a host as defined in {{?RFC1122}}, which can be a client (e.g., a node running a web browser) or a server (e.g., a node running a web server).

Reverse Path:
: The path that is used by a remote node in the context of bidirectional communication.

Subpath:
: Given a path, a subpath is a sequence of adjacent path elements of this path.

Flow:
: One or multiple packets to which the traits of a path or set of subpaths may be applied in a functional sense. For example, a flow can consist of all packets sent within a TCP session with the same five-tuple between two hosts, or it can consist of all packets sent on the same physical link.

Property:
: A trait of one or a sequence of path elements, or a trait of a flow with respect to one or a sequence of path elements. An example of a link property is the maximum data rate that can be sent over the link. An example of a node property is the administrative domain that the node belongs to. An example of a property of a flow with respect to a subpath is the aggregated one-way delay of the flow being sent from one node to another node over this subpath.
A property is thus described by a tuple containing the path element(s), the flow or an empty set if no packets are relevant for the property, the name of the property (e.g., maximum data rate), and the value of the property (e.g., 1Gbps).

Aggregated property:
: A collection of multiple values of a property into a single value, according to a function. A property can be aggregated over multiple path elements (i.e., a subpath), e.g., the MTU of a path as the minimum MTU of all links on the path, over multiple packets (i.e., a flow), e.g., the median one-way latency of all packets between two nodes, or over both, e.g., the mean of the queueing delays of a flow on all nodes along a path.
The aggregation function can be numerical, e.g., median, sum, minimum, it can be logical, e.g., "true if all are true", "true if at least 50% of values are true", or an arbitrary function which maps multiple input values to an output value.

Observed property:
: A property that is observed for a specific path element, subpath, or path, e.g., using measurements. For example, the one-way delay of a specific packet transmitted from one node to another node can be measured.

Assessed property:
: An approximate calculation or assessment of the value of a property. An assessed property includes the reliability of the calculation or assessment. The notion of reliability depends on the property.
For example, a path property based on an approximate calculation may describe the expected median one-way latency of packets sent on a path within the next second, including the confidence level and interval. A non-numerical assessment may instead include the likelihood that the property holds.

Target property:
: An objective that is set for a property over a path element, subpath, or path.
Note that a target property can be set for observed properties, such as one-way delay, but also for properties that cannot be observed by the entity setting the target, such as inclusion of certain nodes on a path.

## Terminology usage for specific technologies

The terminology defined in this document is intended to be general and applicable to existing and future path-aware technologies.
Using this terminology, a path-aware technology can define and consider specific path elements and path properties on a specific level of abstraction.
For instance, a technology may define path elements as IP routers, e.g., in source routing ({{?RFC1940}}). Alternatively, it may consider path elements on a different layer of the Internet Architecture ({{?RFC1122}}) or as a collection of entities not tied to a specific layer, such as an AS or an ERO.
Even within a single path-aware technology, specific definitions might differ depending on the context in which they are used.
For example, the endpoints might be the communicating hosts in the context of the transport layer, ASes that contain the hosts in the context of routing, or specific applications in the context of the application layer.

# Use Cases for Path Properties {#use-cases}

When a path-aware network exposes path properties to endpoints or other entities,
these entities may use this information to achieve different goals.
This section lists several use cases for path properties.

Note that this is not an exhaustive list, as with every new technology and protocol, novel use cases may emerge, and new path properties may become relevant.
Moreover, for any particular technology, entities may have visibility of and control over different path elements and path properties, and consider them on different levels of abstraction.
Therefore, a new technology may implement an existing use case related to different path elements or on a different level of abstraction.

## Path Selection

Nodes may be able to send flows via one (or a subset) out of multiple possible paths, and an entity may be able to influence the decision which path(s) to use.
Path Selection may be feasible if there are several paths to the same destination (e.g., in case of a mobile device with two wireless interfaces, both providing a path), or if there are several destinations, and thus several paths, providing the same service (e.g., Application-Layer Traffic Optimization (ALTO) {{RFC5693}}, an application layer peer-to-peer protocol allowing endpoints a better-than-random peer selection).
Entities can express their intent to achieve a specific goal by specifying target properties for their paths, e.g., related to performance or security.
Then, paths can be selected which best meet the target properties, e.g., the entity can select these paths from all available paths or express the target properties to the network and rely on the network to select appropriate paths.

Target properties relating to network performance typically refer to observed properties, such as One-Way Delay, One-Way Packet Loss, and Link Capacity.
Entities then select paths based on their target property and the assessed property of the available paths that best match the application requirements.
For such performance-related target properties, the observed property is similar to a service level indicator (SLI) and the assessed property is similar to a service level objective (SLO) for IETF network slices {{I-D.ietf-teas-ietf-network-slices}}.
As an example path selection strategy, for sending a small delay-sensitive query, an entity may select a path with a short One-Way Delay, while for retrieving a large file, it may select a path with high Link Capacities on all links.

It is also possible for an entity to set target properties which it cannot (directly) observe, similar to service level expectations (SLEs) for IETF network slices {{I-D.ietf-teas-ietf-network-slices}}.
For example, this can apply to security-related target properties and path selection, such as allowing or disallowing sending flows over paths that involve specific networks or nodes to enforce traffic policies or mandating that all enterprise traffic goes through a specific firewall.

Care needs to be taken when selecting paths based on observed path properties, as path properties that were previously measured may not be helpful in predicting current or future path properties and such path selection may lead to unintended feedback loops. Also, there may be trade-offs between path properties (e.g., One-Way Delay and Link Capacity), and entities may influence these trade-offs with their choices.
Finally, path selection may impact fairness.
For example, if multiple entities concurrently attempt to meet their target properties using the same network resources, one entity's choices may influence the conditions on the path as experienced by flows of another entity.

As a baseline, a path selection algorithm should aim to do a better job at meeting the target properties, and consequently accommodating the user's requirements, than the default case of not selecting a path most of the time.

Path selection can be done either by the communicating node(s) or by other entities within the network:
A network (e.g., an AS) can adjust its path selection for internal or external routing based on path properties.
In BGP, the Multi Exit Discriminator (MED) attribute is used in the decision-making process to select which path to choose among those having the same AS PATH length and origin {{RFC4271}}; in a path-aware network, instead of using this single MED value, other properties such as Link Capacity or Link Usage could additionally be used to improve load balancing or performance {{I-D.ietf-idr-performance-routing}}.

## Protocol Selection

Before sending data over a specific path, an entity may select an appropriate protocol or configure protocol parameters depending on path properties.
For example, an endpoint may cache state on whether a path allows the use of QUIC {{RFC9000}} and if so, first attempt to connect using QUIC before falling back to another protocol when connecting over this path again.
A video streaming application may choose an (initial) video quality based on the achievable data rate or the monetary cost of sending data (e.g., volume-base or flat-rate cost model).

## Service Invocation

In addition to path or protocol selection, an entity may choose to invoke additional functions in the context of Service Function Chaining {{RFC7665}}, which may influence what nodes are on the path.
For example, a 0-RTT Transport Converter {{RFC8803}} will be involved in a path only when invoked by an endpoint; such invocation will lead to the use of MPTCP {{RFC8684}} or TCPinc {{RFC8547}} {{RFC8548}} capabilities while such use is not supported via the default forwarding path.
Another example is a connection which is composed of multiple streams where each stream has specific service requirements. An endpoint may decide to invoke a given service function (e.g., transcoding) only for some streams while others are not processed by that service function.

# Examples of Path Properties {#examples}

This Section gives some examples of path properties which may be useful, e.g., for the use cases described in {{use-cases}}.

Within the context of any particular technology, available path properties may differ
as entities have insight into and are able to influence different path elements and path properties.
For example, an endpoint may have some visibility into path elements that are on a low level of abstraction and close, e.g., individual nodes within the first few hops, or it may have visibility into path elements that are far away and/or on a higher level of abstraction, e.g., the list of ASes traversed.
This visibility may depend on factors such as the physical or network distance or the existence of trust or contractual relationships between the endpoint and the path element(s).
A path property can be defined relative to individual path elements, a sequence of path elements, or "end-to-end", i.e., relative to a path that comprises of two endpoints and a single virtual link connecting them.

Path properties may be relatively dynamic, e.g., the one-way delay of a packet sent over a specific path, or non-dynamic, e.g., the MTU of an Ethernet link which only changes infrequently.
Usefulness over time differs depending on how dynamic a property is:
The merit of a momentarily observed dynamic path propety may diminish greatly as time goes on, e.g.,
it is possible for the observed values of One-Way Delay to change on timescales which are shorter than the One-Way Delay between the measurement point and an entity making a decision such as Path Selection, which may cause the measurement to be outdated when it reaches the decision-making entity. Therefore, consumers of dynamic path properties need to apply caution when using them, e.g., by aggregating them appropriately or applying a dampening function to their changes to avoiding oscillation.
In contrast, the observed value of a less dynamic path property might stay relevant for a longer period of time, e.g. a NAT typically stays on a particular path during the lifetime of a connection involving packets sent over this path.

Access Technology:
: The physical or link layer technology used for transmitting or receiving a flow on one or multiple path elements. If known, the Access Technology may be given as an abstract link type, e.g., as Wi-Fi, Wired Ethernet, or Cellular. It may also be given as a specific technology used on a link, e.g., 3GPP cellular, or 802.11 WiFi. Other path elements relevant to the access technology may include nodes related to processing packets on the physical or link layer, such as elements of a cellular core network. Note that there is no common registry of possible values for this property.

Monetary Cost:
: The price to be paid to transmit or receive a specific flow across a network to which one or multiple path elements belong.

Service function:
: A service function that a path element applies to a flow, see {{RFC7665}}. Examples of abstract service functions include firewalls, Network Address Translation (NAT), and TCP Performance Enhancing Proxies. Some stateful service functions, such as NAT, need to observe the same flow in both directions, e.g., by being an element of both the path and the reverse path.

Transparency:
: When a node performs an action A on a flow F, the node is transparent to F with respect to some (meta-)information M if the node performs A independently of M.
M can for example be the existence of a protocol (header) in a packet or the content of a protocol header, payload, or both.
For example, A can be blocking packets or reading and modifying (other protocol) headers or payloads.
Transparency can be modeled using a function f, which takes as input F and M and outputs the action taken by the node.
If a taint analysis shows that the output of f is not tainted (impacted) by M or if the output of f is constant for arbitrary values of M, then the node is considered to be transparent.
An IP router could be transparent to transport protocol headers such as TCP/UDP but not transparent to IP headers since its forwarding behavior depends on the IP headers.
A firewall that only allows outgoing TCP connections by blocking all incoming TCP SYN packets regardless of their IP address is transparent to IP but not to TCP headers.
Finally, a NAT that actively modifies IP and TCP/UDP headers based on their content is not transparent to either IP or TCP/UDP headers.
Note that according to this definition, a node that modifies packets in accordance with the endpoints, such as a transparent HTTP proxy, as defined in {{RFC2616}}, and a node listening and reacting to implicit or explicit signals, see {{RFC8558}}, are not considered transparent.

Administrative Domain:
: The identity of an individual or an organization that controls access to a path element (or several path elements).
Examples of administrative domains are an IGP area, an AS, or a service provider network.

Routing Domain Identifier:
: An identifier indicating the routing domain of a path element.
Path elements in the same routing domain are in the same administrative domain and use a common routing protocol to communicate with each other.
An example of a routing domain identifier is the globally unique autonomous system number (ASN) as defined in {{RFC1930}}.

Disjointness:
: For a set of two paths or subpaths, the number of shared path elements can be a measure of intersection (e.g., Jaccard coefficient, which is the number of shared elements divided by the total number of elements). Conversely, the number of non-shared path elements can be a measure of disjointness (e.g., 1 - Jaccard coefficient). A multipath protocol might use disjointness as a metric to reduce the number of single points of failure.

Symmetric Path:
: Two paths are symmetric if the path and its reverse path consist of the same path elements on the same level of abstraction, but in reverse order.
For example, a path which consists of layer 3 switches and links between them and a reverse path with the same path elements but in reverse order are considered "routing" symmetric, as the same path elements on the same level of abstraction (IP forwarding) are traversed in the opposite direction.

Path MTU:
: The maximum size, in octets, of a packet or frame that can be transmitted without fragmentation.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over a path or subpath, e.g., whether the path is QUIC-capable or MPTCP-capable, based on input such as policy, cached knowledge, or probing results.

Protocol Features available:
: Whether a specific protocol feature is available over a path or subpath, e.g., Explicit Congestion Notification (ECN), or TCP Fast Open.


Some path properties express the performance of the transmission of a packet or flow over a link or subpath.
Such transmission performance properties can be observed or assessed, e.g., by endpoints or by path elements on the path, or they may be available as cost metrics, see {{I-D.ietf-alto-performance-metrics}}.
Transmission performance properties may be made available in an aggregated form, such as averages or minimums.
Properties related to a path element which constitutes a single layer 2 domain are abstracted from the used physical and link layer technology, similar to {{RFC8175}}.

Link Capacity:
: The link capacity is the maximum data rate at which data that was sent over a link can correctly be received at the node adjacent to the link.
This property is analogous to the link capacity defined in {{RFC5136}} and {{RFC9097}} but not restricted to IP-layer traffic.

Link Usage:
: The link usage is the actual data rate at which data that was sent over a link is correctly received at the node adjacent to the link.
This property is analogous to the link usage defined in {{RFC5136}} and {{RFC9097}} but not restricted to IP-layer traffic.

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

If entities are basing policy or path selection decisions on path properties, they need to rely on the accuracy of path properties that other devices communicate to them.
In order to be able to trust such path properties, entities may need to establish a trust relationship or be able to verify the authenticity, integrity, and correctness of path properties received from another entity.

Entities that reveal their target path properties to the network can negatively impact their own privacy, e.g., if the target property leaks personal information about a user, such as their identity or which (type of) application is used.
Such information could then allow network operators to block or re-prioritize traffic for certain users and/or application.
Conversely, if privacy enhancing technologies, e.g., MASQUE proxies [RFC9298], are used on a path, the path may only be partially visible to any single entity.
This may diminish the usefulness of path-aware technologies over this path.
Security related properties such as confidentiality and integrity protection of payloads are difficult to characterize since they are only meaningful with respect to a threat model which depends on the use case, application, environment, and other factors.
Likewise, properties for trust relations between entities cannot be meaningfully defined without a concrete threat model, and defining a threat model is out of scope for this draft.
Properties related to confidentiality, integrity, and trust are orthogonal to the path terminology and path properties defined in this document.
Such properties are tied to the communicating nodes and the protocols they use (e.g., client and server using HTTPS, or client and remote network node using VPN) while the path is typically oblivious to them.
Intuitively, the path describes what function the network applies to packets, while confidentiality, integrity, and trust describe what function the communicating parties apply to packets.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

Thanks to the Path-Aware Networking Research Group for the discussion and feedback. Specifically, thanks to Mohamed Boucadair for the detailed review, various text suggestions, and shepherding, thanks to Brian Trammell for suggesting the flow definition, and thanks to Luis M. Contreras, Spencer Dawkins, Paul Hoffman, Jake Holland, Colin Perkins, Adrian Perrig, and Matthias Rost for the reviews, comments, and suggestions.
