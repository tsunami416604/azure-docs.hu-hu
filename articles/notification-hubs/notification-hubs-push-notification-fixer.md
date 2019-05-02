---
title: Az Azure Notification Hubs elvetett értesítések diagnosztizálása
description: Megtudhatja, hogyan elvetett értesítések az Azure Notification hubs szolgáltatással kapcsolatos leggyakoribb problémák diagnosztizálásához.
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: eebf9ef63a8622c4cc431322b786fdf30f6352fe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925817"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Az Azure Notification Hubs elvetett értesítések diagnosztizálása

Az Azure Notification hubs szolgáltatással kapcsolatos gyakori kérdés, hogyan lehet a elhárításához, ha az alkalmazás értesítéseit nem jelennek meg az ügyféleszközökön. Ügyfelei tudni szeretnék, hol és miért értesítések eldobva, és hogyan lehet kijavítani a problémát. Ez a cikk azonosítja, ezért előfordulhat, hogy első eldobott vagy eszközök által nem kapott értesítések. Azt is bemutatja, hogyan lehet megállapítani a hiba okát.

Fontos, hogy először tájékozódjon az hogyan Notification Hubs leküldéses értesítések eszköz.

![Notification Hubs-architektúra][0]

Egy tipikus send notification folyamatban az üzenet érkezett a *alkalmazás háttérrendszere* Notification hubs felé. A Notification Hubs dolgozza fel az összes regisztrációját. Figyelembe veszi a konfigurált címkék és a címke kifejezések célok meghatározásához. Célok a regisztrációkat, amely a leküldéses értesítések fogadásához szükséges. Tyto registrace is kiterjedhet a támogatott platformok valamelyikét: Android-, Baidu (Android-eszközök a kínai), Fire operációs rendszer (Amazon) iOS, Windows és Windows Phone.

A létrehozott célokat, a Notification Hubs leküldéses értesítések értesítések a *leküldéses értesítési szolgáltatás* adott eszközplatform számára. Például az Apple Push Notification service (APNs) az Apple és a Firebase Cloud Messaging (FCM) for Google. Notification Hubs leküldéses értesítések elosztja a több köteg regisztrációk. Az a megfelelő leküldéses értesítési szolgáltatás, állítsa be az Azure Portalon, a hitelesítő adatok alapján hitelesíti **értesítési központ konfigurálása**. A leküldéses értesítési szolgáltatás ezután továbbítja az értesítéseket, hogy a megfelelő *ügyféleszközök*.

A lekérdezésértesítés-kézbesítési szolgáltatás utolsó szakasza a platform leküldéses értesítési szolgáltatás és az eszköz között van. A lekérdezésértesítés-kézbesítés sikertelen lehet bármelyik négy szakaszai a leküldéses értesítési folyamat (ügyfél, alkalmazás háttérrendszere, a Notification Hubs és a platform leküldéses értesítéseket kezelő szolgáltatása). A Notification Hubs architektúrájával kapcsolatos további információkért lásd: [Notification Hubs – áttekintés].

Sikertelen értesítések kézbesíthetők szegné meg a kezdeti tesztelési/előkészítési fázisban. Elvetett értesítések ezen a ponton egy konfigurációs problémát jelezhet. Ha az értesítések küldéséhez hiba történik, éles környezetben, vagy azok egy részét az értesítések veszhetnek el. Ebben az esetben egy részletesebb alkalmazást vagy az üzenetkezelési minta probléma jelenik.

A következő szakaszban az értesítések veszhetnek el, és a gyakori, ritka esetekben megvizsgál.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs helytelen konfiguráció ##

Értesítések küldésére a megfelelő leküldéses értesítési szolgáltatásunk, a Notification Hubs hitelesítenie kell magát az alkalmazás környezetében. -A célplatformot notification service (a Microsoft, az Apple, Google stb.) létre kell hoznia egy fejlesztői fiók nevében. Ezt követően regisztrálnia kell az alkalmazás az operációs rendszerben, ahol megkapja a tokenek vagy kulcsok, amellyel a cél PNS dolgozhat.

Platform hitelesítő adatokat hozzá kell adnia az Azure Portalon. Ha nincsenek értesítései az eszköz elérni próbált, az első lépéseként annak érdekében, hogy a helyes hitelesítő adatok vannak konfigurálva, a Notification hubs használatával. A hitelesítő adatoknak egyezniük kell az alkalmazás egy platformspecifikus fejlesztői fiók alatt létrehozott.

Ez a folyamat befejezéséhez részletes utasításokért lásd: [Azure Notification Hubs használatának első lépései].

Az alábbiakban néhány gyakori konfigurációs hibáinak kereséséhez:

### <a name="notification-hub-name-location"></a>Értesítési központ neve helye

Győződjön meg arról, hogy az értesítési központ nevére (nélkül elgépelések) megegyezik az egyes helyek:
   * Kell regisztrálnia az ügyfélről
   * Amikor értesítések küldése a háttérrendszerből
   * Ha konfigurálta a leküldéses értesítési szolgáltatás hitelesítő adatai

Győződjön meg arról, az ügyfél használja a megfelelő közös hozzáférésű jogosultságkódok konfigurációs karakterláncait, és vissza az alkalmazás befejezése. Általában a kell használnia **DefaultListenSharedAccessSignature** az ügyfélen, és **DefaultFullSharedAccessSignature** az alkalmazás a háttéralkalmazás. Ezzel engedélyt ad értesítések küldését a Notification hubs használatával.

### <a name="apn-configuration"></a>APN-konfiguráció ###

Két különböző hubs kell megmaradjanak: egyet az éles és a egy másik a tesztelés. Fel kell tölteni a tanúsítványt használja, mint az éles környezetben használni kívánt tanúsítvány/hub külön hubra egy védőfal mögötti környezet. Ne próbálja a különböző típusú tanúsítványt tölthet fel egy központban. Értesítési hibákat okozhat.

Ha véletlenül egy központban különböző típusú tanúsítványok feltöltése, a központ törléséhez, és kezdés tiszta lappal egy új eseményközpont. Ha valamilyen okból nem törli a hub, legalább törölnie kell a meglévő regisztrációkat az eseményközpontból.

### <a name="fcm-configuration"></a>FCM-konfiguráció ###

1. Ügyeljen arra, hogy a *kiszolgálókulcs* beszerzett Firebase megegyezik a server kulcsot az Azure Portalon regisztrálta.

   ![Firebase kiszolgálókulcs][3]

2. Győződjön meg arról, hogy konfigurálta **Projektazonosító** az ügyfélen. Szerezheti be az értéket **Projektazonosító** a Firebase-irányítópultról.

   ![Firebase-projekt azonosítója][1]

## <a name="application-issues"></a>Alkalmazás-hibák ##

### <a name="tags-and-tag-expressions"></a>Címke és címke kifejezések ###

A célközönség szegmentálása a címkék vagy a címke kifejezések használatakor, akkor lehet, hogy az értesítés elküldéséhez, amikor cél nem található. Ez a hiba a megadott címkék és a küldési hívás címke kifejezéseket alapul.

Tekintse át a regisztrációk annak érdekében, hogy a címkék felel meg, ha értesítést küld. Majd ellenőrizze, hogy csak azokat a regisztrációkat rendelkező ügyfelek az értesítés fogadását.

Tegyük fel például, a Notification hubs használatával az összes regisztrációit használja a címke "Politika". Majd küldünk egy értesítést a címkével ellátott "Sport", ha az nem fog az értesítés bármilyen eszközre. Egy összetett esetet is járhat, ahol regisztrálta "címkével és A" címke kifejezések *vagy* "Címke B", de a megcélzott "Címke A & és címke b" A cikk későbbi részében a öndiagnosztikai tippek a szakasz bemutatja, hogyan regisztrációit, és végezze el az összevetést.

### <a name="template-issues"></a>Sablonokkal kapcsolatos problémák ###

Ha sablonokat használ, győződjön meg arról, hogy kövesse az ismertetett irányelvek [sablonok].

### <a name="invalid-registrations"></a>Érvénytelen a regisztrációk ###

Ha az értesítési központ konfigurálva lett, és a címkék vagy a címke kifejezések helyesen használták, érvényes cél találhatók. Értesítések ezeken a célokon bA küldendő adattípusokat. A Notification Hubs majd ki, párhuzamosan több feldolgozási kötegek következik be. Az egyes kötegek üzeneteket küld a regisztrációk készletét.

> [!NOTE]
> A Notification Hubs kötegek párhuzamosan dolgozza fel, mert a sorrendet, amelyben az értesítések kézbesítése nem garantált.

A Notification Hubs az ", és a legtöbb – egyszer" üzenet kézbesítési modell van optimalizálva. A deduplikáció azt történt kísérlet, úgy, hogy az értesítések egy eszközön egynél többször érkezzenek. Regisztráció a leküldéses értesítési szolgáltatás való továbbítás előtt küld eszközazonosító csak egy üzenet, hogy be van jelölve.

A leküldéses értesítési szolgáltatás, amelyet ezután fogadja és érvényesíti a regisztrációkat az egyes kötegek küld. Ez a folyamat során is lehet, hogy a leküldéses értesítési szolgáltatás észlelni fogja a regisztrációk egy vagy több hiba történt, a batch szolgáltatásban. A folyamat leáll, és a leküldéses értesítési szolgáltatás majd hibát ad vissza a Notification hubs használatával. A leküldéses értesítési szolgáltatás a batch teljes mértékben csökken. Ez különösen igaz az apns-sel, amely a stream TCP protokollt használ.

Ebben az esetben a hibát okozó regisztrációs eltávolítja az adatbázisból. Ezután a többi eszközt, hogy a batch szolgáltatásban a lekérdezésértesítés-kézbesítés próbálkozni.

A sikertelen kézbesítési kísérlet egy regisztrációs elleni további hiba adatokat kérhet le, használhatja a Notification Hubs – REST API-k [üzenetszintű telemetriai adatokhoz: Értesítési üzenet telemetriai adatokat](https://msdn.microsoft.com/library/azure/mt608135.aspx) és [PNS-visszajelzéseket](https://msdn.microsoft.com/library/azure/mt705560.aspx). A mintakódot, tekintse meg a [küldése REST példa](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Leküldéses értesítési szolgáltatási problémák

Miután a leküldéses értesítési szolgáltatás megkapja az értesítést, az értesítés kínál az eszközön. Ezen a ponton a Notification Hubs nem szabályozza, az eszközre az értesítés kézbesítését rendelkezik.

Mivel a platformértesítési szolgáltatásoknak robusztus, értesítések általában néhány másodpercen belül eszközök eléréséhez. A leküldéses értesítési szolgáltatás szabályozza, ha a Notification Hubs egy exponenciális visszatartási stratégia vonatkozik. A leküldéses értesítési szolgáltatás 30 percig marad nem érhető el, ha nincs házirend lejár, és dobja el az üzeneteket véglegesen helye.

Ha egy leküldéses értesítési szolgáltatás megkísérli az értesítések kézbesítésére, de az eszköz offline állapotban, az értesítést a leküldéses értesítési szolgáltatás tárol. Ez csak egy korlátozott ideig nem történik. Amikor elérhetővé válik az eszköz az értesítés érkezik az eszközön.

Minden egyes alkalmazás tárolja csak egy újabb értesítés. Ha több értesítések lesznek küldve, amikor egy eszköz offline állapotban van, minden egyes új értesítés eredményezi, az utolsót elvet. Csak a legújabb értesítés tartja nevezzük *egyesítése* az APNs és *összecsukás* az FCM. (FCM összecsukás kulcsot használ.) Ha az eszköz hosszú ideje offline állapotban marad, az eszközön tárolt értesítések törlődik. További információkért lásd: [APNs – áttekintés] és [FCM-üzenetek].

A Notification Hubs adhat át egy összevonási kulcs használatával egy HTTP-fejlécet az általános SendNotification API-val. Ha például a .NET SDK-hoz, használja `SendNotificationAsync`. A SendNotification API-t is, hogy a megfelelő leküldéses értesítési szolgáltatás átadott HTTP-fejlécek vesz igénybe.

## <a name="self-diagnosis-tips"></a>Öndiagnosztikai tippek

Az alábbiakban elérési útjait elvetett értesítések a Notification Hubs a probléma alapvető okát.

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése ###

#### <a name="push-notification-service-developer-portal"></a>Leküldéses értesítési szolgáltatás fejlesztői portálon ####

Ellenőrizze a hitelesítő adatokat a megfelelő leküldéses értesítési szolgáltatás fejlesztői portálon (APNs, FCM, Windows Notification Service, és így tovább). További információkért lásd: [oktatóanyag: Értesítések küldése az Azure Notification Hubs univerzális Windows-Platformos alkalmazások](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification).

#### <a name="azure-portal"></a>Azure Portal ####

Tekintse át, és a hitelesítő adatokat a leküldéses értesítési szolgáltatás fejlesztői portálról lekért egyeznek, nyissa meg a **hozzáférési szabályzatok** lap az Azure Portalon.

![Hozzáférési szabályzatok Azure Portalon][4]

### <a name="verify-registrations"></a>Ellenőrizze a regisztrációt

#### <a name="visual-studio"></a>Visual Studio ####

A Visual Studio Azure megtekintéséhez és kezeléséhez több Azure-szolgáltatásokkal, mint a Notification Hubs a Server Explorer eszközével kapcsolódhat. Ez a parancs elsősorban hasznos a fejlesztési-tesztelési környezet.

![Visual Studio Server Explorer][9]

Megtekintheti, és minden regisztrációk kezelése a hub. A regisztrációk platform, natív vagy sablon regisztrációs, címkét, leküldéses értesítési szolgáltatás azonosítója, regisztrációs azonosító és lejárati dátum szerint csoportosíthatók. Ezen az oldalon egy regisztrációs szerkesztheti is. Ez különösen hasznos a címkék szerkesztését.

Kattintson a jobb gombbal az az értesítési központ **Server Explorer**, és válassza ki **diagnosztizálása**. 

![Visual Studio Server Explorer: Menü diagnosztizálása](./media/notification-hubs-diagnosing/diagnose-menu.png)

A következő oldal jelenik meg:

![Visual Studio: Diagnosztika lap](./media/notification-hubs-diagnosing/diagnose-page.png)

Váltson a **Eszközregisztrációk** oldalon:

![Visual Studio: Eszközregisztrációk](./media/notification-hubs-diagnosing/VSRegistrations.png)

Használhat **Tesztküldés** lap egy értesítési tesztüzenet küldése:

![Visual Studio: Tesztküldés](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> A Visual Studio használatával csak a fejlesztés és tesztelés alatt, és a regisztrációk korlátozott számú regisztrációk szerkesztése. Ha a regisztrációk tömeges szerkesztése van szüksége, fontolja meg az exportálás, és importálja a ismertetett regisztrációs funkció [Útmutató: Exportálása és módosítása a regisztrációk tömeges](https://msdn.microsoft.com/library/dn790624.aspx).

#### <a name="service-bus-explorer"></a>Service Bus Explorer ####

Sok ügyfél használ [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer) megtekintése és kezelése a notification hubs használatával. Service Bus Explorerrel egy nyílt forráskódú projekt. 

### <a name="verify-message-notifications"></a>Ellenőrizze az üzenetben küldött értesítésekről

#### <a name="azure-portal"></a>Azure Portal ####

Tesztértesítés küldése az ügyfelek számára egy service háttérrendszer városoknak, a nélkül, **támogatás + hibaelhárítás**válassza **Tesztküldés**.

![Küldési funkciók tesztelése az Azure-ban][7]

#### <a name="visual-studio"></a>Visual Studio ####

A Visual Studióból tesztértesítések is küldhet.

![Küldési funkciók tesztelése a Visual Studióban][10]

A Notification Hubs Visual Studio Server Explorerben való használatával kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [A notification hubs eszközregisztrációk megtekintése](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [Részletes bemutatása: Visual Studio 2013 Update 2 RC és az Azure SDK 2.3-as verzióját]
* [A Visual Studio 2013 Update 3 és az Azure SDK 2.4 kibocsátási bejelentése]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Sikertelen értesítések hibakeresése és az értesítés kimenetelét áttekintése

#### <a name="enabletestsend-property"></a>EnableTestSend tulajdonság ####

Notification hubs szolgáltatással értesítés küldése, amikor az értesítési kezdetben várólistára van állítva. A Notification Hubs meghatározza, hogy a megfelelő célokat, és ezután elküldi az értesítést a leküldéses értesítési szolgáltatáshoz. A REST API-t vagy az ügyfél SDK-k valamelyikét használja, ha a Küldés hívás a visszatérési azt jelenti, hogy csak, hogy az üzenetet a Notification hubs használatával az a várólistára. Mi történt, amikor a Notification Hubs végül küld az értesítés a leküldéses értesítési szolgáltatás betekintést, nem biztosít.

Ha nem érkezik meg az értesítést, az ügyféleszközön, előfordulhat, hogy rendelkezik hiba során a Notification Hubs a leküldéses értesítési szolgáltatás kézbesíti. Például a hasznos adatainak mérete meghaladhatja a leküldéses értesítési szolgáltatás által engedélyezett maximális értéket, vagy a hitelesítő adatokat, a Notification Hubs konfigurált valószínűleg érvénytelen.

Információkhoz juthat leküldéses értesítési szolgáltatás hibák, használhatja a [EnableTestSend] tulajdonság. Ez a tulajdonság automatikusan engedélyezve lesz, ha a portálon vagy a Visual Studio ügyfél teszt üzenetet küld. Ez a tulajdonság segítségével részletes hibakeresési információk megjelenítéséhez és API-kon keresztül is. Jelenleg ezzel a .NET SDK-ban. Ez hozzáadódik minden ügyfél SDK-k idővel.

Használatához a `EnableTestSend` tulajdonság a REST-hívással hozzáfűzése egy lekérdezési karakterlánc paramétereként nevű *tesztelése* és a küldési hívás vége. Példa:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Példa .NET SDK-val ####

Íme egy példa (bejelentési) natív felugró értesítés küldése a .NET SDK használatával:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

A végrehajtás végén `result.State` egyszerűen állapotok `Enqueued`. Az eredményeket nem ad meg minden Mi történt a leküldéses értesítés betekintést.

Ezután használhatja a `EnableTestSend` logikai tulajdonság. Használja a `EnableTestSend` táblafiókhoz tulajdonságot `NotificationHubClient` beolvasni egy részletes állapotadatainak megjelenítéséhez leküldéses értesítési szolgáltatás hibák fordulhatnak elő, amikor az értesítés érkezik. A Küldés hívás adja vissza, mert először a Notification Hubs a leküldéses értesítési szolgáltatás kézbesíti az értesítés további időt igényel.

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

#### <a name="sample-output"></a>Példa kimenet ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ez az üzenet azt jelzi, hogy a Notification Hubs konfigurált hitelesítő adatok érvénytelenek, vagy az, hogy nincs-e a regisztrációkat az agyban problémáját. Odstranit tuto registraci, és lehetővé teszik az ügyfél az üzenet elküldése előtt létre újból a regisztrációt.

> [!NOTE]
> Használja a `EnableTestSend` tulajdonság az erősen szabályozott. Használja ezt a beállítást, csak fejlesztési és tesztelési környezetben és a egy regisztrációk korlátozott készletével együtt. Hibakeresési értesítések kiküldése legfeljebb 10-eszközökre. Hibakeresési küld legalább 10 percenként feldolgozási is van korlátozva.

### <a name="review-telemetry"></a>Tekintse át a telemetriai adatok ###

#### <a name="azure-portal"></a>Azure Portal ####

A portálon az összes tevékenység gyors áttekintést kaphat az értesítési központban.

1. Az a **áttekintése** lapon platform regisztrációk, értesítések és a hibák összesített nézete látható.

   ![Notification Hubs – áttekintés irányítópult][5]

2. Az a **figyelő** lapon hozzáadhat számos más platform-specifikus metrikák jobban meg. Tekintse meg kifejezetten adja vissza, ha a Notification Hubs értesítést küldeni a leküldéses értesítési szolgáltatás megpróbálja hibákról.

   ![Az Azure portal tevékenységnapló][6]

3. Áttekintésével **bejövő üzenetek**, **regisztráció műveletek**, és **sikeres értesítések**. Ezután nyissa meg a-platform lapot, tekintse át a hibákat, a leküldéses értesítési szolgáltatás jellemző.

4. Ha az értesítési központ hitelesítési beállításai helytelenek, az üzenet **PNS-hitelesítési hiba** jelenik meg. Fontos ellenőrizni a leküldéses értesítési szolgáltatás hitelesítő adatai jól jelzi.

#### <a name="programmatic-access"></a>Szoftveres hozzáférés ####

Programozott hozzáféréssel kapcsolatos további információkért lásd: [programozás alapú hozzáférést](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Számos telemetriát funkcióiról, exportálása, például és importálása a regisztrációk és API-k, telemetriai adatok elérését csak a Standard szintű szolgáltatáscsomagban elérhető. Ha használja ezeket a funkciókat az ingyenes vagy alapszintű szolgáltatásszint, kap egy kivételre vonatkozó üzenet, ha az SDK-t használja. Hiba történt a HTTP 403 (tiltott) kap, közvetlenül a REST API-k az a funkciók használatakor.
>
> Telemetria kapcsolatos funkciók használatához előbb ellenőrizze az Azure Portalon, a Standard szintű szolgáltatáscsomagban használ-e.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs – áttekintés]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs használatának első lépései]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sablonok]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs – áttekintés]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM-üzenetek]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Részletes bemutatása: Visual Studio 2013 Update 2 RC és az Azure SDK 2.3-as verzióját]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[A Visual Studio 2013 Update 3 és az Azure SDK 2.4 kibocsátási bejelentése]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
