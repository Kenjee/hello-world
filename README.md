# generate java code from openapi 3.0 swagger file
* using https://github.com/openapitools/openapi-generator as codegenerator
  * groupId: org.openapitools
  * artifactId: openapi-generator-maven-plugin
  * version: 4.3.1 

# Problem

according to definition within [swagger.yml](src/main/resources/billing_partner_service_api_v3.yaml)

```
...
schemas:
    # START RESPONSE OBJECTS
    ResponseMessage:
      type: string
      description: Additional information in case of not success
    Response:
      type: object
      description: |
        The default (each) Response has a message field for any kind of text
      properties:
        message:
          $ref: "#/components/schemas/ResponseMessage"
        resultObject:
          type: object
          description: in case of successful request handling there should be a resultObject with all data needed
          example: {}
    SubscriptionResponse:
      type: object
      description: >
        This response contains the information related to single subscriptions of a unique user within the billing partner service.<br>
      allOf:
        - $ref: '#/components/schemas/Response'
        - type: object
          properties:
            resultObject:
              type: object
              properties:
                $ref: '#/components/schemas/Subscription'
          example:
            message: "read user subscriptions was done"
            resultObject:
              productId: 23456789
              subscriptionId: "some_sub_id"
              state: "SUSPENDED"
              stateDetails: "Blocked by Carrier"
...
```

While generating java code i should get something like
`public class SubscriptionResponse extends Response {`

example using https://swagger.io/
```
...
package io.swagger.client.model;

import java.util.Objects;
import java.util.Arrays;
import com.google.gson.TypeAdapter;
import com.google.gson.annotations.JsonAdapter;
import com.google.gson.annotations.SerializedName;
import com.google.gson.stream.JsonReader;
import com.google.gson.stream.JsonWriter;
import io.swagger.client.model.Response;
import io.swagger.client.model.SubscriptionResponseResultObject;
import io.swagger.v3.oas.annotations.media.Schema;
import java.io.IOException;
/**
 * This response contains the information related to single subscriptions of a unique user within the billing partner service.&lt;br&gt; 
 */
@Schema(description = "This response contains the information related to single subscriptions of a unique user within the billing partner service.<br> ")
@javax.annotation.Generated(value = "io.swagger.codegen.v3.generators.java.JavaClientCodegen", date = "2020-08-19T13:47:35.087Z[GMT]")
public class SubscriptionResponse extends Response {
  @SerializedName("resultObject")
  private SubscriptionResponseResultObject subscriptionResponseResultObject = null;

  public SubscriptionResponse subscriptionResponseResultObject(SubscriptionResponseResultObject subscriptionResponseResultObject) {
    this.subscriptionResponseResultObject = subscriptionResponseResultObject;
    return this;
  }
...
```
example openapi-generator 
```
package com.generate.api.api.model;

import java.util.Objects;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.annotation.JsonCreator;
import com.generate.api.api.model.Response;
import com.generate.api.api.model.SubscriptionResponseOneOf;
import com.generate.api.api.model.SubscriptionResponseOneOfResultObject;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import org.openapitools.jackson.nullable.JsonNullable;
import javax.validation.Valid;
import javax.validation.constraints.*;

/**
 * This response contains the information related to single subscriptions of a unique user within the billing partner service.&lt;br&gt; 
 */
@ApiModel(description = "This response contains the information related to single subscriptions of a unique user within the billing partner service.<br> ")
@javax.annotation.Generated(value = "org.openapitools.codegen.languages.SpringCodegen", date = "2020-08-19T16:49:13.433626+02:00[Europe/Berlin]")

public class SubscriptionResponse   {
  @JsonProperty("message")
  private String message;

  @JsonProperty("resultObject")
  private SubscriptionResponseOneOfResultObject resultObject;
...
```

you can test it by clone and `mvn clean install`
https://github.com/OpenAPITools/openapi-generator/issues/6815

