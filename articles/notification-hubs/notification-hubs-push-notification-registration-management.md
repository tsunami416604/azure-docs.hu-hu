---
title: Regisztrációs kezelése
description: Ez a témakör ismerteti az eszközök regisztrálása a notification hubs leküldéses értesítések fogadásához.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 969f6b9654200b7f742b6405faa2cff2b13ba537
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="registration-management"></a>Regisztrációkezelés
## <a name="overview"></a>Áttekintés
Ez a témakör ismerteti az eszközök regisztrálása a notification hubs leküldéses értesítések fogadásához. A témakör ismerteti a regisztrációk magas szinten, majd vezet be az eszközök regisztrálása a két fő minták: közvetlenül az értesítési központ regisztrálná az eszközről, és regisztrálnia kell az alkalmazás-háttéralkalmazás segítségével. 

## <a name="what-is-device-registration"></a>Mi az az eszközök regisztrációja
Eszközregisztráció az egy értesítési központot teszi lehetővé a **regisztrációs** vagy **telepítési**.

#### <a name="registrations"></a>Regisztrációk
Regisztráció a Platform Notification szolgáltatás (PNS) leíró eszköz társítja címkék, és valószínűleg egy sablont. A PNS-leírójának lehet a ChannelURI, az eszköz jogkivonatát, illetve a GCM regisztrációs azonosítót. Címkék segítségével értesítések továbbítani kívánt eszközt kezeli. További információkért lásd: [Útválasztás és címke kifejezések](notification-hubs-tags-segment-push-message.md). Regisztráció átalakítás végrehajtásához használt sablonokat. További információkért lásd: [sablonok](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Telepítés
Egy bővített egy telepítés, amely tartalmazza a leküldéses összessége regisztrációs kapcsolódó tulajdonságok. Az eszközök regisztrálása a legújabb és legjobb módja. Azonban ez nem támogatja az ügyféloldali .NET SDK-val ([Notification Hub SDK a háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) még érvényes.  Ez azt jelenti, ha regisztrál a magáról az ügyféleszközről, akkor kell használni a [Notification hub REST API](https://msdn.microsoft.com/library/mt621153.aspx) megközelítés telepítések támogatására. Ha a háttérszolgáltatáshoz használ, fogja tudni használni [Notification Hub SDK a háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Fő előnye, hogy telepítések a következők:

* Teljes idempotent létrehozásához vagy frissítéséhez egy telepítés. Úgy is újraindítható anélkül, duplikált regisztrációjával kapcsolatos bármilyen probléma merül fel.
* A telepítési modell segítségével egyszerűen egyedi leküldéses értesítések - eszközre célzó tegye. A rendszer címke **"$InstallationId: [végrehajtott]"** automatikusan fel lesz véve az egyes telepítési alapú regisztrációs. Így hívása egy ezt a címkét az adott eszközön anélkül, hogy programozás további küldés.
* Telepítés használatával is lehetővé teszi részleges regisztrációs frissítéseinek. A részleges frissítés telepítésének van szükség a javítás módszer használatával a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902). Ez akkor különösen hasznos, ha frissíti a regisztrációját a címkék. Húzza le a teljes regisztrációját, és küldje el újra a korábbi címkék nincs.

Telepítés tartalmazhat a a következő tulajdonságokkal. A telepítési tulajdonságok teljes listáját lásd: [létrehozása vagy egy telepítési felülírása REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) vagy [telepítési tulajdonságok](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

    // Example installation format to show some supported properties
    {,
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }



Fontos megjegyezni, hogy regisztráció és a telepítések alapértelmezés szerint nem jár le.

Regisztráció és a telepítés tartalmaznia kell egy érvényes PNS-leírójának minden eszköz vagy csatorna. PNS-leírók regisztrálásáért egy ügyfél-alkalmazást az eszközön a csak olvasható be, mert egy minta, közvetlenül az eszközön és az ügyfél alkalmazás regisztrálásához. Másrészt biztonsági szempontok és címkék kapcsolódó üzleti logikai szükség lehet az alkalmazás háttér-eszközregisztrációját kezeléséhez. 

#### <a name="templates"></a>Sablonok
Ha a használni kívánt [sablonok](notification-hubs-templates-cross-platform-push-messages.md), az eszköz telepítése is rendelkezik az összes sablon egy JSON-ban, hogy eszközhöz társított (lásd a fenti minta) formátumban. A nevek segítségével különféle sablonok cél ugyanarra az eszközre.

Vegye figyelembe, hogy minden a sablonnevet van leképezve a sablon szervezet és címkék nem kötelező megadni. Továbbá minden egyes platformhoz lehet további sablontulajdonságok. A Windows Állapottárolójához (WNS) és Windows Phone 8 (MPNS segítségével) egy további-fejléc készlettel a sablon része lehet. Esetén APNs beállíthat egy lejárati tulajdonság egy konstans vagy egy sablon kifejezés. Teljes listáját a telepítési tulajdonságok lásd [létrehozása vagy felülírni a telepítés többi](https://msdn.microsoft.com/library/azure/mt621153.aspx) témakör.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Windows Áruházbeli alkalmazások másodlagos Csempéket
Windows áruház ügyfélalkalmazások, az értesítések küldése másodlagos csempék megegyezik a elküldi őket az aliasok közül. Ez is támogatott telepítések esetén. Ne feledje, hogy másodlagos csempék egy másik ChannelUri, és az ügyfél alkalmazásnak az SDK transzparens módon kezeli.

A SecondaryTiles szótár használja az ugyanazon TileId, a Windows Áruházbeli alkalmazásban a SecondaryTiles objektum létrehozásához használt.
Csakúgy, mint az elsődleges ChannelUri, a másodlagos csempék ChannelUris bármikor módosíthatja. Ahhoz, hogy a telepítések az értesítési központ frissítése tároláshoz, megőrizheti az eszköz frissítenie kell azokat a másodlagos csempék aktuális ChannelUris rendelkező.

## <a name="registration-management-from-the-device"></a>Regisztrációs felügyeleti az eszközről
Eszközregisztráció ügyfél alkalmazásokból való kezelésekor a háttér felelős csak az értesítések küldése. Ügyfél alkalmazások naprakészen PNS-leírók regisztrálásáért, és regisztrálja a címkék. A következő kép azt mutatja be ezt a mintát.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Az eszközök először a PNS-leírójának átveszi a pns-sel, majd közvetlenül regisztrál az értesítési központban. A regisztráció befejezését követően a háttéralkalmazás, hogy a regisztrációs célzó értesítés küldése. Hogyan küldhetők értesítések kapcsolatos további információkért lásd: [Útválasztás és címke kifejezések](notification-hubs-tags-segment-push-message.md).
Vegye figyelembe, hogy ebben az esetben használhatja csak figyelni jogosultsága ahhoz, hogy az eszközön keresztüli elérését a notification hubs használatával. További információkért lásd: [biztonsági](notification-hubs-push-notification-security.md).

Az eszköz regisztrálása a legegyszerűbb módja, de a hátrányokkal azt.
Az első hátránya, hogy egy ügyfélalkalmazás csak képes frissíteni a címkék, ha az alkalmazás aktív. Például ha egy felhasználó két eszközre, amely során az első eszköz regisztrálása egy további (például Seahawks) címke sport csoportokkal kapcsolatos címkék regisztrálni, a második eszköz addig nem kap az értesítéseket a Seahawks kapcsolatos mindaddig, amíg a második eszközre az alkalmazás végrehajtja a rendszer még egyszer. Általában több eszköz által érintett címkék, amikor címkék kezelése a háttérrendszerből beállítás kívánatos.
A második regisztrációs felügyeleti az ügyfél alkalmazásból hátránya, hogy alkalmazásokat is megtámadott, mivel biztonságossá tétele a regisztrációt, hogy az adott címkével igényel különös figyelmet "címke szintű biztonság." szakaszában leírtak szerint

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Példakódot egy értesítési központot egy eszközről egy telepítés regisztrálása
Jelenleg ez csak akkor támogatott használatával a [Notification hub REST API](https://msdn.microsoft.com/library/mt621153.aspx).

Is használhatja a javítás módszer használatával a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902) frissítheti a telepítést.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Példakód regisztrálni a regisztráció segítségével egy eszközről egy értesítési központ
Ezek a módszerek regisztráció létrehozása vagy módosítása egy az eszközhöz, amelyre nevezzük. Ez azt jelenti, hogy a leíró vagy a címkék frissítéséhez, írja felül a teljes regisztrációját. Ne feledje, hogy regisztrációk tranziens, így mindig rendelkeznie kell egy megbízható tároló, amelyet egy adott eszköz aktuális címkékkel.

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }

    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Regisztrációs felügyeleti a háttérrendszerből
Kód írása a háttér regisztrációját kezelése szükséges. Az alkalmazást az eszközről a frissített PNS kezelni a háttérkiszolgálón minden az alkalmazás indításakor (valamint a címkék és sablonok), és a háttér frissítenie kell az értesítési központ leírónak kell megadnia. A következő kép bemutatja, ez a kialakítás.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

A regisztráció kezelése a háttérrendszerből előnyei közé tartozik a regisztrációk címkék módosítása, akkor is, ha a megfelelő alkalmazást az eszközön nem aktív, és az ügyfélalkalmazás hitelesítéséhez a címke hozzáadása a regisztráció előtt.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Példakódot egy értesítési központot, egy kiszolgáló nélküli telepítés használatával történő regisztrálására
Az ügyfél továbbra is lekéri a PNS-leírójának és a vonatkozó telepítési tulajdonságokat a előtt, és hívja egy egyéni API-t a háttérkiszolgálón, amely a regisztráció elvégzéséhez, és engedélyezik a stb címkékkel. A háttérrendszer kihasználhatják a [Notification Hub SDK a háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Is használhatja a javítás módszer használatával a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902) frissítheti a telepítést.

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Példakódot egy értesítési központot egy eszközről egy regisztrációs azonosítójával regisztrálása
Az alkalmazás háttérrendszeréből a regisztrációs alapvető CRUDS műveleteket végezheti el. Példa:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


A háttér egyidejűségi közötti regisztrációs frissítéseket kell kezelni. A Service Bus egyidejű hozzáférések optimista vezérlését regisztrációs Management kínál. A HTTP szinten ez megvalósítása ETag regisztrációs felügyeleti műveletek használatával. Ez a szolgáltatás Microsoft SDKs, amelyek kivételt jelez a frissítés elutasítása párhuzamossági okokból átlátható módon használja. A háttéralkalmazás feladata az ilyen kivételek kezelése, és újra próbálkozik a frissítés, ha szükséges.

