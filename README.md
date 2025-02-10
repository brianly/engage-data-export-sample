# Viva Engage Data Export sample

## Overview
This project demonstrates how to call the Viva Engage (Yammer) data export API with an Entra token to return a ZIP file with multiple content types. It is written with [MSAL](https://learn.microsoft.com/en-us/entra/identity-platform/msal-overview) which means it is relatively easy to translate to languages other than Python.

Python 3.13.1 on Windows was used for the creation of this script, but it'll run on macOS and Linux just as well. [Requests](https://pypi.org/project/requests/) and [MSAL](https://pypi.org/project/msal/) are required for making API requests. Using [uv](https://docs.astral.sh/uv/) is recommended for easy setup and execution, but ```pip``` should work just fine.


# Miscellanea 
## Sample response headers

The data export API is not a normal REST API. It returns a ZIP file which is streamed to the user. The response includes a file name in the *content-disposition* header which is used for the final ZIP file stored by this sample code.

```
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/zip'
'content-disposition': 'attachment; filename=export-1738877601293.zip'
'strict-transport-security': 'max-age=1234513412313; includeSubDomain'
'x-robots-tag': 'none'
'x-lodbrok-cell': 'prod_east_1-c3'
'report-to': '{"max_age":3600,"endpoints":[{"url":"https://mmay.nelreports.net/api/report?cat=yammer-prod_east_1"}]}'
'nel': '{"report_to":"default","max_age":3600,"success_fraction": 0.001}'
'X-Cache': 'CONFIG_NOCACHE'
'X-MSEdge-Ref': 'Ref A: 573BED70EE7E4017AC7B34B6FA0ECFF5 Ref B: EWR311000108047 Ref C: 2025-02-06T21:33:20Z'
'Date': 'Thu, 06 Feb 2025 21:33:21 GMT'
```