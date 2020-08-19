# generate java code from openapi 3.0 swagger file
* using https://github.com/openapitools/openapi-generator as codegenerator
  * groupId: org.openapitools
  * artifactId: openapi-generator-maven-plugin
  * version: 4.3.1 vs 4.3.0 

# Problem are code generation changes

* i tested my [swagger-definition.yml](src/main/resources/billing_partner_service_api_v3.yaml) against swagger.io

```
...
schemas:
...
    Response:
      type: object
      properties:
        message:
          $ref: "#/components/schemas/ResponseMessage"
        resultObject:
          type: object
          description: in case of successful request handling there should be a resultObject with all data needed
    SubscriptionResponse:
      type: object
      description: >
        This response contains the information related to single subscriptions of a unique user within the billing partner service.<br>
      allOf:
        - $ref: '#/components/schemas/Response'
...
```
and while generating java code i got `public class SubscriptionResponse extends Response {` with example using https://swagger.io/

```java
...
package io.swagger.client.model;
...
import io.swagger.client.model.Response;
import io.swagger.client.model.SubscriptionResponseResultObject;
...
/**
 * This response contains the information related to single subscriptions of a unique user within the billing partner service.&lt;br&gt; 
 */
@Schema(description = "This response contains the information related to single subscriptions of a unique user within the billing partner service.<br> ")
@javax.annotation.Generated(value = "io.swagger.codegen.v3.generators.java.JavaClientCodegen", date = "2020-08-19T13:47:35.087Z[GMT]")
public class SubscriptionResponse extends Response {
  @SerializedName("resultObject")
  private SubscriptionResponseResultObject subscriptionResponseResultObject = null;
...
```

while using openapi-generator 4.3.0 i got more less the same
```java
package com.generate.api.api.model;
...
import com.generate.api.api.model.Response;
import com.generate.api.api.model.SubscriptionResponseAllOf;
import com.generate.api.api.model.SubscriptionResponseAllOfResultObject;
...
/**
 * This response contains the information related to single subscriptions of a unique user within the billing partner service.&lt;br&gt; 
 */
@ApiModel(description = "This response contains the information related to single subscriptions of a unique user within the billing partner service.<br> ")
@javax.annotation.Generated(value = "org.openapitools.codegen.languages.SpringCodegen", date = "2020-08-19T18:05:39.954+02:00[Europe/Berlin]")
public class SubscriptionResponse extends Response  {
  @JsonProperty("resultObject")
  private SubscriptionResponseAllOfResultObject resultObject;
...
```

but with 4.3.1 it changes to 
```java
package com.generate.api.client.model;
...
import com.generate.api.client.model.Response;
import com.generate.api.client.model.SubscriptionResponseAllOf;
import com.generate.api.client.model.SubscriptionResponseAllOfResultObject;
...
/**
 * This response contains the information related to single subscriptions of a unique user within the billing partner service.&lt;br&gt; 
 */
@ApiModel(description = "This response contains the information related to single subscriptions of a unique user within the billing partner service.<br> ")
@JsonPropertyOrder({
  SubscriptionResponse.JSON_PROPERTY_MESSAGE,
  SubscriptionResponse.JSON_PROPERTY_RESULT_OBJECT
})
@javax.annotation.Generated(value = "org.openapitools.codegen.languages.JavaClientCodegen", date = "2020-08-19T18:17:34.758665+02:00[Europe/Berlin]")
public class SubscriptionResponse {
  public static final String JSON_PROPERTY_MESSAGE = "message";
  private String message;

  public static final String JSON_PROPERTY_RESULT_OBJECT = "resultObject";
  private SubscriptionResponseAllOfResultObject resultObject;
...
```

as you can see with 4.3.1 SubscriptionResponse does not extend anymore from Response!
and yes there are hints for using "discriminator" according to https://swagger.io/docs/specification/data-models/inheritance-and-polymorphism/
but 'allOf' should be enough

* you can test it by clone and `mvn clean install`
* https://github.com/OpenAPITools/openapi-generator/issues/6815
* https://github.com/OpenAPITools/openapi-generator/issues/2058
