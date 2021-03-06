<!---
Copyright 2016 The AMP HTML Authors. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS-IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# CORS Requests in AMP

Many components and extensions in AMP take advantage of remote endpoints using CORS requests. For more detail
on CORS see [CORS Spec](https://www.w3.org/TR/cors/). This spec explains some of the key aspects of using CORS
in AMP.

## CORS and Cookies

Most of components that use CORS requests in AMP also either automatically set credentials mode or allow the
author to optionally enable it. The credentials mode sends origin cookies in the CORS request and it will also
allow the origin to set cookies in response (subject to 3rd-party cookie restrictions). The origin, however,
must enable credentials mode on the server side using `Access-Control-Allow-Credentials: true` response header.

### 3rd-Party Cookie Restrictions

The normal browser 3rd-party cookie restrictions apply to the credentialed CORS requests. These restrictions depend
on the browser and the platform, but for some browsers, the origin can only set cookies if the user has previously
visited the origin in a 1st-party (top) window. Or, in other words, only after the user has directly visited the
origin website itself. Given this, a service accessed via CORS cannot assume that it will be able to set cookies
by default.

## CORS Security in AMP

This security protocol consists of two components: CORS origin and source origin restrictions.

CORS endpoints receive requesting origin via "Origin" HTTP header. This header has to be restricted to only allow the following origins:
 - *.ampproject.org
 - The Publisher’s own origins

Source origin restrictions has to be implemented by requiring "__amp_source_origin" URL parameter to be within a set of the Publisher's own origins. The "__amp_source_origin" parameter is passed from AMP Runtime in all fetch requests and contains the source origin, e.g. "https://publisher1.com".

The resulting HTTP response has to also contain the following headers:
 - `Access-Control-Allow-Origin: <origin>`. Here "origin" refers to the requesting origin that was allowed via "Origin" request header above. Ex: "https://cdn.ampproject.org". This is a CORS spec requirement.
 - `AMP-Access-Control-Allow-Source-Origin: <source-origin>`. Here "source-origin" indicates the source origin that is allowed to read the authorization response as was verified via "__amp_source_origin" URL parameter. Ex: "https://publisher1.com".
 - `Access-Control-Expose-Headers: AMP-Access-Control-Allow-Source-Origin`. This header simply allows CORS response to contain the "AMP-Access-Control-Allow-Source-Origin" header.
