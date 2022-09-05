# Mock Server config and basic usage

1. Comes Packaged with `spring-test-starter`
2. Some examples then

```java
class SomeIntegrationTest {

	private SomeService serviceToTest;
	private MockRestServiceServer mockServer;

	private RequestMatcher requestMatcher;
	private RequestMatcher requestMatcherForTemplate;

	@BeforeEach
	void init() {
		// needed when anything except the exact url
        // like query params are added on to the request
		requestMatcher = request -> assertThat(request.getURI().getRawPath())
				                            .startsWith("/path/after/base/url");
		requestMatcherForTemplate = request -> assertThat(request.getURI().getRawPath())
				                                       .matches("/path/after/base/url/{regex-for-path-variable}");

		final RestTemplate restTemplate = new RestTemplate();
		// Important that the restTemplate is bound to the mock server
        // mockServer respond to requests in order by default
		mockServer = MockRestServiceServer.bindTo(restTemplate)
				             // add this if the mock server can ignore ordering
				             // not really ideal but hey if need be
				             //.ignoreExpectOrder()
				             .build();
		serviceToTest = new SomeServiceImpl(restTemplate);
	}

	@Test
	void createConfluenceDirectory() throws URISyntaxException {
		var json = "{\"json\":\"body\"}";

		var anotherJsonResponse = "{\"new_json\":\"new_body\"}";

		// this will only reply once and to the first reques
		// 1 https://<SERVER_URL>/path/after/base/url?someHeader=someValue
		mockServer.expect(once(), requestMatcher)
				.andExpect(queryParam("someHeader", "someValue"))
				.andExpect(method(HttpMethod.GET))
				.andRespond(withSuccess().body(json));

		// this will only reply 4 times and that to the request after the prev one is done
		// 4 requests(4,5,6,7)//  https://<SERVER_URL>/path/after/base/url/{some-path-variable}?someHeader=someValue
		mockServer.expect(times(4), requestMatcherForTemplate)
				.andExpect(queryParam("someHeader", "someValue"))
				.andExpect(method(HttpMethod.GET))
				.andRespond(withSuccess().body(anotherJsonResponse));

		// 8 - will also respond to POST and other requests
		mockServer.expect(times(2), requestTo(new URI("http://some-exact-url")))
				.andExpect(method(HttpMethod.POST))
                // can also send no response
				.andRespond(withSuccess());

		val request = new ServiceRequestData("dummy-user", "call-with-dummy-data");
		// we expect it to call 7 APIs that we have setup before
		serviceToTest.callToExternalAPIs(request);

		// verify that all the calls were actually made to the server
		mockServer.verify();
	}
}

```