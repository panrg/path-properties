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

    RFC8175:

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

# Terminology

Path element:
: A path element is a device (including the endpoints), or link used to connect two devices and transmit information on a specific layer.
Path elements may exist on multiple layers (e.g., the endpoint corresponds to a path element on every layer), may be hidden on higher layers (e.g., a layer 2 switch in the local network), or a path element may be an aggregation of several path elements on a lower layer (e.g., the link connecting the endpoints on the transport layer being an aggregation of all network layer path elements).

Path segment:
: A path segment is an ordered set of path elements at the network layer that can be traversed by a packet.

Path:
: A path is defined as an ordered set of path elements at the network layer between two endpoints. A path can be traversed by a packet.

Flow:
: Several packets traversing the same path elements can be combined into a flow (e.g., all packets sent within a UDP session which traverse the same path elements).
As a special case, a flow can consist of just one packet.

Property:
: A property describes a trait of a set of path elements (e.g., capacity of a link, is device X a firewall, one-way maximum data rate which is the minimum of all links' maximum data rates), or a trait of a flow being sent on a set of path elements (e.g., RTT, one-way delay).
A property is thus described by a tuple containing the ordered set of path elements, the set of packets traversing the path (the flow) or an empty set if no packets are relevant for the property, the name of the trait (e.g., maximum data rate), and the value of the trait (e.g., 100mbps).

Aggregated Property:
: A property can be aggregated over a set of path elements (e.g., MTU in the network backbone as the minimum MTU of the individual path elements), or over a set of packets (e.g., median one-way latency of all packets during the last second), or over both (e.g., average time a packets spends in buffers outside the local network).
Aggregation can be numerical (average, sum, min, ...), logical (true if all are true, true if at least X are true, ...), or an arbitrary function which maps a set of input properties to an output property.

Measured & Potential Property:
: A property can be classified by timescale into a measured property, based on concrete previous and current measurements, and a potential property, which is a property with predicted characteristics, possibly including the reliability of such predictions.
An example of a potential property with a high reliability is the maximum data rate of an ethernet link in the local network during the next day, while a potential property with a lower reliability is the expected one-way latency of packets sent to an endpoint on the other side of the planet during the next second.
The notion of reliability depends on the property, it might be the confidence level and interval for numerical properties or the likelihood that a property holds for non-numerical properties.

# Domain Properties

Domain path properties relate to path elements within the first hop or the first few hops, which are usually in the same administrative domain as an endpoint considering them.

Due to the potential physical proximity and pre-existing trust or contractual relationships between endpoints and path elements within the same administrative domain, domain properties may be more accessible to the endpoint than other properties.

Furthermore, endpoints may be able to influence both which domain they are in and which path elements in this domain to connect to, and they may be able to influence the properties of path elements within this domain.
For example, a user might select between multiple potential adjacent path elements by selecting between multiple available WiFi Access Points. Or when connected to an Access Point, the user may move closer to enable their device to use a different access technology, potentially increasing the data rate available to the device.
Another example is a user changing their data plan to reduce the Monetary Cost to transmit a given amount of data across a network.


Access Technology:
: The physical or link layer technology used for transmitting or receiving a flow on one or multiple path elements in the same administative domain. The Access Technology may be given in an abstract way, e.g., as a WiFi, Wired Ethernet, or Cellular link, or as a specific technology, e.g., as a 2G, 3G, 4G, or 5G cellular link, or an 802.11a, b, g, n, or ac WiFi link. Other path elements relevant to the access technology may include on-path devices, such as elements of a cellular backbone network. Note that there is no common registry of possible values for this property.

Monetary Cost:
: The price to be paid to transmit a specific flow across a set of path elements.


# Backbone Properties

Backbone path properties relate to path elements that not within the same domain as an endpoint considering them, thus, in the backbone from the endpoint's point of view.

Typically, backbone properties are less accessible to an endpoint than domain properties, due to the potential increased distance and the lack of pre-existing trust or contractual relationship.

Additionally, endpoints are less likely to be able to influence which path elements form their path in the backbone, as well as their properties.

Some path properties relate to the entire path, part of which often lies outside of an endpoint's domain. Thus, such properties are listed as Backbone Properties.


Presence of a certain network function on the path:
: Indicates that a certain path element performs a certain network function on a flow, e.g., whether the path element acts as a proxy, as a firewall, or performs Network Address Translation (NAT). This path element may be either in the same domain as the endpoint or in a different domain, i.e., the backbone.

Administrative Entity:
: The administrative entity, e.g., the AS, to which a path element or set of path elements belongs.

Disjointness:
: For a set of two paths, the number of shared path elements can be a measure of intersection (e.g., Jaccard coefficient, which is the number of shared elements divided by the total number of elements). Conversely, the number of non-shared path elements can be a measure of disjointness (e.g., 1 - Jaccard coefficient). A multipath protocol might use disjointness of paths as a metric to reduce the number of single points of failure.

Path MTU:
: The maximum size, in octets, of an IP packet that can be transmitted without fragmentation on a path segment.

Transport Protocols available:
: Whether a specific transport protocol can be used to establish a connection over a path or path segment. An endpoint may cache its knowledge about recent successfully established connections using specific protocols, e.g., a QUIC connection, or an MPTCP subflow, over a specific path.

Protocol Features available:
: Whether a specific protocol feature is available over this path, e.g., Explicit Congestion Notification (ECN), or TCP Fast Open.


# Dynamic Properties

Dynamic path properties relate to a path segment with respect to the transmission of an individual packet or of a flow over this path segment.
Properties related to a path element which constitutes single layer 2 domain are abstracted from the used physical and link layer technology, similar to {{RFC8175}}.

Typically, Dynamic Properties can only be approximated and sampled, and might be made available in an aggregated form, such as averages or minimums.
Dynamic Path Properties can be measured by the endpoint itself or somethere in the network.
See {{ANRW18-Metrics}} for a discussion of how to measure some dynamic path properties at the endpoint.

Some dynamic properties are defined in different directions for the same path element, e.g., for transmitting and receiving packets.


Maximum Data Rate (Transmit/Receive):
: The theoretical maximum data rate, in bits per second, that can be achieved on a link, path segment, or path, for receiving or transmitting traffic.

Current Data Rate (Transmit/Receive):
: The data rate, in bits per second, at which a link is currently receiving or transmitting traffic.

Latency:
: The time delay between sending a packet on a path element and receiving the same packet on a different path element.

Latency variation:
: The variation of the Latency within a flow.

Packet Loss:
: The percentage of packets within a flow which are sent by one path element, but not received by a different path element.

Congestion:
: Whether a protocol feature such as ECN has provided information that there currently is congestion on a path.


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
