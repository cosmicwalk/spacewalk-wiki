
# **DEPRECATED, NO LONGER USED**

# Running Spacewalk on a Virtual machine




    #!html
    <div style="background-color:#2b299e; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/workinprogress.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Work in Progress</h2>
       <div style="padding-bottom:0.5em;" >
           <p>This page is currently under construction, page maintainer: antoine at nebula dot nl. </p>
       </div>
    </div>

This Page describes the steps needed to achieve the following *targets*;


    #!html
    <div style="background-color:#d08e13; color:white; background-image:url('/spacewalk/attachment/wiki/WikiSnippetsAndTemplates/important.png?format=raw'); background-repeat:no-repeat; padding-left:7em;background-position:1em;" >
       <h2>Important</h2>
       <div style="padding-bottom:0.5em;" >
           <p>This page does not constitute a procedure, It is meant as an example page of how things can be done, just pick out the right bits and pieces you need when installing or designing a system.</p>
       </div>
    </div>

A multi-disk-vm-based-non-graphical-spacewalk-server

  * Do not use any graphical tooling
  * Create a multi disk VM
  * Install a bare minimal fedora 10 x86_64
  * Install Spacewalk 0.5 x86_64 using internal DB and Web interfase (layered setup is later)
  * Make sure that spacewalk starts to provision fedora 10
  * Make sure automatic updates of fedora 10 are in place (using a kewl script probably :) )
## the targets explained



*No graphical tooling: *

In a lot of company's (yes, even in this broadband world) network connectivity is not as normal as you should/could expect. A lot of time, due to cost-management a lot of the costs are reduced including bandwith. Also, it's simply too easy to do it with all the gfx tooling around.

*Multi disk VM:*

In a spacewalk setup you def want to split up some directories to be able to make it grow. The idea is to create a VM with at least 3 files and a local - resizeale disk. Not because it's logical, but just because we can and I want the reader to have some examples on how to do the one or the other.

*non layered vs layered *

In this setup we've choosen to stick to the standard layout for now, Yes it is a good idea to do things layered, but we need to figure out how and when and where. If we would go for the layered layout we thing about going the httpd -- tomcat -- database -- nfs solution but that needs some researching.. (also a W.I.P :) )

* Fedora 10 auto updates *

Currently no automatic fetching mechanism is in place to be able to download and  update a fedora 10 (or better said: yum based) software
channel. (the current thought is that we (mis)use the yum download-only features combined with rhnpush actions) Not clear yet how this could work but that is also why I've started writing this page in the first place.



=====================
(here)

Standard detailed design doc layout:
# architecture

## Overall architectural design


== design considerations ==

## figurative layout
# hardware requirements

## Hardware type A


== Hardware type B ==
# Software requirements
 

## Software package A

== Software package B ==


= Installation ==
## Installation preprarations

== Technical components == 

## physical install

== Procedure A ==

## Procedure B

== Control list ==
# B.A.U. Business As usual

## BAU Procedures

 

## BAU Monitors

(skipping a lot of $@#$$$!!)
# Other design considerations
 

## Functional modules

== Functional scripts ==

## Backup and Recovery Design

== Security Architecture == 

## System interfase

== Batch jobs / cron ==

## performance and responce Considerations



