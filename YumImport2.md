
# **DEPRECATED, NO LONGER USED**

# Yum Import (Part 2)

### Future Features




 * Do a full sync from channel to repo (remove packages if removed) (but keep the current behaviour as an option)
 * support multiple yum repos -> one channel
 * also, allow one yum repo -> multiple channels (with only one sync) [[https://bugzilla.redhat.com/show_bug.cgi?id=517677]]
 * Better UI support
 * Background periodic (daily, weekly, etc) syncing
 * also get stage2.img and vmlinuz/initrd.img for the kickstartable trees while syncing [[https://bugzilla.redhat.com/show_bug.cgi?id=517165]]
 * allow syncing of all yum repos in one command (i.e. --label should no longer be mandatory) [[https://bugzilla.redhat.com/show_bug.cgi?id=517467]]
   * Addressed in Spacewalk 1.0
 * allow syncing of a base repo together with all its childs without having to specify the childs (e.g. add --with-childs) [[https://bugzilla.redhat.com/show_bug.cgi?id=517468]]
 * ~~Support Mirror lists~~ (Commited in c9c2d15afdf2537fff94aa47c0931aca198cc9d7 in master)
   * Addressed in Spacewalk 0.7
 * http proxy support
 * allow seeing a list of what will be imported (commandline only)
 * allow multiple (different) channels to be synced at the same time.