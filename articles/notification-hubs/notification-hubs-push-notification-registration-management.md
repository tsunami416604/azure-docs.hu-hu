---
title: Regisztrációkezelés
description: Ez a témakör ismerteti az eszközök regisztrálása a notification hubs használatával leküldéses értesítések fogadásához.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 01/23/2019
ms.openlocfilehash: 028e9a2973ed524037f6415d9e802f947458cfa6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166769"
---
# <a name="registration-management"></a>Regisztrációkezelés

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti az eszközök regisztrálása a notification hubs használatával leküldéses értesítések fogadásához. A témakör ismerteti a regisztrációk magas szinten, majd vezet be az eszközök regisztrálásához a két fő minták: közvetlenül az értesítési központban regisztrálása az eszközről, és regisztrálnia kell az alkalmazás háttérrendszerével keresztül.

## <a name="what-is-device-registration"></a>Mi az az eszköz regisztrálása

Eszközregisztráció az egy értesítési központ használatával valósítható meg a **regisztrációs** vagy **telepítési**.

### <a name="registrations"></a>Regisztrációk

Egy regisztrációs a Platformértesítési szolgáltatás (PNS) leíró eszközhöz társítja, címkék, és esetleg egy sablont. A PNS-leíró egy amiatt, eszköztoken vagy FCM-regisztrációs azonosító lehet. Címkék segítségével értesítések átirányítása az eszközleírók megfelelő készletét. További információkért lásd: [az Útválasztás és címke kifejezések](notification-hubs-tags-segment-push-message.md). Sablonok regisztrációs átalakítás végrehajtásához használnak. További információért lásd a [Sablonok](notification-hubs-templates-cross-platform-push-messages.md) szakaszt.

> [!NOTE]
> Az Azure Notification Hubs regisztrációs / 60 címkék legfeljebb támogat.

### <a name="installations"></a>Telepítések

Egy továbbfejlesztett egy telepítés, amely tartalmaz egy leküldéses tulajdonságcsomagjait regisztrációs kapcsolódó tulajdonságok. Az eszközök regisztrálása a legújabb és legjobb módja. Azonban nem támogatott az ügyféloldali .NET SDK-ban ([Notification Hub SDK háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) kezdődően még.  Ez azt jelenti, ha regisztrál a magáról az ügyféleszközről, meg kellene használnia a [Notification Hubs – REST API](https://msdn.microsoft.com/library/mt621153.aspx) megközelítés telepítések támogatásához. Háttérszolgáltatás használatakor kell tudni használni [Notification Hub SDK háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Fő előnye, hogy telepítések az alábbiak:

- Teljes körűen idempotensnek létrehozása vagy frissítése egy telepítés. Ezért is újraindítható bármely duplikált regisztrációk aggodalmak nélkül.
- A telepítési modell megkönnyíti az egyes leküldések - eszközre célzó tegye. A rendszer a címke **"$InstallationId: [installationId]"** automatikusan hozzáadott minden egyes-alapú telepítés a regisztráció. Ezért ezt a címkét egy adott eszköz célként ehhez további kódírás nélkül történő küldés meghívhatja.
- Telepítés használatával is lehetővé teszi, hogy részleges regisztrációfrissítés. A részleges frissítési telepítés kérése történik a PATCH módszer használatával a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902). Ez akkor hasznos, ha frissíti a címkéket a regisztrációt. Nem kell kérje le a teljes regisztrációját, és küldje el újra a korábbi címkéket.

Telepítés az alábbi tulajdonságokat is tartalmazzák. A telepítési tulajdonságok teljes listáját lásd: [létrehozása vagy a telepítés felülírja a REST API-val](https://msdn.microsoft.com/library/azure/mt621153.aspx) vagy [telepítési tulajdonságok](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

```javascript
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
> Alapértelmezés szerint a regisztrációk és a telepítés ne járjanak le.

Regisztráció és a telepítés minden egyes eszköz vagy csatorna érvénytelen PNS-leíró kell tartalmaznia. Egy ügyfélalkalmazás az eszközön a PNS-leírók regisztrálásáért csak szerezhető be, mert egy egyik közvetlenül az adott eszközön az ügyféloldali alkalmazás regisztrálásához. Másrészt biztonsági megfontolásokra, illetve a címkék kapcsolódó üzleti logika szükség lehet, hogy az alkalmazás háttér az eszközregisztráció kezelése.

### <a name="templates"></a>Sablonok

Ha a használni kívánt [sablonok](notification-hubs-templates-cross-platform-push-messages.md), az eszköz telepítése is tartalmazza az összes sablon egy JSON-eszköz társított formázása (lásd a fenti példában). A nevek segítségével a cél különböző sablonok ugyanazon az eszközön.

Minden sablon neve leképez egy sablon törzsét és címkék nem kötelező megadni. Ezen felül a platformonként további sablontulajdonságok rendelkezhet. Windows Store (WNS használatával) és a Windows Phone 8 (MPNS használatával) egy további készletet a fejlécek a sablon része lehet. Esetén APNs beállíthat egy lejárati tulajdonságot egy konstans, vagy egy kifejezés. Teljes listáját a telepítési tulajdonságok lásd [létrehozása vagy a telepítés felülírja a REST segítségével](https://msdn.microsoft.com/library/azure/mt621153.aspx) témakör.

### <a name="secondary-tiles-for-windows-store-apps"></a>A Windows Store Apps másodlagos Csempék

Az ügyfélalkalmazások Windows Store értesítések küldése a másodlagos csempék megegyezik a elküldi azokat a közül. A telepítések is támogatják. Másodlagos csempénél van egy másik amiatt, és az SDK az ügyfélalkalmazás az átlátható módon kezeli.

A SecondaryTiles szótárban az azonos TileId, amellyel a SecondaryTiles objektum létrehozása a Windows Store App használ.
Csakúgy, mint az elsődleges amiatt, a másodlagos csempék ChannelUris bármikor módosíthatja. Annak érdekében, hogy ne a telepítések az értesítési központ frissítése, az eszköz frissítenie kell azokat a az aktuális ChannelUris a másodlagos csempék.

## <a name="registration-management-from-the-device"></a>Az eszközről regisztrációkezelés

Az eszköz regisztrálása az ügyfélalkalmazások kezelésekor a háttérrendszer feladata csak az értesítések küldéséhez. Ügyfélalkalmazások tartsa naprakészen a PNS-leírók regisztrálásáért, és regisztrálja a címkéket. A következő kép szemlélteti ezt a mintát.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Az eszköz először a PNS-leíró lekéri a pns-sel, majd közvetlenül regisztrál az értesítési központban. Miután a regisztráció sikeres, az alkalmazási háttérrendszer a regisztrációs célzó értesítést küldhet. Értesítések küldése kapcsolatos további információkért lásd: [az Útválasztás és címke kifejezések](notification-hubs-tags-segment-push-message.md).

Ebben az esetben, segítségével csak jogosultággal hozzáférhet a notification hubs használatával az eszközről. További információkért lásd: [biztonsági](notification-hubs-push-notification-security.md).

Az eszköz regisztrálása a legegyszerűbb módszer, de azt hátrányokkal jár:

- Egy ügyfélalkalmazás csak frissítheti a címkéket, ha az alkalmazás aktív. Például ha egy felhasználó két eszközök, amelyeket regisztrálni kapcsolatos sport csapatok, amikor az első eszköz regisztrálja magát egy további címke (például a Seahawks) címkéket, a második eszköz nem kap az értesítések a Seahawks mindaddig, amíg az alkalmazás a második eszközön a második alkalommal hajtott végre. Általában a címkék vannak hatással a több eszközre, amikor a háttérrendszerből címkék kezelése beállítás kívánatos.
- Alkalmazások is el lesznek, mivel biztonságossá tétele a meghatározott címkék regisztráció igényel körültekintően, a "címke szintű biztonsági." szakaszban leírtak szerint

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Regisztrálhat az egy értesítési központ segítségével telepítve eszközről kódpéldákat

Jelenleg ez csak támogatott használatával a [Notification Hubs – REST API](https://msdn.microsoft.com/library/mt621153.aspx).

A PATCH módszer használatával is használhatja a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902) frissítheti a telepítést.

```
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

// If we have not stored an installation id in application data, create and store as application data.
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Regisztrálhat az egy értesítési központ használatával egy regisztrációs eszközről kódpéldákat

Ezek a metódusok létrehozása vagy frissítése a neve, amelyen az eszköz regisztrálását. Ez azt jelenti, hogy annak érdekében, hogy a leíró vagy címkék frissítése, hogy felül kell írnia a teljes regisztráció. Ne feledje, hogy a regisztrációk átmeneti, így mindig rendelkeznie kell egy megbízható tárolóban, a jelenlegi címkékkel, amely egy adott eszköznek ahhoz van szüksége.

```
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
```

## <a name="registration-management-from-a-backend"></a>Regisztrációkezelés a háttérrendszerből

Regisztrációk kezelése – a háttéralkalmazás további kód írása szükséges. Az alkalmazást az eszközről, a háttérkiszolgáló kezeli a frissített PNS minden alkalommal, amikor az alkalmazás elindul (valamint a címkékkel és sablonokkal), és a háttérrendszer frissítenie kell az értesítési központ ezt az azonosítót kell megadnia. A következő kép azt mutatja be, ezzel a kialakítással.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

A regisztrációk kezelése – a háttéralkalmazás előnyei közé tartozik a képes regisztrációk címkéket módosíthatja, még akkor is, ha a megfelelő alkalmazást az eszközön nem aktív, és a címke hozzáadása a regisztráció előtt az ügyfélalkalmazás hitelesítéséhez.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Példakód szeretne regisztrálni egy értesítési központ segítségével telepítve a háttérrendszerből

Az ügyfél továbbra is lekéri a PNS-leíró és a megfelelő telepítési tulajdonságokat a előtt, és meghívja az egyéni API-k a háttérkiszolgálón is hajtsa végre a regisztráció, és engedélyezze a címkék stb. A háttérrendszer kihasználhatják a [Notification Hub SDK háttérbeli műveletek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

A PATCH módszer használatával is használhatja a [JSON-javítás standard](https://tools.ietf.org/html/rfc6902) frissítheti a telepítést.

```
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Regisztrálhat az egy értesítési központ egy eszközről egy regisztrációs Azonosítót használó kódpéldákat

Az alkalmazás háttérrendszeréből alapszintű CRUDS műveleteket végezhet a regisztrációk. Példa:

```
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
```

A háttérrendszer között regisztrációfrissítés egyidejűségi kell kezelnie. A Service Bus regisztrációkezelés az optimista egyidejűséget lehetővé teszi. A HTTP-szinten ez az valósítja meg a regisztrációs ETag használatát. Ez a funkció transzparens módon használja a Microsoft-SDKs, ami kivételt jelenít meg, ha egy frissítés párhuzamosság miatt elutasítva. A háttéralkalmazás felelős az ilyen kivételek kezelése, és újra próbálkozik a frissítés, ha szükséges.
