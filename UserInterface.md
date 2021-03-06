= User interface for developers

Note: this documentation is based on the not yet merged bootstrap-css branch

== Basic user interface

Basic styles like lists, forms, buttons, images, tables, notifications, pagination, progress bars, panels, etc are provided by Twitter Bootstrap 3.

You can find the documentation here: http://getbootstrap.com

== Icons

The (font) based icons are provided by Font-Awesome (http://fortawesome.github.io/Font-Awesome/) . For the icon list see http://fortawesome.github.io/Font-Awesome/icons/

For how to use them, see http://fortawesome.github.io/Font-Awesome/examples/

For porting, normally you will port a tag like:


    #!text/html
    <img src="/img/$iconfile.gif" alt="image.key.message" border="0" />

To


    #!text/html
    <i class="icon-$name"></i>

== Accesibility

If the icon is already just decorating a label, there is no need for something like the "alt" attribute in images.
If the icon is the only piece of user interface there, you can add a "title" attribute or use the Bootstrap "sr-only" class, which will be displayed in Screen Readers only:


    #!text/html
    <div class="sr-only"><bean:message key="image.key.message" /></div>

== Lists

Lists are done with the ListTag and you should use the JSP tag whenever possible. However, to emulate the
look of a list, the tag generates the following structure:


    #!text/html
    ... addons
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">This is the title</h3>
        ... addons
      </div>
      <table class="table table-striped">
        ...
      </table>
      <div class="panel-footer">
         .. addons
      </div>
    </div>
    ...addons

If you need to use manual HTML, you can do a simple panel if you don't need the table (mind that you need a panel-body instead), and if you need a simple table you can skip the panel.

The structure of the generated list is like the following (in a pesudo markup HAML like format)


    .spacewalk-list
      .spacewalk-list-top-addons
          .spacewalk-list-alphabar
          .spacewalk-list-top-addons-extra
      .spacewalk-list-top-extra
      .panel.panel-default
        .panel-heading
          .panel-title
            h3
          .spacewalk-list-head-addons
            .spacewalk-list-filter
            .spacewalk-list-head-addons-extra
          .spacewalk-list-head-extra
        table.table.table-striped
        thead ..
        tbody ..
        .panel-footer
          .spacewalk-list-footer-addons  
            .spacewalk-list-reflinks
            .spacewalk-list-footer-addons-extra
          .spacewalk-list-footer-extra
        .spacewalk-list-bottom-addons
          .spacewalk-list-pagination
          .spacewalk-list-bottom-addons-extra
        .spacewalk-list-bottom-extra
## bugs/defects

* ~~hide the documentation question marks on java and perl pages~~

 * tkasparek: 4459f313edad880c8d592606db94599f66e42536 c6b325c90cd95790a4c8f346fc0154f87827de77
* ~~/rhn/systems/Overview.do and other /rhn/systems/*.do pages - entitlements are mentioned 4 times~~
 * this is already fixed by Duncan
* ~~[ISSUE](GENERAL) little arrows for listing in lists are missing (at least in my Opera) (in recent FF as well (there are some utf8 glyphs instead))~~
  * Silvio fixed this
  * ~~Overview -> Subscription Management -> System Entitlements - and here there is too many of these arrows~~
    * also this page have footer broken
    * Silvio fixed this
* ~~Overview -> Most Critical Systems -> Enhancement Errata have no icon in front of that 0 I see these (compare with other two columns there Security and Bugfix)~~
 * tkasparek: looks fixed
* ~~Systems -> System Groups~~
  * ~~note that buttons are note styled~~
   * tkasparek: 3d7d638eee4b065e444a1705d12891e8ffded14f
  * ~~as I do have there group with no system, it have small "check" at the beginning of the row meaning "No applicable errata". That "check" is different than the one in the legend ("blue box with check" vs. "green check in circle")~~
   * tkasparek: ef788cc6d32c21fc54ac2a0ea8efbb512bd84daf
* ~~Systems -> System Groups -> create new group - create new group and ignore its "Details" page (it is .pxt page) and go to "Target Systems"~~
  * ~~"work with group" and "delete group" links should be on the right I guess~~ 4ca619a531965fdbf0505dc87b23ff851223c7ed
  * ~~name of the group should be bigger~~ 4ca619a531965fdbf0505dc87b23ff851223c7ed
  * ~~old icons are present here~~ 279f75f57cdd9d6a44c03eae2183fdc5d50e2eaa b2fa9fa5aef7c3054cfacaa0539edbca46a2199c 
* ~~when you lock system, check "Systems" page and that system have our normal (old) yellow lock icon instead of that one noted in the "System legend"~~
  * mkollar: 4b1e2ad3e1fc44bfc89a259d1f84b61b06f1ece6
* ~~non-virtual system in "Systems" page have different icon instead of the icon in "System legend"~~
 * ~~which one should be used? (legend is wrong)~~
 * b5aefe004eb6839ede6e0cff62635496f4f4368c
* ~~On Systems -> <some_system> -> Details - there is multiple windows like "System Info", "System Events", "System Properties" and "Subscribed Channels" - second two windows are located differently than in pre-rebranded version, they are stretched to full width instead of respecting two column layout of that webpage area~~ -> dmacvicar (not pushed yet)
* ~~[ISSUE](GENERAL) In Systems -> <your_system> -> Software -> Software Channels - buttons are not styled - looks like this is problem with all other sections on this level like ... -> Software -> Packages -> Verify (here check button and drop-down menu for "items on page")~~
 * tkasparek: 3d7d638eee4b065e444a1705d12891e8ffded14f
* ~~In Systems -> <your_system> -> Software -> Packages -> Verify - when you type something into "Filter by Package Name" field and press "Enter", packages are not filtered. If you click on eye icon instead of pressing "Enter", it works as expected.~~
* ~~in pre-rebranded version when you have used filtering of the list by first letter, in the resulting list you had little arrow next to first item starting with the given letter (Systems -> <your_system> -> Software -> Packages -> Verify - click on "J" and you get result listing packages iproute, iptables, iputils, isorelax, jabberd - but small arrow marking start of J-packages is missing there)~~
* ~~if a list is sorted by a column, the column used to have "sorted up-down" / "sorted down-up" icons (now fa-sort-amount-asc / fa-sort-amount-desc could be used)~~
* ~~Systems -> <your_system> -> Configuration -> Add Files -> Create File - form is messy here~~
  * [[tkasparek]] looks fixed
  * [[duncan]] looks fixed
* ~~Systems -> <your_system> -> Provisioning -> Snapshots - table with list of the snapshots is not styled properly~~
  * [[tkasparek]] looks fixed
  * [[duncan]] looks fixed
* /rhn/kickstart/CreateProfileWizard.do - when you fill the form but chooses channel without KS tree associated and click "Next", nothing happens (no error message)
  * [[tkasparek]] looks fixed
* Channels -> <my_channel> -> Manage Software Channels -> Repositories -> Sync - part where you are choosing date is in the mess (same in Admin -> Task Schedules -> <some_schedule_name> -> Frequency)
 * look fine, can somebody confirm that it is ugly?
* ~~ /network/systems/system_list/in_channel_family.pxt?cfam_id=1000 and /network/systems/system_list/regular_in_channel_family.pxt?cfam_id=1000 - not rebranded ~~ 9bbabcf7f653f53d8db1521b941accf2d57a084e 31928ce93c928792e60f03203854de2c248d3aad
* ~~/rhn/account/Addresses.do - strangely big "Mailing Address" and surrounding text as well~~ (dmacvicar)
* ~~icon links /rhn/account/UserPreferences.do and /rhn/Logout.do in upper right corner do not have some hover texts~~
  * mkollar: 0cb00ed9e28e3b35f6a5ecee1703585dc98085e0
* ~~in Sat560 link to change language is in upper left corner, not in up middle as in SWnightly~~
 * ~~not an issue IMO - tkasparek~~
* ~~when you hover the mouse over the organization name in upper right corner it gets underlined which evokes it is a link, but it is not (in Sat560 it is not underlined)~~
 * mkollar: 78f697722a2cc3b7758c5505db2eeef408f99719 beabf914f15796b85594945a4487a47b1d5a613d
* generated HTML seems not to be valid (xmllint fails like [here](http://pastebin.test.redhat.com/177086))
  * ~~usage of `targer="_new"`, which is not valid construct~~
    * mkollar: fcc32c9cb7a62860286fd265c68781fa52a36d4b
  * I have not found page where xmllint would complain if --html option was used.
    Tags `nav`, `header`, footer, and `aside` are part of HTML 5 standard.
* ~/help/about.pxt has broken links - [Read more](https://www.redhat.com/red_hat_network/deploydetail) about RHN architectures.
* ~~/rhn/systems/provisioning/preservation/PreservationList.do - selecting and then deleting doesn't do anything, the same on /rhn/configuration/file/ManageRevision.do?cfid=?.~~
  * jdobes: 34a271e910250384d8ff1fe7fb21549c3ffd7d9e
* ~~ ~/rhn/apidoc/index.jsp, ~/rhn/apidoc/faqs.jsp, ~/rhn/apidoc/scripts.jsp - icon in title needs to be changed with FA icon~~ 1a4b309479661a9e9a642ef83ed3080d63bb9e7f
  * ~~useless link in title~~ d1200e8117eb900aa1633721f71f6f8ad76369e6
* ~~ ~/rhn/apidoc/scripts.jsp - syntax of example script may be highlighted (e.g. use [highlight.js](http://highlightjs.org/)) ~~
* ~~ ~/rhn/account/LocalePreferences.do - in 21st century and utf8 fonts, do we need pictures with text in national alphabets (if you select hindi as webui language, we have to use some fonts supporting hindi anyway), therefore getting rid of images would be really nice? ~~
* speaking of fonts - title font is changed to "Roboto Light", but body text remains set to Helvetica, ... -> why not to use one font (different style - Thin, Light, Normal, Medium, Bold, Ultra-Bold)
* ~~ ~/rhn/systems/SystemEntitlements.do - table navigation icons are wierd and wrongly placed ~~ fixed by Silvio
  * ~~ broken page footer ~~ fixed by Silvio
* ~~ ~/rhn/channels/software/Entitlements.do - missing table navigation icons ~~ fixed by Silvio
* across the whole webui - combining old style selection list on some places with 'bootstraped' new lists doesn't look well
* ~~/rhn/systems/DuplicateIPList.do - squared + icon cannot be expanded~~
 * ~~looks like DuplicateSystemsRowRenderer is broken~~
* ~~ ~/network/systems/groups/errata_list.pxt?sgid= - old icons and unstyled title ~~ 6f22cd46ae6f5c097694213e5bdced6c275d19a8 fb8d77fa49ac14bf2b4e756188dce9384305cc82 8ea182a4bedeedf6694088e8f3ee9ce4f9660eba
* ~~ ~/network/systems/ssm/groups/index.pxt - the whole page is in old style ~~ 
* ~~~/rhn/systems/customdata/CustomDataList.do - broken layout~~ (dmacvicar)
* ~~~/rhn/systems/customdata/CreateCustomKey.do - newline before/after icon/title~~ (dmacvicar)
* ~~~/rhn/ssm/index.do - old system icon used there~~
  * mkollar: 3d535ac4e9292f9cffc15068cd4a122c29273c61
* ~~~/rhn/YourRhn.do - Recently Scheduled Actions shows green icon for completed actions while in legend there's gray icon~~
  * mkollar: fe8ada220de98e6a87b8e1e881a1912c50aecccd
* ~~"Sytems" -> <system> -> "Events" -> "History" uses old style icon(s)~~ cff9a22f3e09cd0ad063f8b07bb72cd47be7c08b
* ~~deactivate user uses old icon~~ 7eaabf32cbd631015e76ba508e0bd4dc3d0ce5f0
* ~~some strings on /rhn/ssm/index.do are missing~~
* ~~self-closing i-tags are not allowed (i.e. /rhn/systems/SystemEntitlements.do)~~
* ~~java/code/src/com/redhat/rhn/frontend/taglibs/list/ListTag.java render header with multiple closing </tr> tags. See for example ~rhn/systems/Overview.do~~
 * looks fixed
 * [e6cfd68](master-bootstrap-css-fixes) if it is the same thing
* ~~/rhn/systems/Overview.do?showgroups=true difference when showing systems (button "View System Groups" vs link "View Systems")~~
* ~~`<meta name="page-decorator"` leaks into html... was not problem with xhtml 1.0 transitional, but is with html 5.
  Is this some kind of forgotten piece of code from old dark ages?~~
* ~~/rhn/ssm/index.do missing **ssm.overview.misc.delete** and **ssm.overview.misc.reboot** resource strings~~
* ~~/rhn/systems/ssm/MigrateSystems.do popup menu (aka select) after 'Target Orgenization' is not styled~~ fixed by duncan [97cc7c7](master-bootstrap-css-fixes)
  * the same issue is also on other pages -> where?
* https://bugzilla.redhat.com/show_bug.cgi?id=1048981
* ~~/rhn/kickstart/KickstartDetailsEdit.do?ksid=1 - form is messy here (the form is outside of screenview)~~ 97b2c561bcdc05c65ac8ef6c879b09588b31cb5b
* /rhn/systems/details/virtualization/ProvisionVirtualizationWizard.do - in the provisioning virtual guest ("Advanced Kickstart Configuration"), buttons "Basic Options" and "Schedule Kickstart and Finish" are not functional. JS error: "TypeError?: form is null"
* ~~ https://bugzilla.redhat.com/show_bug.cgi?id=1051230 ~~
* https://bugzilla.redhat.com/show_bug.cgi?id=1058816
* SSM message [[NUMBER]] systems selected is broken in all languages apart from English
* ~~rhn/systems/details/SystemChannels.do Very strange display of channels~~
* ~~Unstyled elements:~~ fixed by duncan [23b7285](master-bootstrap-css-fixes)
  * ~~ ~/rhn/errata/manage/AddPackages.do?eid=...~~
* ~/rhn/configuration/ChannelUploadFiles.do?ccid=1
  * file upload button looks... bad/wierd/ugly/inconsistent.
  * maybe adding `btn-file` class to parent element?
* rewrite /network/monitoring/scout/index.pxt to java
* /rhn/errata/Search.do (/pages/errata/erratasearch.jsp) still uses tables not bootstrap divs/spans
* ~~rhn/systems/details/packages/PackageList.do?sid=1000010005&&sid=1000010005&list_4021519721_alpha_key=G
  alphabet seeking is rendered with two consecutive ampersands (&amp;&amp;)... calling such url
  causes warnings in catalina:~~
  {{{
Feb 17, 2014 9:17:44 AM org.apache.tomcat.util.http.Parameters processParameters
WARNING: Parameters: Invalid chunk '' ignored.
  }}}
* /rhn/software/packages/Details.do?pid=14 - all text in Details, RPM package, Download, ... tables are shifted up; most likely missing text css class
* Information bar/panel, what is displayed, when we add new snapshotTag does not have correct CSS class (missing "alert alert-info", it has old "site-info") (mkorbel)
  * The same situation, when we try add a existing name of tag
  * Also deleting
* /rhn/systems/details/ListCustomData.do?sid=1000010000 - broken layout (mkorbel)
* Channels -> Manage Software Channels -> <Channel> -> Repositories -> Sync ... broken layout of checkboxes, (you may need to have some repos to see it).
## usability

* ~~need of color icons - not clear on the /rhn/systems/Overview.do and other /rhn/systems/*.do pages, what systems are critical, inactive, ... - in legend there are colored version of these~~

 * tkasparek: 71c98a54d044621e53db0ce6ee292e864eb0960e
* ~~move the '+ create new ...' to the right and make it green, e.g. 'create new note' on /rhn/systems/details/Notes.do page~~ 845d79295a847d7c388b008b4ba6d54388af38ee
* make expanded sub-menu lighter than the higher level menu - e.g. /rhn/systems/SystemList.do page
* [[jhutar]] overall theme seems to have low contras to me, I have found this recommendations:
  * http://www.w3.org/TR/WCAG20-CSS-TECHS/G17.html - Ensuring that a contrast ratio of at least 7:1, but there is also another recommendation speaking about 4.5:1
  * http://www.javascripter.net/faq/rgbtohex.htm - convert rgb(...) to hex notations if you are lazy like I'm
  * http://juicystudio.com/services/luminositycontrastratio.php - count contrast of two colors
  * I have tried these:
    * h1 (like "Overview") vs. body which gave me 9.51:1 - good but seems worse to me personally
    * menu item vs. its background gave me 5.13:1 - bad
    * when you fill something into the form, filled text vs. form (e.g. plain input row) is 4:0 - very bad
* [[jhutar]] In Systems -> <choose_your_system> when in "Details" note second level menu which should be smaller I believe (to express this is just sub-menu)
* API overview list shall be displayed on the right page side, e.g. /rhn/apidoc/handlers/SystemHandler.jsp
* ~~differenciate info message vs. warning/error messages (used to be blue vs. red, now blue vs. blue)~~
* ~~if multiple errors occurs only one error message is shown and other are ignored~~
* schedule date picker is not consistent with implementation on other pages:
  * SSM > Errata (/rhn/systems/ssm/ListPatches.do)
  * SSM > Misc > Reboot (/rhn/systems/ssm/misc/RebootSystem.do)
* pages doesn't implement "two phase" confirmation (i.e. confirmation page after submit):
  * SSM > Errata (/rhn/systems/ssm/ListPatches.do)
  * ~~SSM > Misc > Reboot (/rhn/systems/ssm/misc/RebootSystem.do)~~ eea3c6320bfc3dae8532844a7c0e71dcc5712c39
  * Systems > <System> > Schedule System Reboot
* Create user page (/rhn/users/CreateUser.do?account_type=into_org)
  * formerly Login: and Account Information: tables were aligned vertically 
  * prefix, firstNames and lastName inputs were aligned horizontally in a row (so label "First, Last Name*:" made sense but now it does not)
* List pagination and alpha bar
  * ~~bar goes from 0..9A..Z on /rhn/systems/details/packages/PackageList.do but A..Z0..9 on /rhn/systems/SystemEntitlements.do and /network/systems/system_list/regular_in_channel_family.pxt~~ Fixed by duncan [c245169](master-bootstrap-css-fixes)
    * Come on guys, open /usr/lib/perl5/vendor_perl/5.10.0/Sniglets/ListView/List.pm and render_alphabar foreach my $alpha ('A' .. 'Z', '0' .. '9'), even in 2.0 :-)
  * ~~different set of pagination arrows on /network/systems/system_list/regular_in_channel_family.pxt~~ fixed by duncan [90ed0c7](master-bootstrap-css-fixes)
  * ~~strange horizontal line separating A .. J | K .. Z on /network/systems/system_list/regular_in_channel_family.pxt~ just a side effect of numbers being incorrectly at the end on the perl part
  * active letters have border on left and are 1-2px higher than other letters on /rhn/systems/SystemEntitlements.do
  * box around search icon (eye) is 1-2px higher than search input box on /rhn/systems/SystemEntitlements.do
  * ~~in some browsers (Google Chrome) pagination selector displays wrong number (eg. when selected 500 items, 50 is shown; when 250 -> 25 is shown, in case of 100 -> 10 is shown), but correct number of items is shown~~ 63a5b26f9b5cbdc41fa8e05aedf82185f90e957a
* ~~layout of   /rhn/systems/details/probes/ProbeDetails.do~~
  * ~~create / delete / edit should be aligned to right~~ 845d79295a847d7c388b008b4ba6d54388af38ee
  * ~~tables should not have 100% width~~ d691ee9b0663f8db8a71ae689a44e83235cb240c
  * ~~edit probe has wrong icon~~ 03cb1a20fd696365e6b1f0d6448a26b544f90025
* ~~/rhn/admin/config/MonitoringConfig.do~~ not bootstrap specific (2.0 has it too) fixed by duncan [12b3302](master-bootstrap-css-fixes)
  * help texts ((e.g. 'redhat.com' for myemail@redhat.com)) should be bellow input bars (see /rhn/activationkeys/Edit.do?tid=1 how it should look like)
* ~~/rhn/software/channels/All.do~~
  * ~~there missing opening tags (THEAD and TR) in channel table~~
* ~~/rhn/systems/details/packages/InstallPackages.do?sid=1000010001~~ fixed by duncan [8839652](master-bootstrap-css-fixes)
  *  The tag (input type="hidden" name="list_4021519721_filtername) does not have a right angle bracket. The tag is not closed.
  * I've not managed to reproduce it
* /rhn/apidoc/handlers/PackagesHandler.jsp (this is problem of whole API documentation)
  * When I click on the link (detail of API method, ex. getDetails), than I lost left menu and header of page (look at this /rhn/apidoc/handlers/PackagesHandler.jsp#getDetails)
  * Sometimes, I get the same problem on standard SW pages, but with unusable reproducer.
  * I have got the fix of this problem: remove line 6407 from spacewalk.css  (.spacewalk-main-column-layout > overflow: hidden; ), jdobes: yes it works but it brokes layout
  * jdobes: the reason is described there: http://stackoverflow.com/questions/10609288/bug-negative-margin-with-anchor , we need to use another solution for columns heights
## not related to Twitter Bootstrap items

* ~~/rhn/channels/manage/repos/RepoCreate.do - When I try to create new repo with url, what have been used in another repo, I get ISE 500~~

 * ~~this is also present in Sat 5.6 so not a bootstrap issue~~