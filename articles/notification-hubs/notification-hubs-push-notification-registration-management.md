---
title: Regisztráció kezelése
description: Ez a témakör bemutatja, hogyan regisztrálhatja az eszközöket az értesítési központokkal a leküldéses értesítések fogadásához.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455367"
---
# <a name="registration-management"></a>Regisztráció kezelése

Ez a témakör bemutatja, hogyan regisztrálhatja az eszközöket az értesítési központokkal a leküldéses értesítések fogadásához. A témakör ismerteti a regisztrációk magas szinten, majd bemutatja a két fő minták eszközök regisztrálása: regisztráció az eszközről közvetlenül az értesítési központba, és regisztráció egy alkalmazás háttérrendszeren keresztül.

## <a name="what-is-device-registration"></a>Mi az eszköz regisztrációja?

Az értesítési központtal történő eszközregisztráció **regisztráció** vagy **telepítés**segítségével történik.

### <a name="registrations"></a>Regisztrációk

A regisztráció társítja a Platform Értesítési Szolgáltatás (PNS) leíróját egy olyan eszközhöz, amely címkékkel és esetleg sablonnal van ellátva. A PNS-leíró lehet ChannelURI, eszköztoken vagy FCM regisztrációs azonosító. A címkék segítségével az értesítéseket a megfelelő eszközfogókhoz irányítják. További információt az [Útválasztás és címkekifejezések című témakörben talál.](notification-hubs-tags-segment-push-message.md) A sablonok a regisztrációnkénti átalakítás megvalósításához használatosak. További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

> [!NOTE]
> Az Azure Notification Hubs eszközönként legfeljebb 60 címkét támogat.

### <a name="installations"></a>Telepítések

A telepítés egy továbbfejlesztett regisztráció, amely magában foglalja a csomag push kapcsolódó tulajdonságokat. Ez a legújabb és legjobb megközelítés az eszközök regisztrálásához. Az ügyféloldali .NET SDK ([Értesítési központ SDK háttérműveletekhez](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) azonban még nem támogatja.  Ez azt jelenti, hogy ha magáról az ügyféleszközről regisztrál, akkor az [Értesítési központok REST API-t](/rest/api/notificationhubs/create-overwrite-installation) kell használnia a telepítések támogatásához. Ha háttérszolgáltatást használ, képesnek kell lennie az [Értesítési központ SDK-t a háttérműveletekhez.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

A telepítés használatának néhány kulcsfontosságú előnye a következő:

- A telepítés létrehozása vagy frissítése teljesen idempotens. Így újra próbálkozhat a duplikált regisztrációkkal kapcsolatos aggodalmak nélkül.
- A telepítési modell egy speciális`$InstallationId:{INSTALLATION_ID}`címkeformátumot ( ) támogat, amely lehetővé teszi az értesítés közvetlen küldését az adott eszközre. Ha például az alkalmazás kódja beállítja `joe93developer` az adott eszköz telepítési azonosítóját, a fejlesztő `$InstallationId:{joe93developer}` megcélozhatja ezt az eszközt, amikor értesítést küld a címkének. Ez lehetővé teszi, hogy egy adott eszközt anélkül célozzon meg, hogy további kódolást kellene végeznie.
- A telepítések használata lehetővé teszi a részleges regisztrációs frissítések et is. A telepítés részleges frissítését a [JSON-Patch szabványt](https://tools.ietf.org/html/rfc6902)alkalmazó PATCH módszerrel kell megadni. Ez akkor hasznos, ha frissíteni szeretné a címkéket a regisztráción. Nem kell lehúznia a teljes regisztrációt, majd újra küldenie az összes korábbi címkét.

A telepítés a következő tulajdonságokat tartalmazhatja. A telepítési tulajdonságok teljes listáját a [Telepítés létrehozása vagy felülírása REST API-val vagy](/rest/api/notificationhubs/create-overwrite-installation) telepítési [tulajdonságokkal című témakörben található.](/dotnet/api/microsoft.azure.notificationhubs.installation)

```json
// Example installation format to show some supported properties
{
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
```

> [!NOTE]
> Alapértelmezés szerint a regisztrációk és a telepítések nem járnak le.

A regisztrációknak és telepítéseknek minden eszközhez/csatornához érvényes PNS-leírót kell tartalmazniuk. Mivel a PNS-leírók csak az eszközön lévő ügyfélalkalmazásban szerezhetők be, az egyik minta az, hogy közvetlenül regisztrálaz adott eszközön az ügyfélalkalmazással. Másrészt a címkékkel kapcsolatos biztonsági szempontok és üzleti logika szükségessé teheti az eszközregisztráció kezelését az alkalmazás háttérrendszerében.

> [!NOTE]
> A telepítésAPI nem támogatja a Baidu szolgáltatást (bár a Registrations API nem). 

### <a name="templates"></a>Sablonok

Ha [sablonokat](notification-hubs-templates-cross-platform-push-messages.md)szeretne használni, az eszköz telepítése az eszközhöz társított összes sablont JSON formátumban tartalmazza (lásd a fenti mintát). A sablonnevek segítségével különböző sablonokat céloznak meg ugyanahhoz az eszközhöz.

Minden sablonnév egy sablontörzshöz és egy választható címkekészlethez van rendelve. Ezenkívül minden platform további sablontulajdonságokkal is rendelkezhet. A Windows Áruház (WNS használatával) és a Windows Phone 8 (MPNS használatával) esetén a sablon egy további fejléckészlete is része lehet. APN-ek esetén a lejárati tulajdonságot állandóra vagy sablonkifejezésre állíthatja be. A telepítési tulajdonságok teljes listáját [lásd: Telepítés létrehozása vagy felülírása REST témakörben.](/rest/api/notificationhubs/create-overwrite-installation)

### <a name="secondary-tiles-for-windows-store-apps"></a>Másodlagos csempék A Windows Áruházbeli alkalmazások

Windows Áruházbeli ügyfélalkalmazások esetén az értesítések másodlagos csempékre küldése megegyezik az elsődleges csempére való elküldésükkel. Ez a telepítések is támogatott. A másodlagos csempék egy másik ChannelUri-val rendelkeznek, amelyet az ügyfélalkalmazás SDK-ja transzparens módon kezel.

A SecondaryTiles szótár ugyanazt a TileId azonosítót használja, amely a SecondaryTiles objektum létrehozásához használatos a Windows Áruházbeli alkalmazásban. Csakúgy, mint az elsődleges ChannelUri, ChannelUris másodlagos csempe változhat bármikor. Annak érdekében, hogy a telepítéseket az értesítési központban frissítse, az eszköznek frissítenie kell őket a másodlagos csempék aktuális ChannelUris-aival.

## <a name="registration-management-from-the-device"></a>Regisztráció kezelés az eszközről

Az ügyfélalkalmazásokeszköz-regisztrációjának kezelése során a háttérrendszer csak az értesítések küldéséért felelős. Az ügyfélalkalmazások naprakészen tartják a PNS-kezelőket, és regisztrálják a címkéket. Az alábbi képen ez a minta látható.

![Regisztráció eszközről](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Az eszköz először lekéri a PNS-leírót a PNS-ből, majd közvetlenül regisztrálja az értesítési központot. A regisztráció sikeres befejezése után az alkalmazás háttérrendszere értesítést küldhet a regisztrációt célzó. Az értesítések küldéséről az [Útválasztás és címkekifejezések című](notification-hubs-tags-segment-push-message.md)témakörben talál további információt.

Ebben az esetben csak figyelési jogokat használ az értesítési központok eléréséhez az eszközről. További információ: [Security](notification-hubs-push-notification-security.md).

Az eszközről történő regisztráció a legegyszerűbb módszer, de van néhány hátránya:

- Az ügyfélalkalmazások csak akkor frissíthetik a címkéket, ha az alkalmazás aktív. Ha például egy felhasználókét olyan eszközzel rendelkezik, amelyek a sportcsapatokhoz kapcsolódó címkéket regisztrálnak, amikor az első eszköz regisztrál egy további címkére (például Seahawks), a második eszköz nem kapja meg a Seahawks-ról szóló értesítéseket, amíg a második eszközön lévő alkalmazás másodszor is végrehajtják. Általánosabban fogalmazva, ha a címkéket több eszköz is érinti, a címkék háttérterületről történő kezelése kívánatos lehetőség.
- Mivel az alkalmazások feltörhetők, a regisztráció bizonyos címkékre való biztosítása extra gondossággal jár el, amint azt a [Biztonság](notification-hubs-push-notification-security.md)című cikk ismerteti.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Példa kód, amelyet egy értesítési központtal regisztrálhat egy telepítést használó eszközről

Jelenleg ez csak az [Értesítési központok REST API-jával](/rest/api/notificationhubs/create-overwrite-installation)támogatott.

A PATCH metódust a [JSON-Patch szabvány](https://tools.ietf.org/html/rfc6902) használatával is használhatja a telepítés frissítéséhez.

```csharp
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

// If we have not stored an installation ID in application data, create and store as application data.
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
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Példa kód, amelyet egy értesítési központtal regisztrálhat egy eszközről egy regisztrációt használó eszközről

Ezek a módszerek létrehoznak vagy frissítenek egy regisztrációt arra az eszközre, amelyen meghívják őket. Ez azt jelenti, hogy a leíró vagy a címkék frissítéséhez felül kell írnia a teljes regisztrációt. Ne feledje, hogy a regisztrációk átmenetiek, ezért mindig rendelkeznie kell egy megbízható tárolóval az aktuális címkékkel, amelyeket egy adott eszköznek szüksége van.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
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
```

## <a name="registration-management-from-a-backend"></a>Regisztráció kezelése háttérrendszerből

A háttérből történő regisztrációk kezeléséhez további kódot kell írni. Az alkalmazásnak az eszközről minden alkalommal meg kell adnia a frissített PNS-leírót a háttérrendszernek az alkalmazás indításakor (címkékkel és sablonokkal együtt), és a háttérrendszernek frissítenie kell ezt a leírót az értesítési központban. Az alábbi képen ez a látványterv látható.

![Regisztráció kezelése](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

A háttérrendszerből történő regisztrációk kezelésének előnyei közé tartozik a címkék módosítása a regisztrációkhoz akkor is, ha az eszköz megfelelő alkalmazása inaktív, és hitelesítheti az ügyfélalkalmazást, mielőtt hozzáadná a címkét a regisztrációhoz.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Példa kód, amelyet egy értesítési központtal regisztrálhat egy háttérrendszerből egy telepítés használatával

Az ügyféleszköz továbbra is megkapja a PNS-leíróját és a megfelelő telepítési tulajdonságait, mint korábban, és meghívja az egyéni API-t a háttérrendszeren, amely képes végrehajtani a regisztrációt és engedélyezni a címkéket stb. A háttérrendszer használhatja az [értesítési központ SDK a háttér-műveletek.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

A PATCH metódust a [JSON-Patch szabvány](https://tools.ietf.org/html/rfc6902) használatával is használhatja a telepítés frissítéséhez.

```csharp
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
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
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
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Példa kód, amelyet egy értesítési központtal regisztrálhat egy eszközről regisztrációs azonosító használatával

Az alkalmazás háttérszámítógépéről alapvető CRUDS-műveleteket hajthat végre a regisztrációkon. Példa:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

A háttérrendszernek kezelnie kell a regisztrációs frissítések közötti egyidejűséget. A Service Bus optimista egyidejűség-ellenőrzést biztosít a regisztráció kezeléséhez. A HTTP szinten ez az ETag használatával valósul meg a regisztrációs kezelési műveletekben. Ezt a szolgáltatást a Microsoft SDK-k transzparens módon használják, amelyek kivételt okoznak, ha egy frissítést egyidejűségi okokból elutasítanak. Az alkalmazás háttérrendszer felelős a kivételek kezeléséért és szükség esetén a frissítés újbóli megkísérléséért.
