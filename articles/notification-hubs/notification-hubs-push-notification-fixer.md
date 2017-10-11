---
title: "Azure Notification Hubs – diagnosztikai irányelvek"
description: "Az Azure Notification Hubs szolgáltatással kapcsolatos gyakori hibák diagnosztizálása irányelveket."
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 32e3a2e6f840afd865375a622cfae0d33ba65090
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Az Azure Notification Hubs - diagnosztikai irányelvek
## <a name="overview"></a>Áttekintés
Azure Notification Hubs-ügyfél hozzáadunk leggyakoribb kérdések egyike van hogyan megállapíthatja, hogy miért nem láthatják az alkalmazás háttérrendszerből küldött értesítés jelenik meg az ügyféleszköz - értesítések eldobva hol és miért és megoldásához. Ebben a cikkben azt végighaladnia a különböző okokból, miért kerülhetnek vagy értesítések ne zárja az eszközökön. Keresztül, amelyben elemezheti és mérje fel, az alapvető ok többféleképpen is fog keresni. 

Első lépésként kiemelten fontos annak megértése, hogyan Azure Notification Hubs leküldéses értesítések értesítéseket az eszközökön.
![][0]

Egy tipikus send notification folyamatában, az üzenet érkezik a **alkalmazás háttér** való **Azure Notification Hub (NH)** amelynek pedig egyes feldolgozási figyelembe véve az összes regisztrációs szerepe a konfigurált címkék & címke kifejezések "cél", azaz összes regisztrációját, amely a leküldéses értesítések fogadásához kell meghatározni. A regisztrációk vagy azok egy részének a támogatott platformok -, iOS és Google, a Windows, Windows Phone között is kiterjedhet Kindle és az Android Kína Baidu. A célok létrejöttük NH majd leküldéses értesítések értesítéseket,-e osztani több kötegelt regisztrációk, hogy az adott eszközplatform **Push Notification szolgáltatás (PNS)** – például az Apple APNS, GCM a Google stb. NH a megfelelő pns-sel a klasszikus Azure-portál, az értesítési központ konfigurálása lapon állított be hitelesítő adatok alapján hitelesíti. A pns-sel, majd továbbítja az értesítések a megfelelő **ügyféleszközök**. Ez az a platform ajánlott leküldéses értesítéseket, és vegye figyelembe, hogy az értesítések kézbesítése utolsó szakasza történik, a platform pns-sel és az eszköz között. Ezért tudunk négy fő összetevőből - *ügyfél*, *alkalmazás háttér*, *Azure Notification Hubs (NH)* és *leküldéses értesítési szolgáltatások (PNS) * és a ezek az értesítések megszakad. További részleteket a ebbe az architektúrába érhető el a [Notification Hubs – áttekintés].

Értesítéseket hiba fordulhat elő, a kezdeti teszt előkészítés során ez arra utalhat a beállítási hibákra. fázis vagy az lehet, hogy hiba akkor fordulhat elő ahol mindegyike vagy némelyike, az értesítéseket is lehet megszakad éles környezetben jelölő néhány mélyebb alkalmazás vagy üzenetkezelési mintát probléma. A szakasz alatt követően áttekintjük kezdve közös, amelyek azt tapasztalhatja nyilvánvaló ritkább milyen és más nem sok különböző eldobott értesítések lehetőségeket. 

## <a name="azure-notifications-hub-mis-configuration"></a>Az Azure értesítési központ nem megfelelő konfiguráció
Az Azure Notification Hubs melyekkel hitelesítenie tudja magát a fejlesztői alkalmazás sikeresen értesítéseket küldhessen a megfelelő pns-sel való környezetében. Ez tette lehetővé a fejlesztői fejlesztői fiók létrehozása az adott platform (Google, Apple Windows stb.), és regisztrálja, ahol azok beszerezni a hitelesítő adatokat, amelyek úgy kell konfigurálni a Notification Hubs portál alkalmazását konfigurációs szakasz. Ha értesítés keresztül, első lépése annak érdekében, hogy a megfelelő hitelesítő adatok az értesítési központban az alkalmazáshoz, a platform adott fejlesztői fiók alatt létrehozott egyeztetése vannak-e konfigurálva kell lennie. Látni fogja a [első lépéseket bemutató Oktatóanyagainkat] hasznos a folyamat áthaladhat részletes módon. Az alábbiakban néhány gyakori hibás konfigurációk:

1. **Általános**
   
    egy.) Győződjön meg arról, hogy az értesítési központ nevére (nélkül gépelési) ugyanaz:
   
   * Amikor regisztrál az ügyfélről 
   * Ahol küldendő értesítések a háttérrendszerből  
   * Ha konfigurálta a PNS hitelesítő adatokat és 
   * Az ügyfél és a háttérkiszolgáló a van konfigurálva, amelynek SAS hitelesítő adatait. 
     
     b.) Győződjön meg arról, hogy a megfelelő SAS konfigurációs karakterláncok használja az ügyfél és az alkalmazás háttéralkalmazása. A szokásos megoldás, mint kell használnia a **DefaultListenSharedAccessSignature** az ügyfélen és **DefaultFullSharedAccessSignature** az alkalmazás háttérkiszolgálón (amely tennie engedély értesítés küldése a NH)
2. **Apple Push Notification (APN) szolgáltatás konfigurálása**
   
    Két, különböző hubs – egy termelési környezetben kell fenntartani, és egy másik tesztelési célból. Ez azt jelenti, hogy a védőfal mögötti környezet egy külön hubhoz használni kívánt tanúsítványt és egy külön hubhoz éles környezetben használni kívánja a tanúsítvány feltöltése. Ne próbálkozzon tanúsítványok különböző típusú tölthet fel ugyanabban a központban, mivel előfordulhat, hogy a sor le értesítési hibák. Ha egy helyen, ahol véletlenül feltöltött tanúsítvány különböző típusú ugyanabban a központban, ajánlott a központ törölje, majd indítsa el a friss. Ha valamilyen okból nem tudja törölni a hub majd legalább, törölnie kell a központ összes meglévő regisztrációját. 
3. **Google Cloud Messaging (GCM) konfigurálása** 
   
    egy.) Győződjön meg arról, hogy engedélyezi a "Google Cloud Messaging az Android" a felhő projekt alatt. 
   
    ![][2]
   
    b) ellenőrizze, hogy a "Server"kulcsot hoz létre, amíg a szükséges a hitelesítő adatokat, mely NH hitelesítéshez használni kívánt GCM-mel. 
   
    ![][3]
   
    c.) Győződjön meg arról, hogy az ügyfél, amely teljesen numerikus entitás, amely ezt úgy szerezheti be az irányítópultról konfigurálta "Projekt ID":
   
    ![][1]

## <a name="application-issues"></a>Alkalmazással kapcsolatos problémák
1) **Címkék / kifejezések címke**

Címkék vagy címke kifejezések segítségével végzi a szegmentálhatja a célközönséget, esetén mindig lehetséges, hogy az értesítést küld, amikor nincs nincs target alatt található meg a send hívásban címkék/címke kifejezések alapján. Célszerű áttekinteni a regisztrációk győződjön meg arról, hogy nincsenek-e címkék, amely megfelel a értesítést küldeni, és ellenőrizze a visszaigazoláshoz csak a regisztrációkat az ügyfélszámítógépekről. Például Ha az összes NH az a regisztráció kész volt mondja ki a címke "Politika", és küldünk egy értesítés a címke "Sport", akkor nem kap minden olyan eszközre. Összetett eset az alábbiak lehetnek címke kifejezések, ahol csak regisztrált "Címke A" vagy "Címke B", de közben értesítések küldését, "Címke A & & címke B" céloz meg. A tippek önálló diagnosztizálása területen az alábbi módon, amelyben áttekintheti a regisztrációk a címkékkel rendelkeznek együtt. 

2) **Sablon problémák**

Ha sablonokat használ, akkor győződjön meg arról, hogy az útmutató ismerteti [sablon útmutatást]. 

3) **Érvénytelen a regisztrációk**

Ha az értesítési központ konfigurálva lett, és címkék/címke kifejezéseket használták, érvényes célok, amelyhez az értesítések kell küldeni a keresett megfelelően eredő, NH következik be - párhuzamosan több feldolgozási kötegek ki minden kötegelt küldése üzenetek regisztrációk-készlethez. 

> [!NOTE]
> Végezzük párhuzamos feldolgozását, mert azt nem garantálják a sorrendet, amelyben az értesítések kézbesítése történik. 
> 
> 

Azure Értesítésközpontról most egy "a legtöbb egyszer" üzenet kézbesítési modell van optimalizálva. Ez azt jelenti, hogy azt kísérlet történt egy deaktiválás duplikálva lettek-e az, hogy az értesítés egynél többször szállítják ki egy eszközt. Győződjön meg arról, ez azt nézze át a regisztráció, és győződjön meg arról, hogy csak egy üzenetet küld eszköz azonosítója ténylegesen az üzenetet a pns-sel történő elküldése előtt. Egy köteg a rendszer elküldi a pns-sel, ami viszont fogad, és a regisztráció érvényesítése, mert, akkor előfordulhat, hogy a pns-sel észleli hiba történt legalább egy kötegben, a regisztráció az Azure NH hibát ad vissza, és leállítja a feldolgozást, így az adott kötegelt komple eldobása tely. Ez különösen igaz az APN szolgáltatás, amely adatfolyam TCP protokollt használ. Bár azt vannak optimalizálva, a szélső után szállítási, ez esetben azt a hibát okozó regisztrációs eltávolítja az adatbázisból, és ismételje meg a többi eszközt, hogy a kötegben értesítés kézbesítése.

Kaphat a sikertelen kézbesítési kísérlet az Azure Notification hub REST API-k használatával regisztráció elleni hiba információi: [üzenet Telemetriai: első értesítési üzenet Telemetriai](https://msdn.microsoft.com/library/azure/mt608135.aspx) és [PNS visszajelzés](https://msdn.microsoft.com/library/azure/mt705560.aspx). Tekintse meg a [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) például kód.

## <a name="pns-issues"></a>Pns-sel kapcsolatos problémák
Miután az értesítési üzenet megkapta a megfelelő pns-sel, majd a felelőssége, hogy az értesítés kézbesítése az eszköz. Az Azure Notification Hubs a képen látható itt kívül esik, és rendelkezik nem szabályozza, amikor, vagy ha az értesítési lehet juttatni az eszközre. Mivel a platform értesítési szolgáltatások közérthető robusztus, az értesítéseket, általában az eszközök eléréséhez néhány másodpercen belül a pns-sel való. Ha a pns-sel azonban a szabályozás Azure Notification Hubs alkalmazhatja az exponenciális biztonsági stratégia ki, és hogy a pns-sel nem érhető el, 30 percig marad, majd azt a házirend lejárjanak és azokat az üzeneteket véglegesen drop helyen. 

Ha egy PNS értesítések kézbesítésére kísérleteket, de az eszköz offline állapotban, az értesítés a pns-sel egy korlátozott ideig tárolja, és juttatni az eszközre, amikor elérhetővé válik. Egy adott alkalmazás csak egy legutóbbi értesítési tárolja. Több értesítés is érkezett kapnak, amikor az eszköz offline állapotban, ha minden új értesítés hatására a előzetesen elvesznek. Ez a viselkedés csak a legújabb értesítési legyenek nevezzük értesítést küld a APNS egyesítése és a GCM (összecsukás kulcsot használó) bezárásával. Ha az eszköz hosszú ideig offline állapotban marad, a rendszer elveti a tárolt volt folyamatban az értesítések. Forrás - [APNS útmutatást] & [GCM útmutató]

Az Azure Notification Hubs - keresztül az általános HTTP-fejléc is át összevonási kulcs `SendNotification` API (pl. a .NET SDK – `SendNotificationAsync`) átadása pedig HTTP-fejléceket is figyelembe, hogy a megfelelő PNS. 

## <a name="self-diagnose-tips"></a>Önálló diagnosztizálása tippek
Itt azt megvizsgálja a különböző diagnosztizálhatja és a rendszere következtében értesítési központ probléma merül fel:

### <a name="verify-credentials"></a>Hitelesítő adatok ellenőrzése
1. **PNS fejlesztői portálján**
   
    Azok a megfelelő PNS fejlesztői portal (APNS, GCM, WNS stb.) az ellenőrzéséhez a [első lépéseket bemutató Oktatóanyagainkat].
2. **Klasszikus Azure portál**
   
    Ugrás a konfigurálása lapon tekintse át, és a hitelesítő adatok megegyeznek a PNS-fejlesztői portálján a. 
   
    ![][4]

### <a name="verify-registrations"></a>Regisztráció ellenőrzése
1. **Visual Studio**
   
    Visual Studio fejlesztési használatakor meg csatlakozni a Microsoft Azure és a nézet és álló, lemezcsoport típusú többek között az értesítési központ a "Server Explorer" Azure-szolgáltatások kezelése. Ez elsősorban fontos fejlesztési és tesztelési célú környezetnek. 
   
    ![][9]
   
    Is megtekinthető és kezelhető a Központ amely szépen kategóriába sorolni platform, natív regisztrációk vagy sablon regisztrációs, címkéket, PNS azonosítója, regisztrációs azonosítót és a lejárati dátum. A regisztráció - parancsprogramok akkor hasznos, mondja ki, ha módosítani szeretné a címkéket szerkesztheti is. 
   
    ![][8]
   
   > [!NOTE]
   > Regisztráció szerkesztése a Visual Studio funkció csak akkor használható, fejlesztési és tesztelési célú korlátozott számú regisztráció során. Hárítsa el a regisztrációk tömeges szükség esetén, érdemes lehet az Exportálás/importálás regisztrációs funkcióit ismerteti, - [exportálási/importálási regisztrációk](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **A Service Bus explorer**
   
    Számos ügyfél használja a Szolgáltatásbusz explorer leírt ide - [Szolgáltatásbusz Explorer] megtekintéséhez és kezeléséhez az értesítési központban. Nyílt forráskódú projektként érhető el code.microsoft.com - érdemes [Szolgáltatásbusz Explorer kódot]

### <a name="verify-message-notifications"></a>Ellenőrizze az értesítő üzenetek
1. **Klasszikus Azure portál**
   
    A "Debug" lapot a vizsgálat értesítések küldéséhez az ügyfelek számára bármely szolgáltatás háttér be kellene és futtatása nélkül elvégezheti. 
   
    ![][7]
2. **Visual Studio**
   
    A Visual Studio comforts is küldhet tesztértesítést:
   
    ![][10]
   
    További a Visual Studio Notification Hub Azure explorer funkcióival ide - 
   
   * [Visual STUDIO Server Explorer áttekintése]
   * [Visual STUDIO Server Explorer blogbejegyzés - 1]
   * [Visual STUDIO Server Explorer blogbejegyzés - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Sikertelen értesítések Debug / tekintse át az értesítés kimenetelét
**EnableTestSend tulajdonság**

Notification Hubs keresztül küldi el, ha először azt csak lekérdezi a sorba állított a teendő, hogy az összes cél feldolgozása NH, és majd végül NH elküldi a pns-sel. Ez azt jelenti, hogy REST API-t vagy az ügyfél SDK bármelyike használ, a Küldés hívás sikeres visszatérési csak azt jelenti, hogy, hogy az üzenet sikeresen sorba lett értesítési központban. Mi történt, amikor NH végül kapott az üzenetet küldeni a PNS betekintést azt nem ad. Ha az értesítés nem érkező az ügyféleszközön, akkor annak a lehetősége, hogy NH sikerült kézbesíteni az üzenetet PNS, pl. a hasznos mérete meghaladta a megengedett a pns-sel hiba történt, vagy NH konfigurált hitelesítő adatok érvénytelen stb. Ahhoz, hogy a PNS-hibák betekintést, tulajdonságot, vezettek azt [EnableTestSend szolgáltatás]. Ez a tulajdonság teszt üzenetek küldésekor, a portál vagy a Visual Studio-ügyfél automatikusan engedélyezve van, és ezért lehetővé teszi a részletes hibakeresési információk megjelenítéséhez. Ezzel a .NET SDK, ahol már elérhető a példa véve API-k segítségével, és nem kerülnek be az összes ügyfél SDK-k felé. Ezzel a REST-hívást, hogy egyszerűen csak egy lekérdezési karakterlánc paraméter "teszt" a küldési hívás végén pl. hozzáfűzése 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Példa (.NET SDK-val)*

Tegyük fel, hogy a .NET SDK használatával natív bejelentési értesítés küldéséhez:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State`rendszer egyszerűen állapot `Enqueued` a végrehajtás nélkül a Mi történt a leküldéses betekintést végén. Most már használhat a `EnableTestSend` logikai tulajdonság inicializálása során a `NotificationHubClient` és kérheti le a PNS hibákat tapasztalt, miközben az értesítés részletes állapotát. A Küldés hívás itt lépnek további időt, mert csak adja vissza után NH elküldte az értesítést a eredményének meghatározásához a pns-sel való visszatéréshez. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Minta kimenet*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Ez az üzenet azt jelzi, vagy érvénytelen hitelesítő adatok van konfigurálva, az értesítési központ vagy a regisztráció, a központ problémát, és az ajánlott állomásokon lenne, ez a regisztráció törlése, és lehetővé teszik az ügyfél az üzenet elküldése előtt hozza létre újra. 

> [!NOTE]
> Vegye figyelembe, hogy a tulajdonság használatával fokozottan folyamatban van, és ezért csak akkor kell használnia a fejlesztési/tesztelési környezetben a regisztrációk korlátozott számú. Jelenleg csak hibakeresési értesítések küldéséhez 10-eszközökre. Azt is maximális száma 10 percenként hibakeresési küld feldolgozása. 
> 
> 

### <a name="review-telemetry"></a>Telemetria áttekintése
1. **Használja a klasszikus Azure portálon**
   
    A portál segítségével gyorsan áttekintheti a tevékenység az értesítési központ. 
   
    a) az "irányítópult" lapon megtekintheti a regisztráció, értesítések, valamint minden egyes platformhoz hibák összesített nézete. 
   
    ![][5]
   
    b) azt is megteheti sok más platform adott mérőszámok érvénybe különösen adott vissza, ha értesítést küldeni a pns-sel megpróbálja NH PNS konkrét hibáit a mélyebb betekintést a "Figyelés" lapon. 
   
    ![][6]
   
    c) meg kell kezdődnie, tekintse át a **bejövő üzenetek**, **regisztrációs műveletek**, **sikeres értesítések** majd lépjen / platform lapon tekintse át a pns-sel bizonyos hibákat. 
   
    d) Ha az értesítési központ PNS hitelesítési hiba látni fogja, majd a hitelesítési beállítások konfigurációja. Ez a jól jelzi a PNS-hitelesítő adatok ellenőrzéséhez. 

2) **Programozott hozzáférés**

További részletek itt- 

* [Telemetria programozott hozzáférés]
* [A minta API-k hozzáférésének telemetriai adat] 

> [!NOTE]
> Több telemetriával kapcsolatos funkciók, például **exportálási/importálási regisztrációk**, **Telemetriai API-k hozzáférésének** stb csak érhetők el a Standard csomagra. Ha használja ezeket a szolgáltatásokat, ha az ingyenes vagy az alapszintű csomag megpróbálja majd üzenetet fog kapni kivétel erről az SDK-t és egy HTTP 403 (tiltott) használata, ha azokat közvetlenül a REST API-k a közben. Győződjön meg arról, hogy áttelepítette legfeljebb Standard réteg a klasszikus Azure portálon keresztül.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs – áttekintés]: notification-hubs-push-notification-overview.md
[Bevezető oktatóanyagok]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Sablon útmutató]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNS útmutató]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM útmutató]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[A Szolgáltatásbusz Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[A Szolgáltatásbusz Explorer kódot]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Visual STUDIO Server Explorer áttekintése]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Visual STUDIO Server Explorer blogbejegyzés - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Visual STUDIO Server Explorer blogbejegyzés - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend szolgáltatás]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Telemetria programozott hozzáférés]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[A minta API-k hozzáférésének telemetriai adat]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

