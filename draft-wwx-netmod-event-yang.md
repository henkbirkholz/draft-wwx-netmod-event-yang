---
stand_alone: true
ipr: trust200902
docname: draft-wwx-netmod-event-yang-latest
cat: std
pi:
  toc: 'yes'
  symrefs: 'yes'
  sortrefs: 'yes'
  iprnotified: 'no'
  strict: 'yes'
title: A YANG Data model for ECA Policy Management
abbrev: ECA YANG
area: OPS Area
wg: NETMOD Working Group
kw: RFC
date: 2020
author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
- ins: Q. Wu
  name: Qin Wu
  org: Huawei
  street: 101 Software Avenue, Yuhua District
  city: Nanjing
  region: Jiangsu
  code: '210012'
  country: China
  email: bill.wu@huawei.com
- ins: I. Bryskin
  name: Igor Bryskin
  org: Individual
  email: i_bryskin@yahoo.com
- ins: X. Liu
  name: Xufeng Liu
  org: Volta Networks
  email: xufeng.liu.ietf@gmail.com
- ins: B. Claise
  name: Benoit Claise
  org: Cisco
  email: bclaise@cisco.com
  - ins: A. Bierman
  name: Andy Bierman
  org: YumaWorks
  email: andy@yumaworks.com
normative:
  RFC7950:
  RFC3688:
  RFC6020:
  RFC6241:
  RFC6242:
  RFC6536:
  RFC3460:
informative:
  RFC8342:
  RFC8340:
  RFC8040:
  RFC5246:

--- abstract

This document defines a YANG data model for the Event Condition
Action (ECA) policy management.  The ECA policy YANG provides the
ability for the network management function (within a network
element) to control the configuration and monitor state change and
take simple and instant action on the server when a trigger condition
on the system state is met.

--- middle

# Introduction {#intro}

Network management consists of using one or multiple device-,
technology-, service specific policies to influence management
behavior within the system and make sure policies are enforced or
executed correctly.

Event-driven management of states of managed objects across a wide
range of devices can be used to monitor state changes of managed
objects or resource and automatic trigger of rules in response to
events so as to better service assurance for customers and provide
rapid autonomic response that can exhibit self-management properties
including self-configuration, self-healing, self-optimization, and
self-protection.  Following are some of the use-cases where such ECA
Policy can be used:

* To filter out of objects underneath a requested a subtree, the
  subscriber may use YANG Push smart filter to request the network
  server to monitor specific network management data objects and send
  updates only when the value falls within a certain range.

* To filter out of objects underneath a requested a subtree, the
  subscriber may use YANG Push smart filter to request the network
  server to monitor specific network management data objects and send
  updates only when the value exceeds a certain threshold for the
  first time but not again until the threshold is cleared.

* To provide rapid autonomic response that can exhibit
  self-management properties, the management system delegate event
  response behaviors (e.g., auto-recover from network failure) to the
  network device so that the network can react to network change as
  quickly as the event is detected. The event response behaviors
  delegation can be done using ECA policy,e.g., to preconfigure
  protection/ restoration capability on the network device.

* To perform troubleshoot failures (i.e., fault verification and
  localization) and provide root cause analysis, the management system
  monitoring specific network management data objects may request the
  network device to export state information of a set of managed data
  objects when the value of monitored data object exceeds a certain
  threshold.

* To set up an LSP and reserve resources within the network via
  NETCONF protocol operation, Path Computation API RPC model can be
  invoked to calculate a path meeting end-to-end network performance
  criteria.

This document defines a ECA Policy management YANG data model. The
ECA Policy YANG provides the ability for the network management function
(within a network element) to control the configurations and monitor
state parameters and take simple and instant action on the server when a
trigger condition on the system state is met.

The data model in this document is designed to be compliant with the
Network Management Datastore Architecture (NMDA) {{RFC8342}}.

# Conventions used in this document

## Terminology

{::boilerplate bcp14}

The following terms are defined in {{RFC7950}} {{RFC3460}} and are not
redefined here:

* Server

* Client

* Policy Variable

* Policy Value

* Implicit Policy Variable

* Explicit Policy Variable

This document uses the following terms:

Event:

: Something that happens which may be of
  interest or trigger the invocation of the rule. A fault, an alarm,
  a change in network state, network security threat, hardware
  malfunction, buffer untilization crossing a threshold, network
  connection setup, an external input to the system, for
  example.

Condition:

: Condition can be seen as a logical test
  that, if satisfied or evaluated to be true, cause the action to be
  carried out.

Action:

: Updates or invocations on local managed
  object attributes.

## Tree Diagrams

Tree diagrams used in this document follow the notation defined in
{{RFC8340}}.

# Overview of ECA YANG Data Model

A ECA policy rule is read as: when event occurs in a situation where
condition is true, then action is executed. Therefore ECA comprises
three key elements: event, associated conditions, and associated
actions. These three elements should be pushed down and configured on
the server by the client. If the action is rejected by the server duing
ECA policy execution, the action should be rolled back and cleaned
up.

## ECA Policy Variable and Value

ECA policy variable (PV) generically represents information that
changes (or "varies"), and that is set or evaluated by software. ECA
policy value is used for modeling values and constants used in policy
conditions and actions. In policy, conditions and actions can abstract
information as "policy variables" to be evaluated in logical
expressions, or set by actions, e.g., the Policy Condition has the
semantics "variable matches value" while Policy Action has the
semantics "set variable to value".

In ECA, two type of policy variables are defined, implicit variable
and explicit variable. Explicit variables are bound to exact data
object instance in the model while implicit variables are defined and
evaluated outside of a model. Each ECA policy variable has the
following attributes:

* Name with Globally unique or ECA unique scope ;

* Type either implicit or explicit; The implicit or explicit type
  can be further broken down into global or local.

* Value data stored in the policy variable structured according
  to the PV type. This structure can be used to keep intermediate
  results/meta data during the execution of an ECA policy.

The following operations are allowed with/on a PV:

* initialize (with a constant/enum/identity);

* set (with contents of another same type PV);

* read (retrieve datastore contents pointed by the specified same
  type XPath/sub-tree);

* write (modify configuration data in the datastore with the PV's
  content/value);

* insert (PV's content into a same type list);

* iterate (copy into PV one by one same type list elements)

* function calls in a form of F(arg1,arg2,...), where F is an
  identity of a function from extendable function library,
  arg1,arg2,etc are PVs respectively, the function's input
  parameters, with the result returned in result policy
  variable.

PVs could be used as input/output of an ECA invoked RPC and policy
argument in the func calls. PVs could also be a source of information
sent to the client in notification messages.

PVs could be used in condition expressions

The model structure for the Policy Variable is shown below:

~~~~ TREE
{::include policy-variables.tree}
~~~~

## ECA Event

ECA Event is any subscribable event notification either explicitly
defined in a YANG module (e.g., alarm model) supported by the server
or a event stream conveyed to the server via YANG Push subscription.
The ECA event are used to keep track of state of changes associated
with one of multiple operational state data objects in the network
device. Typical examples of ECA event include a fault, an alarm, a
change in network state, network security threat, hardware
malfunction, buffer utilization crossing a threshold, network
connection setup, and an external input to the system.

Each ECA Event has the following attributes:

* name, the name of ECA event;

* type, either one time or periodic scheduling;

* group-id, which can be used to group a set of events that can
  be executed together,e.g., deliver a service or provide service
  assurance;

* scheduled-time,in case of periodic scheduling.

A client may define an event of interest by making use of YANG PUSH
subscription.  Specifically, the client may configure an ECA event
according to the ECA model specifying the event's name, as well as
the name of corresponding PUSH subscrition.  In this case the server
is expected to:

* Register the event recording its name and using the referred PUSH
  subsription trigger as definition of the event firing trigger;

* Auto-configure the event's ECA input in the form of local PVs
  using the PUSH subscription's filters;

* At the moment of event firing intercept the notifications that
  would be normally sent to the PUSH subscription's client(s); copy
  the data store states pointed by the PUSH subscription's filters
  into the auto-configured ECA's local PVs and execute the ECA's
  condition-action chain.

All events (specified in at least one ECA pushed to the server) are
required to be constantly monitored by the server.  One way to think
of this is that the server subscribes to its own publications with
respect to all events that are associated with at least one ECA.

The model structure for the ECA Event is shown below:

~~~~ TREE
<CODE BEGINS>
{::include event.tree}
<CODE ENDS>
~~~~

## ECA Condition

The ECA Condition is the logical expression that is specified in a
form of Xpath expression and evaluated to TRUE or FALSE.  The XPath
expression specifies an arbitrary logical/mathematical expression;
The elements of the ECA Condition expression are referred by the
XPaths pointing to referred datastore states.

The ECA Condition expression in the form of XPath expression allows
for specifying a condition of arbitrary complexity as a single string
with an XPath expression, in which pertinent PVs and datastore states
are referred to by their respective positions in the YANG tree.

ECA Conditions are associated with ECA Events and evaluated only
within event threads triggered by the event detection.

When an ECA Condition is evaluated to TRUE, the associated ECA Action
is executed.

The model structure for the condition is shown below:
   
~~~~ TREE
{::include condition.tree}
~~~~

## ECA Action

The action list consists of updates or invocations on local managed
object attributes and a set of actions are defined as follows, which
will be performed when the corresponding event is triggered:

* sending one time notification

* (-re)configuration - modifying a configuration data in the
   conventional configuration datastore.

* (re-)configuration scheduling - scheduling one time or periodic
  (re-)configuration in the future

* adding/removing event notify subscription (essentially, the same
  action as performed when a client explicitly adds/removes a
  subscription)

* executing an RPC defined by a YANG module supported by the server
  (the same action as performed when a client interactively calls
  the RPC);

* performing operations and function calls on PVs (such as assign,
  read, insert, iterate, etc);

* starting/stopping timers;

* stopping current ECA;

* invoking another ECA;

* NO-ACTION action - meaningful only within ECA's Cleanup Condition-
  Action list to indicate that the ECA's Normal Condition-Action
  thread must be terminated as soon as one of the required Actions
  is rejected by the server.

Two points are worth noting:

* When a NETWONF/YANG RPC appears in an ECA Action body, the server
  is expected to interpret this as follows: execute the same logic,
  as when the client explicitly calls said RPC via NETCONF.  For
  example, when TE_Tunnel_Path_Computation RPC is found in the
  currently executed Action, the server is expected to call its TE
  path computation engine and pass to it the specified parameters in
  the Action input.

 * When a "Send notification" action is configured as an ECA Action,
   the notification message to be sent to the client may contain not
   only elements of the data store (as, for example, YANG PUSH or
   smart filter notifications do), but also the contents of global
   and local PVs, which store results of arbitrary operations
   performed on the data store contents (possibly over arbitrary
   period of time) to determine, for example, history/evolution of
   data store changes, median values, ranges and rates of the
   changes, results of configured function calls and expressions,
   etc. - in short, any data the client may find interesting about
   the associated event with all the logic to compute said data
   delegated to the server.  Importantly, ECA notifications are the
   only ECA actions that directly interact with and hence need to be
   unambiguously understood by the client.  Furthermore, the same ECA
   may originate numerous single or repetitive semantically different
   notifications within the same or separate event firings.  In order

 to facilitate for the client the correlation of events and ECA
 notifications received from the server, the GNCA model requires
 each notification to carry mandatory information, such as event
 and (event scope unique) notification names.

 Multiple ECA Actions could be triggered by a single ECA event.

 Any given ECA Condition or Action may appear in more than one ECAs.

 The model structure for the actions is shown below:

~~~~ TREE
{::include action.tree}
~~~~

# ECA
An ECA container includes:

* ECA name

* List of local PVs.  As mentioned, local PVs could be configured as
  dynamic (their instances appear/disappear with start/stop of the
  ECA execution) or static (their instances exisr as long as the ECA
  is configured).  The ECA input (the contents of the associated
  notification message, such as YANG PUSH or smart filter
  notification message) are the ECA's implict local dynamic PVs with
  automatically generated names

* Normal CONDITION-ACTION list: configured conditions each with
  associated actions to be executed if the condition is evaluated to
  TRUE

* Cleanup CONDITION-ACTION list: configured conditions/actions to be
  evaluated/executed in case any Action from the Normal CONDITION-
  ACTION list was attempted and rejected by the server.  In other
  words, this list specifies the ECA cleanup/unroll logic after
  rejection of an Action from the Normal CONDITION-ACTION list.  An
  empty Cleanup CONDITION-ACTION list signifies that the ECA's
  normal Actions should be executed regardless of whether the
  previously attempted ECA Actions were rejected or not by the
  server.  Cleanup CONDITION-ACTION list containing a single NO-
  ACTION Action signifies that the ECA thread is to be immediately
  terminated on rejection of any attempted Action (without executing
  any cleanup logic)

# Where ECA scripts are executed?

It could be said that the main idea of the ECA is decoupling the
place where the network control logic is defined from the place where
it is executed.  In previous sections of this document it is assumed
that the network control logic is defined by a client and pushed to
and executed by the network (server).  This is accomplished via ECA
scripts, which are essentially bunches of regular NETCONF style
operations (such as get, set, call rpc) and event notifications glued
together via Policy Variables, PVs.

# ECA YANG Model (Tree Structure)

The following tree diagrams {{RFC8340}} provide an overview of the data
model for the "ietf-eca" module.

~~~~ TREE
{::include ietf-eca.tree}
~~~~

# ECA YANG Module

~~~~ YANG
<CODE BEGINS> file "ietf-eca@2020-04-15.yang"
{::include ietf-eca.yang}
<CODE ENDS>
~~~~

# Security Considerations

The YANG modules defined in this document MAY be accessed via the
RESTCONF protocol {{RFC8040}} or NETCONF protocol {{RFC6241}}. The lowest
RESTCONF or NETCONF layer requires that the transport-layer protocol
provides both data integrity and confidentiality, see Section 2 in
{{RFC8040}} and {{RFC6241}}. The lowest NETCONF layer is the secure
transport layer, and the mandatory-to-implement secure transport is
Secure Shell (SSH) {{RFC6242}}. The lowest RESTCONF layer is HTTPS, and
the mandatory-to-implement secure transport is TLS {{RFC5246}}.

The NETCONF access control model {{RFC6536}} provides the means to
restrict access for particular NETCONF or RESTCONF users to a
preconfigured subset of all available NETCONF or RESTCONF protocol
operations and content.

There are a number of data nodes defined in this YANG module that are
writable/creatable/deletable (i.e., config true, which is the default).
These data nodes may be considered sensitive or vulnerable in some
network environments. Write operations (e.g., edit-config) to these data
nodes without proper protection can have a negative effect on network
operations. These are the subtrees and data nodes and their
sensitivity/vulnerability:

* /gnca:policy-variables/gnca:policy-variable/gncd:name

* /gnca:events/gnca:event/gncd:name

* /gnca:conditions/gnca:condition/gnca:name

* /gnca:actions/gnca:action/gnca:name

* /gnca:ecas/gnca:eca/gnca:name

* /gnca:eca-scripts/gnca:eca-script/gnca:script-name

# IANA Considerations

This document registers two URIs in the IETF XML registry {{RFC3688}}. Following the format in {{RFC3688}},
the following registrations are requested to be made:


~~~~
---------------------------------------------------------------------
   URI: urn:ietf:params:xml:ns:yang:ietf-eca
   Registrant Contact: The IESG.
   XML: N/A, the requested URI is an XML namespace.
---------------------------------------------------------------------
~~~~

This document registers one YANG module in the YANG Module Names
registry {{RFC6020}}.


~~~~
---------------------------------------------------------------------
   Name:         ietf-eca
   Namespace:    urn:ietf:params:xml:ns:yang:ietf-eca
   Prefix:       gncd
   Reference:    RFC xxxx
---------------------------------------------------------------------
~~~~

--- back

# Appendix A.  ECA Condition Expression Examples
   Here are two examples of Condition Expression:

   (a) a condition that only includes data store states and constants,
   for example:

  TE metric of Link L in Topology T  greater than 100,
  it can be expressed as follows:

   "/nw:networks/nw:network[network-id='T']/nt:link[link-id='L']/tet:te\
   /tet:te-link-attributes/tet:te-delay-metric > 100"

   (b) a condition that also includes a Policy Variable, for example:

  Allocated bandwidth of Link L in Topology T greater than 75% of
  what is stored in Policy Variable B, it can be expressed as follows:

  "/nw:networks/nw:network[network-id='T']/nt:link[link-id='L']/tet:te\
   /tet:te-link-attributes/tet:max-resv-link-bandwidth\
   > (ietf-eca:policy-variables/policy-variable[name='B']/value) * 0.75"

# Appendix B. ECA Model Usage Example

     +---------------------------+
     |     Management System     |
     +---------------------------+
               |
           ECA |
         Model |
               |
               V
    +----------------------^-----+
    |      Managed Device  |     |
    |                      |     |
    |    //--\\ Condition--+     |
    |   | Event|       /    \    |
    |   |      |----->|Actions   |
    |    \\--//        \    /    |
    |                   ----     |
    +----------------------------+

The management system pushes down one ECA policy to control
temperature sensor behavior in the managed device or the router
chasis that supports NETCONF/RESTCONF protocol operation.  The event
type is chosen as alarm-event-stream, each alarm instance is
identified by 3 tuples (resource, alarm-type identifier, and alarm-
type qualifier), in this example, resource instance-identifier is set
to '/hw:hardware/hw:component[hw:name='fan1']', the alarm-type is set
to 'enviromental-alarm', the alarm-type qualifier is set to 'fan-
temperature-anamoly'.  The event name is set to 'fan-temperature-
monitoring', when the temperature of fan component within the managed
device cross preconfigured threshold 80 degree Celsius , the managed
device needs to send one time log notification to the management
system.  The XML example snippet is shown as below:

<gnca>
 <events>
   <event>
    <name>fan-temperature-monitoring</name>
    <resource>/hw:hardware/hw:component[hw:name='fan1']</resource>
    <alarm-type-id>enviromental-alarm</alarm-type-id>
    <alarm-type-qualifier>fan-temperature-anamoly</alarm-type-qualifier>
  </event>
 </events>
 <conditions>
  <condition>
    <name>fan-temperature-condition</name>
    <condition-xpath>
    /hw:hardware/hw:component[hw:name='fan1']/hw:sensor-data/hw:value > 80 &&
    /hw:hardware/hw:component[hw:name='fan1']/hw:sensor-data/hw:value-type='celsius'
    </condition-xpath>
  </condition>
 </conditions>
 <actions>
  <action>
    <name>fan-temperature-anamoly</name>
    <action-element>
    <name>log-report</name>
     <notify-operation>
       <name>anamoly-report</name>
       <policy-argument>
       <xpath>/hw:hardware/hw:component[hw:name='fan1']/hw:sensor-data/hw:value</xpath>
       </policy-argument>
     </notify-operation>
    </action-element>
  </action>
 </actions>
 <ecas>
  <eca>
    <name>fan-temperature-handling</name>
    <event-name>fan-temperature-monitoring</event-name>
    <condition-action>
     <name>temperature-cross-threshold-handling</name>
     <condition>fan-temperature-condition</condition>
     <action>fan-temperature-anamoly</action>
   </condition-action>
 </eca>
 </ecas>
</gnca>
   
# Changes between Revisions
v07 - v08
* Add Andy Bierman as coauthor based on input and suggestion during
  the ML discussion.
* Cleanup the references.

v06 - v07

* Reuse alarm notification event received on an event stream (RFC
  8639) in ECA logic
* Remove SUPA related text and reference
* Remove smart filter example in the appendix 2
* Remove ECA example in the appendix 3
* Remove the section on the relation with YANG Push
* Replace condition definition and design with the one proposed in Igor's draft
* Add ecas subtree and eca-scripts subtree 8. Event, Condition, Action, ECA 
  definition and description update.

v05 - v06

* Decouple ECA model from NETCONF protocol and make it applicable
  to other network mangement protocols.

* Move objective section to the last section with additional
  generic objectives.


v04 - v05

* Harmonize with draft-bryskin and add additional attributes in the
  models (e.g., policy variable, func call enhancement, rpc
  execution);

* ECA conditions part harmonization;

* ECA Event, Condition, Action, Policy Variable and Value
  definition;

* Change ietf-event.yang into ietf-eca.yang and remove
  ietf-event-trigger.yang


v02 - v03

* Usage Example Update: add an usage example to introduce how to
  reuse the ietf-event-trigger module to define the
  subscription-notification smarter filter.


v01 - v02

* Introduce the group-id which allow group a set of events that can
  be executed together

* Change threshold trigger condition into variation trigger
  condition to further clarify the difference between boolean trigger
  condition and variation trigger condition.

* Module structure optimization.

* Usage Example Update.


v00 - v01

* Separate ietf-event-trigger.yang from Event management modeland
  ietf-event.yang and make it reusable in other YANG models.

* Clarify the difference between boolean trigger condition and
  threshold trigger condition.

* Change evt-smp-min and evt-smp-max into min-data-object and
  max-data-object in the data model.

# Contributors
{: numbered="no"}

~~~~
   Alexander Clemm
   Futurewei
   Email: ludwig@clemm.org
   Zitao Wang
   Huawei
   Email: wangzitao@huawei.com

   Chongfeng Xie
   China Telecom
   Email: xiechf@ctbri.com.cn

   Xiaopeng Qin
   Huawei
   Huawei Bld., No.156 Beiqing Rd.
   Beijing  100095
   China
   qinxiaopeng@huawei.com

   Tianran Zhou
   Huawei
   Email: zhoutianran@huawei.com

   Aihua Guo
   Individual
   aihguo1@gmail.com

   Nicola Sambo
   Scuola Superiore Sant'Anna
   Via Moruzzi 1
   Pisa  56124
   Italy
   Email: nicola.sambo@sssup.it

   Giuseppe Fioccola
   Huawei Technologies
   Riesstrasse, 25
   Munich  80992
   Germany
   Email: giuseppe.fioccola@huawei.com

~~~~

# Acknowledgements
{: numbered="no"}

Igor Bryskin, Xufeng Liu, Alexander Clemm, Tianran
Zhou contributed to an earlier version of [GNCA]. We would like to thank
the authors of that document on event response behaviors delegation for
material that assisted in thinking that helped improve this
document.
