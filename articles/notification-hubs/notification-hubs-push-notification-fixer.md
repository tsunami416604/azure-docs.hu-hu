---
title: Az Azure Notification Hubs eldobott értesítési elemzés céljából
description: Útmutató az Azure Notification Hubs eldobott értesítések kapcsolatos gyakori hibák diagnosztizálása érdekében.
services: notification-hubs
documentationcenter: Mobile
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bc9ef70560f0485da81c1f54aa955cee76d280ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777607"
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Az eldobott értesítések a Notification Hubs diagnosztizálása

A leggyakoribb kérdések az Azure Notification Hubs-ügyfél egyik hibaelhárítása, amikor egy alkalmazás küldött üzenetek nem jelennek meg az ügyféleszközökön. Szeretnék tudni, hogy hol és miért értesítések eldobva és hárítsa el a problémát. Ez a cikk azonosítja, ezért előfordulhat, hogy első eldobott vagy nem fogadhatják a eszközök értesítések. Megtudhatja, hogyan elemezheti és okának meghatározásához. 

Fontos, hogy először tájékozódjon a módját a Notification Hubs szolgáltatás leküldéses értesítések értesítést egy eszközre.

![Notification Hubs-architektúra][0]

Egy tipikus send notification folyamatában, az üzenet érkezik a *alkalmazás háttér* a Notification hubs használatával. Notification hubs használatával végzi a regisztrációk néhány terhelése. A feldolgozási figyelembe veszi, a konfigurált címkék és a címke kifejezések meghatározásához "célok." A célpontjai a leküldéses értesítések fogadásához szükséges összes regisztrációját. A regisztrációk is kiterjedhet bármely vagy a támogatott platformok: iOS-, Google, a Windows, Windows Phone, Kindle és Kína Android a Baidu.

A meghatározott célkitűzések, a Notification Hubs szolgáltatás értesítések leküldéses értesítések a *leküldéses értesítési szolgáltatás* az adott eszközplatform. Például az Apple Push Notification szolgáltatás (APNs) az Apple és Firebase Cloud Messaging (FCM) a Google. Notification Hubs leküldéses értesítések értesítések-e osztani több kötegek regisztrációk. A megfelelő leküldéses értesítési szolgáltatás, amely az Azure portálon, a hitelesítő adatok alapján hitelesíti a Notification Hubs **értesítési központ konfigurálása**. A leküldéses értesítési szolgáltatás majd továbbítja az értesítések a megfelelő *ügyféleszközök*. 

A lekérdezésértesítés-kézbesítés utolsó szakasza a platform leküldéses értesítéseket kezelő szolgáltatása és az eszköz között történik. A leküldéses értesítési (ügyfél, alkalmazás háttér, a Notification Hubs és a platform leküldéses értesítéseket kezelő szolgáltatása) folyamat négy fő összetevőből bármelyikét okozhat értesítések megszakad. A Notification Hubs architektúrájával kapcsolatos további információkért lásd: [Notification Hubs – áttekintés].

Értesítéseket hiba léphet fel a kezdeti során teszt/átmeneti fázisban. Az eldobott értesítések ezen a ponton jelezhetik konfigurációs hiba lépett fel. Hiba esetén értesítéseket éles környezetben, néhány vagy összes értesítést veszhetnek el. Ebben az esetben egy mélyebb alkalmazást vagy az üzenetkezelési minta probléma jelzi. 

A következő szakasz ellenőrzi, hogy az forgatókönyvek, amelyben értesítések veszhetnek el, a beállításnak több ritka és gyakori közötti.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs helytelen konfigurálása
Az értesítések sikeresen küldését a megfelelő leküldéses értesítéseket kezelő szolgáltatása, a Notification Hubs szolgáltatás melyekkel hitelesítenie tudja magát a fejlesztői alkalmazás környezetében. A fejlesztői ez megtörténik, a megfelelő platform (Google, Apple, Windows és így tovább) hoz létre a fejlesztői fiók létrehozása. A fejlesztői, majd regisztrálja az alkalmazásokban a platform, ahol azok beszerezni a hitelesítő adatokat. 

Hozzá kell adnia az Azure-portálon platformhitelesítő adataival. Ha az eszköz nincs értesítések elérni próbált, az első lépés annak érdekében, hogy a megfelelő hitelesítő adatok konfigurálva vannak-e a Notification Hubs kell lennie. A hitelesítő adatoknak egyezniük kell az alkalmazást, amely egy platform-specifikus fejlesztő fiókkal jön létre. 

A folyamat befejezéséhez lépésenkénti útmutatót lásd: [Ismerkedés az Azure Notification Hubs].

Az alábbiakban néhány gyakori konfigurációs hibák kereséséhez:

* **Általános**
   
    * Győződjön meg arról, hogy az értesítési központ nevére (nélkül gépelési) a ugyanaz az egyes helyeken:

        * Ha regisztrálja az ügyféltől.
        * Ha Ön értesítéseket küld a háttérből.
        * Ha konfigurálta a leküldéses értesítési szolgáltatás hitelesítő adatait.
    
    * Győződjön meg arról, hogy az ügyfél és az alkalmazás háttérben futó használja a megfelelő megosztott hozzáférési aláírást konfigurációs karakterláncok. Általában kell használnia **DefaultListenSharedAccessSignature** az ügyfélen és **DefaultFullSharedAccessSignature** az alkalmazás háttér (engedélyt ad az értesítések küldése Notification hubs használatával).

* **APNs-konfiguráció**
   
    Két különböző hubok kell fenntartani: egy hub termelési környezetben, és egy másik központi tesztelési. Ez azt jelenti, hogy a tanúsítványt egy védőfal mögötti környezet, mint a tanúsítványt és a hub, amely a termelési használni szeretne egy külön hubhoz használó kell tölteni. Ne próbálja meg feltölteni a tanúsítványok különböző típusú ugyanabban a központban. Ez az értesítési hibákat okozhat. 
    
    Ha véletlenül feltöltött tanúsítványok különböző típusú ugyanabban a központban, azt javasoljuk, hogy a központ töröl, és új központ jön. Ha valamilyen okból kifolyólag nem törölhető, a központ legalább a meglévő regisztrációk a központ törölnie kell. 

* **FCM konfiguráció** 
   
    1. Győződjön meg arról, hogy a *kiszolgálókulcs* nyert Firebase megegyezik kiszolgáló regisztrált az Azure portálon.
   
    ![Firebase kiszolgálókulcs][3]
   
    2. Győződjön meg arról, hogy konfigurálta **Projektazonosítónak** az ügyfélen. Ezt úgy szerezheti be a következő **Projektazonosítónak** az Firebase irányítópulton.
   
    ![Firebase projekt azonosítója][1]

## <a name="application-issues"></a>Alkalmazással kapcsolatos problémák
* **Címkék és a címke kifejezések**

    Címkék vagy címke kifejezések használatával szegmentálhatja a célközönséget, akkor lehetséges, hogy az értesítés elküldéséhez, amikor nincs target megtalálható a címkéket és a send hívásban megadott címke kifejezések alapján. 
    
    Tekintse át a regisztrációk győződjön meg arról, hogy nincsenek címke Ha értesítést küldünk. Ellenőrizze a visszaigazoláshoz regisztrációkat rendelkező ügyfelek csak a. 
    
    Tegyük fel ha az összes regisztrációját, a Notification hubs használatával végzett "Politika" címkével, és küld értesítést a címkével ellátott "Sport", elküldi az értesítést nem bármilyen eszközről. Egy összetett esetben előfordulhat, hogy tartalmaz, amely a címke kifejezések, amelyben regisztrálva "Címke A" vagy "Címke B", de értesítést küld, miközben "Címke A & & Címke b" cél A cikk későbbi részében a önellenőrzést tippek szakaszban azt bemutatják a tekintse át a regisztráció és címkétől. 

* **Sablon problémák**

    Ha sablonokat használ, győződjön meg arról, hogy kövesse a leírt irányelveket [sablonok]. 

* **Érvénytelen a regisztrációk**

    Ha az értesítési központ konfigurálva lett, és bármely címkék vagy címke kifejezések helytelenül használták, érvényes célok találhatók. Az ezeken a célokon kell értesítéseket küldeni. A Notification Hubs szolgáltatást, majd ki párhuzamosan több feldolgozási kötegek következik be. Minden egyes üzeneteket küld a regisztrációk készlete. 

    > [!NOTE]
    > Párhuzamos feldolgozás hajtja végre, mert a sorrendet, amelyben az értesítések kézbesítése nem garantált. 

    A Notification Hubs egy "a legtöbb egyszer" üzenet kézbesítési modell van optimalizálva. A deduplikáció azt történt kísérlet, így értesítés egynél többször szállítják ki egy eszközt. Ennek érdekében azt regisztráció ellenőrzése és győződjön meg arról, hogy csak egy üzenetet küld eszköz azonosítója a leküldéses értesítési szolgáltatáshoz az üzenet elküldése előtt. 

    Az egyes kötegekben zajlik a leküldéses értesítési szolgáltatás, amely viszont fogadja, és a regisztráció érvényesítése, akkor lehetséges, hogy a leküldéses értesítéseket kezelő szolgáltatása fog egy vagy több regisztrációk hiba kötegben. Ebben az esetben a leküldéses értesítéseket kezelő szolgáltatása hibaüzenetet küld vissza a Notification Hubs és a folyamat leáll. A leküldéses értesítéseket kezelő szolgáltatása elutasítja azokat az adott kötegelt teljesen. Ez különösen igaz az apns-sel, amely adatfolyam TCP protokollt használ. 

    Jelenleg a legtöbb optimalizált egyszer kézbesítését. Azonban ebben az esetben a hibás regisztráció eltávolítják az adatbázisból. Ezután értesítések kézbesítése az eszközöket, hogy a kötegben a többi próbálkozni.

    Ahhoz, hogy hiba további információt a sikertelen kézbesítési kísérlet egy regisztrációs ellen, használhatja a Notification hub REST API-k [üzenet Telemetriai: első értesítési üzenet Telemetriai](https://msdn.microsoft.com/library/azure/mt608135.aspx) és [PNS visszajelzés](https://msdn.microsoft.com/library/azure/mt705560.aspx). Mintakód, tekintse meg a [küldése REST példa](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Leküldéses értesítési szolgáltatás kapcsolatos hibák elhárítása
Miután az értesítési üzenetet kapott a platform leküldéses értesítéseket kezelő szolgáltatása, feladata az értesítés kézbesítése az eszközön a leküldéses értesítési szolgáltatást. A Notification Hubs szolgáltatást ezen a ponton a kép kívül esik, és rendelkezik befolyással keresztül, amikor, vagy ha az értesítést a rendszer az eszköz. 

Mivel a platform értesítési szolgáltatások robusztus, értesítések általában néhány másodpercen belül a leküldéses értesítési szolgáltatásból eszközök eléréséhez. A leküldéses értesítéseket kezelő szolgáltatása a szabályozás, ha a Notification Hubs exponenciális vissza az indító stratégia vonatkozik. A leküldéses értesítéseket kezelő szolgáltatása 30 percig marad nem érhető el, ha egy házirend tudunk lejárjanak és azokat az üzeneteket véglegesen drop helyen. 

Ha egy leküldéses értesítéseket kezelő szolgáltatása értesítések kézbesítésére kísérleteket, de az eszköz offline állapotban, az értesítés a leküldéses értesítési szolgáltatás egy korlátozott ideig tárolja. Az értesítés juttatni az eszközre, amikor az elérhetővé válik. 

Minden alkalmazást csak egy legutóbbi értesítési van tárolva. Több értesítés is érkezett kapnak, amikor egy eszköz offline állapotban, ha minden új értesítés hatására a előzetesen elvesznek. Csak a legújabb értesítési tartása nevezzük *összevonási értesítések* APNs, a és *bezárásával* a FCM (amely összecsukás kulcsot használ). Ha az eszköz hosszú ideig offline állapotban marad, a rendszer elveti a tárolóeszközein tárolt alatt az eszköz értesítések. További információkért lásd: [APNs – áttekintés] és [kapcsolatos FCM üzenetek].

Az Azure Notification Hubs egy összevonási kulcs átviheti via HTTP-fejléc az általános SendNotification API használatával. Például a .NET SDK-ban, használhatja **SendNotificationAsync**. A SendNotification API is veszi át lettek adva, mint a HTTP-fejlécek-van a megfelelő leküldéses értesítési szolgáltatáshoz. 

## <a name="self-diagnosis-tips"></a>Önellenőrzést tippek
Az alábbiakban elérési útjait diagnosztizálása az eldobott értesítések a Notification Hubs alapvető ok:

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése
* **Leküldéses értesítési szolgáltatás fejlesztői portálján**
   
    Ellenőrizze a hitelesítő adatokat a megfelelő leküldéses értesítési szolgáltatás fejlesztői portálra (APNs, FCM, a Windows értesítési szolgáltatás, és így tovább). További információkért lásd: [Ismerkedés az Azure Notification Hubs].

* **Azure Portal**
   
    Tekintse át és felel meg a hitelesítő adatokat a portálról beszerzett a leküldéses értesítési szolgáltatás fejlesztői, az Azure portálon keresse fel a **hozzáférési házirendek** fülre. 
   
    ![Hozzáférési házirendek az Azure portálon][4]

### <a name="verify-registrations"></a>Regisztráció ellenőrzése
* **Visual Studio**
   
    Ha a Visual Studio fejlesztői használja, csatlakozhat Azure Server Explorer megtekintéséhez és kezeléséhez több Azure-szolgáltatásokkal, beleértve a Notification Hubs használatával. Ez elsősorban fontos fejlesztési és tesztelési célú környezetnek. 
   
    ![A Visual Studio Server Explorer][9]
   
    Akkor is és megtekintéséhez és kezeléséhez az központban platform, natív kategorizálta a regisztrációk vagy sablon regisztrációs, bármely címkék, leküldéses értesítési szolgáltatás azonosítója, regisztrációs Azonosítót, lejárati dátuma. A regisztrációs ezen a lapon szerkesztheti is. Ez különösen fontos címkék szerkesztésre. 
   
    ![A Visual Studio eszközök regisztrációk][8]
   
   > [!NOTE]
   > Visual Studio használatával csak a fejlesztési és tesztelési célú során, és a regisztrációk korlátozott számú regisztrációk szerkesztése. Ha a regisztrációk tömeges szerkesztése van szüksége, fontolja meg az exportálás, és importálási ismertetett regisztrációs funkciójával [exportálni, és módosítsa a tömeges regisztrációk](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Service Bus Explorer**
   
    Számos ügyfél használja [Service Bus Explorer] megtekintéséhez és kezeléséhez az értesítési központban. Service Bus Explorer nyílt forráskódú projektben. Minták, lásd: [Service Bus Explorer kód].

### <a name="verify-message-notifications"></a>Ellenőrizze az értesítő üzenetek
* **Azure Portal**
   
    Tesztértesítés küldése az ügyfelek számára anélkül, hogy az a szolgáltatás háttérből lépéseivel, **támogatási + hibaelhárítás**, jelölje be **küldés tesztelése**. 
   
    ![Teszt küldése funkció az Azure-ban][7]
* **Visual Studio**
   
    A Visual Studio eszközből is küldhet tesztértesítést.
   
    ![A Visual Studio küldési funkció tesztelése][10]
   
    A Visual Studio Server Explorer Notification Hubs használatával kapcsolatban további információkért lásd: ezek a cikkek: 
   
   * [A notification hubs eszköz regisztrációk megtekintése]
   * [Részletes bemutatója: a Visual Studio 2013 Update 2 RC és az Azure SDK 2.3]
   * [A Visual Studio 2013 Update 3 és az Azure SDK 2.4 bejelentése]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Nem sikerült értesítést hibakeresését, és tekintse át az értesítés kimenetelét
**EnableTestSend tulajdonság**

Ha kezdetben küldése Notification Hubs keresztül, az értesítés feldolgozásra a Notification Hubs várólistára van állítva. A Notification Hubs határozza meg a megfelelő célokat, és ezután elküldi az értesítést a leküldéses értesítéseket kezelő szolgáltatása. A REST API vagy bármely, az ügyfél SDK-k használatakor a küldési hívás sikeres visszatérési azt jelenti, hogy csak, hogy az üzenet sikeresen várólistára került a Notification hubs használatával. Mi történt, amikor a Notification Hubs a leküldéses értesítéseket kezelő szolgáltatása végül az üzenetet küldött bármely betekintést nincs. 

Az értesítés nem érkeznek az ügyféleszközön, akkor lehetséges, hogy hiba történt a Notification Hubs sikerült kézbesíteni az üzenetet a leküldéses értesítéseket kezelő szolgáltatása. Például a terhelés méretének meghaladhatja a leküldéses értesítési szolgáltatás által engedélyezett maximális, vagy lehet, hogy a hitelesítő adatokat a Notification Hubs konfigurált érvénytelen. 

Webhelynaplókat leküldéses értesítési szolgáltatás hibák, használhatja a [EnableTestSend] tulajdonság. Ez a tulajdonság automatikusan engedélyezve van, a portál vagy a Visual Studio ügyfél teszt üzenetek küldésekor. Ez a tulajdonság segítségével tekintse meg a hibakeresési információkat. API-k segítségével tulajdonság is használhatja. Jelenleg használhatja a .NET SDK-ban. Előfordulhat hogy hozzáadandó minden ügyfél SDK-k. 

Használatához a **EnableTestSend** tulajdonságot, amelyben a REST-hívást hozzáfűzése egy lekérdezési karakterlánc paraméter *tesztelése* a küldési hívás végéig. Példa: 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Példa (.NET SDK-val)**

Íme egy példa natív előugró (bejelentési) értesítés küldése a .NET SDK használatával:

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

A végrehajtás végén **eredménye. Állapot** egyszerűen állapotok **a várólistában levő**. Az eredmények nem ad meg semmilyen Mi történt a leküldéses értesítési betekintést. 

Ezután használhatja a **EnableTestSend** logikai tulajdonság. Használja a **EnableTestSend** tulajdonság akkor inicializálása közben **NotificationHubClient** a hibaüzenet részletes állapottal kapcsolatos leküldéses értesítési szolgáltatás, amely akkor történik, ha az értesítés. A Küldés hívás vissza, mert csak azt követően a Notification Hubs elküldte az értesítést a leküldéses értesítési szolgáltatást a eredményének meghatározásához adja vissza további időt vesz igénybe. 

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

**Minta kimenet**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Az üzenet azt jelzi, hogy érvénytelen hitelesítő adatok a Notification hubs használatával vannak konfigurálva, vagy a központban regisztrációk probléma van. Azt javasoljuk, hogy ez a regisztráció törlése, és lehetővé teszik az ügyfél az üzenet elküldése előtt hozza létre újból a regisztrációs. 

> [!NOTE]
> A használatára a **EnableTestSend** tulajdonság fokozottan folyamatban van. Használja ezt a beállítást csak fejlesztési/tesztelési környezetben, és csak bizonyos regisztrációk. Hibakeresési értesítéseket kapni csak 10-eszközökre. Azt is maximális száma 10 percenként hibakeresési küld feldolgozása. 
> 
> 

### <a name="review-telemetry"></a>Telemetria áttekintése
* **Az Azure Portal használata**
   
    A portálon a tevékenység gyors áttekintést kaphat, ha az értesítési központban. 
   
    1. Az a **áttekintése** lapon megjelenik a regisztráció, értesítések és a hibák összesített nézete platformja. 
   
        ![Notification Hubs – áttekintés irányítópult][5]
   
    2. Az a **figyelő** lapon mélyebb betekintést sok más platform-specifikus metrikáinak is hozzáadhat. Vessen egy pillantást kifejezetten visszaadott, ha a Notification Hubs szolgáltatás megpróbálja a leküldéses értesítési szolgáltatáshoz értesítést küldeni a leküldéses értesítéseket kezelő szolgáltatása kapcsolatos hibákat. 
   
        ![Az Azure portál műveletnapló][6]
   
    3. Kezdje az alábbi áttekintésével **bejövő üzenetek**, **regisztrációs műveletek**, és **sikeres értesítések**. Ezt követően folytassa a leküldéses értesítéseket kezelő szolgáltatása vonatkozó hibákat nézze meg a-platform lapon. 
   
    4. Ha a hitelesítés az értesítési központ helytelenül beállítva, az üzenet **PNS hitelesítési hiba** jelenik meg. Ez a jól jelzi a leküldéses értesítési szolgáltatás hitelesítő adatok ellenőrzéséhez. 

* **Programozott hozzáférés**

    Programozott hozzáférés kapcsolatos további információkért lásd: ezek a cikkek: 

    * [Telemetria programozott hozzáférés]  
    * [A minta API-k hozzáférésének telemetriai adat] 

    > [!NOTE]
    > Több telemetriával kapcsolatos funkciók, exportálása, például és importálása a regisztráció és a telemetriai adatok hozzáférésének API-k, csak a Standard szolgáltatási rétegben elérhető. Ha úgy próbálja használni az ingyenesről vagy alapvető felhasználásokhoz szolgáltatásréteget, olyan kivétel üzenetet kap, ha az SDK-t, és a HTTP 403 (tiltott) hiba, ha a szolgáltatás közvetlenül a REST API-k használatára. 
    >
    >Telemetriai kapcsolatos funkciók használatához először győződjön meg róla az Azure portálon, hogy a szabványos szolgáltatásréteg használ.  
> 
> 

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
[Ismerkedés az Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[sablonok]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs – áttekintés]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[kapcsolatos FCM üzenetek]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer kód]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[A notification hubs eszköz regisztrációk megtekintése]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Részletes bemutatója: a Visual Studio 2013 Update 2 RC és az Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[A Visual Studio 2013 Update 3 és az Azure SDK 2.4 bejelentése]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Telemetria programozott hozzáférés]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[A minta API-k hozzáférésének telemetriai adat]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

