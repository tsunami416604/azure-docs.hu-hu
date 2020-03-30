---
title: Az eldobott értesítések diagnosztizálása az Azure Értesítési központokban
description: Ismerje meg, hogyan diagnosztizálhatja az eldobott értesítésekkel kapcsolatos gyakori problémákat az Azure Értesítési központokban.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657583"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Az eldobott értesítések diagnosztizálása az Azure Értesítési központokban

Az Azure Értesítési központok gyakori kérdése, hogyan háríthatja el a hibaelhárítást, ha egy alkalmazásból származó értesítések nem jelennek meg az ügyféleszközökön. Az ügyfelek tudni szeretnék, hogy hol és miért ejtették az értesítéseket, és hogyan oldják meg a problémát. Ez a cikk azonosítja, hogy miért lehet, hogy az értesítések megszakadnak, vagy nem kapnak az eszközök. Azt is elmagyarázza, hogyan lehet meghatározni a kiváltó oka.

Fontos, hogy először is ismerje meg, hogyan értesítési központok leküldéseértesítéseket az eszközre.

![Értesítési központok architektúrája][0]

Egy tipikus küldési értesítési folyamat, az üzenet az *alkalmazás háttérrendszeréről* az értesítési központok. Az Értesítési központok feldolgozza az összes regisztrációt. Figyelembe veszi a konfigurált címkéket és címkekifejezéseket a célok meghatározásához. A célok azok a regisztrációk, amelyeknek meg kell kapniuk a leküldéses értesítést. Ezek a regisztrációk bármelyik támogatott platformunkra kiterjedhetnek: Android, Baidu (Android-eszközök Kínában), Fire OS (Amazon) iOS, Windows és Windows Phone.

A létrehozott célok at, Notification Hubs leküldéses értesítéseket a *leküldéses értesítési szolgáltatás* az eszköz platform. Ilyen például az Apple Push Notification service (APN) iOS és macOS, valamint a Firebase Cloud Messaging (FCM) androidos eszközökön. Az értesítési központok leküldi a regisztrációk több kötegére felosztott értesítéseket. Az Azure Portalon beállított hitelesítő adatok alapján az **Értesítési központ konfigurálása**csoportban hitelesíti magát a megfelelő leküldéses értesítési szolgáltatással. A leküldéses értesítési szolgáltatás ezután továbbítja az értesítéseket a megfelelő *ügyféleszközökre*.

Az értesítés kézbesítésének utolsó szakasza a platform leküldéses értesítési szolgáltatása és az eszköz között van. Az értesítés kézbesítése sikertelen lehet a leküldéses értesítési folyamat négy szakaszának bármelyikében (ügyfél, alkalmazás háttérrendszer, értesítési központok és a platform leküldéses értesítési szolgáltatása). Az Értesítési központok architektúrájáról az [Értesítési központok – áttekintés című témakörben olvashat bővebben.]

Az értesítések kézbesítése sikertelen lehet a kezdeti teszt/előkészítési fázisban. Az eldobott értesítések ebben a szakaszban konfigurációs problémát jelezhetnek. Ha az értesítések nem lesznek érvényben éles környezetben, előfordulhat, hogy az értesítések egy része vagy egésze elfordul. Ebben az esetben egy mélyebb alkalmazás- vagy üzenetkezelési mintaprobléma jelen áll fenn.

A következő szakasz azokat a forgatókönyveket vizsgálja, amelyekben az értesítések eldobásra kerülhetnek, a gyakoritól a ritkaig.

## <a name="notification-hubs-misconfiguration"></a>Az értesítési központok helytelen beállítása

Az értesítések küldéséhez a megfelelő leküldéses értesítési szolgáltatás, Notification Hubs hitelesítenie kell magát az alkalmazás környezetében. Létre kell hoznia egy fejlesztői fiókot a célplatform értesítési szolgáltatásával (Microsoft, Apple, Google stb.). Ezután regisztrálnia kell az alkalmazást az operációs rendszerrel, ahol egy jogkivonatot vagy kulcsot kap, amelyet a cél PNS-sel való munkához használ.

Platform hitelesítő adatokat kell hozzáadnia az Azure Portalhoz. Ha nincsenek értesítések az eszköz elérésekor, az első lépés annak biztosítása, hogy a megfelelő hitelesítő adatok konfigurálva legyenek az értesítési központokban. A hitelesítő adatoknak meg kell egyezniük a platformspecifikus fejlesztői fiók alatt létrehozott alkalmazással.

A folyamat végrehajtásához szükséges részletes útmutatásért olvassa el az Azure Értesítési központok – Első lépések az [Azure Értesítési központokkal (Azure Notification Hubs) témakört.]

Az alábbiakban néhány gyakori helytelen konfigurációt kell ellenőrizni:

### <a name="notification-hub-name-location"></a>Értesítési központ nevének helye

Győződjön meg arról, hogy az értesítési központ neve (elírás nélkül) megegyezik az alábbi helyeken:

* Hol regisztrál az ügyféltől?
* Hol küld értesítéseket a háttérből?
* A leküldéses értesítési szolgáltatás hitelesítő adatainak konfigurálása

Győződjön meg arról, hogy a megfelelő megosztott hozzáférési aláírás konfigurációs karakterláncait használja az ügyfélen és az alkalmazás háttérrendszerén. Általában **a DefaultListenSharedAccessSignature programot** kell használnia az ügyfélen, a **DefaultFullSharedAccessSignature-et** pedig az alkalmazás háttérrendszerén. Ez engedélyeket ad az értesítési központoknak küldött értesítések küldésére.

### <a name="apn-configuration"></a>APN-konfiguráció

Két különböző hubot kell karbantartania: az egyiket éles környezetben, a másikat tesztelésre. A sandbox környezetben használt tanúsítványt egy külön hubra kell feltöltenie, mint az éles környezetben használt tanúsítványt/hubot. Ne próbáljon meg különböző típusú tanúsítványokat feltölteni ugyanabba a hubba. Értesítési hibákat fog okozni.

Ha véletlenül különböző típusú tanúsítványokat tölt fel ugyanabba a hubba, törölje a hubot, és kezdje újra egy új központtal. Ha valamilyen okból nem tudja törölni a hubot, legalább törölnie kell az összes meglévő regisztrációt a hubról.

### <a name="fcm-configuration"></a>FCM-konfiguráció

1. Győződjön meg arról, hogy a Firebase-ből beszerzett *kiszolgálókulcs* megegyezik az Azure Portalon regisztrált kiszolgálókulnival.

   ![Firebase kiszolgálókulcs][3]

2. Győződjön meg arról, hogy konfigurálta a **Project ID-t** az ügyfélen. A **Project ID** értékét a Firebase irányítópultjáról szerezheti be.

   ![Firebase projektazonosító][1]

## <a name="application-issues"></a>Alkalmazási problémák

### <a name="tags-and-tag-expressions"></a>Címkék és címkekifejezések

Ha címkéket vagy címkekifejezéseket használsz a célközönség szegmentálására, lehetséges, hogy az értesítés elküldésekor nem talál célpontot. Ez a hiba a küldési hívásban megadott címkéken vagy címkekifejezéseken alapul.

Tekintse át a regisztrációkat, és győződjön meg arról, hogy a címkék egyeznek, amikor értesítést küld. Ezután ellenőrizze az értesítési nyugtát csak a regisztrációval rendelkező ügyfelektől.

Tegyük fel például, hogy az értesítési központokkal történő összes regisztrációja a "Politika" címkét használja. Ha ezután értesítést küld a "Sport" címkével, az értesítést nem küldi el semmilyen eszközre. Egy összetett eset tartalmazhat címkekifejezéseket, ahol a "Tag A" *vagy* a "B címke" használatával regisztrált, de ön az "A && Tag B" kifejezést célozta meg. Az öndiagnosztizálás tippek szakasz a cikk későbbi részében bemutatja, hogyan lehet áttekinteni a regisztrációk és a címkéket.

### <a name="template-issues"></a>Sablonnal kapcsolatos problémák

Sablonok használata esetén győződjön meg arról, hogy betartja a Sablonok című részben ismertetett [irányelveket.]

### <a name="invalid-registrations"></a>Érvénytelen regisztrációk

Ha az értesítési központ megfelelően van konfigurálva, és a címkék vagy címkekifejezések helyesen vannak használva, a rendszer érvényes célokat talál. Ezeket a célokat értesítéseket kell küldeni. Az értesítési központok ezután több feldolgozási köteget indít el párhuzamosan. Minden köteg üzeneteket küld a regisztrációk egy készletének.

> [!NOTE]
> Mivel az Értesítési központok a kötegeket párhuzamosan dolgozza fel, az értesítések kézbesítésének sorrendje nem garantált.

Az értesítési központok egy "legegyszeri" üzenetkézbesítési modellre vannak optimalizálva. Kísérletünk van a deduplikációra, hogy egyetlen értesítés se kézbesítsen többször egy eszközre. A regisztrációk ellenőrzik, hogy eszközazonosítónként csak egy üzenet kerüljön elküldésre a leküldéses értesítési szolgáltatásnak való küldés előtt.

Minden köteg et küld a leküldéses értesítési szolgáltatás, amely viszont elfogadja és érvényesíti a regisztrációkat. A folyamat során lehetséges, hogy a leküldéses értesítési szolgáltatás hibát észlel egy vagy több regisztrációval egy kötegben. A leküldéses értesítési szolgáltatás ezután hibát ad vissza az értesítési központoknak, és a folyamat leáll. A leküldéses értesítési szolgáltatás teljesen eldobja a köteget. Ez különösen igaz a TCP-adatfolyam-protokollt használó APN-ekre.

Ebben az esetben a hibás regisztráció törlődik az adatbázisból. Ezután újra megpróbáljuk az értesítési kézbesítést az adott kötegben lévő többi eszközre vonatkozóan.

Ha további hibainformációkat szeretne kapni a regisztráció sikertelen kézbesítési kísérletéről, használhatja az Értesítési központok REST API-jait [üzenetenként telemetria: Értesítési üzenet telemetriai adatainak és](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry) [PNS-visszajelzésének beolvasása.](https://msdn.microsoft.com/library/azure/mt705560.aspx) A mintakódot lásd a REST küldése példa című [témakörben.](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)

## <a name="push-notification-service-issues"></a>Leküldéses értesítési szolgáltatás sal kapcsolatos problémák

Miután a leküldéses értesítési szolgáltatás megkapja az értesítést, az értesítést kézbesíti az eszköznek. Ezen a ponton az értesítési központok nem rendelkeznek az értesítés kézbesítésének az eszközre történő kézbesítése felett.

Mivel a platformértesítési szolgáltatások robusztusak, az értesítések néhány másodpercen belül elérik az eszközöket. Ha a leküldéses értesítési szolgáltatás szabályozása, notification hubs alkalmaz egy exponenciális back-off stratégia. Ha a leküldéses értesítési szolgáltatás 30 percig nem érhető el, van egy szabályzat, amely lejár, és véglegesen eldobja az üzeneteket.

Ha egy leküldéses értesítési szolgáltatás értesítést próbál kézbesíteni, de az eszköz offline állapotban van, az értesítést a leküldéses értesítési szolgáltatás tárolja. Csak korlátozott ideig tárolják. Az értesítés akkor érkezik az eszközre, amikor az eszköz elérhetővé válik.

Minden alkalmazás csak egy legutóbbi értesítést tárol. Ha egy eszköz offline állapotban több értesítést küld, minden új értesítés az utolsó értesítés elvetése miatt történik. Csak a legújabb értesítés *megtartása* az APN-ek ben és az *FCM-ben összecsukású* nak van nevezve. (Az FCM összecsukható kulcsot használ.) Ha az eszköz hosszú ideig offline állapotban marad, az eszközhöz tárolt értesítések elvesznek. További információt az [APN-ek áttekintése] és [az FCM-üzenetek – kapcsolat című témakörben talál.]

Az értesítési központok segítségével egy összeolvadási kulcsot adhat át egy HTTP-fejlécen keresztül az általános SendNotification API használatával. A . `SendNotificationAsync` A SendNotification API http-fejléceket is vesz, amelyek a megfelelő leküldéses értesítési szolgáltatásnak adott adatoknak megfelelően kerülnek átadásra.

## <a name="self-diagnosis-tips"></a>Öndiagnosztikai tippek

Az értesítések értesítési központokban eldobott értesítések kiváltó okának diagnosztizálására az alábbiakat az eldobott értesítések alapvető okainak diagnosztizálására szolgál.

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése

#### <a name="push-notification-service-developer-portal"></a>Leküldéses értesítési szolgáltatás fejlesztői portálja

Ellenőrizze a hitelesítő adatokat a megfelelő leküldéses értesítési szolgáltatás fejlesztői portálján (APNs, FCM, Windows Értesítési szolgáltatás és így tovább). További információ: [Oktatóanyag: Értesítések küldése univerzális Windows Platform-alkalmazásoknak az Azure Értesítési központok használatával című témakörben.](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)

#### <a name="azure-portal"></a>Azure portál

A hitelesítő adatok áttekintéséhez és egyeztetéséhez a leküldéses értesítési szolgáltatás fejlesztői portálján kapott hitelesítő adatokkal, nyissa meg az Azure Portal **Hozzáférési szabályzatok** lapján.

![Az Azure Portal Access irányelvei][4]

### <a name="verify-registrations"></a>Regisztrációk ellenőrzése

#### <a name="visual-studio"></a>Visual Studio

A Visual Studio-ban a Kiszolgálón keresztül csatlakozhat az Azure-hoz több Azure-szolgáltatás, köztük az értesítési központok megtekintéséhez és kezeléséhez. Ez a parancsikon elsősorban a fejlesztési/tesztelési környezetben hasznos.

![Visual Studio Server Intéző][9]

![Server Explorer (Kiszolgálókezelő)](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Megtekintheti és kezelheti az összes regisztrációt a központban. A regisztrációk platform, natív vagy sablonregisztráció, címke, leküldéses értesítési szolgáltatás azonosítója, regisztrációs azonosító és lejárati dátum szerint kategorizálhatók. Ezen az oldalon is szerkesztheti a regisztrációt. Különösen hasznos a címkék szerkesztéséhez.

Kattintson a jobb gombbal az értesítési központra a **Kiszolgálókezelőben,** és válassza **a Diagnosztizálás parancsot.** 

![Visual Studio Server Explorer: Diagnosztizálás menü](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

A következő oldal jelenik meg:

![Visual Studio: Diagnosztizálás lap](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Váltás az **Eszközregisztrációk** lapra:

![Visual Studio: Eszközregisztrációk](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

A **Küldés tesztlap** segítségével tesztértesítési üzenetet küldhet:

![Visual Studio: Teszt küldés](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> A Visual Studio segítségével csak a fejlesztés/tesztelés során és korlátozott számú regisztráció val szerkeszti a regisztrációkat. Ha tömegesen kell módosítania a regisztrációkat, fontolja meg a [Regisztrációk](https://msdn.microsoft.com/library/dn790624.aspx)tömeges exportálása és módosítása című részben leírt exportálási és importálási regisztrációs funkciót.

#### <a name="service-bus-explorer"></a>Service Bus Explorer

Sok ügyfél használja a [Service Bus Explorert](https://github.com/paolosalvatori/ServiceBusExplorer) az értesítési központok megtekintéséhez és kezeléséhez. A Service Bus Explorer egy nyílt forráskódú projekt. 

### <a name="verify-message-notifications"></a>Üzenetértesítések ellenőrzése

#### <a name="azure-portal"></a>Azure portál

Ha a szolgáltatás biztonsági szolgálatának futtatása nélkül szeretne tesztértesítést küldeni az ügyfeleknek, a **SUPPORT + TROUBLESHOOTING**csoportban válassza **a Küldés teszt**lehetőséget.

![Küldés tesztelése funkció az Azure-ban][7]

#### <a name="visual-studio"></a>Visual Studio

Tesztértesítéseket is küldhet a Visual Studio-ból.

![Küldés immár a Visual Studio-ban][10]

Az értesítési központok Visual Studio Server Explorer böngészővel való használatáról az alábbi cikkekben talál további információt:

* [Értesítési központok eszközregisztrációinak megtekintése](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Mély merülés: Visual Studio 2013 Update 2 RC és Az Azure SDK 2.3]
* [A Visual Studio 2013 3- as és Az Azure SDK 2.4-es frissítésének bejelentése]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Hibakeresés sikertelen értesítések és felülvizsgálati értesítés eredménye

#### <a name="enabletestsend-property"></a>EnableTestSend tulajdonság

Amikor értesítést küld az Értesítési központokon keresztül, az értesítés kezdetben várólistára kerül. Az értesítési központok meghatározzák a megfelelő célokat, majd elküldik az értesítést a leküldéses értesítési szolgáltatásnak. Ha a REST API-t vagy bármely ügyfél SDK-t használ, a küldési hívás visszaadása csak azt jelenti, hogy az üzenet várólistára kerül az értesítési központokkal. Nem nyújt betekintést abba, hogy mi történt, amikor az értesítési központok végül elküldte az értesítést a leküldéses értesítési szolgáltatásnak.

Ha az értesítés nem érkezik meg az ügyféleszközre, hiba léphetett fel, amikor az Értesítési központok megpróbálták kézbesíteni azt a leküldéses értesítési szolgáltatásnak. Előfordulhat például, hogy a tartalom mérete meghaladja a leküldéses értesítési szolgáltatás által engedélyezett maximális értéket, vagy az értesítési központokban konfigurált hitelesítő adatok érvénytelenek lehetnek.

Az EnableTestSend tulajdonság ot használhatja az [EnableTestSend] tulajdonság használatával. Ez a tulajdonság automatikusan engedélyezve van, amikor tesztüzeneteket küld a portálról vagy a Visual Studio-ügyfélprogramból. Ezzel a tulajdonsággal részletes hibakeresési információkat, valamint API-kon keresztül is megtekintheti. Jelenleg használhatja a .NET SDK.Currently, you can use it in the .NET SDK. Végül minden ügyfél SDK-hoz hozzálesz adva.

Ha a `EnableTestSend` tulajdonságot a REST-hívással szeretné használni, fűzze hozzá a *teszt* nevű lekérdezési karakterlánc-paramétert a küldési hívás végéhez. Példa:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Példa .NET SDK

Íme egy példa a .NET SDK használatával natív előugró (bejelentési) értesítés küldésére:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Végén a végrehajtás, `result.State` egyszerűen `Enqueued`kimondja . Az eredmények nem nyújtanak betekintést abba, hogy mi történt a leküldéses értesítéssel.

Ezután használhatja `EnableTestSend` a Logikai tulajdonságot. Használja `EnableTestSend` a tulajdonságot `NotificationHubClient` inicializálás közben, hogy részletes állapotot kapjon a leküldéses értesítési szolgáltatás hibáiról, amelyek az értesítés elküldésekor fordulnak elő. A küldési hívás visszatéréséhez több időt vesz igénybe, mivel először értesítési központokra van szüksége az értesítés kézbesítéséhez a leküldéses értesítési szolgáltatásnak.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Példa kimenet

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ez az üzenet azt jelzi, hogy az értesítési központokban konfigurált hitelesítő adatok érvénytelenek, vagy hogy probléma van a hubregisztrációival. Törölje ezt a regisztrációt, és hagyja, hogy az ügyfél újra létrehozza a regisztrációt az üzenet elküldése előtt.

> [!NOTE]
> A `EnableTestSend` szálláshely használata erősen szabályozott. Ezt a beállítást csak fejlesztési/tesztelési környezetben és korlátozott számú regisztrációval használja. A hibakeresési értesítéseket csak 10 eszközre küldi a rendszer. Van is egy korlátozás a feldolgozás hibakeresési küld, a 10 percenként.

### <a name="review-telemetry"></a>Telemetriai adatok áttekintése

#### <a name="azure-portal"></a>Azure portál

A portálon gyors áttekintést kaphat az értesítési központban lévő összes tevékenységről.

1. Az **Áttekintés** lapon a regisztrációk, értesítések és hibák platformonkénti összesített nézetét tekintheti meg.

   ![Értesítési központok – áttekintés irányítópult][5]

2. A **Figyelő** lapon számos más platformspecifikus metrikát is hozzáadhat a mélyebb megjelenéshez. Megnézheti a hibákat, amelyek akkor adnak vissza, amikor az értesítési központok megpróbálja elküldeni az értesítést a leküldéses értesítési szolgáltatásnak.

   ![Az Azure Portal tevékenységnaplója][6]

3. Kezdje a **bejövő üzenetek**, a regisztrációs műveletek és **a**sikeres **értesítések áttekintésével.** Ezután lépjen a platformonkénti fülre a leküldéses értesítési szolgáltatásra jellemző hibák áttekintéséhez.

4. Ha az értesítési központ hitelesítési beállításai helytelenek, megjelenik a **PNS-hitelesítési hiba** üzenet. Ez egy jó jelzés, hogy ellenőrizze a leküldéses értesítési szolgáltatás hitelesítő adatait.

#### <a name="programmatic-access"></a>Szoftveres hozzáférés

Az automatizált hozzáférésről további információt a Programozott hozzáférés című [témakörben talál.](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))

> [!NOTE]
> Számos telemetriai kapcsolatos funkciók, például a regisztrációk exportálása és importálása és az API-kon keresztüli telemetriai hozzáférés, csak a standard szolgáltatási szinten érhetők el. Ha megpróbálja használni ezeket a funkciókat az ingyenes vagy alapszintű szolgáltatási szintről, az SDK használata esetén kivételüzenetet kap. Http 403-as (tiltott) hibaüzenet jelenik meg, ha a szolgáltatásokat közvetlenül a REST API-kból használja.
>
> Telemetriai szolgáltatások használatához először győződjön meg arról, hogy az Azure Portalon, hogy a standard szolgáltatási szint.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Értesítési központok – áttekintés]: notification-hubs-push-notification-overview.md
[Ismerkedés az Azure Értesítési központtal]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sablonok]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs áttekintése]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Az FCM-üzenetek –]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Mély merülés: Visual Studio 2013 Update 2 RC és Az Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[A Visual Studio 2013 3- as és Az Azure SDK 2.4-es frissítésének bejelentése]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend (Engedélyezés: TestSend)]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
