## Old Way

All pages using the new List Tag has an action the looks similar to this ... We encode the complete logic needed to handle the list stuff in the RhnAction.. Something like



    #!java
    public class ViewModifyPathsAction extends RhnAction {
        public static final String LIST_NAME = "files";
        public static final String DATA_SET = "pageList";
    
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
            RequestContext context = new RequestContext(request);
            User user = context.getLoggedInUser();
            Set set = SessionSetHelper.lookupAndBind(request, getDecl());
    
            //if its not submitted
            // ==> this is the first visit to this page
            // clear the 'dirty set'
            if (!context.isSubmitted()) {
                set.clear();
            }
            ...
            SessionSetHelper helper = new SessionSetHelper(request);
            
            if (request.getParameter(DISPATCH) != null) {
                // if its one of the Dispatch actions handle it..            
                helper.updateSet(set, LIST_NAME);
                if (!set.isEmpty()) {
                    return handleDispatchAction(mapping, context);
                }
                else {
                    RhnHelper.handleEmptySelection(request);
                }
            }        
            
    
            Server server = context.lookupAndBindServer();
            ConfigurationManager cm = ConfigurationManager.getInstance();
            List dataSet = cm.listManagedPathsFor(server, user, 
                                                 getType(mapping.getParameter()));
            // if its a list action update the set and the selections
            if (ListTagHelper.getListAction(LIST_NAME, request) != null) {
                helper.execute(set, 
                                LIST_NAME,
                                dataSet);
            }        
    
            // if I have a previous set selections populate data using it       
            if (!set.isEmpty()) {
                helper.syncSelections(set, dataSet);
                ListTagHelper.setSelectedAmount(LIST_NAME, set.size(), request);            
            }
            request.setAttribute(ListTagHelper.PARENT_URL, 
                                request.getRequestURI() + "?sid=" + server.getId());
            request.setAttribute(DATA_SET, dataSet);
    
            ListTagHelper.bindSetDeclTo(LIST_NAME, getDecl(), request);
            return mapping.findForward(RhnHelper.DEFAULT_FORWARD);
    
        }
        
        protected String getDecl() {
            return getClass().getName();
        }
    
        private ActionForward  handleDispatchAction(ActionMapping mapping, 
                                                    RequestContext context) {
            ....
             int size = copySelectedToChannel(server.getSandboxOverride(),
                        context.getRequest(),
                        user);
            ....
        }
    
          private int copySelectedToChannel(ConfigChannel channel, HttpServletRequest request,
                                            User user) {
    		//.... ddo whatever
        }  
    
    }	
### Cons

 * Ordering of clearing sets /  submitting sets, get the results at the right place etc are important..

 * Boiler plate code copy pasted in each action when interface with hooks points may have been easily used..
 * Hard to change if the list logic is wrong.. Change has to be done in every Action..
## Better Way



I looked at what exactly differed between each list 5 things ->
 * List getResult(RequestContext context); 
 * String getListName();
 * String getDataSetName();
 * String getParentUrl(RequestContext context);
 * String getDecl(RequestContext context);  -> Needs to be unique for every set ...
 * ActionForward handleDispatch(ActionMapping mapping, ActionForm formIn, HttpServletRequest request, HttpServletResponse response);

I added these methods to a simple "ListSubmitable" interface
## Approach 1




    #!java
    Using ListSessionSetHelper or ListRhnSetHelper and ListSubmitable interfaces
    public class ViewModifyPathsAction extends RhnAction implements ListSubmitable {
        public static final String LIST_NAME = "files";
        public static final String DATA_SET = "pageList";
    
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
                    ListSessionSetHelper helper = new ListSessionSetHelper(this);
                    return helper.execute(mapping, formIn, request, response);
            }
    
        public String getListName()  { 
                    return LIST_NAME;
            }
            
        public String getDataSetName() {
                    return DATA_SET;
            }
        
        public  String getDecl(RequestContext context) {
            return getClass().getName() + context.getRequest().getParameter("sid");
        }
    
       public String getParentUrl(RequestContext context) {
    	Server server = context.lookupAndBindServer();
    	return context.getRequest().getRequestURI() + "?sid=" + server.getId());
        
       }
    
        public List getResult(RequestContext context, ActionMapping mapping) {
                    Server server  = context.lookupAndBindServer();
                    User user = context.getLoggedInUser();
                    return cm.listManagedPathsFor(server, user, ConfigChannelType.normal());      
        }
    
        public ActionForward  handleDispatch(ActionMapping mapping, 
                                    ActionForm formIn, HttpServletRequest request,
                                    HttpServletResponse response) {
            ....
             int size = copySelectedToChannel(server.getSandboxOverride(),
                        request,
                        user);
            ....
        }
    
          private int copySelectedToChannel(ConfigChannel channel, HttpServletRequest request,
                                            User user) {
    		//.... ddo whatever
        }  
    
    }
### Annoyances

* Need to implement all the methods of the interface, even if there are defaults

* the ListSessionSetHelper is doing a bit too much by not only dealing with the setup part but also the submits ..
* Pages with 2 lists  get murky...
## Approach 2





    #!java
    public class ViewModifyPathsAction extends RhnAction {
        public static final String LIST_NAME = "files";
        public static final String DATA_SET = "pageList";
    
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
    	ChannelSet channelSet = new ChannelSet(request);
    	if (channelSet.isDispatched()) {
    		return	handleDispatch (mapping, formIn, request, response);
            }
            return mapping.findForward(RhnHelper.DEFAULT_FORWARD);
            }
    
        public ActionForward  handleDispatch(ActionMapping mapping, 
                                    ActionForm formIn, HttpServletRequest request,
                                    HttpServletResponse response) {
            ....
             int size = copySelectedToChannel(server.getSandboxOverride(),
                        request,
                        user);
            ....
        }
    
          private int copySelectedToChannel(ConfigChannel channel, HttpServletRequest request,
                                            User user) {
    		//.... ddo whatever
        }  
    
       private static class ChannelSet extends WebSessionSet {
    	public ChannelSet(HttpServletRequest  request) {
      		super(request);
            }
    	public List getResults() {
    
    	}
            public  String getDecl() {
              RequestContext context = getContext();
    	  return getClass().getName() + context.getRequest().getParameter("sid");
            }
    
           public String getListName()  { 
                    return LIST_NAME;
            }
            
           public String getDataSetName() {
                    return DATA_SET;
            }
    
           public List getResult(RequestContext context) {
                    Server server  = context.lookupAndBindServer();
                    User user = context.getLoggedInUser();
                    return cm.listManagedPathsFor(server, user, 
                                             ConfigChannelType.normal());      
           }
    
       	public String getParentUrl(RequestContext context) {
    		Server server = context.lookupAndBindServer();
    		return context.getRequest().getRequestURI() + "?sid=" + server.getId());
        
    	}
       }
    
    }
### Approach 3



    #!java
    public class ViewModifyPathsAction extends RhnAction implements Listable {
        public static final String LIST_NAME = "files";
        public static final String DATA_SET = "pageList";
    
        public ActionForward execute(ActionMapping mapping,
                ActionForm formIn,
                HttpServletRequest request,
                HttpServletResponse response) {
    		RequesContext context = new RequestContext(request);
                    ListSessionSetHelper helper = new ListSessionSetHelper(this);
    		helper.setListName(getListName());
    		helper.setDataSetName(getListName());
    		helper.setDecl(getDect(context));
    		helper.setParentUrl(getParentUrl(context));
      	if (helper.isDispatched()) {
    		return	handleDispatch (mapping, formIn, request, response);
            }
    
    		
    	return mapping.findForward(RhnHelper.DEFAULT_FORWARD);
            }
    
        public String getListName()  { 
                    return LIST_NAME;
            }
            
        public String getDataSetName() {
                    return DATA_SET;
            }
    
       public String getParentUrl(RequestContext context) {
    	Server server = context.lookupAndBindServer();
    	return context.getRequest().getRequestURI() + "?sid=" + server.getId());
        
       }    
        public  String getDecl(RequestContext context) {
            return getClass().getName() + context.getRequest().getParameter("sid");
        }
    
        public List getResult(RequestContext context, ActionMapping mapping) {
                    Server server  = context.lookupAndBindServer();
                    User user = context.getLoggedInUser();
                    return cm.listManagedPathsFor(server, user, ConfigChannelType.normal());      
        }
    
        public ActionForward  handleDispatch(ActionMapping mapping, 
                                    ActionForm formIn, HttpServletRequest request,
                                    HttpServletResponse response) {
            ....
             int size = copySelectedToChannel(server.getSandboxOverride(),
                        request,
                        user);
            ....
        }
    
          private int copySelectedToChannel(ConfigChannel channel, HttpServletRequest request,
                                            User user) {
    		//.... ddo whatever
        }  
    
    }
    
    