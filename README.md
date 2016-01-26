webjars-locator
===============

This project provides a means to locate assets within WebJars.

[![Build Status](https://travis-ci.org/webjars/webjars-locator.svg?branch=master)](https://travis-ci.org/webjars/webjars-locator)

Obtain the full path of an asset
--------------------------------

	WebJarAssetLocator locator = new WebJarAssetLocator();
	String fullPathToRequirejs = locator.getFullPath("require.js");
	
Obtain all of the assets within a base folder
---------------------------------------------
	
	WebJarAssetLocator locator = new WebJarAssetLocator();
	Set<String> fullPathsOfAssets = locator.listAssets("/multiple/1.0.0");

Advanced usage
--------------

The locator can also be configured with the class loaders that it should use for looking up resources and filter the types of resources that should be included for searching. Please visit the source code for more information.

OSGi
--------------

Example servlet registered by Blueprint with http whiteboard

    @OsgiServiceProvider( classes = Servlet.class )
    @Properties( {
                         @Property( name = "osgi.http.whiteboard.servlet.pattern", value = "/webjarsjs" ),
                         @Property( name = "osgi.http.whiteboard.servlet.name", value = "WebJars RequireJS Handler" )
                 } )
    @Named( "WebJarsRequireJSServlet" )
    public class WebJarsRequireJSServlet extends HttpServlet {
        @Inject
        private BundleContext bundleContext;

        @Override
        protected void doGet ( HttpServletRequest req, HttpServletResponse resp ) throws ServletException, IOException {
            resp.setContentType ( "application/javascript" );
            try ( ServletOutputStream out = resp.getOutputStream () ) {
                out.print (
                        RequireJS.generateSetupJavaScript ( Collections.singletonList ( "/webjars/" ) ) );
            }
        }

        @PostConstruct
        public void init () {
            RequireJS.setResourceLocator ( new OSGIResourceLocatorImpl ( bundleContext ) );
        }
    }
