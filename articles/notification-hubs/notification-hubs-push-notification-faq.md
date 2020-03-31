---
title: Az Azure Értesítési központok gyakori kérdések (GYIK) | Microsoft dokumentumok
description: Gyakori kérdések a megoldások Azure Értesítési központokban történő tervezésével és megvalósításával kapcsolatban.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
editor: jwargo
keywords: push értesítés, push értesítések, iOS push értesítések, android push értesítések, iOS push, android push
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 3212520f37d33a2d8fb1b071506f688b9f75f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263829"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Leküldéses értesítések az Azure Értesítési központtal: gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Mi az értesítési központok erőforrás-szerkezete?

Az Azure Notification Hubs két erőforrásszinttel rendelkezik: hubokkal és névterekkel. A hub egyetlen leküldéses erőforrás, amely képes tárolni a platformfüggetlen leküldéses információkat egy alkalmazás. A névtér egy régióban található csomópontok gyűjteménye. Az ajánlott leképezés egy névtérnek felel meg egy alkalmazással. Egy névtérben rendelkezhet egy éles elosztóval, amely együttműködik az éles alkalmazással, egy tesztelési központtal, amely együttműködik a tesztelési alkalmazással, és így tovább.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Mi az értesítési központok ármodellje?

A legújabb árképzési részletek az [Értesítési központok díjszabási] oldalán találhatók. Az értesítési központok számlázása a névtér szintjén történik. (A névtér meghatározásáról a "Mi az értesítési központok erőforrás-szerkezete?") Az Értesítési központok három réteget kínálnak:

* **Ingyenes**: Ez a szint jó kiindulási pont a leküldéses képességek felfedezéséhez. Éles alkalmazások hoz nem ajánlott. 500 eszközt és 1 millió leküldéset kap névtérenként havonta, szolgáltatásiszint-szerződés (SLA) garancia nélkül.
* **Alapszintű:** Ez a szint (vagy a standard szint) kisebb éles alkalmazások számára ajánlott. 200 000 eszközt és 10 millió leküldéset kap névtérenként havonta alapkonfigurációként.
* **Standard**: Ez a szint közepes és nagy éles alkalmazások számára ajánlott. 10 millió eszközt és 10 millió leküldéset kap névtérenként havonta alapkonfigurációként. Gazdag telemetriai adatokat tartalmaz (a leküldéses állapottal kapcsolatos további adatokat).

Standard szintű funkciók:

* **Gazdag telemetriai adatok:** Az értesítési központok üzenetenkéntt telemetriával nyomon követheti a leküldéses kérelmeket és a platform értesítési rendszer visszajelzései hibakereséshez.
* **Több-bérlős:** A platform értesítési rendszer hitelesítő adatok névtér szinten. Ez a beállítás lehetővé teszi, hogy könnyen feloszthatja a bérlők hubok ugyanabban a névtérben.
* **Ütemezett leküldéses:** Az értesítésekbármikor elküldhetők.
* **Tömeges műveletek**: Engedélyezi a regisztrációk exportálási/importálási funkcióját a [Regisztrációk exportálása/importálása] bizonylatban leírtak szerint.

### <a name="what-is-the-notification-hubs-sla"></a>Mi az értesítési központok SLA?

Alapszintű és szabványos értesítési központok szintek esetén a megfelelően konfigurált alkalmazások az idő legalább 99,9 százalékában küldhetnek leküldéses értesítéseket, vagy regisztrációs felügyeleti műveleteket hajthatnak végre. Ha többet szeretne megtudni az SLA-ról, látogasson el az [Értesítési központok SLA-oldalára.](https://azure.microsoft.com/support/legal/sla/notification-hubs/)

> [!NOTE]
> Mivel a leküldéses értesítések a külső platformértesítési rendszerektől, például az Apple leküldéses értesítési szolgáltatásától (APN) és a Google Firebase Cloud Messaging (FCM) szolgáltatásától függenek, az ilyen üzenetek kézbesítésére nincs SLA garancia. Miután az értesítési központok elküldik a kötegeket a platformértesítési rendszereknek (garantált SLA), a platformértesítési rendszerek felelőssége a leküldéses leküldések kézbesítése (nincs garantált SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hogyan frissíthetem vagy minősíthetem le a hubomat vagy a névteremet egy másik szintre?

Nyissa meg az **[Azure Portal]** > **értesítési központok névterét** vagy **értesítési központokat.** Jelölje ki a frissíteni kívánt erőforrást, és lépjen a **Tarifacsomag ra.** Vegye figyelembe a következő követelményeket:

* A frissített tarifacsomag a névtér *összes* hubjára vonatkozik, amelyekkel dolgozik.
* Ha az eszközök száma meghaladja a korlátot, amelyhez visszaminősít, törölnie kell az eszközöket a visszalépés előtt.

## <a name="design-and-development"></a>Tervezés és fejlesztés

### <a name="which-server-side-platforms-do-you-support"></a>Mely szerveroldali platformokat támogatja?

A kiszolgáló SDK-k .NET, Java, Node.js, PHP és Python esetén érhetők el. Az értesítési központok API-jai REST-felületeken alapulnak, így közvetlenül dolgozhat a REST API-kkal, ha különböző platformokat használ, vagy nem szeretne további függőséget. További információt az [Értesítési központok REST API-k] lapján talál.

### <a name="which-client-platforms-do-you-support"></a>Milyen ügyfélplatformokat támogat?

A leküldéses értesítések támogatottak [iOS,](notification-hubs-ios-apple-push-notification-apns-get-started.md) [Android,](notification-hubs-android-push-notification-google-fcm-get-started.md) [Windows Universal,](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), Android [China (Via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin[(iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) és Android, és [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). További információt az [Értesítési központok első lépései oktatóanyagok] lapon talál.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Támogatja a szöveges üzeneteket, e-maileket vagy webes értesítéseket?

Az Értesítési központok értesítéseket küld a mobilalkalmazásokat futtató eszközöknek. Nem biztosít e-mail vagy szöveges üzenet képességeket. Az Értesítési központok nem biztosít a böngészőben lévő leküldéses értesítési szolgáltatást sem. Az ügyfelek ezt a funkciót a signalr használatával valósíthatják meg a támogatott kiszolgálóoldali platformokon. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hány eszközt támogathatok, ha leküldéses értesítéseket küldök az Értesítési központokon keresztül?

A támogatott eszközök számával kapcsolatos részleteket az [Értesítési központok díjszabási] oldalán találja.

Ha több mint 10 millió regisztrált eszköz támogatására van szüksége, több hubon kell particionálnia az eszközöket.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hány leküldéses értesítést küldhetek?

A kiválasztott szinttől függően az Azure Értesítési központok automatikusan felskálázódik a rendszeren keresztül áramló értesítések száma alapján.

> [!NOTE]
> A teljes használati költség növekedhet az elküldött leküldéses értesítések száma alapján. Győződjön meg arról, hogy tisztában van az [Értesítési központok díjszabási] lapján ismertetett rétegkorlátokkal.

Ügyfeleink az Értesítési központok segítségével naponta több millió leküldéses értesítést küldhetnek. Nem kell semmi különlegeset tennie a leküldéses értesítések elérésének méretezéséhez, amíg az Azure Értesítési központokat használja.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Mennyi ideig tart, amíg az elküldött leküldéses értesítések elérik az eszközömet?

Normál használat esetén, ahol a bejövő terhelés konzisztens, és egyenletes, az Azure Értesítési Központ legalább *1 millió leküldéses értesítést tud feldolgozni percenként.* Ez az arány a címkék számától, a bejövő küldés jellegétől és más külső tényezőktől függően változhat.

A becsült szállítási idő alatt a szolgáltatás kiszámítja a célokat platformonként, és a regisztrált címkék vagy címkekifejezések alapján továbbítja az üzeneteket a leküldéses értesítési szolgáltatásba (PNS). A PNS felelőssége, hogy értesítéseket küldjön az eszközre.

A PNS nem garantál SLA-t az értesítések kézbesítéséhez. A legtöbb leküldéses értesítés azonban az értesítési központoknak való küldéstől számított néhány percen belül (általában 10 percen belül) érkezik a céleszközökre. Néhány értesítés több időt vehet igénybe.

> [!NOTE]
> Az Azure Notification Hubs rendelkezik egy szabályzatot, amely eldobja a leküldéses értesítéseket, amelyek nem kézbesítették a PNS 30 percen belül. Ez a késés számos okból fordulhat elő, de leggyakrabban azért, mert a PNS szabályozása az alkalmazás.

### <a name="is-there-any-latency-guarantee"></a>Van-e késési garancia?

A leküldéses értesítések jellege miatt (külső, platformspecifikus PNS-ek kézbesítik őket), nincs késési garancia. A leküldéses értesítések többsége általában néhány percen belül kézbesítésre került.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Mit kell figyelembe venni, amikor névterekkel és értesítési központokkal rendelkező megoldást tervez?

#### <a name="mobile-appenvironment"></a>Mobilalkalmazás/környezet

* Mobilalkalmazásonként és környezetenként egy értesítési központot használjon.
* Egy több-bérlős forgatókönyv, minden bérlő nek rendelkeznie kell egy külön hub.
* Soha ne ossza meg ugyanazt az értesítési központot éles és tesztkörnyezetekben. Ez a gyakorlat problémákat okozhat az értesítések küldésekor. (Az Apple külön hitelesítő adatokkal kínál sandbox- és éles leküldéses végpontokat.)
* Alapértelmezés szerint az Azure Portalon vagy a Visual Studio integrált Azure-összetevőjén keresztül küldhet tesztértesítéseket regisztrált eszközeire. A küszöbérték 10 eszközre van beállítva, amelyek véletlenszerűen vannak kiválasztva a regisztrációs készletből.

> [!NOTE]
> Ha a hubot eredetileg Apple sandbox-tanúsítvánnyal konfigurálták, majd újrakonfigurálták az Apple éles tanúsítványhasználatára, az eredeti eszköztokenek érvénytelenek. Az érvénytelen jogkivonatok sikertelensé taszították a leküldéseket. Különítse el éles és tesztelési környezeteit, és használjon különböző elosztókat a különböző környezetekben.

#### <a name="pns-credentials"></a>PNS-hitelesítő adatok

Ha egy mobilalkalmazás regisztrálva van egy platform fejlesztői portálján (például az Apple vagy a Google), a rendszer alkalmazásazonosítót és biztonsági jogkivonatokat küld. Az alkalmazás-háttérrendszer biztosítja ezeket a jogkivonatokat a platform PNS-hez, így leküldéses értesítésekküldhetők az eszközökre. A biztonsági tokenek lehetnek tanúsítványok (például Apple iOS vagy Windows Phone) vagy biztonsági kulcsok (például Google Android vagy Windows). Ezeket az értesítési központokban kell konfigurálni. A konfiguráció általában az értesítési-hub szintjén történik, de több-bérlős forgatókönyv esetén a névtér szintjén is elvégezhető.

#### <a name="namespaces"></a>Névterek

A névterek központi telepítési csoportosításhoz használhatók. Azt is fel lehet használni, hogy képviselje az összes értesítési központok az összes bérlő az azonos alkalmazás egy több-bérlős forgatókönyvben.

#### <a name="geo-distribution"></a>Geoeloszlás

A földrajzi terjesztés nem mindig kritikus a leküldéses értesítési forgatókönyvekben. Különböző PNS-ek (például APN-ek vagy FCM), amelyek leküldéses értesítéseket az eszközök nem egyenletesen elosztott.

Ha globálisan használt alkalmazással rendelkezik, különböző névterekben hozhat létre hubokat az Értesítési központok szolgáltatás használatával a világ különböző Azure-régióiban.

> [!NOTE]
> Nem javasoljuk ezt a megállapodást, mert növeli a felügyeleti költségeket, különösen a regisztrációk esetében. Ezt csak akkor kell megtenni, ha kifejezetten szükség van rá.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Az alkalmazás háttérrendszeréről vagy közvetlenül az ügyféleszközökről kell regisztrációkat csinálnom?

Az alkalmazás háttérszolgáltatásából történő regisztrációk akkor hasznosak, ha a regisztráció létrehozása előtt hitelesítenie kell az ügyfeleket. Akkor is hasznosak, ha olyan címkékkel rendelkezik, amelyeket az alkalmazás háttér-logikája alapján az alkalmazás háttér-tartalékának kell létrehoznia vagy módosítania. További információkért látogasson el a [háttérrendszer regisztrációs útmutató] és [háttér-regisztrációs útmutató 2] oldalakon.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Mi a leküldéses értesítés kézbesítési biztonsági modellje?

Az Azure Notification Hubs [egy megosztott hozzáférésű aláíráson](../storage/common/storage-dotnet-shared-access-signature-part-1.md)alapuló biztonsági modellt használ. Használhatja a megosztott hozzáférésű aláírás jogkivonatokat a gyökérnévtér szintjén vagy a részletes értesítési központ szintjén. A megosztott hozzáférésű aláírás-jogkivonatok beállíthatók úgy, hogy különböző engedélyezési szabályokat kövessenek, például üzenetengedélyek küldéséhez vagy értesítési engedélyek figyeléséhez. További információt az [Értesítési központok biztonsági modell] dokumentumcímű dokumentumban talál.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hogyan kezeljem a bizalmas hasznos adatokat a leküldéses értesítésekben?

Minden értesítést a platform PNS-e kézbesít a céleszközökre. Amikor egy értesítést küld az Azure Notification Hubs, feldolgozása és átadása a megfelelő PNS-hez.

Minden kapcsolat, a küldő től az Azure Értesítési központok a PNS, HTTPS-t használ.

> [!NOTE]
> Az Azure Értesítési központok nem naplózza az üzenetek hasznos adatait.

Bizalmas hasznos adatok küldéséhez javasoljuk a Biztonságos leküldéses minta használatát. A feladó egy ping értesítést küld egy üzenetazonosítóval az eszköznek a bizalmas tartalom nélkül. Amikor az alkalmazás az eszközön megkapja a hasznos adat, az alkalmazás meghívja a biztonságos API-t közvetlenül az üzenet részleteinek beolvasásához. A minta megvalósításának módjáról az [Értesítési központok biztonságos leküldéseoktató anyagoldalában] talál.

## <a name="operations"></a>Műveletek

### <a name="what-support-is-provided-for-disaster-recovery"></a>Milyen támogatást nyújtanak a katasztrófa utáni helyreállításhoz?

Metaadat-vész-helyreállítási lefedettséget biztosítunk a mi részünkről (az értesítési központok neve, a kapcsolati karakterlánc és egyéb kritikus információk). Vész-helyreállítási forgatókönyv aktiválásakor a regisztrációs adatok az értesítési központok infrastruktúrájának egyetlen elveszett *szegmense.* A helyreállítás után végre kell hajtania egy megoldást az új hubba való újrafeltöltéshez:

1. Hozzon létre egy másodlagos értesítési központot egy másik adatközpontban. Azt javasoljuk, hogy hozzon létre egyet az elejétől, hogy megvédje Önt egy vész-helyreállítási esemény, amely hatással lehet a felügyeleti képességek. A vész-helyreállítási esemény idején is létrehozhat egyet.

2. Feltöltheti a másodlagos értesítési központot az elsődleges értesítési központból történő regisztrációkkal. Nem javasoljuk, hogy mindkét csomóponton próbálja meg karbantartani a regisztrációkat, és a regisztrációk beérkezésekeksorán szinkronban tartsa őket. Ez a gyakorlat nem működik jól, mert a pns oldalon a regisztrációk eredendő tendenciája lejár. Az értesítési központok törli őket, mivel pns-visszajelzést kap a lejárt vagy érvénytelen regisztrációkról.  

Két javaslatunk van az alkalmazás háttérrendszerekhez:

* Használjon olyan alkalmazás-háttért, amely a regisztrációk egy adott készletét tartja fenn a végén. Ezután tömeges beszúrást hajthat végre a másodlagos értesítési központba.
* Használjon egy alkalmazás-háttérrendszer, amely leáll egy rendszeres kiírása regisztrációk az elsődleges értesítési központ biztonsági másolatként. Ezután tömeges beszúrást hajthat végre a másodlagos értesítési központba.

> [!NOTE]
> Regisztrációk A standard csomagban elérhető export/importálás funkció a [Regisztrációk exportálása/importálása] dokumentumban található.

Ha nem rendelkezik háttérrendszerrel, amikor az alkalmazás elindul a céleszközökön, új regisztrációt hajtanak végre a másodlagos értesítési központban. Végül a másodlagos értesítési központ az összes aktív eszköz regisztrálva lesz.

Lesz egy időszak, amikor a bontatlan alkalmazásokkal rendelkező eszközök nem kapnak értesítéseket.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Az összes adatomat titkosított formában tárolja?

Az Azure Értesítési központok titkosítja az összes inaktív ügyféladatot, a regisztrációs címkék kivételével. Ezért ne tároljon személyes vagy bizalmas adatokat címkék használatával.

### <a name="is-there-audit-log-capability"></a>Van naplófunkció?

Igen. Minden értesítési központ felügyeleti művelet frissíti az Azure-tevékenységnaplót, amely az [Azure Portalon]elérhető. Az Azure-tevékenységnapló betekintést nyújt az előfizetésekben lévő erőforrásokon végrehajtott műveletekbe. A tevékenységnapló segítségével meghatározhatja, hogy az előfizetésben lévő erőforrásokhoz milyen, ki és mikor készült az írási műveletekhez.Using the Activity Log, you can determine the what, who, and when for any write operations (PUT, POST, DELETE) made for the resources in your subscription. A műveletek és más releváns tulajdonságok állapotát is megismerheti. Azonban. a tevékenységnapló nem tartalmazza az olvasási (GET) műveletet.

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás

### <a name="what-troubleshooting-capabilities-are-available"></a>Milyen hibaelhárítási lehetőségek érhetők el?

Az Azure Notification Hubs számos hibaelhárítási funkciót kínál, különösen az eldobott értesítések leggyakoribb forgatókönyve esetén. További információt az [Értesítési központok hibaelhárítási tanulmányában] talál.

### <a name="what-telemetry-features-are-available"></a>Milyen telemetriai funkciók érhetők el?

Az Azure Notification Hubs lehetővé teszi a telemetriai adatok megtekintését az [Azure Portalon.] A mérőszámok részletei az [Értesítési központok metrikák] oldalán érhetők el.

A mérőszámok programozott módon is hozzáférhetnek. További információkért tekintse át a következő cikkeket:

- [Az Azure Monitor metrikák lekérése a .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)használatával. Ez a minta a felhasználónevet és a jelszót használja. Tanúsítvány használatához a FromServicePrincipal metódus túlterhelésével adjon meg egy tanúsítványt a [példában](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)látható módon. 
- [Metrikák és tevékenységnaplók beszerzése egy erőforráshoz](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Monitoring REST API forgatókönyve](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> A sikeres értesítések egyszerűen azt jelentik, hogy a leküldéses értesítések kézbesítve lettek a külső PNS-hez (például az iOS és a macOS vagy az Android-eszközökfcm-hez készült APN-ek). A PNS felelőssége, hogy az értesítéseket a céleszközöknek kézbesítse. A PNS általában nem teszi elérhetővé a kézbesítési mutatókat harmadik felek számára.  

[Azure-portál]: https://portal.azure.com
[Értesítési központok díjszabása]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Értesítési központok REST API-jai]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Értesítési központok – első lépések oktatóanyagai]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Háttérrendszer regisztrációs útmutatója]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Háttérrendszer regisztrációs útmutató2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Értesítési központok biztonsági modellje]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Értesítési központok biztonságos leküldéses oktatóanyaga]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Értesítési központok – hibaelhárítás]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Értesítési központok metrikák]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Regisztrációk exportálása/importálása]: https://docs.microsoft.com/azure/notification-hubs/export-modify-registrations-bulk
[Azure-portál]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
