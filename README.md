# Viva Engage Data Export sample

## Overview
This project demonstrates how to call the Viva Engage (Yammer) data export API with an Entra token to return a ZIP file with multiple content types. It is written with [MSAL](https://learn.microsoft.com/en-us/entra/identity-platform/msal-overview) which means it is relatively easy to translate to languages other than Python.

Python 3.13.1 on Windows was used for the creation of this script, but it'll run on macOS and Linux just as well. [Requests](https://pypi.org/project/requests/) and [MSAL](https://pypi.org/project/msal/) are required for making API requests. Using [uv](https://docs.astral.sh/uv/) is recommended for easy setup and execution, but ```pip``` should work just fine.

### Project updates
- 2025-02-10: First release demonstrating data export API usage.

## Getting started

Using this project you can get an idea of how the data export works with minimal options applied. It takes a start date for the export in the past and exports all available content until the current time. The feasibility of a successful import depends on how much content exists, so the threshold for the start date can vary widely. 

Before running this script, you must [register an Application in Entra](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app) and update these constants:

- ```CLIENT_ID```: Take this verbatim from the Entra portal after creating an application.
- ```AUTHORITY```: Replace the tenant ID on the end of the URL.

In production use, it is best to use day or week intervals for the start and end dates. Adding a custom end date to this project is a simple exercise, but correctly generating other intervals is more involved. Token acquisition and export is combined to avoid persisting admin tokens on disk when running this script. In production use, you will need to securely store an admin token and refresh it so that you can avoid the need to authenticate on every run.

Get familiar with Entra authentication and tokens by reviewing the [Yammer API with AAD tokens Postman Collection](https://techcommunity.microsoft.com/blog/viva_engage_blog/yammer-api-with-aad-tokens-postman-collection/857923) blog post and testing in Postman. Any HTTP client will work, but you'll have extract the endpoints from the downloadable collection on the blog. 

Once you have the API working in Postman with the Entra application you registered then you are ready to use this script. If API calls are failing in Postman, fix those before moving on because it will simplify debugging.

## Exporting content
### `network-export.py`

Exports all content from the start date to now after acquiring an Entra ID token. It includes all available models in the data export which may differ over time and across networks. The Entra ID token is used once by this script and is not persisted.

#### Usage
```powershell
uv run .\network-export.py 2020-02-01
```
#### Example
```powershell
uv run .\network-export.py 2020-02-01
Start:  2020-02-01T00:00:00+00:00
End:    2025-02-10T17:01:15+00:00
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code [CODE APPEARS HERE] to authenticate.
Getting a token...
Access token acquired.
Engage export saved as export-1739224896811.zip.
```

#### Tips

- Ensure the user that you authenticate using the device code has appropriate permissions. They need to be a Viva Engage administrator (called "Yammer administrator") in Entra ID. It may take some time role changes to sync from Entra ID to Viva Engage. 
- You may need to create a [custom launch.json file](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) for debugging in VS Code. Example showing how to pass the start date argument:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python Debugger: Current File",
            "type": "debugpy",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "args": ["2020-01-31"],
        },

    ]
}
```

## Troubleshooting

### Failed to authorize the export request. Check that the user has the necessary permissions.
This error indicates that the user associated with the token does not have sufficient permissions to perform an export. Make sure that the user has been assigned the correct role in Entra ID. Test that the user can perform exports from the Engage admin center.

## Miscellanea 
### Sample response headers

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

## License
This project is licensed under the MIT License.

## Help and support
For general issues or questions, please file an issue in this repo.

There is no formal support for this script and some issues may not be resolved. Please do not send requests to Microsoft Support asking for help with code in this repo as it is intended for demonstration purposes only.
