---
title: Using PlayReady dynamic common encryption | Microsoft Docs
description: Azure Media Services enables you to deliver MPEG-DASH, Smooth Streaming, and Http-Live-Streaming (HLS) streams protected with Microsoft PlayReady DRM. 
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: juliako

---
# Using PlayReady dynamic common encryption

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-drm.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>
>

Azure Media Services enables you to deliver MPEG-DASH, Smooth Streaming, and HTTP-Live-Streaming (HLS) streams protected with [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). PlayReady is encrypted per the Common Encryption (ISO/IEC 23001-7 CENC) specification.

Media Services provides a service for delivering PlayReady DRM licenses. Media Services also provides APIs that let you configure the rights and restrictions that you want for the PlayReady DRM runtime to enforce when a user plays back protected content. When a user requests a DRM protected content, the player application will request a license from the AMS license service. The AMS license service will issue a license to the player if it is authorized. A PlayReady license contains the decryption key that can be used by the client player to decrypt and stream the content.

Media Services supports multiple ways of authorizing users who make key requests. The content key authorization policy could have one or more authorization restrictions: open or token restriction. The token restricted policy must be accompanied by a token issued by a Secure Token Service (STS). Media Services supports tokens in the [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) format and [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) format. For more information, see Configure the content key’s authorization policy.

To take advantage of dynamic encryption, you need to have an asset that contains a set of multi-bitrate MP4 files or multi-bitrate Smooth Streaming source files. You also need to configure the delivery policies for the asset (described later in this topic). Then, based on the format specified in the streaming URL, the On-Demand Streaming server will ensure that the stream is delivered in the protocol you have chosen. As a result, you only need to store and pay for the files in a single storage format and Media Services will build and serve the appropriate HTTP response based on each request from a client.

This topic would be useful to developers that work on applications that deliver media protected with multiple DRMs, such as PlayReady. The topic shows you how to configure the PlayReady license delivery service with authorization policies so that only authorized clients could receive PlayReady. It also shows how to use dynamic encryption encryption with PlayReady over DASH.

>[!NOTE]
>When your AMS account is created a **default** streaming endpoint is added to your account in the **Stopped** state. To start streaming your content and take advantage of dynamic packaging and dynamic encryption, the streaming endpoint from which you want to stream content has to be in the **Running** state. 

## Download sample
You can download the sample described in this article from [here](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

## Configuring Dynamic Common Encryption and DRM License Delivery Services

The following are general steps that you would need to perform when protecting your assets with PlayReady, using the Media Services license delivery service, and also using dynamic encryption.

1. Create an asset and upload files into the asset.
2. Encode the asset containing the file to the adaptive bitrate MP4 set.
3. Create a content key and associate it with the encoded asset. In Media Services, the content key contains the asset’s encryption key.
4. Configure the content key’s authorization policy. The content key authorization policy must be configured by you and met by the client in order for the content key to be delivered to the client.

	When creating the content key authorization policy, you need to specify the following: delivery method (PlayReady), restrictions (open or token), and information specific to the key delivery type that defines how the key is delivered to the client ([PlayReady](media-services-playready-license-template-overview.md) license template).

5. Configure the delivery policy for an asset. The delivery policy configuration includes: delivery protocol (for example, MPEG DASH, HLS, Smooth Streaming or all), the type of dynamic encryption (for example, Common Encryption), PlayReady license acquisition URL.

	You could apply different policy to each protocol on the same asset. For example, you could apply PlayReady encryption to Smooth/DASH and AES Envelope to HLS. Any protocols that are not defined in a delivery policy (for example, you add a single policy that only specifies HLS as the protocol) will be blocked from streaming. The exception to this is if you have no asset delivery policy defined at all. Then, all protocols will be allowed in the clear.

6. Create an OnDemand locator in order to get a streaming URL.

You will find a complete .NET example at the end of the topic.

The following image demonstrates the workflow described above. Here the token is used for authentication.

![Protect with PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

The rest of this topic provides detailed explanations, code examples, and links to topics that show you how to achieve the tasks described above.

## Current limitations
If you add or update an asset delivery policy, you must delete the associated locator (if any) and create a new locator.


## Create an asset and upload files into the asset
In order to manage, encode, and stream your videos, you must first upload your content into Azure Media Services. Once uploaded, your content is stored securely in the cloud for further processing and streaming.

For detailed information, see [Upload Files into a Media Services account](media-services-dotnet-upload-files.md).

## Encode the asset containing the file to the adaptive bitrate MP4 set
With dynamic encryption all you need is to create an asset that contains a set of multi-bitrate MP4 files or multi-bitrate Smooth Streaming source files. Then, based on the specified format in the manifest and fragment request, the On-Demand Streaming server will ensure that you receive the stream in the protocol you have chosen. As a result, you only need to store and pay for the files in single storage format and Media Services service will build and serve the appropriate response based on requests from a client. For more information, see the [Dynamic Packaging Overview](media-services-dynamic-packaging-overview.md) topic.

For instructions on how to encode, see [How to encode an asset using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Create a content key and associate it with the encoded asset
In Media Services, the content key contains the key that you want to encrypt an asset with.

For detailed information, see [Create content key](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configure the content key’s authorization policy
Media Services supports multiple ways of authenticating users who make key requests. The content key authorization policy must be configured by you and met by the client (player) in order for the key to be delivered to the client. The content key authorization policy could have one or more authorization restrictions: open or token restriction.

For detailed information, see [Configure Content Key Authorization Policy](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a id="configure_asset_delivery_policy"></a>Configure asset delivery policy
Configure the delivery policy for your asset. Some things that the asset delivery policy configuration includes:

* The DRM license acquisition URL.
* The asset delivery protocol (for example, MPEG DASH, HLS, Smooth Streaming or all).
* The type of dynamic encryption (in this case, Common Encryption).

For detailed information, see [Configure asset delivery policy ](media-services-rest-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Create an OnDemand streaming locator in order to get a streaming URL
You will need to provide your user with the streaming URL for Smooth, DASH or HLS.

> [!NOTE]
> If you add or update your asset’s delivery policy, you must delete an existing locator (if any) and create a new locator.
>
>

For instructions on how to publish an asset and build a streaming URL, see [Build a streaming URL](media-services-deliver-streaming-content.md).

## Get a test token
Get a test token based on the token restriction that was used for the key authorization policy.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string.
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);


You can use the [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) to test your stream.

## <a id="example"></a>Example
The following sample demonstrates functionality that was introduced in Azure Media Services SDK for .Net -Version 3.5.2 . The following Nuget package command was used to install the package:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Create a new Console project.
2. Use NuGet to install and add Azure Media Services .NET SDK.
3. Add additional references: System.Configuration.
4. Add config file that contains the account name and key information:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup>
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>

                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>

                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>
7. Overwrite the code in your Program.cs file with the code shown in this section.

	>[!NOTE]
	>There is a limit of 1,000,000 policies for different AMS policies (for example, for Locator policy or ContentKeyAuthorizationPolicy). You should use the same policy ID if you are always using the same days / access permissions, for example, policies for locators that are intended to remain in place for a long time (non-upload policies). For more information, see [this](media-services-dotnet-manage-entities.md#limit-access-policies) topic.

    Make sure to update variables to point to folders where your input files are located.

    ```
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    using Newtonsoft.Json;

    namespace DynamicEncryptionWithDRM
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            private static readonly String _defaultScope = "urn:WindowsAzureMediaServices";

            // Azure China uses a different API server and a different ACS Base Address from the Global.
            private static readonly String _chinaApiServerUrl = "https://wamsshaclus001rest-hs.chinacloudapp.cn/API/";
            private static readonly String _chinaAcsBaseAddressUrl = "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn";

            private static readonly Uri _sampleIssuer =
                new Uri(ConfigurationManager.AppSettings["Issuer"]);
            private static readonly Uri _sampleAudience =
                new Uri(ConfigurationManager.AppSettings["Audience"]);

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;

            private static readonly string _mediaFiles =
                Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
                Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey,
                                _defaultScope,
                                _chinaAcsBaseAddressUrl);

                // Create the API server Uri
                _apiServer = new Uri(_chinaApiServerUrl);

                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_apiServer, _cachedCredentials);

                bool tokenRestriction = false;
                string tokenTemplateString = null;

                IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                Console.WriteLine("Uploaded asset: {0}", asset.Id);

                IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

                IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                Console.WriteLine();

                if (tokenRestriction)
                    tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                else
                    AddOpenAuthorizationPolicy(key);

                Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                Console.WriteLine();

                CreateAssetDeliveryPolicy(encodedAsset, key);
                Console.WriteLine("Created asset delivery policy. \n");
                Console.WriteLine();

                if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                {
                    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                    // back into a TokenRestrictionTemplate class instance.
                    TokenRestrictionTemplate tokenTemplate =
                        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                    // Note, you need to pass the key id Guid because we specified 
                    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                            DateTime.UtcNow.AddDays(365));
                    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                    Console.WriteLine();
                }

                // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady).

                string url = GetStreamingOriginLocator(encodedAsset);
                Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

                Console.ReadLine();
            }

            static public IAsset UploadFileAndCreateAsset(string singleFilePath)
            {
                if (!File.Exists(singleFilePath))
                {
                    Console.WriteLine("File does not exist.");
                    return null;
                }

                var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                var policy = _context.AccessPolicies.Create(
                                        assetName,
                                        TimeSpan.FromDays(30),
                                        AccessPermissions.Write | AccessPermissions.List);

                var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

                Console.WriteLine("Upload {0}", assetFile.Name);

                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);

                locator.Delete();
                policy.Delete();

                return inputAsset;
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
            {
                var encodingPreset = "H264 Multiple Bitrate 720p";

                IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                        inputAsset.Name,
                                        encodingPreset));

                var mediaProcessors =
                    _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                var latestMediaProcessor =
                    mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                encodeTask.InputAssets.Add(inputAsset);
                encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);

                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                job.Submit();
                job.GetExecutionProgressTask(CancellationToken.None).Wait();

                return job.OutputMediaAssets[0];
            }

            static public IContentKey CreateCommonTypeContentKey(IAsset asset)
            {

                Guid keyId = Guid.NewGuid();
                byte[] contentKey = GetRandomBuffer(16);

                IContentKey key = _context.ContentKeys.Create(
                                        keyId,
                                        contentKey,
                                        "ContentKey",
                                        ContentKeyType.CommonEncryption);

                // Associate the key with the asset.
                asset.ContentKeys.Add(key);

                return key;
            }

            static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
            {

                // Create ContentKeyAuthorizationPolicy with Open restrictions 
                // and create authorization policy          

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                };

                // Configure PlayReady license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();



                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);


                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with no restrictions").
                            Result;

                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                // Associate the content key authorization policy with the content key.
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;
            }

            public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
            {
                string tokenTemplateString = GenerateTokenRequirements();

                List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                };

                // Configure PlayReady license templates.
                string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();


                IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                    _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                        ContentKeyDeliveryType.PlayReadyLicense,
                            restrictions, PlayReadyLicenseTemplate);


                IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                            ContentKeyAuthorizationPolicies.
                            CreateAsync("Deliver Common Content Key with token restrictions").
                            Result;

                contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);

                // Associate the content key authorization policy with the content key
                contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                contentKey = contentKey.UpdateAsync().Result;

                return tokenTemplateString;
            }

            static private string GenerateTokenRequirements()
            {
                TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                template.PrimaryVerificationKey = new SymmetricVerificationKey();
                template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                template.Audience = _sampleAudience.ToString();
                template.Issuer = _sampleIssuer.ToString();
                template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                return TokenRestrictionTemplateSerializer.Serialize(template);
            }

            static private string ConfigurePlayReadyLicenseTemplate()
            {
                // The following code configures PlayReady License Template using .NET classes
                // and returns the XML string.

                //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                //It contains a field for a custom data string between the license server and the application 
                //(may be useful for custom app logic) as well as a list of one or more license templates.
                PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

                // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                // to be returned to the end users. 
                //It contains the data on the content key in the license and any rights or restrictions to be 
                //enforced by the PlayReady DRM runtime when using the content key.
                PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                //Configure whether the license is persistent (saved in persistent storage on the client) 
                //or non-persistent (only held in memory while the player is using the license).  
                licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                // AllowTestDevices controls whether test devices can use the license or not.  
                // If true, the MinimumSecurityLevel property of the license
                // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                licenseTemplate.AllowTestDevices = true;

                // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                // It grants the user the ability to playback the content subject to the zero or more restrictions 
                // configured in the license and on the PlayRight itself (for playback specific policy). 
                // Much of the policy on the PlayRight has to do with output restrictions 
                // which control the types of outputs that the content can be played over and 
                // any restrictions that must be put in place when using a given output.
                // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                //then the DRM runtime will only allow the video to be displayed over digital outputs 
                //(analog video outputs won’t be allowed to pass the content).

                //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                // If the output protections are configured too restrictive, 
                // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

                // For example:
                //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                responseTemplate.LicenseTemplates.Add(licenseTemplate);

                return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
            }

            

            static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
            {
                // Get the PlayReady license service URL.
                Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

                Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                        {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}
                    };

                // In this case we only specify Dash streaming protocol in the delivery policy,
                // All other protocols will be blocked from streaming.
                var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                    AssetDeliveryPolicyType.DynamicCommonEncryption,
                    AssetDeliveryProtocol.Dash,
                    assetDeliveryPolicyConfiguration);

                // Add AssetDelivery Policy to the asset
                asset.DeliveryPolicies.Add(assetDeliveryPolicy);

            }

            /// <summary>
            /// Gets the streaming origin locator.
            /// </summary>
            /// <param name="assets"></param>
            /// <returns></returns>
            static public string GetStreamingOriginLocator(IAsset asset)
            {

                // Get a reference to the streaming manifest file from the  
                // collection of files in the asset. 

                var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                             EndsWith(".ism")).
                                             FirstOrDefault();

                // Create a 30-day readonly access policy. 
                IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                    TimeSpan.FromDays(30),
                    AccessPermissions.Read);

                // Create a locator to the streaming content on an origin. 
                ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

                // Create a URL to the manifest file. 
                return originLocator.Path + assetFile.Name;
            }

            static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                    ((IJob)sender).Name,
                    e.CurrentState,
                    DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
            }

            static private byte[] GetRandomBuffer(int length)
            {
                var returnValue = new byte[length];

                using (var rng =
                    new System.Security.Cryptography.RNGCryptoServiceProvider())
                {
                    rng.GetBytes(returnValue);
                }

                return returnValue;
            }
        }
    }
    ```

##See also

[CENC with Multi-DRM and Access Control](./media-services-cenc-with-multidrm-access-control.md)