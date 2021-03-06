## List Tag

There have been several list tag implementations throughout the history of the project.  The original perl list tag, an older Java list tag, and the new Java list tag (known as ListTag 3.0).  New and updates to existing pages should use ListTag 3.0.  ListTag 3.0 has the largest set of features features (including filters, pagination, page size selector, csv support) and is the easiest to use.


## Using List Tag 3.0

Like any struts page, it consists of 

 * Struts Action
 * JSP

A single action is typically used for both the retrieval of the data to display on the page (including any filtering that takes place) as well as handling form submissions from the page. This is a slight change from the older model that used a *SetupAction to populate the data and a *Action class to collect the selected results and process them.

It's been a common trend in Spacewalk code to use Data Transfer Objects (DTO) to hold the data that gets rendered by the ListTag. ListTag 3.0 can readily use any DTO or beanified object/value holders. In fact one of the primary motivations of ListTag 3.0 was to make it favorably work with both Hibernate objects and DTO objects. However objects that are used in List which have checkbox in them need to implement the com.redhat.rhn.frontend.struts.Selectable interface. This is because the list tag needs to hold the checkbox selections somewhere and the DTO passed to the view was deemed as the best place to do it.

## Action side

#### Summary

The following is a high-level summary of the steps needed in the action.

 * The action must extend RhnAction (overriding the execute method) and implement Listable (implementing the getResults method).
 * The action should use one of the "ListHelper" classes to set up the action correctly. Possible options are (details on each follow):
   * ListHelper - used when there is no user selection involved in the page.
   * ListRhnSetHelper - used when the user's selections should be stored in an RhnSet.
   * ListSessionSetHelper - used when the user's selections should be stored in the session.
 * The getResults() method should return the data to display in the page.
 * Often, the ListHelper "isDispatched()" method is used to determine if the page form has been submitted.

#### Simplest case


For a very simple list with non-selectable objects all you need is this:


```java
    public class SystemGroupListSetupAction extends RhnAction implements Listable {
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
            ListHelper helper = new ListHelper(this, request);
            helper.execute();
            return mapping.findForward("default");
        }
        
        public  List getResult(RequestContext context) {
            User user =  context.getLoggedInUser();
            return SystemManager.groupList(user, null);
        }
    }
```

The List returned but the "getResult" method is the actual list of objects we want to display. 

### Sets

If you would like to add sets to the mix, it takes a bit more code. There are 2 kind of sets SessionSet and RhnSet. SessionSet has a life cycle of a session. This is usually adequate for 90% of the cases, but for cases like the system set manager where we want to keep a selected list of systems in a database we use RhnSet (because we want it to be persistent). You will probably find more occurrences of RhnSets than SessionSets in the code because Session sets were only recently introduced.

#### Rhn Set



```java
    public class SystemGroupListAction extends RhnAction implements Listable {
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
    
            ListRhnSetHelper helper = new ListRhnSetHelper(this, request,RhnSetDecl.SYSTEM_GROUPS)
            helper.execute();
            if(helper.isDispatched()) {
                //handle the dispatch action (like removing groups etc)
                return  mapping.findForward("success");
            } 
            
            return mapping.findForward("default");
        }
        
        protected List getResults(RequestContext context) {
            User user =  requestContext.getLoggedInUser();
            return SystemManager.groupList(user, null);
        }
    }
```

Basically, the ListRhnSetHelper does all the ground work to setup the set object. All we need to provide is the set declaration...

#### Session Set

Session Sets uses the traditional java.util.Set to keep track of the checkboxes, basically, you need a unique declaration name to bind the set to the session.



```java
    public class SystemGroupListAction extends RhnAction implements Listable {
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
            ListSessionSetHelper helper = new ListSessionSetHelper(this, request);
            helper.execute();
            if(helper.isDispatched()) {
                //handle the dispatch action (like removing groups etc)
                Set<String> set = helper.getSet();
                for (String element : set) {
                    //do remove or waht ever operation
                }
                helper.destroy() ; //destroys the set..
                return  mapping.findForward("success");
            } 
            
            return mapping.findForward("default");
        }
        
        protected List getResults(RequestContext context) {
            User user =  requestContext.getLoggedInUser();
            return SystemManager.groupList(user, null);
        }
    }
```

Basic difference s/ListRhnSetHelper/ListSessionSetHelper here is a slightly more compilcated example

```java
    public class ViewModifyPathsAction extends RhnAction implements Listable {
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
            RequestContext context = new RequestContext(request);
            User user = context.getLoggedInUser();
            request.setAttribute("type", getType(mapping.getParameter());
            Map params = new HashMap();
            params.put(RequestContext.SID, context.getRequiredParam(RequestContext.SID));
            ListSessionSetHelper helper = new ListSessionSetHelper(this, request, params);
            helper.execute();
            if (helper.isDispatched()) {
                ActionForward forward =  handleDispatchAction(mapping, context);
                helper.destroy();
            }
            return mapping.findForward(RhnHelper.DEFAULT_FORWARD);
        }
    
        public List getResult(RequestContext context) {
            ConfigChannelType type =  (ConfigChannelType) 
                            context.getRequest().getAttribute("type");
            Server server = context.lookupAndBidServer();
            User user = context.getLoggedInUser();
            return cm.listManagedPathsFor(server, user, type);
        }
    
        private ActionForward  handleDispatchAction(ListSessionSetHelper helper,
                                                    ActionMapping mapping, 
                                                    RequestContext context) {
            ...    
            int size = copySelectedToChannel(helper, server.getSandboxOverride(),
                       context.getRequest(),
                       user);
            ...
        }
    
        private int copySelectedToChannel(ListSessionSetHelper helper,
                                          ConfigChannel channel,
                                          HttpServletRequest request,
                                          User user) {
            ConfigurationManager cm = ConfigurationManager.getInstance();
            Set <String> set = helper.getSet();
            for (String key : set) {
                Long fileId = Long.valueOf(key);
                ConfigFile cf = cm.lookupConfigFile(user, fileId);
                ConfigRevision cr = cf.getLatestConfigRevision();
                cm.copyConfigFile(cr, channel, user);
            }
            int size = set.size();
            return size;
        }  
    
    }
```
 
## jsp side

Here is an example of a complete jsp showing a very simple list with only one column (name).


Note: the rl:listset tag will take care of creating the HTML form element for you. Any other form elements, such as hidden parameters, should be placed within the rl:listset tag.


```html
    <%@ taglib uri="http://rhn.redhat.com/rhn" prefix="rhn" %>
    <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
    <%@ taglib uri="http://jakarta.apache.org/struts/tags-html" prefix="html" %>
    <%@ taglib uri="http://jakarta.apache.org/struts/tags-bean" prefix="bean" %>
    <%@ taglib uri="http://rhn.redhat.com/tags/list" prefix="rl" %>
    
    <html:xhtml/>
    <html>
    <head>
        <meta name="page-decorator" content="none" />
    </head>
    <body>
    
    <html:messages id="message" message="true">
        <rhn:messages><c:out escapeXml="false" value="${message}" /></rhn:messages>
    </html:messages>
    
    <rhn:toolbar base="h1" img="/img/rhn-icon-system_group.gif" imgAlt="system.common.groupAlt"
     helpUrl="/rhn/help/reference/en/s2-sm-system-group-list.jsp"
     creationUrl="/network/systems/groups/create.pxt" 
     creationType="group"
     creationAcl="user_role(system_group_admin)">
      <bean:message key="grouplist.jsp.header"/>
    </rhn:toolbar>
    
    <rl:listset name="groupSet" legend="system-group">
    
    <rl:list emptykey="assignedgroups.jsp.nogroups">
            
    		<rl:decorator name="ElaborationDecorator"/>
    
      	   <!--Name Column -->
    		<rl:column sortable="true" 
    				   bound="false"
                               styleclass="first-column last-column"
    		           headerkey="grouplist.jsp.name" 
    		           sortattr="name"
                               defaultsort="asc"  >
    			<c:out value="<a href=\"/network/systems/groups/details.pxt?
                                      sgid=${current.id}\">${current.name}</a>" escapeXml="false" />
    		</rl:column>		
    
    
    </rl:list>
    </rl:listset>
    
    </body>
    </html>
```

## Selectable column


```html
    	 	<rl:selectablecolumn value="${current.id}"
    	 						selected="${current.selected}"
    	 						styleclass="first-column"/>
```

## filters
 

Every List in the new List Tag can have a filter, basically, you add the filterattr attribute to the rl:column

```html
    <rl:list .......>
    ...
    <rl:column .... filterattr="name" .../>
    </rl:list>
```

## alpha bar

To add the alpha bar simply add


alphabarcolumn="<field-name>" to the rl:list tag, where <field-name> is the name of the field in the table's entity object that should be associated with the alpha bar. For example, to have the alpha bar correspond to the description field: 


```html
    <rl:listset name="keySet"> 
        
       <rl:list width="100%"
             styleclass="list"
             emptykey="activelist.jsp.noUsers"
             alphabarcolumn="description">
             
           <COLUMNS.....>
            
          </rl:list>
    </rl:listset>
```

## csv export

To add CSV support to a page add the below tag to the jsp.


```html
    <rl:csv dataset="dataset"
            name="list" 
            exportColumns="userLogin,userLastName,userFirstName,roleNames,lastLoggedIn"/>
```

This should be outside of a  <rl:list> </rl:list>  section, but within <rl:listset></rl:listset>

(Optional) "dataset" is the name of the dataset which is holding your list 

(Optional) "list" should match the name given to the <rl:list> this works with (by default its the word list) . This allows the CSV tag to reuse an Elaborator if it was stored with the associated <rl:list>.

exportColumns is a comma separated string of attributes you want to export.


> Note: The CSV is handled by it's own Action, CSVDownloadAction, all data is passed to it in Session context. 


## page size widget
 

To add a page Size widge (that can now be seen on the rhn/systems/SystemList.do page, simply use the PageSizeDecorator:

```html
    <rl:decorator name="PageSizeDecorator"/>
```
Feel free to look in system_listdisplay.jspf for an example of usage. 

## decorators

Optional functionality on the ListTag is provided through the use of decorators. A decorator is a tag that adds a specific piece of functionality simply by including it in the JSP.


For more information on how to write a decorator, see the ListTagDecorators page.
# Useful Notes



 * If your table supports a view change (such as displaying only certain packages in a channel - see adding packages to an erratum for an example), keep the following in mind:
  * I found it easiest to put the drop down in a separate form, which prevents the helper.isDispatched from accidentally tripping.
  * Somewhere deep in the List*Helper classes is code to automatically populate a message about no selection being made if the page is submitted without checking any selectable items. This clashed with the view change functionality since I don't care if the user has selected anything. I had to add a check for the view parameter in the request; if it is present call "helper.ignoreEmptySelection();" on the helper instance. That will prevent the "no selection made" message for the view submissions only.
 * The values for the rl:listset "legend" attribute are the names of the files in WEB-INF/includes/legends without the "-legend.jsp" portion (for example, the errata legend attribute value is simply "errata", which corresponds to the file "WEB-INF/includes/legends/errata-legend.jsp").
 * Columns whose data came from an elaborator *cannot be sorted.* The elaborator lazily loads the details as needed and thus a global sort is not applicable in this case. Attempting to sort on an elaborator column will provide the visual cues that the column is sorted (arrow in header, column is colored) but the data order will not change.
 * The rl:listset tag and html:form elements do not play well together. When embedding other form elements inside of the form created by rl:listset, use straight HTML instead.