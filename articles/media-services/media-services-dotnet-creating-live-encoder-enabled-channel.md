---
title: Élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a .NET-tel | Microsoft Docs
description: Ez az útmutató lépésről lépésre ismerteti, hogyan hozhat létre egy csatornát, amely a fogadott egyféle sávszélességű élő adatfolyamokat a .NET SDK használatával többféle sávszélességűvé kódolja.
services: media-services
documentationcenter: ''
author: anilmur
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/12/2016
ms.author: juliako;anilmur

---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-.net"></a>Élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a .NET használatával
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)
> 
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> 

## <a name="overview"></a>Áttekintés
Ez az útmutató lépésről lépésre bemutatja, hogyan hozhat létre egy **csatornát**, amely a fogadott egyféle sávszélességű élő adatfolyamokat többféle sávszélességűvé kódolja.

További elméleti információk a valós idejű kódolásra képes csatornákról: [Élő adatfolyam továbbítása az Azure Media Services használatával, és többféle sávszélességű adatfolyamok létrehozása](media-services-manage-live-encoder-enabled-channels.md)

## <a name="common-live-streaming-scenario"></a>Gyakori élő adatfolyam-továbbítási forgatókönyv
A következő lépések a gyakran használt élő adatfolyam-továbbítási alkalmazások létrehozásához elvégzendő feladatokat írják le.

> [!NOTE]
> Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha egy ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@Microsoft.com e-mail címen.
> 
> 

1. Csatlakoztasson egy videokamerát a számítógéphez. Indítson el és állítson be egy helyszíni valós idejű kódolót, amely képes egy egyféle sávszélességű kimeneti adatfolyam továbbítására a következő protokollok valamelyikével: RTMP, Smooth Streaming vagy RTP (MPEG-TS). További tudnivalók: [Azure Media Services RMTP-támogatása és valós idejű kódolók](http://go.microsoft.com/fwlink/?LinkId=532824)

Ezt a lépést a csatorna létrehozása után is elvégezheti.

1. Hozzon létre és indítson el egy csatornát.
2. Kérje le a csatorna feldolgozó URL-címét.

Az élő kódoló a bemeneti URL-címet használva küldi el a streamet a csatornának.

1. Kérje le a csatorna előnézeti URL-címét.

Ezen az URL használatával ellenőrizheti, hogy a csatornája megfelelően fogadja-e az élő adatfolyamot.

1. Hozzon létre egy adategységet.
2. Ha azt szeretné, hogy az adategység a lejátszás során dinamikusan legyen titkosítva, tegye a következőket:
3. Hozzon létre egy tartalomkulcsot.
4. Konfigurálja a tartalomkulcs engedélyezési házirendjét.
5. Konfigurálja az adategység továbbítási házirendjét (amelyet a dinamikus csomagolás és a dinamikus titkosítás használ).
6. Hozzon létre egy programot, és állítsa be, hogy az az imént létrehozott adategységet használja.
7. Tegye közzé a programhoz társított adategységet egy OnDemand-kereső létrehozásával.

Biztosítsa, hogy legyen legalább egy, a folyamatos adatátvitelhez fenntartott egység a streamvégpontján, amelyről a tartalmat továbbítani kívánja.

1. Indítsa el a programot, ha készen áll az adatfolyam-továbbításra és az archiválásra.
2. További lehetőségként jelzést adhat a valós idejű kódolónak egy hirdetés elindítására. A hirdetés a kimeneti adatfolyamba lesz beszúrva.
3. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.
4. Törölje a programot (esetlegesen törölje az adategységet is).

## <a name="what-you'll-learn"></a>Ismertetett témák
Ez a témakör bemutatja, hogyan hajthat végre különböző műveleteket csatornákon és programokon a Media Services .NET SDK használatával. A műveletek között számos hosszú futású művelet található, így hosszú futású műveleteket felügyelő .NET API-kat használunk.

Ez a témakör bemutatja, hogyan végezze el a következőket:

1. Csatorna létrehozása és elindítása. Hosszú futású API-k vannak használatban.
2. A csatorna feldolgozó (bemeneti) végpontjának lekérése. Ezt a végpontot kell megadni az egyféle sávszélességű élő adatfolyamot küldő kódolónak.
3. Az előnézeti végpont lekérése. Ez a végpont az adatfolyam előnézetéhez használatos.
4. A tartalmakat tároló adategység létrehozása. Az adategység továbbítási házirendjeit szintén konfigurálni kell, ahogy az a példában is látható.
5. Egy program létrehozása, és annak beállítása, hogy az a korábban létrehozott adategységet használja. A program elindítása. Hosszú futású API-k vannak használatban.
6. Egy kereső létrehozása az adategységhez, hogy a tartalom közzétételre kerüljön és továbbítható legyen az ügyfelek részére.
7. A befutók megjelenítése és elrejtése. A hirdetések elindítása és leállítása. Hosszú futású API-k vannak használatban.
8. A csatornát és a hozzá kapcsolódó erőforrások tisztán tartása.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzésének a következők a feltételei.

* Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](/pricing/free-trial/?WT.mc_id=A261C142F). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a jóváírásokat el is használta, továbbra is megtarthatja a fiókot és használhatja az ingyenes szolgáltatásokat és lehetőségeket, mint például a Web Apps szolgáltatást az Azure App Service alatt.

* Egy Media Services-fiók szükséges. Egy Media Services-fiók létrehozásához lásd: [Fiók létrehozása](media-services-portal-create-account.md)
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate vagy Express) vagy későbbi verzió.
* A Media Services .NET SDK legalább 3.2.0.0 vagy újabb verziójával kell rendelkeznie.
* Egy webkamera és egy egyféle sávszélességű élő adatfolyamot küldő kódoló.

## <a name="considerations"></a>Megfontolások
* Jelenleg az élő események maximálisan ajánlott időtartama 8 óra. Ha egy ennél tovább futó csatornára van szüksége, lépjen velünk kapcsolatba az amslived@Microsoft.com e-mail címen.
* Biztosítsa, hogy legyen legalább egy, a folyamatos adatátvitelhez fenntartott egység a streamvégpontján, amelyről a tartalmat továbbítani kívánja.

## <a name="download-sample"></a>Minta letöltése
Töltsön le és futtasson egy mintát [innen](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).

## <a name="set-up-for-development-with-media-services-sdk-for-.net"></a>A .NET-keretrendszerhez készült Media Services SDK-val történő fejlesztés előkészítése
1. Hozzon létre egy konzolalkalmazást a Visual Studio használatával.
2. Adja hozzá a konzolalkalmazáshoz a .NET-keretrendszerhez készült Media Services SDK-t a Media Services NuGet csomagjának segítségével.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz
Javasoljuk, hogy a Media Services-nevet és -fiókkulcsot egy app.config fájlban tárolja.

> [!NOTE]
> A név és a kulcs értékeinek megtekintéséhez nyissa meg Azure Portal-fiókját. Jobb oldalt megjelenik a Beállítások ablak. A Beállítások ablakban válassza a Kulcsok lehetőséget. A szövegdobozok melletti ikonokra való kattintás a rendszer vágólapjára másolja az értékeket.
> 
> 

Adja hozzá az appSettings szakaszt az app.config fájlhoz, és állítsa be a Media Services-fiókjához tartozó név és fiókkulcs értékeit.

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>


## <a name="code-example"></a>Mintakód
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";

            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;


            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                IChannel channel = CreateAndStartChannel();

                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();

                Console.WriteLine("Intest URL: {0}", ingestUrl);


                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();

                Console.WriteLine("Preview URL: {0}", previewEndpoint);

                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();

                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);

                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();

                IProgram program = CreateAndStartProgram(channel, asset);

                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);

                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);

                // Once you are done streaming, clean up your resources.
                Cleanup(channel);

            }

            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();


                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };

                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");

                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();

                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");

                return channel;
            }

            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }

            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);

                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

                asset.DeliveryPolicies.Add(policy);

                return asset;
            }

            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);

                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");

                return program;
            }

            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );

                return locator;
            }

            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));

                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);

                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();

                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");

                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");

                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");

                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");

                Log("Deleting slate asset");
                slateAsset.Delete();
            }

            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();

                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");

                        program.Delete();

                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();

                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }

                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");

                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }


            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;

                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);

                return entityId;
            }

            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {

                bool completed = false;

                entityId = null;

                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }


            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else?"></a>Valami mást keres?
Amennyiben ebben a témakörben nem találta meg azt, amire számított; ha a témakörből hiányzik valami; vagy bármilyen egyéb módon nem felelt meg az elvárásainak, kérjük, küldjön nekünk visszajelzést alább, egy Disqus-hozzászóláson keresztül.

<!--HONumber=Oct16_HO3-->


