
<a name="frequently-asked-questions"></a>
## Frequently Asked Questions

This section contains all Azure Portal FAQ's.

<!-- TODO:  FAQ Format in the individual docs  is ###Link, ***title***, Description, Solution, 3 Asterisks -->

<a name="debugging-extensions"></a>
## Debugging Extensions

FAQ's that are associated with ordinary extension testing.

<a name="faqs-for-debugging-extensions"></a>
## FAQs for Debugging Extensions

<a name="faqs-for-debugging-extensions-ssl-certificates"></a>
### SSL certificates

***How do I use SSL certs?***

[portalfx-extensions-faq-onboarding.md#sslCerts](portalfx-extensions-faq-onboarding.md#sslCerts)

* * *

<a name="faqs-for-debugging-extensions-loading-different-versions-of-an-extension"></a>
### Loading different versions of an extension

***How do I load different versions of an extension?***

Understanding which extension configuration to modify is located at [portalfx-extensions-configuration-overview.md#understanding-which-extension-configuration-to-modify](portalfx-extensions-configuration-overview.md#understanding-which-extension-configuration-to-modify).

* * *

<a name="faqs-for-debugging-extensions-other-debugging-questions"></a>
### Other debugging questions

***How can I ask questions about debugging ?***

You can ask questions on Stackoverflow with the tag [ibiza](https://stackoverflow.microsoft.com/questions/tagged/ibiza).



<a name="extension-configurations"></a>
## Extension Configurations

FAQ's that are associated with configurations for extensions.

<a name="frequently-asked-questions"></a>
## Frequently asked questions

<a name="frequently-asked-questions-ssl-certs"></a>
### SSL certs

***How do I use SSL certs?***

[portalfx-extensions-faq-onboarding.md#sslCerts](portalfx-extensions-faq-onboarding.md#sslCerts)

* * *

<a name="frequently-asked-questions-loading-different-versions-of-an-extension"></a>
### Loading different versions of an extension

***How do I load different versions of an extension?***

Understanding which extension configuration to modify is located at [portalfx-extensions-configuration-overview.md#understanding-which-extension-configuration-to-modify](portalfx-extensions-configuration-overview.md#understanding-which-extension-configuration-to-modify).




<a name="extensions-onboarding"></a>
## Extensions Onboarding

FAQ's for developers that are new to the Azure Portal Extension development process.

<a name="frequently-asked-questions"></a>
## Frequently asked questions

<a name="frequently-asked-questions-onboarding-faq"></a>
### Onboarding FAQ

***Where are the onboarding FAQs for Sparta (ARM/CSM-RP)?***

The SharePoint Sparta Onboarding FAQ is located at [http://sharepoint/sites/AzureUX/Sparta/SpartaWiki/Sparta%20Onboarding%20FAQ.aspx](http://sharepoint/sites/AzureUX/Sparta/SpartaWiki/Sparta%20Onboarding%20FAQ.aspx).

* * *
<a name="frequently-asked-questions-ssl-certs"></a>
### SSL Certs
   
***How do I use SSL certs?***
 
 SSL Certs are relevant only for teams that host their own extensions.  Azure Portal ONLY supports loading extensions from HTTPS URLs. Use a wildcard SSL cert for each environment to simplify maintenance, for example,   ``` *.<extensionName>.onecloud-ext.azure-test.net  ``` or  ``` *.<extensionName>.ext.azure.com) ``` .    To simplify overall management when your team is building separate, independent extensions, you can also use  ``` <extensionName>.<team>.ext.azure.com ``` and create a wildcard SSL cert for  ``` *.<team>.ext.azure.com ```. Internal teams can create SSL certs for the DogFood environment using the SSL Administration Web page that is located at [http://ssladmin](http://ssladmin). 
 
  Production certs must follow your organization’s PROD cert process. 

 **NOTE** Do not use the SSL Admin site for production certs.

 * * *
 ### Compile on Save

 ***What is Compile on Save ?**
Compile on Save is a TypeScript option that   . To use it, make sure that TypeScript 2.0.3 was installed on your machine. The version can be verified by executing the following  command:

```bash
$>tsc -version
```
Then, verify that when a typescript file is saved, that  the following text is displayed in the bottom left corner of your the visual studio application.

![alt-text](../media/portalfx-ide-setup/ide-setup.png "CompileOnSaveVisualStudio")

 * * *

<a name="frequently-asked-questions-other-onboarding-questions"></a>
### Other onboarding questions

***How can I ask questions about onboarding ?***

You can ask questions on Stackoverflow with the tag [onboarding](https://stackoverflow.microsoft.com/questions/tagged/onboarding).



<a name="hosting-service"></a>
## Hosting Service



<a name="frequently-asked-questions"></a>
## Frequently asked questions

<!-- TODO:  FAQ Format is ###Link, ***title***, Description, Solution, 3 Asterisks -->


<a name="frequently-asked-questions-content-unbundler-throws-aggregate-exception"></a>
### Content Unbundler throws aggregate exception

***Content Unbundler throws an Aggregate Exception during build.***

This usually happens when the build output generated by content unbundler is different from expected format.  The solution is as follows.

1. Verify build output directory is called **bin**
1. Verify you can point IIS to **bin** directory and load extension

For more information, see [portalfx-extensions-hosting-service-overview.md#prerequisites-for-onboarding-hosting-service](Portalfx-extensions-hosting-service-overview.md#prerequisites-for-onboarding-hosting-service).

* * *

<a name="frequently-asked-questions-finding-old-ux-after-deployment"></a>
### Finding old UX After Deployment

***Some customers of my extension are finding the old UX even after deploying the latest package. Is there a bug in hosting service ?***

No this is not a bug. All clients will not get the new version as soon as it gets deployed. The new version is only served when the customer refreshes the Portal. We have seen customers that keep the Portal open for long periods of time. In such scenarios, when customer loads the extension they are going to get the old version that has been cached.
We have seen scenarios where customers did not refresh the Portal for 2 weeks. 

* * * 

<a name="frequently-asked-questions-friendly-name-support"></a>
### Friendly name support

***When will support for friendly names become available ?***

Azure support for friendly names became available in SDK release 5.0.302.834.

* * *

<a name="frequently-asked-questions-how-extensions-are-served"></a>
### How extensions are served

***How does hosting service serve my extension?***

The runtime component of the hosting service is hosted inside an Azure Cloud Service. When an extension onboards, a publicly accessible endpoint is provided by the extension developer which will contain the contents that the hosting service should serve. For the hosting service to pick them up, it will look for a file called `config.json` that has a specific schema described below. 

The hosting service will upload the config file, look into it to figure out which zip files it needs to download. There can be multiple versions of the extension referenced in `config.json`. The hosting service will upload them and unpack them on the local disk. After it has successfully uploaded and expanded all versions of the extension referenced in `config.json`, it will write `config.json` to disk.

For performance reasons, once a version is uploaded, it will not be uploaded again. 

* * * 

<a name="frequently-asked-questions-output-zip-file-incorrectly-named"></a>
### Output zip file incorrectly named

***When I build my project, the output zip is called HostingSvc.zip instead of \<some version>.zip.***

The primary cause of this issue is that your `web.config` appSetting for **IsDevelopmentMode** is `true`.  It needs to be set to `false`.  Most do this using a `web.Release.config` transform. For example,

```xml
    <?xml version="1.0" encoding="utf-8"?>

    <!-- For more information on using web.config transformation visit http://go.microsoft.com/fwlink/?LinkId=125889 -->

    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
      <appSettings>
        <!-- dont forget to ensure the Key is correct for your specific extension -->
        <add key="Microsoft.Portal.Extensions.Monitoring.ApplicationConfiguration.IsDevelopmentMode" value="false" xdt:Transform="SetAttributes" xdt:Locator="Match(key)"/>
      </appSettings>
    </configuration>

```

* * * 

<a name="frequently-asked-questions-rollout-time-for-stages"></a>
### Rollout time for stages

***How much time does hosting service take to rollout a new version of extension to the relevant stage?*** 

The hosting service takes about 5 minutes to publish the latest version to all data centers.

* * *


<a name="frequently-asked-questions-sas-tokens"></a>
### SAS Tokens

***Can I provide a SAS token instead of keyvault for EV2 to access the storage account ?***

The current rolloutspec generated by **ContentUnbundler** only provides support for using keyvault. If you would like to use SAS tokens, please submit a request on [user voice](https:\\aka.ms\portalfx\uservoice)

* * *

<a name="frequently-asked-questions-speed-up-test-cycles"></a>
### Speed up test cycles

***My local build is slow. How can I speed up the dev/test cycles ?***

The default F5 experience for extension development remains unchanged however with the addition of the **ContentUnbundler** target some teams prefer to optimize to only run it on official builds or when they set a flag to force it to run.  The following example demonstrates how the Azure Scheduler extension is doing this within **CoreXT**.

```xml
    <PropertyGroup>
        <ForceUnbundler>false</ForceUnbundler>
    </PropertyGroup>
    <Import Project="$(PkgMicrosoft_Portal_Tools_ContentUnbundler)\build\Microsoft.Portal.Tools.ContentUnbundler.targets" 
            Condition="'$(IsOfficialBuild)' == 'true' Or '$(ForceUnbundler)' == 'true'" />
```

* * * 

<a name="frequently-asked-questions-storage-account-registration"></a>
### Storage account registration

***Do I need to register a new storage account everytime I need to upload zip file ?***

No. Registering a storage account with the hosting service is one-time process, as specified in . This allows the hosting service to find the latest version of your extension.

* * * 

<a name="frequently-asked-questions-zip-file-replaced-in-storage-account"></a>
### Zip file replaced in storage account

***What happens if instead of publishing new version to my storage account I replace the zip file ?***

Hosting service will only serve the new versions of zip file. If you replace version `1.0.0.0.zip` with a new version of `1.0.0.0.zip`, then hosting service will not detect.
It is required that you publish new zip file with a different version number, for example `2.0.0.0.zip`, and update `config.json` to reflect that hosting service should service new version of extension.

Sample config.json for version 2.0.0.0

```json
    {
        "$version": "3",
        "stage1": "2.0.0.0",
        "stage2": "2.0.0.0",
        "stage3": "2.0.0.0",
        "stage4": "2.0.0.0",
        "stage5": "2.0.0.0",
    }
```
**NOTE**: This samples depicts that all stages are serving version 2.0.0.0.

* * * 

<a name="frequently-asked-questions-other-hosting-service-questions"></a>
### Other hosting service questions

***How can I ask questions about hosting service ?***

You can ask questions on Stackoverflow with the tag [ibiza-deployment](https://stackoverflow.microsoft.com/questions/tagged/ibiza-deployment).

* * * 

<a name="samples"></a>
## Samples

FAQ's that are associated with Azure samples.

<a name="frequently-asked-questions"></a>
## Frequently asked questions

<a name="frequently-asked-questions-samples"></a>
### Samples

***Samples will not compile. How do I fix this?***

 Right out of the box, the samples are not aware of whether V1 or V2 is being used, or whether the IDE options match the version. There may be an error message TS1219.  Error may also occur based on the version of the SDK.

 SOLUTION:  Add a `tsconfig.json` file to the project that specifies that decorators are experimental, as in the following code.

 ```cs
 {
  "compilerOptions": {
    "noImplicitAny": false,
    "noEmitOnError": true,
    "removeComments": false,
    "sourceMap": true,
    "target": "es5",
    "experimentalDecorators": true
  },
  "exclude": [
    "node_modules"
  ]
}
 ```
 
 For more information, see [https://stackoverflow.com/questions/35460232/using-of-typescript-decorators-caused-errors](https://stackoverflow.com/questions/35460232/using-of-typescript-decorators-caused-errors).

 * * *

 ---------------------------
Microsoft Visual Studio
---------------------------
Unable to launch the IIS Express Web server.

Failed to register URL "https://localhost:44306/" for site "SamplesExtension" application "/". Error description: Cannot create a file when that file already exists. (0x800700b7)


SOLUTION:  terminate iis express processes in Task Manager and f5 again.

* * *

<a name="testing-in-production"></a>
## Testing in Production

FAQ's that are associated with testing an extension in the production environment.

<a name="frequently-asked-questions"></a>
## Frequently asked questions

If there are enough FAQ's on the same subject, like sideloading, they have been grouped together in this document. Otherwise, FAQ's are listed in the order that they were encountered. Items that are specifically status codes or error messages can be located in [portalfx-extensions-status-codes.md](portalfx-extensions-status-codes.md).

<a name="frequently-asked-questions-faqs-for-debugging-extensions"></a>
### FAQs for Debugging Extensions

***Where are the FAQ's for normal debugging?***

The FAQs for debugging extensions is located at [portalfx-extensions-faq-debugging.md](portalfx-extensions-faq-debugging.md).

* * *

<a name="frequently-asked-questions-sandboxed-iframe-security"></a>
### Sandboxed iframe security

*** I get an error 'Security of a sandboxed iframe is potentially compromised by allowing script and same origin access'. How do I fix this? ***

You need to allow the Azure Portal to frame your extension URL. For more information, [click here](portalfx-creating-extensions.md).

* * *

<a name="sideloading-faqs"></a>
## Sideloading FAQs

<a name="sideloading-faqs-sideloading-extension-gets-an-err_insecure_response"></a>
### Sideloading Extension gets an ERR_INSECURE_RESPONSE

*** My Extension fails to side load and I get an ERR_INSECURE_RESPONSE in the browser console ***

![ERR_INSECURE_RESPONSE](../media/portalfx-testinprod/errinsecureresponse.png)

In this case the browser is trying to load the extension but the SSL certificate from localhost is not trusted the solution is to install/trust the certificate.

Please checkout the stackoverflow post: [https://stackoverflow.microsoft.com/questions/15194/ibiza-extension-unable-to-load-insecure](https://stackoverflow.microsoft.com/questions/15194/ibiza-extension-unable-to-load-insecure)

* * *

<a name="sideloading-faqs-sideloading-in-chrome"></a>
### Sideloading in Chrome

***Ibiza sideloading in Chrome fails to load parts***
    
Enable the `allow-insecure-localhost` flag, as described in [https://stackoverflow.microsoft.com/questions/45109/ibiza-sideloading-in-chrome-fails-to-load-parts](https://stackoverflow.microsoft.com/questions/45109/ibiza-sideloading-in-chrome-fails-to-load-parts)

* * *

<a name="sideloading-faqs-sideloading-in-chrome-sideloading-gallery-packages"></a>
#### Sideloading gallery packages

***Trouble sideloading gallery packages***

SOLUTION:  Some troubleshooting steps are located at [https://stackoverflow.microsoft.com/questions/12136/trouble-side-loading-gallery-packages](https://stackoverflow.microsoft.com/questions/12136/trouble-side-loading-gallery-packages)

* * *

<a name="sideloading-faqs-sideloading-in-chrome-sideloading-friendly-names"></a>
#### Sideloading friendly names

***Sideloading friendly names is not working in the Dogfood environment***

In order for Portal to load  a test version of an extension, i.e., load without using the PROD stamp, developers can append the feature flag `feature.canmodifystamps`. The following example uses the sideload url to load the "test" version of extension.

`https://portal.azure.com?feature.canmodifystamps=true&<extensionName>=test`

The parameter `feature.canmodifystamps=true` is required for side-loading, and 
 **extensionName**, without the angle brackets, is replaced with the unique name of extension as defined in the `extension.pdl` file. For more information, see [https://stackoverflow.microsoft.com/questions/64951/extension-hosting-service-side-loading-friendlynames-not-working-in-dogfood](https://stackoverflow.microsoft.com/questions/64951/extension-hosting-service-side-loading-friendlynames-not-working-in-dogfood).

* * *

<a name="sideloading-faqs-other-testing-questions"></a>
### Other testing questions

***How can I ask questions about testing ?***

You can ask questions on Stackoverflow with the tag [ibiza-test](https://stackoverflow.microsoft.com/questions/tagged/ibiza-test).

--------