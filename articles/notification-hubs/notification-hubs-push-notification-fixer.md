---
title: Diagnosztika az Azure Notification Hubs – elvetett értesítések
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
ms.openlocfilehash: 4af86025e714c65d0ae225b271a2d0970bb96ee8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281641"
---
# <a name="azure-notification-hubs---diagnose-dropped-notifications"></a>Diagnosztika az Azure Notification Hubs – elvetett értesítések

Azure Notification hubs használatával ügyfeleink a leggyakoribb kérdések egyike hibaelhárítása, amikor egy alkalmazásból küldött értesítő e-nem jelennek meg az ügyféleszközökön. Szeretné tudnia, hol és miért értesítések eldobva, hogyan háríthatja el a problémát. Ez a cikk azonosítja, ezért előfordulhat, hogy első eldobott vagy eszközök által nem kapott értesítések. Ismerje meg, hogyan elemezheti és megállapítani a hiba okát.

Fontos, hogy először tájékozódjon az Notification Hubs szolgáltatás hogyan értesítések leküldi eszközt.

![Notification Hubs-architektúra][0]

Egy tipikus send notification folyamatban az üzenet érkezett a *alkalmazás háttérrendszere* Notification hubs felé. A Notification Hubs a regisztrációkhoz feldolgozást végez. A feldolgozás figyelembe veszi, a konfigurált címkék és a címke kifejezések meghatározásához "célok." Tárolók, amely a leküldéses értesítések fogadásához szükséges összes regisztrációját is. Tyto registrace is kiterjedhet bármely vagy a támogatott platformok: iOS-, Google, Windows, Windows Phone, Kindle és az China Android Baidu.

A létrehozott célokat, az a Notification Hubs szolgáltatás értesítések leküldi a *leküldéses értesítési szolgáltatás* adott eszközplatform számára. Például az Apple Push Notification service (APNs) az Apple és a Firebase Cloud Messaging (FCM) for Google. Notification Hubs leküldéses értesítések elosztja a több köteg regisztrációk. A megfelelő leküldéses értesítési szolgáltatás alapján a hitelesítő adatokat állíthat be az Azure Portalon, a Notification hubs használatával végzi a hitelesítést **értesítési központ konfigurálása**. A leküldéses értesítési szolgáltatás ezután továbbítja az értesítéseket, hogy a megfelelő *ügyféleszközök*.

A lekérdezésértesítés-kézbesítési szolgáltatás utolsó szakasza történik a platform leküldéses értesítési szolgáltatás és az eszköz között. A leküldéses értesítési folyamat (ügyfél, alkalmazás háttérrendszere, a Notification Hubs és a platform leküldéses értesítési szolgáltatás) négy fő összetevőből bármelyikét bejelentések eldobni kívánt okozhat. A Notification Hubs architektúrájával kapcsolatos további információkért lásd: [Notification Hubs – áttekintés].

Az értesítések kézbesítése sikertelen szegné meg a kezdeti tesztelési/előkészítési fázisban. Elvetett értesítések ezen a ponton egy konfigurációs problémát jelezhet. Ha az értesítések küldéséhez hiba történik, éles környezetben, néhány vagy összes értesítést veszhetnek el. Ebben az esetben egy részletesebb alkalmazást vagy az üzenetkezelési minta probléma jelzi.

A következő szakaszban az értesítések veszhetnek el, és a gyakori a több ritka esetekben megvizsgál.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs helytelen konfiguráció

Sikeresen küldhetők értesítések a megfelelő leküldéses értesítési szolgáltatásunk, a Notification Hubs szolgáltatás maga az alkalmazás a fejlesztői környezetben hitelesítenie kell. A fejlesztői az adott platform (Google, az Apple, Windows és így tovább) hoz létre egy fejlesztői fiók nevében. A fejlesztő ezután regisztrálja alkalmazását a hitelesítő adatok ered platform.

Platform hitelesítő adatokat hozzá kell adnia az Azure Portalon. Ha nincsenek értesítései az eszköz elérni próbált, az első lépés annak érdekében, hogy a megfelelő hitelesítő adatokat a Notification Hubs vannak-e konfigurálva kell lennie. A hitelesítő adatoknak egyezniük kell az alkalmazás egy platformspecifikus fejlesztői fiók alatt létrehozott.

Ez a folyamat befejezéséhez részletes utasításokért lásd: [Azure Notification Hubs használatának első lépései].

Az alábbiakban néhány gyakori konfigurációs hibáinak kereséséhez:

**Általános**

Győződjön meg arról, hogy az értesítési központ nevére (nélkül elgépelések) megegyezik az egyes helyek:
   * Ha regisztrálja az ügyfélről.
   * Ahol, értesítések küldése a háttérrendszerből.
   * Ha konfigurálta a leküldéses értesítési szolgáltatás hitelesítő adatait.

Győződjön meg arról, hogy a helyes-e közös hozzáférésű jogosultságkódok konfigurációs karakterláncait az ügyfél és az alkalmazás háttérrendszere. Általában a kell használnia **DefaultListenSharedAccessSignature** az ügyfélen, és **DefaultFullSharedAccessSignature** az alkalmazás háttér (engedélyt szeretne értesítéseket küldeni Notification hubs használatával).

**APN-konfiguráció**

Két különböző hubs kell megmaradjanak: egy eseményközpont éles környezetben, és a egy másik hub tesztelési. Ez azt jelenti, hogy fel kell töltenie a tanúsítvány egy védőfal mögötti környezet, mint a tanúsítványt és a hubnak, amely az éles környezetben használni kívánja, külön hubra során használt. Ne próbálja a különböző típusú tanúsítványt tölthet fel egy központban. Emiatt előfordulhat, hogy értesítés sikertelen.

Ha véletlenül egy központban különböző típusú tanúsítványt feltölteni, javasoljuk, hogy a központ törléséhez, és kezdés tiszta lappal egy új eseményközpont. Ha valamilyen okból nem törölhető, a központ legalább törölnie kell a meglévő regisztrációk a hubról.

**FCM-konfiguráció**

1. Ügyeljen arra, hogy a *kiszolgálókulcs* beszerzett Firebase megegyezik a server kulcs, amely az Azure Portalon regisztrálta.

   ![Firebase kiszolgálókulcs][3]

2. Győződjön meg arról, hogy konfigurálta **Projektazonosító** az ügyfélen. Szerezheti be az értéket **Projektazonosító** a Firebase-irányítópultról.

   ![Firebase-projekt azonosítója][1]

## <a name="application-issues"></a>Alkalmazás-hibák

**Címke és címke kifejezések**

A célközönség szegmentálása a címkék vagy a címke kifejezések használatakor, akkor lehet, hogy az értesítés elküldéséhez, amikor nincs célja nem talált a címkék vagy a címke kifejezések, amelyeket a küldési hívás.

Tekintse át, győződjön meg róla, hogy nincsenek címke értesítés küldése, ha a regisztráció. Majd ellenőrizze, hogy csak az ügyfelekről, amelyek azokat a regisztrációkat az értesítés fogadását.

Például ha a Notification hubs használatával az összes regisztrációit "Politika" címke használatával végzett és küld értesítést a címkével ellátott "Sport", az értesítés nem a rendszer bármilyen eszközre. Egy összetett esetet is járhat, amelyben regisztrált "Címkét egy" vagy "Címke B" címke kifejezések, de értesítések küldésekor "Címke A & és címke b" cél A cikk későbbi részében öndiagnosztikai tippek szakaszban hogy bemutatják, hogyan regisztrációit, és végezze el az összevetést.

**Sablonokkal kapcsolatos problémák**

Ha sablonokat használ, győződjön meg arról, hogy kövesse az ismertetett irányelvek [sablonok].

**Érvénytelen a regisztrációk**

Ha az értesítési központ konfigurálva lett, és minden olyan címkék vagy a címke kifejezések helyesen használták, érvényes cél találhatók. Értesítések ezeken a célokon bA küldendő adattípusokat. A Notification Hubs szolgáltatás majd ki, párhuzamosan több feldolgozási kötegek következik be. Az egyes kötegek üzeneteket küld a regisztrációk készletét.

> [!NOTE]
> Párhuzamos feldolgozás hajtja végre, mert a sorrendet, amelyben az értesítések kézbesítése nem garantált.

A Notification Hubs az "a legnagyobb egyszer" üzenet kézbesítési modell van optimalizálva. A deduplikáció azt történt kísérlet, úgy, hogy az értesítések egy eszközön egynél többször érkezzenek. Ennek biztosítására, hogy ellenőrizze regisztrációk, és győződjön meg arról, hogy csak egy üzenetet küld eszköz azonosítója, a leküldéses értesítési szolgáltatásunk, az üzenet elküldése előtt.

A leküldéses értesítési szolgáltatás, amelyet ezután fogadja, és a regisztrációk ellenőrzése, hogy az egyes kötegek zajlik, lehetőség, hogy a leküldéses értesítési szolgáltatás egy vagy több regisztrációk hiba észleli a batch szolgáltatásban. Ebben az esetben a leküldéses értesítési szolgáltatásunk, a Notification Hubs hibát ad vissza, és a folyamat leáll. A leküldéses értesítési szolgáltatás a batch teljes mértékben csökken. Ez különösen igaz az apns-sel, amelyeket egy stream TCP protokollt használja.

A Microsoft a legtöbb vannak optimalizálva egyszer kézbesítési. Azonban ebben az esetben a hibát okozó regisztráció eltávolítása az adatbázisból. Ezután a többi eszközt, hogy a batch szolgáltatásban a lekérdezésértesítés-kézbesítés próbálkozni.

A sikertelen kézbesítési kísérlet egy regisztrációs elleni további hiba adatokat kérhet le, használhatja a Notification Hubs – REST API-k [üzenetszintű telemetriai adatokhoz: Értesítési üzenet telemetriai adatokat](https://msdn.microsoft.com/library/azure/mt608135.aspx) és [PNS-visszajelzéseket](https://msdn.microsoft.com/library/azure/mt705560.aspx). A mintakódot, tekintse meg a [küldése REST példa](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Leküldéses értesítési szolgáltatási problémák

Miután az értesítési üzenet érkezett, a platform leküldéses értesítési szolgáltatás, feladata a leküldéses értesítési szolgáltatás kézbesíti az értesítés az eszköz. A Notification Hubs szolgáltatást ezen a ponton a képen kívül esik, és rendelkezik a nem szabályozza, vagy ha az eszköz az értesítést a rendszer továbbítja.

Mivel a platformértesítési szolgáltatásoknak robusztus, értesítések általában a leküldéses értesítési szolgáltatásból eszközök eléréséhez néhány másodpercen belül. Ha a leküldéses értesítési szolgáltatás szabályozza, a Notification Hubs szolgáltatás vonatkozik egy exponenciális visszatartási stratégia. A leküldéses értesítési szolgáltatás 30 percig marad nem érhető el, ha lejár, és dobja el az üzeneteket véglegesen helyen van egy szabályzatot.

Ha leküldéses értesítést küldő szolgáltatás megkísérli az értesítések kézbesítésére, de az eszköz offline állapotban, az értesítést a leküldéses értesítési szolgáltatás egy korlátozott ideig tárolja. Amikor elérhetővé válik az eszköz az értesítés érkezik az eszközön.

Minden alkalmazáshoz csak egy újabb értesítés tárolódik. Ha több értesítések lesznek küldve, amikor egy eszköz offline állapotban van, minden egyes új értesítés hatására elvet előzetes értesítés. Csak a legújabb értesítés tartja nevezzük *összevonási értesítések* az APN, és *dimenziógranularitását* az FCM (összecsukás kulcsot használó). Az eszköz hosszú ideje offline állapotban marad, ha folyamatban van az eszköz eltárolt értesítések elvesznek. További információkért lásd: [APN áttekintése] és [FCM-üzenetek].

Az Azure Notification Hubs adhat át egy összevonási kulcs használatával egy HTTP-fejlécet az általános SendNotification API-val. Ha például a .NET SDK-hoz, használja `SendNotificationAsync`. A SendNotification API veszi adhatók be HTTP-fejléceket is –, hogy a megfelelő leküldéses értesítési szolgáltatás.

## <a name="self-diagnosis-tips"></a>Öndiagnosztikai tippek

Az alábbiakban elérési útjait elvetett értesítések a Notification Hubs a probléma alapvető okát.

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése

**Leküldéses értesítési szolgáltatás fejlesztői portálon**

Ellenőrizze a hitelesítő adatokat a megfelelő leküldéses értesítési szolgáltatás fejlesztői portálon (APNs, FCM, Windows Notification Service, és így tovább). További információkért lásd: [Azure Notification Hubs használatának első lépései].

**Azure Portal**

Tekintse át és felel meg a hitelesítő adatokat, amelyek kapott, a leküldéses értesítési szolgáltatás fejlesztői portált, az Azure Portalon nyissa meg a **hozzáférési szabályzatok** fülre.

![Hozzáférési szabályzatok Azure Portalon][4]

### <a name="verify-registrations"></a>Ellenőrizze a regisztrációt

**Visual Studio**

Ha használja a Visual Studio fejlesztési, csatlakozhat Azure-t a Server Explorer megtekintéséhez és kezeléséhez több Azure-szolgáltatásokkal, beleértve a Notification hubs használatával. Ez hasznos elsődlegesen a fejlesztési-tesztelési környezet.

![Visual Studio Server Explorer][9]

Megtekintheti, és a platform natív kategorizálta az központ összes regisztrációja vagy sablon regisztrációs, minden olyan címkék, leküldéses értesítési szolgáltatás azonosítója, regisztrációs azonosító és lejárati dátum kezelése. Ezen az oldalon egy regisztrációs szerkesztheti is. Ez különösen hasznos a címkék szerkesztését.

Kattintson a jobb gombbal a **értesítési központ** a a **Server Explorer**, és válassza ki **diagnosztizálása**. 

![A Visual Studio - Server Explorer – menü diagnosztizálása](./media/notification-hubs-diagnosing/diagnose-menu.png)

A következő oldal jelenik meg: 

![A Visual Studio - Diagnosztika lap](./media/notification-hubs-diagnosing/diagnose-page.png)

Váltson a **Eszközregisztrációk** oldalon: 

![Visual Studio Device Registrations](./media/notification-hubs-diagnosing/VSRegistrations.png)

Használhat **Tesztküldés** lap egy értesítési tesztüzenet küldése:

![Visual Studio – küldés tesztelése](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> A Visual Studio használatával csak a fejlesztés + tesztelés alatt, és a regisztrációk korlátozott számú regisztrációk szerkesztése. Ha a regisztrációk tömeges szerkesztése van szüksége, fontolja meg az exportálás, és importálja a ismertetett regisztrációs funkció [exportálása és módosítása a regisztrációk tömeges](https://msdn.microsoft.com/library/dn790624.aspx).

**Service Bus Explorer**

Sok ügyfél használ [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer) megtekintéséhez és kezeléséhez az értesítési központban. Service Bus Explorerrel egy nyílt forráskódú projekt. 

### <a name="verify-message-notifications"></a>Ellenőrizze az üzenetben küldött értesítésekről

**Azure Portal**

Tesztértesítés küldése az ügyfelek számára egy service háttérrendszer városoknak, a nélkül, **támogatás + hibaelhárítás**válassza **Tesztküldés**.

![Küldési funkciók tesztelése az Azure-ban][7]

**Visual Studio**

A Visual Studióból tesztértesítések is küldhet.

![Küldési funkciók tesztelése a Visual Studióban][10]

A Notification Hubs Visual Studio Server Explorerben való használatával kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [A notification hubs nézet eszközregisztrációk]
* [Részletes bemutatása: Visual Studio 2013 Update 2 RC és az Azure SDK 2.3-as verzióját]
* [A Visual Studio 2013 Update 3 és az Azure SDK 2.4 kibocsátási bejelentése]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Sikertelen értesítések hibakeresése és az értesítés kimenetelét áttekintése

**EnableTestSend tulajdonság**

Ha kezdetben Notification hubs szolgáltatással, értesítést küld, az a Notification Hubs feldolgozáshoz sorba állított az értesítés. A Notification Hubs meghatározza, hogy a megfelelő célokat, és ezután elküldi az értesítést a leküldéses értesítési szolgáltatáshoz. Ha a REST API-t vagy bármely, az ügyfél SDK-kat használ, a sikeres, a Küldés hívás visszatérési azt jelenti, hogy csak, hogy az üzenet rendszer sikeresen várólistára helyezte a Notification hubs használatával. Nem kell minden olyan Mi történt, amikor a leküldéses értesítési szolgáltatás végül elküldte az üzenetet a Notification Hubs betekintést.

Ha az értesítés nem érkezik meg az ügyféleszközön:, lehetséges, hogy hiba történt, amikor a Notification Hubs próbált kézbesíteni az üzenetet a leküldéses értesítési szolgáltatás. Például a hasznos adatainak mérete meghaladhatja a leküldéses értesítési szolgáltatás által engedélyezett maximális értéket, vagy a hitelesítő adatokat, a Notification Hubs konfigurált valószínűleg érvénytelen.

Információkhoz juthat leküldéses értesítési szolgáltatás hibák, használhatja a [EnableTestSend] tulajdonság. Ez a tulajdonság automatikusan engedélyezve lesz, ha a portálon vagy a Visual Studio ügyfél teszt üzenetet küld. Ez a tulajdonság segítségével részletes hibakeresési információk megjelenítéséhez. A tulajdonság API-kon keresztül is használhatja. Jelenleg ezzel a .NET SDK-ban. Végül Ez hozzáadódik minden ügyfél SDK-k.

Használatához a `EnableTestSend` tulajdonság a REST-hívással hozzáfűzése egy lekérdezési karakterlánc paramétereként nevű *tesztelése* és a küldési hívás vége. Példa:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

**Példa (.NET SDK-val)**

Íme egy példa (bejelentési) natív felugró értesítés küldése a .NET SDK használatával:

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

A végrehajtás végén `result.State` egyszerűen állapotok `Enqueued`. Az eredményeket nem ad meg minden Mi történt a leküldéses értesítés betekintést.

Ezután használhatja a `EnableTestSend` logikai tulajdonság. Használja a `EnableTestSend` táblafiókhoz tulajdonságot `NotificationHubClient` beolvasni egy részletes állapotadatainak megjelenítéséhez leküldéses értesítési szolgáltatás hibák fordulhatnak elő, amikor az értesítés érkezik. A Küldés hívás adja vissza, mert csak azt követően a Notification Hubs elküldte az értesítést a leküldéses értesítési szolgáltatás a serkenti az eredményt meghatározni a visszaadott további időt igényel.

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

**Kimeneti példa**

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Az üzenet azt jelzi, hogy érvénytelen hitelesítő adatok vannak konfigurálva, a Notification Hubs vagy a regisztrációkat az agyban a probléma. Azt javasoljuk, hogy törli a regisztrációt, és lehetővé teszik az ügyfél az üzenet elküldése előtt létre újból a regisztrációt.

> [!NOTE]
> Használja a `EnableTestSend` tulajdonság az erősen szabályozott. Használja ezt a beállítást, csak fejlesztési és tesztelési környezetben, és a regisztrációk korlátozott készletével együtt. Legfeljebb 10-eszközökre a hibakeresési értesítéseket küldünk. A hibakeresési küld percenként 10 feldolgozási korlát is van.

### <a name="review-telemetry"></a>Tekintse át a telemetriai adatok

**Azure Portal**

A portálon az összes tevékenység gyors áttekintést kaphat az értesítési központban.

1. Az a **áttekintése** lapon platform regisztrációk, értesítések és a hibák összesített nézete látható.

   ![Notification Hubs – áttekintés irányítópult][5]

2. Az a **figyelő** lapon hozzáadhat számos más platform-specifikus metrikák jobban meg. Tekintse meg kifejezetten a leküldéses értesítési szolgáltatáshoz kapcsolódó esetleges hibákat, ha a Notification Hubs szolgáltatás megpróbálja értesítést küldeni a leküldéses értesítési szolgáltatás visszaadott.

   ![Az Azure portal tevékenységnapló][6]

3. Áttekintésével **bejövő üzenetek**, **regisztráció műveletek**, és **sikeres értesítések**. Ezután nyissa meg a-platform lapot, tekintse át a hibákat, a leküldéses értesítési szolgáltatás jellemző.

4. Ha az értesítési központ hitelesítési beállításai helytelenek, az üzenet **PNS-hitelesítési hiba** jelenik meg. Fontos ellenőrizni a leküldéses értesítési szolgáltatás hitelesítő adatai jól jelzi.

**Szoftveres hozzáférés**

Programozott hozzáféréssel kapcsolatos további információkért lásd: [Telemetria programozott hozzáférés].

> [!NOTE]
> Számos telemetriát funkcióiról, exportálása, például és importálása a regisztrációk és API-k, telemetriai adatok elérését csak a Standard szintű szolgáltatáscsomagban elérhető. Próbálja meg használni, ha ezeket a funkciókat az ingyenes vagy alapszintű szolgáltatásszintet, olyan kivétel üzenetet kap, ha használja az SDK-t, és a egy HTTP 403 (tiltott) hiba közvetlenül a REST API-k az a funkciók használatakor.
>
> Telemetria kapcsolatos funkciók használatához előbb ellenőrizze az Azure Portalon, hogy használja a Standard szolgáltatásszinten.  

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
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM-üzenetek]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[A notification hubs nézet eszközregisztrációk]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Részletes bemutatása: Visual Studio 2013 Update 2 RC és az Azure SDK 2.3-as verzióját]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[A Visual Studio 2013 Update 3 és az Azure SDK 2.4 kibocsátási bejelentése]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetria programozott hozzáférés]: https://msdn.microsoft.com/library/azure/dn458823.aspx
