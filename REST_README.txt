
RESTful Web Services
--------------------
Banu Prakash. C
banuprakashc@yahoo.co.in
https://github.com/BanuPrakash/VISA_REST

Softwares Required:

Java 8
Eclipse for JEE Mars+
Tomcat 8.0.x [optional]
--------------------------------

	Restful services
	----------------

	Resource : information/content residing on a server
	It could be lik users data, customers data or tangible things like printer

	RESTful services is a way to provide a snapshot of the resources
	to the users in variaous representations

	Representions can be : JSON, XML, ATOM, RSS Feed, HTML, etc

	REST Design:

	Level 0: POX -> Plain old xml
		http://visa.in

		<create_customer>
			payload
		</create_customer>

		<delete_employee>
			<id>10</id>
		</delete_employee>

	Level 1: Nouns
			URIs
			http://visa.in/customers
			http://visa.in/employees

	Level 2: Verbs
			CRUD operations [ GET, POST, PUT, PATCH, DELETE]

		Reading information: READ

		GET
			URI: http://visa.in/customers
			Payload: blank
			action: Fetch all customers
		GET
			URI: http://visa.in/customers/24
			Payload: blank
			action: Fetch customer whose id is 24 [ extra path param ]
		GET
			URI: http://visa.in/customers?city=Bangaluru
			Payload: blank
			action: Fetch all customers from bangaluru
		GET
			URI: http://visa.in/customers?fields=firstName,email,address(city,state)
			Payload: blank
			action: Fetch partial info all customers 
		GET
			URI: http://visa.in/customers?start=1&end=10
			Payload: blank
			action: Fetch all customers from 1 to 10

		------------

		CREATE
		POST
			URI: http://visa.in/customers 
			Payload: cotains the new customer data
			action: add a new customer to customers resource
		PUT
			URI: http://visa.in/customers/53 
			Payload: contains the new customer data
			action: add a new customer to customers resource


		for update
		PUT
			URI: http://visa.in/customers/53 
			Payload: contains the new customer data
			action: update a new customer to customers resource

		PATCH
			URI: http://visa.in/customers/53 
			Payload: contains partial info of customer data
			action: update a new customer to customers resource

		for DELETE
			URI: http://visa.in/customers/53 
			Payload:no payload
			action: delete customer 53 form customers resource
	---------------------------------------------------------------------
	
	Content Negotiation:
	use Accept header to identify the representation to be sent to the client
	use content-type to identify the representation sent by client

	You can also use:
	URI and query param to identity
	http://visa.in/customers.json
	http://visa.in/customers?mediaType=json

	--------------------------------------------------

	CamelCase, Snake_Case and spinal-case

	URI:--> Prefer Snake_Case

	Parameter: --> camelCase or Snake_case

	http://visa.in/customers?fields=firstName,email,address(city,state)

	---------------------------------------

	versioninig [ URI, QUERYSTRING or HEADER]

	http://visa.in/v1/customers 

	http://visa.in/v1/customers?v1.exp

	----------------------------------------------

	SECUIRITY:
		Prefer OAuth2 with JWT [ CORS ]
	------------------------------------------------
	
	HATEOAS [ Level 3 RESTful services ]
		Hypermedia As the Engine Of Application State

	--------
	Documentation
		Prefer: Swagger API	
	---------------

	Prefer different domains for api, oauth and documentation

	---------------------------------------------------

	JAX-RS is an specification for building RESTful services
	JAX-WS is an specification for SOAP based web services

	-------------------------------------------------------

	Jersey is a framework implementing JAX-RS

-----------------------

	Jersey Restful services Framework is based on JAX-RS Specification

	JAX-RS implementations:
		1) Jersey
		2) RESTEasy [ Jboss ]
		3) Apache CXF

	-------
	@Path is an annotation to specify the endpoint
	http://localhost:8080/products

	@Path("products")
	public class ProductService {

	}

	@GET, @POST, @PUT, @PATH, @DELETE [ CRUD operations ]


	@Path("products")
	public class ProductService {
		@GET
		someMethod() {

		}
	}

	@Consumes and @Produces

	@Consumes uses content-type header to identify the type of data
	sent by the client.

	The request data which is in the form of String is convereted to
	JSON or XML using MessageBodyReader

	@Consumes({"application/json","application/xml"})
	public someMethod(Product p) {

	}

	@Produces uses "accept" header to convert JAva Object
	to different representations, it uses MessageBodyWriter to do this

	---------------------

	GrizzlyHttpServer is a standalone servlet container
----------------------------------------------------------------------
 public static class ObjectMapperProvider implements ContextResolver<ObjectMapper> {

        @Override
        public ObjectMapper getContext(final Class<?> type) {
            return new ObjectMapper().enable(SerializationFeature.INDENT_OUTPUT);
        }
    }

   register(ObjectMapperProvider.class);
----------

	@InjectLinks({
			@InjectLink(resource = ProductService.class, style = Style.ABSOLUTE, 
					rel = "self", 
					bindings = @Binding(name = "id", value = "${instance.id}") , 
					method = "getProduct"),
			@InjectLink(resource = ProductService.class, 
			style = Style.ABSOLUTE, rel = "purchase", 
			bindings = @Binding(name = "id", value = "${instance.id}") , 
			method = "purchaseProduct", 
			condition = "${instance.qty > 0}") })
	@XmlJavaTypeAdapter(Link.JaxbAdapter.class)
	@XmlElement(name = "link")
	private List<Link> links;
----------------------------

@GET
public void asyncGetWithTimeout(@Suspended final AsyncResponse asyncResponse) {
    asyncResponse.setTimeoutHandler(new TimeoutHandler() {
        @Override
        public void handleTimeout(AsyncResponse asyncResponse) {
            asyncResponse.resume(Response.status(Response.Status.SERVICE_UNAVAILABLE)
                    .entity("Operation time out.").build());
        }
    });
    asyncResponse.setTimeout(20, TimeUnit.SECONDS);
    new Thread(new Runnable() {
        @Override
        public void run() {
            String result = veryExpensiveOperation();
            asyncResponse.resume(result);
        }
 
        private String veryExpensiveOperation() {
            // ... very expensive operation that typically finishes within 20 seconds
        }
    }).start();
}


-------
	nUnit Testing frameworks 

	JUnit
	TestNg
	NUnit

	JerseyUnit is an decorator to use these testing framework
--------------------------------

Day 2
-----
Recap

JAX-RS
	@Path and @Provider are auto-discoverable

	@Path contains REST endpoints
	@Provider are helpers for the services

@GET, @POST, @PUT, @DELETE and @PATH
@Consumes and @Produces

JerseyTest

--------------------------------------------

Server: Standalone

Servlet 2.5 API specification

web.xml
<servlet>
	<servlet-name>ServletContainer</servlet-name>
	<servlet-class>org.jersey.....ServletContainer</servlet-class>
	<init-param>
			<param-name>javax.ws.rs.Application</param-name>
			<param-value>com.visa.rest.MyConfig</param-value>
	</init-param>
</servlet>

<servlet-mapping>
	<servlet-name>ServletContainer</servlet-name>
	<url-pattern>/rest/*</url-pattern>
</servlet-mapping>


class MyConfig extends ResourceConfig {
	
	public MyConfig() {
		packages("com.visa.rest");
	}
}

--------------------

Jersey with Servlet 3.0 API [ No Deployment Descriptor ]

@Application("/rest")
class MyConfig extends ResourceConfig {
	
	public MyConfig() {
		packages("com.visa.rest");
	}
}

------------------------------------

CDI

Context and Dependency Injection:

public class ProductService {
	
	@Context
	ServletConfig config;

	@Context
	ServletContext ctx;

	public Response addProduct(@Context HttpServletRequest req, Product p) {
		// code
	}
}

--------------------

JSONP

JSON with padding --> one of the way to support Cross Origin requests

CORS

----------------------------------------

Content Negotiation

application/vnd.ms-excel

application/vnd.msword

application/vnd.visa.csv

------------
Swagger
 url = "http://localhost:8080/rest/api-docs";


		register(ApiListingResource.class);
		register(ApiDeclarationProvider.class);
		register(ApiListingResourceJSON.class);
		register(ResourceListingProvider.class);

	}

	@PostConstruct
	/**
	 * Initializes Swagger Configuration
	 */
	public void initializeSwaggerConfiguration() {

		final ReflectiveJaxrsScanner scanner = new ReflectiveJaxrsScanner();
		scanner.setResourcePackage("com.visa.rest");
		ScannerFactory.setScanner(scanner);
		ClassReaders.setReader(new DefaultJaxrsApiReader());
		final SwaggerConfig config = ConfigFactory.config();
		config.setApiVersion("1.0");
		config.setBasePath("http://localhost:8080/rest/");
	}



-------------------

HATEOAS
	Hypermedia As The Engine Of Application State

	Albums --> many Album --> Artist

	public class Album {
		int id;
		String name;
		int qty;
		Artist artist;


	}

	List<Album> getAlbums() {

	}

	[{
		"id":1,
		"name": "A",
		"artist": "MJ",
		"qty" : 12
	},
	{
		"id":2,
		"name": "B",
		"artist": "JB",
		"qty" : 9009
	}
	]

	[{
		"id":1,
		"name": "A",
		"artist": "MJ",
		"qty" : 12,
		"link": "<a href=http://localhost:9090/1 rel="self">",
		"link" : "<a href=http://localhost:9090/1 rel="purchase">",
		"link" : "<a href=http://localhost:9090/MJ rel="artist">",
	},
	{
		"id":2,
		"name": "B",
		"artist": "JB",
		"qty" : 9009
	}
	]


	@InjectLinks({
			@InjectLink(style = Style.ABSOLUTE, 
				resource=ProductService.class,
				method="getProduct",
				rel="self",
				bindings = @Binding(name="id", value="${instance.id}")
			),
			@InjectLink(style = Style.ABSOLUTE, 
			resource=ProductService.class,
			method="updateProduct",
			rel="purchase",
			bindings = @Binding(name="id", value="${instance.id}")
		)})
	@XmlJavaTypeAdapter(Link.JaxbAdapter.class)
	@XmlElement(name = "link")

----------------

Documentation
-------------
	Swagger API
	https://github.com/swagger-api/swagger-ui

----------------

	@Api

	@ApiOperations

	@Apimodel
--------------------------


 Spring as a Framework for building RESTful services
 ---------------------------------------------------

 https://github.com/BanuPrakash/SPRING_VISA

 @Component
 @Repository
 @Service

 @Autowired

 