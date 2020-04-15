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
- ins: M. Wang
  name: Michael Wang
  org: Huawei Technologies,Co.,Ltd
  abbrev: Huawei
  street: 101 Software Avenue, Yuhua District
  city: Nanjing
  region: ''
  code: '210012'
  country: China
  email: wangzitao@huawei.com
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
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
normative:
  RFC7950:
#  RFC7952:
  RFC3688:
  RFC6020:
  RFC6241:
  RFC6242:
#  RFC6370:
  RFC6536:
#  RFC2981:
  RFC8328:
  RFC3460:
informative:
  RFC8342:
  RFC8340:
  RFC8340:
  RFC8040:
  RFC8040:
  RFC5246:

--- abstract

RFC8328 defines a policy-based management framework that allows
definition of a data model to be used to represent high-level, possibly
network-wide policies. Policy discussed in RFC8328 are classified into
imperative policy and declarative policy, Event Condition Action (ECA)
policy is an typical example of imperative policy. This document defines
a YANG data model for the ECA policy management. The ECA policy YANG
provides the ability for the network management function (within a
network element) to control the configuration and monitor state change
and take simple and instant action on the server when a trigger
condition on the system state is met.

--- middle

# Introduction {#intro}

Network management consists of using one or multiple device-,
technology-, service specific policies to influence management behavior
within the system and make sure policies are enforced or executed
correctly.

{{RFC8328}} defines a policy-based management framework that allow
definition of a data model to be used to represent high-level, possibly
network-wide policies. Policies discussed in {{RFC8328}} are classified
into imperative policy and declarative policy. Declarative policy
specifies the goals to be achieved but not how to achieve those goals
while imperative policy specifies when Events are triggered and what
actions must be performed on the occurrence of an event. Event Condition
Action (ECA) policy is a typical example of imperative policy.

Event-driven management of states of managed objects across a wide
range of devices can be used to monitor state changes of managed objects
or resource and automatic trigger of rules in response to events so as
to better service assurance for customers and to provide rapid autonomic
response that can exhibit self-management properties including
self-configuration, self-healing, self-optimization, and
self-protection. Following are some of the use-cases where such ECA
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

# Relationship to YANG Push

YANG-push mechanism provides a subscription service for updates from
a datastore. And it supports two types of subscriptions which are
distinguished by how updates are triggered: periodic and on-change.

The on-change push allow receivers to receive updates whenever
changes to target managed objects occur. This document specifies a
mechanism that provides three trigger conditions:

* Existence: When a specific managed object appears,disappear or
  object change, the trigger fires, e.g. reserved ports are
  configured.

* Boolean: The user can set the type of boolean operator (e.g.
  unequal, equal, less, less-or-equal, greater, greater-or-equal, etc)
  and preconfigured threshold value (e.g. Pre-configured threshold).
  If the value of a managed object meet Boolean conditions, the
  trigger fires, e.g., when the boolean operator type is 'less', the
  trigger will be fired if the value of managed object is less than
  the pre-configured Boolean value.

* Threshold: The user can set the rising threshold,the falling
  threshold, the delta rising threshold, the delta falling threshold.
  A threshold test regularly compares the value of the monitored
  object with the threshold values, e.g., an event is triggered if the
  value of the monitored object is greater than or equal to the rising
  threshold or an event is triggered if the difference between the
  current measurement value and the previous measurement value is
  smaller than or equal to the delta falling threshold.

In these three trigger conditions, existence with type set to object
change is similar to on Push change.

In addition, the model defined in this document provides a method for
closed loop network management automation which allows automatic trigger
of rules in response to events so as to better service assurance for
customers and to provide rapid autonomic response that can exhibit
self-management properties including self-configuration, self-healing,
self-optimization, and self-protection. The details of the usage example
is described in Appendix A.

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
policy Value is used for modeling values and constants used in policy
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

The ECA event are used to keep track of state of changes associated
with one of multiple operational state data objects in the network
device. Typical examples of ECA event include a fault, an alarm, a
change in network state, network security threat, hardware
malfunction, buffer utilization crossing a threshold, network
connection setup, and an external input to the system.

Each ECA Event has the following attributes:

* name, the name of ECA event;

* type, either one time or peridic scheduling;

* group-id, which can be used to group a set of events that can
  be executed together,e.g., deliver a service or provide service
  assurance;

* scheduled-time,configuration scheduling - scheduling one time
  or periodic.

Nested-event are supported by allowing one event's trigger to
reference other event's definitions using the call-event
configuration. Called events apply their triggers and actions before
returning to the calling event's trigger and resuming evaluation. If
the called event is triggered, then it returns an effective boolean
true value to the calling event. For the calling event, this is
equivalent to a condition statement evaluating to a true value and
evaluation of the event continues.

All events specified in the ECA policy model are continuously
monitored by the server.

The model structure for the ECA Event is shown below:

~~~~ TREE
<CODE BEGINS>
{::include event.tree}
<CODE ENDS>
~~~~

## ECA Condition

Condition can be seen as a logical test that, if satisfied or
evaluated to be true, cause the action to be carried out. In this
model, condition can be specified as logical combinations of the
following three condition expressions:

* Existence: An existence condition monitors and manages the
  absence, presence, and change of a data object, for example,
  interface status. When a monitored object is specified, the system
  reads the value of the monitored object regularly.

  * If the existence test type is Absent, the system triggers a
    network event and takes the specified action when the
    monitored object disappears.

  * If the existence test type is Present, the system triggers
    a network event and takes the specified action when the
    monitored object appears.

  * If the existence test type is Changed, the system triggers
    a network event and takes the specified action when the value
    of the monitored object changes.

* Boolean: A Boolean test compares the value of the monitored
  object with the reference value and takes action according to the
  comparison result. The comparision hierarchy is logical
  hierarchies specified in a form of:

~~~~
<policy-variable> <relation> <policy-value> or
<policy-variable1> <relation> <policy-variable2>

relation is one of the comparison operations from the set:
==, !=, >, <, >=, <=
~~~~


* The operation types include unequal, equal, less, lessorequal,
  greater, and greaterorequal. For example, if the comparison type
  is equal, an event is triggered when the value of the monitored
  object equals the reference value. The event will not be triggered
  again until the value becomes unequal and comes back to equal.

* Threshold: A threshold trigger condition regularly compares the
  value of the monitored object with the threshold values , with one
  of the following mechanisms:

  * A rising event is triggered if the value of the monitored
    object is greater than or equal to the rising threshold.

  * A falling event is triggered if the value of the monitored
    object is smaller than or equal to the falling threshold.

  * A rising event is triggered if the difference between the
    current measurement value and the previous measurement value
    is greater than or equal to the delta rising threshold.

  * A falling network event is triggered if the difference
    between the current measurement value and the previous
    measurement value is smaller than or equal to the delta
    falling threshold.

  * A falling event is triggered if the values of the monitored
    object, the rising threshold, and the falling threshold are
    the same.

  * A falling event is triggered if the delta rising threshold,
    the delta falling threshold, and the difference between the
    current sampled value and the previous sampled value is the
    same.

> If the value of the monitored object crosses a threshold multiple
times in succession, the managed device triggers an event only for
the first crossing.

In addition, logical operation type can be used to describe complex
logical operations between different condition lists under the same
event, for example, (condition A & condition B) or condition C.

The model structure for the condition is shown below:

~~~~ TREE
{::include condition.tree}
~~~~

## ECA Action

The action list consists of updates or invocations on local managed
object attributes and a set of actions are defined as follows, which
will be performed when the corresponding event is triggered:

* sending one time log notification

* (-re)configuration - modifying a configuration data in the
  conventional configuration datastore.

* adding/removing event notify subscription (essentially, the
  same action as performed when a client explicitly adds/removes a
  subscription)

* executing an RPC defined by a YANG module supported by the
  server (the same action as performed when a client interactively
  calls the RPC);

* performing operations and function calls on PVs (such as
  assign, read, insert, iterate, etc);


Multiple ECA Actions could be triggered by a single ECA event.

Any given ECA Condition or Action may appear in more than one
ECAs.

The model structure for the actions is shown below:

~~~~ TREE
{::include action.tree}
~~~~

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

* /eca/event/name

* /eca/policy-variables/policy-variable/name

* /eca/event/actions/action/name

* /eca/event/condition/name



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
   Prefix:       eca
   Reference:    RFC xxxx
---------------------------------------------------------------------
~~~~


# Objectives for existing and possible future extension

This section describes some of the design objectives for the ECA
Policy management Data Model:

* Clear and precise identification of Event types in the ECA
  Policy.

* Clear and precise identification of managed object (i.e., policy
  variable) in the ECA Policy.

* Allow nested ECA policy,e.g, one event to be able to call another
  nested event.

* Allow the client use NETCONF/RESTCONF protocol or any other
  management protocol to configure ECA Policy.

* Allow the server send updates only when the value falls within a
  certain range.

* Allow the server send updates only when the value exceeds a
  certain threshold for the first time but not again until the
  threshold is cleared.

* Allow the client optimize the system behavior across the whole
  network to meet objectives and provide some performance guarantees
  for network services.

* Allow the the server provide rapid autonomic response in the
  network device that can exhibit self-management properties including
  self-configuration, self-healing, self-optimization, and
  self-protection.

* Allow the ECA execution thread in the server use YANG Push/YANG
  Push extension to communicate with the client.



--- back

# ECA Model Usage Example

~~~~
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
~~~~

For Example:

The management system push down one ECA policy to control interface
behavior in the managed device that supports NETCONF protocol
operation.

The explicit policy variable of Event "interface-state-monitoring" is
set to "/if:interfaces/if:interface[if:name='eth0']", the trigger list
contains two conditions: 1)The publisher sends a push-change-update
notification; 2) the value of "in-errors" of interface[name='eth0']
exceeded the pre-configured threshold. When these conditions are met,
corresponding action will be performed, i.e. disable
interface[name='eth0']. The XML examples are shown as below:

~~~~
  <notification xmlns="urn:ietf:params:xml:ns:netconf:notification:1.0">
   <eventTime>2017-10-25T08:22:33.44Z</eventTime>
   <push-change-update
        xmlns="urn:ietf:params:xml:ns:yang:ietf-yang-push">
     <id>89</id>
     <datastore-changes>
       <yang-patch>
         <patch-id>0</patch-id>
         <edit>
           <edit-id>edit1</edit-id>
           <operation>replace</operation>
           <target>/ietf-interfaces:interfaces</target>
           <value>
             <interfaces
                  xmlns="urn:ietf:params:xml:ns:yang:ietf-interfaces">
               <interface>
                 <name>eth0</name>
                 <oper-status>up</oper-status>
               </interface>
             </interfaces>
           </value>
         </edit>
       </yang-patch>
     </datastore-changes>
   </push-change-update>
  </notification>

    <eca>
     <event>
     <name>interface-state-monitoring</name>
     <type>interface-exception</type>
     <scheduled-time>
     <periodic>
     <interval>10m</interval>
     </periodic>
     </scheduled-time>
     <condition>
      <name>state-push-change</name>
      <condition-description>sent a yang push
       \changed notification</condition-description>
      <test>
       <existence>
       <policy-variable>/yp:notification/yp:push-change-update/yp:id[id=89]\
      /yp:datastore-changes/.../yp:target="/ietf-interfaces:interfaces='eth0'\
     </policy-variable>
        </existence>
      </test>
     </explict-variable>
     <condition>
      <name>evaluate-in-errors</name>
      <condition-description>evaluate the number of
       the error packets</condition-description>
      <test>
       <boolean>
        <operator>greater-or-equal</operator>
        <policy-value>
         <policy-argument>
          <policy-value>100</policy-value>
         </policy-argument>
        </policy-value>
        <policy-variable>
         <policy-argument>
         <explict-variable>/if:interfaces/if:interface[if:name='eth0']\
        /if:statistic/if:in-errors</explict-variable>
         </policy-argument>
        </policy-variable>
       </boolean>
      </test>
     </condition>
     <action>
      <name>foo</name>
       <set>
        <policy-variable>/if:interfaces/if:interface[if:name='eth0']</policy-variable>
        <value>
         <interfaces>
          <interface>
           <name>eth0</name>
           <enable>false</enable>
          </interface>
         </interfaces>
        </value>
       </set>
     </action>
    </event>
   </eca>
~~~~


# Usage Example of Reusing Trigger-Grouping in smarter filter

The "ietf-eca.yang" module defines a set of groupings for a generic
condition expression. It is intended that these groupings can be reused
by other models that require the trigger conditions, for example, in
some subscription and notification cases, many applications do not
require every update, only updates that are of certain interest. The
following example describe how to reuse the "ietf-eca" module to define
the subscription and notification smarter filter.


~~~~
  import ietf-subscribed-notifications {
     prefix sn;
  }
  import ietf-eca {
   prefix eca;
  }

  augment "/sn:subscriptions/sn:subscription" {
    description "add the smart filter container";
    container smart-filter {
       description "It concludes filter configurations";
       uses eca:trigger-grouping;
    }
  }
~~~~

The tree diagrams:


~~~~
  module: ietf-smart-filter
  augment /sn:subscriptions/sn:subscription:
    +--rw smart-filter
      +-- (test)?
         +--:(existences)
         |  +-- existences
         |     +-- type?              enumeration
         |     +-- policy-variable?
         |             -> /policy-variables/policy-variable/name
         +--:(boolean)
         |  +-- boolean
         |     +-- operator?          operator
         |     +-- policy-value
         |     |  +-- policy-argument
         |     |     +-- (argument)?
         |     |        +--:(explict-variable)
         |     |        |  +-- explict-variable?   leafref
         |     |        +--:(implict-variable)
         |     |        |  +-- implict-variable?   leafref
         |     |        +--:(value)
         |     |           +-- policy-value?       leafref
         |     +-- policy-variable
         |        +-- policy-argument
         |           +-- (argument)?
         |              +--:(explict-variable)
         |              |  +-- explict-variable?   leafref
         |              +--:(implict-variable)
         |                 +-- implict-variable?   leafref
         +--:(threshold)
            +-- threshold
               +-- rising-value?                    leafref
               +-- rising-policy-variable*
               |       -> /policy-variables/policy-variable/name
               +-- falling-value?                   leafref
               +-- falling-policy-variable*
               |       -> /policy-variables/policy-variable/name
               +-- delta-rising-value?              leafref
               +-- delta-rising-policy-variable*
               |       -> /policy-variables/policy-variable/name
               +-- delta-falling-value?             leafref
               +-- delta-falling-policy-variable*
               |       -> /policy-variables/policy-variable/name
               +-- startup?                         enumeration
~~~~


# Changes between Revisions

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

   Chongfeng Xie
   China Telecom
   Email: xiechf@ctbri.com.cn

   Xiaopeng Qin
   Huawei
   Huawei Bld., No.156 Beiqing Rd.
   Beijing  100095
   China
   qinxiaopeng@huawei.com

   Alexander Clemm
   Futurewei
   Email: ludwig@clemm.org

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

This work has benefited from the discussions of ECA Policy over the
years. In particular, the SUPA project [
https://datatracker.ietf.org/wg/supa/about/ ] provided approaches to
express high-level, possibly network-wide policies to a network
management function (within a controller, an orchestrator, or a network
element).

Igor Bryskin, Xufeng Liu, Alexander Clemm, Tianran
Zhou contributed to an earlier version of [GNCA]. We would like to thank
the authors of that document on event response behaviors delegation for
material that assisted in thinking that helped improve this
document.
