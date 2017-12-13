---
title: "Az Azure Notification hubs használatával: Gyakori kérdések (GYIK) |} Microsoft Docs"
description: "A Notification Hubs megoldások designing/végrehajtási – gyakori kérdések"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "leküldéses értesítések, leküldéses értesítések, leküldéses értesítések iOS, android leküldéses értesítések, ios leküldéses, android leküldéses"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.openlocfilehash: d19a1b7c8d50ef0fde3cf65c9fd469bc34a27adc
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Leküldéses értesítések az Azure Notification hubs használatával: gyakori kérdések
## <a name="general"></a>Általános kérdések
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Mi az az erőforrás-szerkezet értesítési központját?

Az Azure Notification Hubs két erőforrás olyan szintje van: hubok és névtereket. A központ a egyetlen leküldéses erőforrása, amely a platformok közötti leküldéses adatokat egy alkalmazás tárolására képes. A névtér egy régióban hubok gyűjteménye.

Ajánlott leképezési megegyezik egy névtér egy alkalmazással. Egy adott névtéren belül is egy üzemi hub, amely a termelési alkalmazással együttműködve, egy tesztelési hub, amely a tesztelési alkalmazást, és így tovább működik.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Mi az a Notification Hubs ár modellt?
A legújabb árképzése található meg a [Notification Hubs-díjszabás] lap. A Notification Hubs számlázása a névterek szintjén történik. (A meghatározása névtér: "Mi az az erőforrás-szerkezet értesítési központját?") A Notification Hubs három réteg kínálja:

* **Szabad**: Ebben a rétegben az jó kiindulási pont felderítését lehetővé tevő leküldéses képességeit. Termelési alkalmazások esetében nem ajánlott. 500 eszközök kap, és nincs szolgáltatás szolgáltatásiszint-szerződés (SLA) garantált száma havonta, névtér része kimenő 1 millió.
* **Alapszintű**: Ebben a rétegben (vagy Standard csomagra) ajánlott kisebb termelési alkalmazások esetében. 200 000 eszköz kap, és 10 millió leküldéses értesítések száma havonta kiindulópontként névtér része. Kvóta növekedési beállítások tartoznak.
* **Standard**: Ebben a rétegben nagy üzemi alkalmazások közepes javasoljuk. 10 millió eszközök kap, és 10 millió leküldéses értesítések száma havonta kiindulópontként névtér része. Kvóta növelését beállítások és a gazdag telemetriai képességek érhetők el.

Standard szint funkciói:
* **Részletes telemetria**: bármely leküldéses kérelmek és Platform Notification System visszajelzés nyomon a hibakereséshez a Notification Hubs egy üzenet Telemetriai is használhatja.
* **Több vállalat kiszolgálása**: a névtér szinten Platform Notification System hitelesítő adatok használhatók. Ez a beállítás lehetővé teszi, hogy könnyen bérlők ossza fel az azonos névtérben hubok.
* **Ütemezett leküldéses**: ütemezheti az értesítések küldéséhez bármikor kiküldésre.

### <a name="what-is-the-notification-hubs-sla"></a>Mi az a Notification Hubs SLA-t?
Basic és standard szintű Notification Hubs rétegek, a megfelelően konfigurált alkalmazások leküldéses értesítéseket küldeni, vagy legalább 99,9 %-ában regisztrációs felügyeleti műveleteket végezhet. A szolgáltatásiszint-szerződést kapcsolatos további tudnivalókért keresse fel a [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) lap.

> [!NOTE]
> Leküldéses értesítések függ külső Platform Notification System (például az Apple APNS és Google FCM), mert nincs egyetlen (SLA) garantált ezek az üzenetek a szállítási. Miután a Notification hubs használatával küldi el a kötegek Platform Notification System (garantált SLA), feladata a, a Platform Notification System képes biztosítani a leküldéses értesítések (nem garantált SLA).

### <a name="which-customers-are-using-notification-hubs"></a>Mely felhasználók, akik használják a Notification Hubs?
Sok ügyfél a Notification Hubs használatával. Az itt felsorolt néhány fontos néhányat a meglévők közül:

* Sochi 2014: Több száz érdeklődési körök alapján, 3 + millió eszközöket, és 150 + millió értesítések elküldése két hétben. [Esettanulmány: Sochi]
* Skanska: [esettanulmány: Skanska]
* Budapest alkalommal: [esettanulmány: Budapest alkalommal]
* Mural.LY: [esettanulmány: Mural.ly]
* 7Digital: [esettanulmány: 7Digital]
* Bing-alkalmazásokat: Eszközök tízezer értesítésküldést 3 millió / nap.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hogyan frissítést vagy a központi vagy a másik réteghez névtér használni?
Lépjen a  **[Azure-portálon]** > **Notification Hubs névterek** vagy **Notification Hubs**. Válassza ki a frissíteni, majd a kívánt erőforrás **Tarifacsomagot**. Vegye figyelembe az alábbi követelményeknek:

* A frissített tarifacsomag vonatkozik *összes* hubok dolgozunk a névtérben.
* Ha az eszközök száma meghaladja a korlátot, a réteg, akkor még a visszaminősítése, eszközök törlése előtt meg használni szeretné.


## <a name="design-and-development"></a>Tervezési és fejlesztési
### <a name="which-server-side-platforms-do-you-support"></a>Mely kiszolgálóoldali platformokon támogatott?
A .NET, Java, Node.js, PHP és Python Server SDK-k érhetők el. Notification Hubs API-k REST felületek, alapulnak, így közvetlenül a REST API-k közben használható, ha nem szeretné, további függőségi vagy használjuk a különböző platformokon. További információkért látogasson el a [Notification hub REST API-k] lap.

### <a name="which-client-platforms-do-you-support"></a>Milyen ügyfélplatformokat támogatja?
Leküldéses értesítések támogatottak [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [univerzális Windows-](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [(Keresztül Baidu) android Kína](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) és [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome-alkalmazások](notification-hubs-chrome-push-notifications-get-started.md), és [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). További információkért látogasson el a [Notification Hubs használatának első lépéseit oktatóanyagok] lap.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Támogatott az SMS-üzenet, e-mailek vagy webes értesítések?
A Notification Hubs elsősorban célja, hogy az értesítések küldése a mobile apps szolgáltatásban. Azt nem adja meg e-mailben vagy szöveges üzenetek kezelésére képes. Azonban ezeket a képességeket biztosító harmadik fél platformok integrálható a Notification Hubs használatával natív leküldéses értesítések küldéséhez [Mobile Apps].

A Notification Hubs is nem biztosít egy böngészőn belüli leküldéses értesítési kézbesítési szolgáltatás nem. Az ügyfelek ezt a funkciót a támogatott platformok kiszolgálóoldali fölött SignalR használatával is létrehozható. Ha meg kíván értesítést küldeni a Chrome védőfal böngészőben megjelenő alkalmazásokba, tekintse meg a [Chrome-alkalmazások oktatóanyag].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Hogyan történik a Mobile Apps és az Azure Notification hubs használatával kapcsolatos és mikor használni őket?
Ha vissza egy meglévő mobilalkalmazás end, és azt szeretné, csak a leküldéses értesítések küldéséhez funkció hozzáadása, használhatja az Azure Notification hubs használatával. Ha be szeretné állítani a mobilalkalmazás biztonsági másolatot end teljesen új, fontolja meg az Azure App Service Mobile Apps szolgáltatásának használatát. A mobilalkalmazások automatikusan egy értesítési központot látja el, hogy könnyen küldhet leküldéses értesítést a mobilalkalmazásban háttérből. A Mobile Apps árképzési tartalmaz egy értesítési központot alap költsége. Ha csak a a belefoglalt leküldéses értesítések túllépi kell fizetnie. További részletek a költségek, látogasson el a [App Service szolgáltatás díjszabása] lap.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Eszközök számáról is támogatásnak Ha küldhető a leküldéses értesítések a Notification Hubs használatával?
Tekintse meg a [Notification Hubs-díjszabás] lapon talál részletes információt a támogatott eszközök számát.

Ha több mint 10 millió regisztrált eszközök esetében segítségre [, lépjen velünk kapcsolatba](https://azure.microsoft.com/overview/contact-us/) közvetlenül és segíteni fogunk a megoldás méretezése.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hány leküldéses értesítéseket is küldhető?
Attól függően, hogy a kijelölt réteg Azure Notification Hubs automatikusan méretezés során a rendszer átfutó értesítések száma alapján.

> [!NOTE]
> Az általános felhasználás költsége növelheti a kiszolgált leküldéses értesítések száma alapján. Gondoskodjon arról, hogy tisztában legyen a leírt réteg korlátokat a [Notification Hubs-díjszabás] lap.
> 
> 

Az ügyfelek a Notification Hubs használatával naponta a több millió leküldéses értesítést küldeni. Nincs tennivalója különleges révén a leküldéses értesítések az méretezési mindaddig, amíg az Azure Notification Hubs használata.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Mennyi időt vesz igénybe a küldött leküldéses értesítések az eszköz eléréséhez?
Normál-használata esetén, ahol a bejövő terhelés konzisztens, és akkor is, Azure Notification hubs használatával tud feldolgozni legalább *1 millió leküldéses értesítést küld egy percet*. Ez a számláló a címkék száma, a bejövő küld, és más külső tényezők jellegétől függően változhat.

A becsült szállítási idő alatt a szolgáltatás számítja ki a célokat, minden egyes platformhoz, és útvonalak üzenetek számára a leküldéses értesítési szolgáltatás (PNS) regisztrált címkék vagy címke kifejezések alapján. A feladata az értesítések küldéséhez az eszköz PNS.

A pns-sel nem garantálható, hogy bármely SLA értesítések kézbesítéséhez. Azonban a legtöbb leküldéses értesítések vannak a céleszközökre kézbesítendő (általában legalább 10 perccel) néhány percen belül a Notification Hubs küldött óta. Néhány értesítések több időt vehet igénybe.

> [!NOTE]
> Az Azure Notification Hubs rendelkezik egy házirendet a pns-sel 30 percen belül nem kézbesített leküldéses értesítések eldobni. Ez a késleltetés akkor fordulhat elő, a számos oka, de a legtöbb gyakran, mert a pns-sel az alkalmazás a szabályozás.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>Van bármilyen késés garancia?
(Azok érkeznek által egy külső, a platform-specifikus PNS) leküldéses értesítések jellege miatt nincs késés garancia. Általában a leküldéses értesítések a legtöbb i néhány percen belül.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Mit kell a névtereket és a notification hubs egy megoldás tervezése során figyelembe kell venni?
#### <a name="mobile-appenvironment"></a>Mobile app-környezet
* Használjon egy értesítési központ mobilalkalmazás környezet másodpercenkénti száma.
* Több-bérlős-konfigurációban mindegyik bérlő külön hubot kell rendelkeznie.
* Ne ossza meg az azonos értesítési központ üzemi és tesztelési környezetben. Ez az eljárás problémákat okozhat, értesítések küldéséhez. (Apple védőfal és a termelési leküldéses végpont, külön-külön hitelesítő adatokat biztosít.)
* Alapértelmezés szerint küldhet tesztértesítést a regisztrált eszközökön az Azure-portálon vagy az Azure integrált összetevőt a Visual Studio használatával. A küszöbértéke 10-eszközökre, a regisztrációs készletből véletlenszerűen kiválasztott.

> [!NOTE]
> Ha a központ volt konfigurálva, az Apple védőfal tanúsítványt, és majd Apple éles tanúsítvány használatára lett konfigurálni, az eredeti eszköz jogkivonatok érvénytelen. Érvénytelen jogkivonatok miatt a leküldéses értesítések sikertelen. A termelési és tesztkörnyezetek külön, és különböző hubok használni a különböző környezetekhez.
> 
> 

#### <a name="pns-credentials"></a>PNS hitelesítő adatok
Ha a mobilalkalmazás regisztrálva lett a platform fejlesztői portálján (például az Apple vagy a Google), az alkalmazás azonosítóját és a biztonsági jogkivonatok küldése. Az alkalmazás háttér, hogy a leküldéses értesítések is küldhetők eszközöket biztosít a platform PNS ezeket a jogkivonatokat. Biztonsági jogkivonatok tanúsítványok (például Apple iOS- vagy Windows Phone) vagy a biztonsági kulcsok (például a Google Android vagy Windows) lehet. Azok a notification hubs használatával kell konfigurálni. Konfigurációs általában a értesítésiközpont szintjén történik, de azt is megteheti egy több-bérlős forgatókönyvben a névterek szintjén.

#### <a name="namespaces"></a>Névterek
Névterek telepítési csoportosításához használható. Akkor is használható minden értesítési központok képviselő ugyanahhoz az alkalmazáshoz több-bérlős esetén az összes bérlői számára.

#### <a name="geo-distribution"></a>Földrajzi terjesztési
Földrajzi terjesztési fontosságú nem mindig a leküldéses értesítési forgatókönyvek. Nem egyenletes eloszlású a különböző PNSes (például APNS vagy GCM), hogy a leküldéses értesítések az eszközökre.

Ha egy alkalmazás, amelynek globálisan, létrehozhat hubok különböző névterekben a világ különböző Azure-régiók a Notification Hubs szolgáltatással.

> [!NOTE]
> Ezzel az elrendezéssel nem ajánlott, mert növeli a felügyeleti költségek, különösen a regisztrációhoz. El kell végezni, csak akkor, ha explicit szükség van.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>Teendő regisztrációk az alkalmazás háttérből, vagy közvetlenül az ügyfél eszközöket?
Az alkalmazás háttérből regisztrációk hasznosak, ha a regisztráció létrehozása előtt hitelesíti az ügyfeleket, hogy. Fontosságúak is hasznos, ha rendelkezik, amely alapján a app logic alkalmazást háttér módosította hozhatók létre, és ki kell címkék. További információkért látogasson el a [háttér regisztrációs útmutatót] és [háttér regisztrációs útmutatót 2] lapokat.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Mi az a leküldéses értesítési biztonsági modell?
Az Azure Notification Hubs használ egy [közös hozzáférésű jogosultságkódot](../storage/common/storage-dotnet-shared-access-signature-part-1.md)-alapú biztonsági modellt. A megosztott hozzáférési aláírást jogkivonatok használhatja a gyökérszinten névtér vagy a részletes notification hub szinten. Közös hozzáférésű jogosultságkód jogkivonatok hajtsa végre a különböző engedélyezési szabályokat, például a küldendő üzenet engedélyek vagy tartozó értesítési engedélyek állítható be. További információkért lásd: a [Notification Hubs biztonsági modell] dokumentum.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hogyan kezelje a leküldéses értesítések bizalmas tartalom?
Az értesítések céleszközökre kézbesítendő által a platform PNS. Azure Notification Hubs egy értesítést küld, amikor feldolgozása és a megfelelő PNS átadott.

Az összes kapcsolat, az Azure Notification Hubs a pns-sel, hogy a küldőtől a HTTPS használatára.

> [!NOTE]
> Az Azure Notification Hubs nem naplózza a tartalom az üzenetek az bármely módon.
> 
> 

Küldött bizalmas Payload van jelen, ajánlott biztonságos leküldéses minta használatával. A küldő egy ping értesítési üzenet-azonosító nélkül a bizalmas tartalom továbbítja. A névjegyadatokat az eszköz megkapja a hasznos, ha az alkalmazás meghívja a biztonságos API-t közvetlenül a üzenet adatai beolvasása. Ebben a mintában megvalósításához egy útmutató, látogasson el a [Notification Hubs biztonságos leküldéses oktatóanyag] lap.

## <a name="operations"></a>Műveletek
### <a name="what-support-is-provided-for-disaster-recovery"></a>Milyen támogatja-e a vész-helyreállítási?
(A Notification Hubs neve a kapcsolati karakterlánc és más kritikus fontosságú adatokat) az End nyújtunk metaadatok vész-helyreállítási érvényességének. Vész-helyreállítási forgatókönyv kiváltásakor van-e a regisztrációs adatok a *csak szegmentálni* a Notification Hubs infrastruktúra, amely elvész. Akkor tudja kezelni a ezeket az adatokat az új központi a helyreállítás után a megoldás megvalósításához:

1. Hozzon létre egy másodlagos értesítésközpontról ugyanabban az adatközpontban. Azt javasoljuk, hogy hozzon létre egyet az elejéről történő védelme biztosítható, hogy a vész helyreállítási eseményeket, amelyek hatással lehetnek a felügyeleti képességek. Is létrehozhat egy, a vész-helyreállítási esemény időpontjában.

2. A másodlagos értesítési központ és az elsődleges értesítési központ a regisztrációk feltöltéséhez. Mindkét hubok regisztrációnak karbantartása és szinkronban maradjanak, a regisztráció térjen nem ajánlott. Ez az eljárás nem működik jól miatt a rejlő hajlamos regisztrációk PNS oldalán lejár. A Notification Hubs a szükségtelenné vált őket, lejárt vagy érvénytelen a regisztrációk PNS visszajelzést kap.  

Alkalmazás hátsó végpontok két javaslatok vezetünk be:

* Egy alkalmazás háttér végén regisztrációk egy adott csoportjának által használható. A tömeges beszúrás be a másodlagos értesítési központ majd ellátásához.

* Egy alkalmazás háttér, amely egy rendszeres biztonsági másolat regisztrációk lekérése az elsődleges értesítési központ biztonsági mentéséhez használja. A tömeges beszúrás be a másodlagos értesítési központ majd ellátásához.

> [!NOTE]
> Regisztráció Exportálás/importálás funkció érhető el, a normál rétegben ismertetett a [regisztrációk exportálási/importálási] dokumentum.
> 
> 

Ha a háttérből nincs céleszközökön meg az alkalmazás indításakor, egy új regisztrációs általuk másodlagos értesítési központban. Végül a másodlagos értesítésiközpont az aktív eszközök regisztrálva lesz.

Egy adott időszakban, amikor a megnyitott alkalmazásokat futtató eszközökről nem értesítéseket lesz.

### <a name="is-there-audit-log-capability"></a>Ellenőrzési napló lehetőségek van?
Minden értesítési központok felügyeleti műveletek műveleti naplói, amelyek ki vannak téve az Ugrás az [Azure-portálon].

## <a name="monitoring-and-troubleshooting"></a>Figyelés és hibaelhárítás
### <a name="what-troubleshooting-capabilities-are-available"></a>Milyen hibaelhárítási lehetőségek érhetők el?
Az Azure Notification Hubs számos szolgáltatást biztosít, különösen a leggyakoribb forgatókönyvet eldobott értesítések kapcsolatos problémák megoldása. További információkért lásd: a [Notification Hubs hibaelhárítási] találhatók meg.

### <a name="what-telemetry-features-are-available"></a>Milyen telemetriai funkciók érhetők el?
Az Azure Notification Hubs lehetővé teszi, hogy a telemetriai adatokat a [Azure-portálon]. A metrikák részletei érhetők el a [Notification Hubs metrikák] lap.

> [!NOTE]
> Sikeres értesítések egyszerűen jelenti, hogy leküldéses értesítéseket a külső pns-sel (például az Apple APNS) vagy a Google GCM lesz küldve. Ez a pns-sel, hogy az értesítések céleszközökre feladata. A pns-sel általában nem fed fel kézbesítési metrikák át harmadik félnek.  
> 
> 

Azt is programozott módon (a normál rétegben,) a telemetriai adatok exportálása képességet biztosít. További információkért lásd: a [Notification Hubs metrikák minta].

[Azure-portálon]: https://portal.azure.com
[Notification Hubs-díjszabás]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Esettanulmány: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Esettanulmány: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Esettanulmány: Budapest alkalommal]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Esettanulmány: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Esettanulmány: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Notification hub REST API-k]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs használatának első lépéseit oktatóanyagok]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome-alkalmazások oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[háttér regisztrációs útmutatót]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[háttér regisztrációs útmutatót 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs biztonsági modell]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs biztonságos leküldéses oktatóanyag]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Notification Hubs hibaelhárítási]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs metrikák]: https://msdn.microsoft.com/library/dn458822.aspx
[Notification Hubs metrikák minta]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[regisztrációk exportálási/importálási]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service szolgáltatás díjszabása]: https://azure.microsoft.com/pricing/details/app-service/
