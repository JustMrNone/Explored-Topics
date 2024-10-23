# **URL Breakdown**

```plaintext
https://www.somewebsite.com/abc/123?param1=123&something=abc#somewhereindocument
```

| **Component** | **Explanation** |
| --- | --- |
| `https://` | **Protocol**: Specifies the protocol used to communicate with the server. In this case, HTTPS (secure HTTP). |
| `www.` | **Subdomain**: The part before the domain name, typically used for organizing different services under the domain (e.g., `mail.somewebsite.com`). |
| `somewebsite` | **Second-Level Domain (SLD)**: The primary domain name that users recognize, usually representing a brand or organization. |
| `.com` | **Top-Level Domain (TLD)**: The highest level in the domain name hierarchy, like `.com`, `.org`, `.net`, etc. |
| `/abc/123` | **Path**: Specifies the location of a resource (a page, a file, etc.) on the server. Here, it's navigating to the `/abc/123` path. |
| `?param1=123&something=abc` | **Query Parameters**: Optional key-value pairs used to pass data to the server, often for filtering or searching (`param1=123` and `something=abc`). |
| `#somewhereindocument` | **Anchor (Fragment)**: Used to jump to a specific section within the document. This part is handled client-side. |

* * * * *

## **Glossary of Terms**

- **URL (Uniform Resource Locator)**: A specific type of URI that provides the location of a resource and the protocol to retrieve it. It is the web address we commonly use.

- **URI (Uniform Resource Identifier)**: A more general term that refers to a string that identifies a resource, either by location, name, or both. Every URL is a URI, but not every URI is a URL.

- **URN (Uniform Resource Name)**: A specific type of URI that names a resource but doesn't specify its location or how to access it. Example: `urn:isbn:0451450523` refers to a book by its ISBN but doesn't tell you where to find it.

- **Protocol**: Defines how data is transferred between the client and server. Common ones include `http`, `https`, `ftp`, etc.

- **Domain**: The human-readable part of the web address, representing the server hosting the resource.

- **Top-Level Domain (TLD)**: The highest part of the domain name system, like `.com`, `.edu`, `.org`.

- **Second-Level Domain (SLD)**: Directly below the TLD in hierarchy, usually the core name of the domain, such as `somewebsite` in `somewebsite.com`.

- **Subdomain**: A prefix to the domain name that separates different services or sections. For example, `mail.somewebsite.com` or `www.somewebsite.com`.

- **Path**: The part of the URL after the domain that leads to a specific resource on the server. This might point to a file or a dynamic endpoint on a website.

- **Query Parameters**: Key-value pairs appended to the URL after a `?`. They are typically used to pass data, often for searches, filters, or dynamic content requests.

- **Anchor (Fragment)**: A section in the URL that leads to a specific part of the webpage. This is handled on the client side to jump to an element with a matching ID.

- **Host**: Refers to the domain name or IP address of the server where the resource resides. In the example, `www.somewebsite.com` is the host.

* * * * *

### **URL vs. URI vs. URN**

- **URI (Uniform Resource Identifier)**: An identifier for a resource, and it can be a URL, URN, or both. It's the most general concept.

- Example: `http://www.example.com/page` (a URL is also a URI).
- **URL (Uniform Resource Locator)**: A specific type of URI that not only identifies the resource but also provides a means of retrieving it by specifying its location.

- Example: `https://www.somewebsite.com/page` (specifies where the page is and how to access it).
- **URN (Uniform Resource Name)**: A URI that names the resource but doesn't tell you where or how to get it.

- Example: `urn:isbn:0451450523` (names the book by its ISBN, but doesn't indicate where to find it).

* * * * *

### **Example of a URN and URL**

- **URN**: `urn:ietf:rfc:3986` --- This is the name of an RFC (Request for Comments) document, without specifying where to retrieve it.

- **URL**: `https://tools.ietf.org/html/rfc3986` --- This specifies both the name and the location (on the IETF website) of the RFC document.

### in short

- URL is nothing more than the address of a given unique resource on the Web.
- A domain name is an identification string that defines a realm of administrative#
autonomy, authority or control within the Internet.

https://www.somewebsite.com/abc/123?param1=123&something=abc#somewhereindocument
|------|
 protocol
       |---|
        subdomain
           |-----------|
            second level domain
                       |---|
                        top level domain
                           |------|
                            path
                                  |------------------------|
                                       query params
                                                           |------------------|
                                                               anchor
                        
                        
- each '.' adds another level domain
- host is `www.somewebsite.com`
