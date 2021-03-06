# Requirements

## Trusted Relationships




The concept of 'Trusted Relationships' will be added to the Multiorg feature to allow System Administrators the ability to share resources (system migration, channels) between different Organizations. The satellite admin defines a trust relationship that applies for either orboth system migration and content sharing while the Org admin define the granularity of which orgs share the specific channel. 

The following logic should occur to support this new trusted relationship concept:

 * The satellite shall support defining trust relationships between organizations. Once a "trust" has been established, organizations within that trust shall be able to perform operations such as sharing channels and migrating systems with other organizations within that trust
 * The satellite administrator shall have the ability to configure the trust relationship between Organizations
   * The Satellite administrator will have the option to configure the trust relationship as relating to channel sharing only, system migration only, or both channel sharing and system migration.
 * The trust relationship is bi-directional.
   * If system migration is enabled in the trust relationship, org a can migrate systems to org b, and org b can migrate systems to org a.
   * If content sharing is enabled in the trust relationship, org a can access shared channels from org b, and org b can access shared channels from org a.
 * Trust relationships may be provisioned by either the UI or API
 * Trust relationships can be set up with a single organization, multiple organizations or ALL organizations
 * Trust relationships will default to none initially
 * The All trusted relationship includes all future orgs, not the current "snapshot" of organizations at the time of creation
 * Trust relationships are lower precedence than channel sharing properties, so if a channel is protected in an ALL org trusting org, the other orgs still can't see it.
 * No sharing of kickstarts, configuration channels, etc. will be supported in the first phase
 * Each individual relationship is unique and mutually exclusive from other org's trust relationship. For example, if Org2 trust Org 3 and Org1 trust Org2, then Org1 will not trust Org3 unless a trust relationship is defined between Org1/Org3 or Org1 trusts all Orgs
 * Example Scenario:
  * Pre-requisite: Org 1, Org 2, Org 3, .... OrgN exist. 
  * Satellite administrator provisions the following trusts relationships: 
   * Trust 1: Org 1 + Org 2 + Org 3 
   * Trust 2: Org 4 + Org 5, system migrate only, no channel sharing.
   * Trust 3: Org 3 + Org 4 
   * Trust 4: Org 7 has a trust all 
  * In this scenario, 
   * Org 1 may share channels or migrate systems to Org 2 & Org 3 
   * Org 2 may share channels or migrate systems to Org 1 & Org 3 
   * Org 3 may share shannels or migrate systems to Org 1 & Org 2 & Org 4.
   * Org 4 may migrate systems to Org 5. Org 4 may not share channels with org 5. Org 4 may share channels and migrate systems with org 3.
   * Org 5 may migrate systems to Org 4. Org 5 may not share channels with org 4.
   * Org 6 - Org 10 do not have permission to share channels or migrate systems with any organization
   * Org 1 - Org 5 do not have permission to share channels or migrate systems with Org 6 - Org 10 
## Software Channels

The channel sharing capability shall apply only to custom/cloned channels. The reason being, official Red Hat channels are already shared across organizations. The Satellite Administrator enables content sharing by allowing a trusted relationship to occur between specific orgs. The Organization Administrator selects which channels to share within the trusted relationship. The following requirements will be supported: 

 * The channel sharing capability shall utilize the trust relationships configured by the satellite administrator and only those organizations in a trusted relationship will be able to share channels amongst each other. Refer to  Trust Relationship for more details. 
 * The channel sharing capability shall apply only to custom/cloned channels. The reason being, official Red Hat channels are already shared across organizations. 
 * An organization or channel administrator shall have the ability to share channels with organizations within it's trust. 
 * If the channel being shared has a parent channel and that parent is a custom channel, the parent must also be shared
 * If the channel being shared has a parent channel and that parent is a base channel, the shared channel will only be in the channel list of organizations that have a compatible base channel. 
 * For any channel that has been shared, all errata and packages associated with that channel shall also be shared enabling them to appear in the trusted organizations UI pages as well as available via search and API.
 * Custom channels will still be created private and only become public if explicitly shared. 
 * Shared custom/cloned channels are read only in all organizations except the one they were originally created in.
 * The UI shall clearly display what organization shared a channel and the administrator that shared it. 
 * The channels list UI at /rhn/software/channels will be revamped to include the following channel filters:
   * *All Channels*: lists ALL channels except retired channels
   * *Popular Channels*: lists channels that have at least one system registered to them, sorted in parent channel order of greatest to least number of systems registered. Has a drop down at the top where you can select: "View channels with [ no | < 10 | < 50 | < 100 | < 500 | < 1000 | 1000+ ] systems registered."
   * *Red Hat Channels*: lists all Red Hat provided channels.
   * *My Channels*: lists all cloned and custom channels.
   * *Shared Channels*: has two tabs.
     * *Channels I Share*: list of channels your org is sharing
     * *Channels Shared With Me*: list of channels made available from other orgs.
   * *Retired Channels*: Red Hat provided channels that are no longer supported. 
 * Organization and channel administrators shall have the ability to revert a public channel to private status at any time. 
 * If a channel is unshared, that channel will continue to be listed within organizations that have at least one system subscribed to it. In this case, the channel will no longer be available for updates to systems in that organization and it will appear disabled (e.g. grayed out) in the UI. The intent is to inform the organization and channel administrator that an action needs to be taken. Once there are no more systems subscribed to that channel within the organization, the channel will no longer appear in the organization's channel list.
 * Channel status will also be affected to organizations that are removed from the sharing organization's trusted relationship. 
 * If the channel being unshared is a parent to another channel that is being shared (i.e. child channel), the child channel must also be private
 * allow for global disablement of pre-append of OrgID to activation keys within WebUI and API calls. Default is to keep same behavior as with Satellite 5.1 (Multi-Org phase I implementation)
## System Migration

Due to rapidly expanding complexity we've made intentional efforts to limit the scope of system migration while planning for future expansions. Requirements currently for system migration:

 * Migration can be performed by users with the org admin role
 * Systems may be migrated to any other org that is trusted
 * Migration will be performed via an API call
 * Migration will remove remove all associations with objects in the old org, migrate the system and it's history over to the new org
 * Migration will return entitlements to old org
 * Migration will remove channels, system groups, virtual guest associations (guests remain in old org as unaffiliated virtual systems), monitoring probes, configuration channels from old org
 * Migration should occur immediately (no moderation queue).
 * An event should appear in the system history indicating that this system was migrated from another org
## API Support

Api supported calls are referenced at [[wiki:/ApiAdditions]]

## Future Features?



