# API Resource Guidelines

Created by: Ray Avila
Created on: May 17, 2022 5:43 PM
Document Version: V1
Last update: May 18, 2022 11:00 AM
Last update by: Ray Avila
Repository: atrato-api

## General description

The objective is to establish a development guide for the API that will serve as a guideline to implement common guidelines for its design and thus lead to better functionality, order and flexibility.

## Structure and implementation

### REST API Design Guidelines and Conventions

1. **Use JSON as the format for sending and receiving data.**

    To ensure that the client interprets JSON data correctly, you must set the ***Content-Type = application/json*** in the response header when making the request.

2. **Implementation and naming of routes**

    **Use nouns instead of verbs in end-points.**
    When implementing a route you should not use verbs in the end-point routes.
    End-points should use nouns, indicating what each end-point does. This is because HTTP methods such as GET, POST, PUT and DELETE are already in verb format to perform basic CRUD (Create, Read, Update, Delete) operations.

    Example:
    PUT [https://atrato/...../invoices/234](https://atrato/...../invoices/234) instead of [https://atrato/...../invoices/234/edit](https://atrato/...../invoices/234/edit)

    *For the resource invoice with the identifier 234, regardless of whether we are going to edit it, delete it, consult it or read only one of its concepts: /invoices/234 just by changing the HTTP verb.*

    more examples:
    GET [https://atrato/...../invoices/234](https://atrato/...../invoices/234)
    POST [https://atrato/...../invoices/234](https://atrato/...../invoices/234)
    DELETE [https://atrato/...../invoices/234](https://atrato/...../invoices/234)

    **Note**: For dynamic queries and paging see the section on "Filtering and other operations.”

    **Nouns must be plural.**

    Example:
    [https://atrato/...../customers](https://atrato/...../customers) instead of [https://atrato/...../customer](https://atrato/...../customer)

    Unless they describe a singleton. Singleton resources may represent a resource for direct interaction of an existing resource instance, e.g., a user's single profile.

    Example:
    GET [https://atrato/...../me](https://atrato/...../me) instead of [https://atrato/...../users/12345](https://atrato/...../users/12345)
    avoiding the need for the consumer to know his own user ID.

    **Do not make nouns unnecessarily long and complicated.**

    Example:
    [https://atrato/...../users](https://atrato/...../users) instead of [https://atrato/...../system-users](https://atrato/...../system-users)

    If the noun is necessarily composed of several words they must use a hyphen as a separator (/legal-documents). There is a very basic reason for this: when URLs are hyperlinks, they are often underlined, which would make an underscore separator invisible in any documentation.

    **Use common terminology**

    Example:
    [https://atrato/...../customers](https://atrato/...../customers) instead of [https://atrato/...../special-client-users](https://atrato/...../special-client-users)

    **Use lowercase**

    To avoid ambiguity around the case-sensitivity of URLs.

    **Use resource identifiers in the route.**

    For individual resources, include resource identifiers in the path, not in the url query.

    Example:
    [https://atrato/...../contacts/22](https://atrato/...../contacts/22) instead of [https://atrato/...../contacts?id=22](https://atrato/...../contacts?id=22)

    **Moderate use of Controller or Actions**
    Used to represent actions that do not necessarily in all cases modify the state of a resource on the server.

    To model the url of the route as a resource as a noun is the right way to represent the API functionalities and the actions are identified by the HTTP method (GET, PUT, POST, DELETE) but sometimes it is complicated to model a functionality in this way some examples of its possible use:

    - If we want to model the execution of a batch process.
    - If we want to perform some server-side validation, for example to validate if a customer's email is valid or not.
    - If we want to solve a task that is difficult to model by means of a business entity, commonly applied to solve technical tasks such as migrating records from one database to another.

    In this case the functionality could be modeled as a 'Controller' pattern and for this case it is recommended to always use the POST method and when naming the resource it will always be done as an action or verb.

    Examples:
    Simulate the granting of a bank loan. We will do it by defining a resource that represents loans which we will call 'loans' and from which hangs an action called (as a verb) 'simulate'.

    **POST** [https://atrato/...../loans/simulate](https://atrato/...../loans/simulate)

    Another example: running a batch process that eliminates duplicate clients.
    **POST** [https://atrato/...../customers/remove-duplicates](https://atrato/...../customers/remove-duplicates)

    As a final note, this pattern should be used with reservation and only in case the desired functionality cannot be modeled with any of the other patterns.

    **Routes should maintain a logical hierarchy**
    Routes should indicate a hierarchy of sub-resources but complex structures requiring more than two levels of nesting should be avoided.

    Example:
    [https://atrato/...../clientes/007/invoices/234](https://atrato/...../clientes/007/invoices/234)

    For the resource invoice with the identifier 234 of customer 007 a clear hierarchical relationship can be seen, since a customer can have many invoices

    **Filtering and other operations.**
    The query part of the URL is for dynamic search and filtering.

    Example:
    [https://atrato/...../invoices?first_name=Anna&limit=20](https://atrato/...../invoices?first_name=Anna&limit=20)

    To filter, sort, paginate or search for information in a resource, we must make a query on the URI, using HTTP parameters instead of including them in the query.

    **Using versions**
    It is a good practice that our API has a version, and keep it always visible in the requests, only in cases where the resources are consumed by external users, for the case of internal use it is not a mandatory practice but it is a good practice.

3. **HTTP States**

    Because APIs rely on HTTP standards, the status of each request is used to communicate the outcome of the request, such as success or failure.
    Each status code provides a protocol-readable response in addition to a human-readable message.

    I have selected a short list of those that should definitely be used:

    200 OK :Response to a successful GET, PUT or DELETE.
    Can also be used for a POST that does not result in a creation.
    201 Created : Response to an action that results in a creation.
    204 No content : Response to a successful request that will not return a body (such as a DELETE request).
    400 Incorrect request : The request has an incorrect format, e.g. if the body is not correct.
    401 Unauthorized : When no authentication details are provided or no valid authentication data is provided.
    403 Forbidden: When authentication was successful but the authenticated user does not have access to the resource.
    404 Not found: When a non-existent resource is requested.
    405 Method not allowed: When an HTTP method is requested that is not allowed for the authenticated user.
    415 Unsupported media type: If an incorrect content type or format was provided as part of the request.
    422 Unprocessable entity: Used for validation errors.
    429 Too many requests: When a request is rejected due to a rate limitation.
    500 Internal error: Report an exception during processing.

    There are more, of course (see [https://developer.mozilla.org/es/docs/Web/HTTP/Status](https://developer.mozilla.org/es/docs/Web/HTTP/Status)), so it makes sense to use the correct status code for the appropriate situation while designing the API.

4. **Receipt and response forms**

    REST APIs must accept JSON for the request payload and also send JSON responses.

    **JSON naming convention**

    Property names must be in uppercase and lowercase, use the camelCase convention.
    The first character must be a letter, an underscore (_) or a dollar sign ($).

    Example:
    { "thisPropertyIsAnIdentifier": "identifier value" }

    **Response structure**

    There are 2 types of responses in each API request, which are Success Response and Error Response.

    The main response structure must have 4 main data, which are:
    **success**: always returns true or false if the response is correct, it will return true, otherwise it will get false.
    **message**: to return information about the process, either error or transaction information.
    **error_code**: (optional) to indicate if there was an error in the request. You can use the id of the error log or you can send the status-code of the request, it is recommended to handle error codes according to the section or process where the exception took place for a quick support response).
    **data**: Return information corresponding to the transaction.


## Additional Material

HTTP response status codes

[https://developer.mozilla.org/es/docs/Web/HTTP/Status](https://developer.mozilla.org/es/docs/Web/HTTP/Status)),
