---
title: Azure Notification Hubs
description: "Ismerje meg, hogyan adhat leküldéses értesítési képességek az Azure Notification hubs használatával."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs"></a>Azure Notification Hubs
## <a name="overview"></a>Áttekintés
Az Azure Notification Hubs egy egyszerűen használható, többplatformos, kibővített leküldéses alrendszeren adja meg. Egyetlen platformfüggetlen API-hívással egyszerűen küldhet megcélzott és a testreszabott leküldéses értesítések bármilyen mobilplatformra bármilyen felhőalapú vagy helyszíni háttérrendszerből.

A Notification Hubs esetében működik jól mind vállalati és végfelhasználói célokra. Íme néhány példa az ügyfelek a Notification Hubs használata:

* Legfrissebb híreket tartalmazó értesítések küldése millióinak minimális késéssel.
* Helyalapú kuponok küldése érdekelt felhasználói szegmenseknek.
* Esemény kapcsolatos értesítések küldése felhasználóknak vagy csoportoknak media/sport/pénzügyi/játékalkalmazások.
* Promóciós tartalma leküldése alkalmazások szólítsa meg, és az ügyfél számára.
* Felhasználók értesítése olyan vállalati eseményekről, mint az új üzeneteket, és a munkaelemek.
* Kódokat a multi-factor authentication küldeni.

## <a name="what-are-push-notifications"></a>Mik azok a leküldéses értesítések?
Leküldéses értesítések formája, amely egy alkalmazás-felhasználói kommunikációs ahol értesítse a felhasználókat a mobilalkalmazások egyes kívánt információk, általában a előugró ablak vagy párbeszédpanel. Felhasználók általában választható megtekintéséhez, vagy hagyja figyelmen kívül az üzenetet, és válassza a korábbi nyílik meg a mobilalkalmazásban, amely kellett ezekről az értesítés.

Leküldéses értesítések létfontosságú a fogyasztói alkalmazásokba az alkalmazással kapcsolatos marketingtevékenységeket és a használat növelése, valamint a vállalati alkalmazások naprakész üzleti adatok kommunikáció során. Mert jobb mobil eszközök, az értesítések feladók rugalmas, és elérhető, amíg a megfelelő alkalmazások nem aktívak a legjobb alkalmazás és a felhasználók kommunikációs.

További információ a leküldéses értesítések néhány népszerű platformokhoz:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>A leküldéses értesítések működése
Leküldéses értesítések érkeznek platform-specifikus nevű infrastruktúrákon keresztül *Platform Notification System* (PNSes). Barebone leküldéses funkciók kézbesítési üzenetben a megadott eszközök kezelésére, és nincs közös felületük biztosítanak. Értesítés küldése az összes ügyfél számára az iOS, Android és Windows alkalmazás, a fejlesztői kell működik APNS (Apple Push Notification szolgáltatás), a FCM (Firebase Cloud Messaging) és a WNS (a Windows értesítési szolgáltatás), a küld kötegelés közben.

Magas szinten itt van leküldéses működése:

1. Az ügyfélalkalmazás úgy dönt, akkor kéri a leküldéses értesítések ezért kapcsolatba lép a megfelelő PNS egyedi és ideiglenes leküldéses leíróját beolvasása. A leíró típusa attól függ, hogy a rendszer (pl. WNS rendelkezik URI-azonosítók míg APNS jogkivonatok).
2. Az ügyfélalkalmazás ezt a leírót az alkalmazás vagy a szolgáltató tárolja.
3. Egy leküldéses értesítést küldeni, az alkalmazás kapcsolatba lép a pns-sel, amelyekre egy adott ügyfélalkalmazás a leíró használatával.
4. A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![][0]

## <a name="the-challenges-of-push-notifications"></a>A leküldéses értesítések kihívásai
Amíg PNSes nem hatékony, azok hagyjuk alkalmazásfejlesztőre az alkalmazás fejlesztőjének még gyakori leküldéses értesítési forgatókönyvek, például a szórásos küldés leküldéses értesítések szegmentált felhasználók számára vagy megvalósításához.

Leküldéses az egyik leginkább kért funkciója a mobileszközök felhőalapú szolgáltatások, mert a munkát igényel összetett infrastruktúra, az alkalmazás fő üzleti logikájához nem kapcsolt. Az infrastrukturális kihívást vannak:

* **Platformfüggőség**: 

  * A háttérkiszolgáló az értesítések küldését a különböző platformokon eszközök PNSes vannak nem egységes módon összetett és rögzített karbantartása platformfüggő logika rendelkeznie kell.
* **Skála**:

  * PNS-irányelvek eszközök tokenjeit minden alkalmazás indítás után frissíteni kell. Ez azt jelenti, hogy a háttér foglalkozó nagy mennyiségű forgalom és az adatbázis hozzáférést csak a tokenek naprakész állapotban tartása érdekében. Az eszközök száma nő, több száz és több millió ezer, a költség, létrehozásának és fenntartásának Ez az infrastruktúra esetén nagy.
  * A legtöbb PNSes nem támogatják a szórásos több eszközre. Ez azt jelenti, hogy egy egyszerű szórásos egy millió hívások a PNSes egy millió eszközök eredményez. A forgalom mennyisége méretezést, minimális késés nem magától értetődő.
* **Útválasztás**:
  
  * PNSes nyújtanak olyan üzenetek küldése eszközre, bár a legtöbb alkalmazások értesítéseket felhasználóknak vagy érdeklődési köröknek célzott. Ez azt jelenti, hogy a háttér kell fenntartani a beállításjegyzék eszközök társítandó érdeklődési körök alapján, a felhasználók, a tulajdonságok, stb. Ez a terhelés hozzáadódik az alkalmazások piacra és fenntartási költségeihez időt.

## <a name="why-use-notification-hubs"></a>Miért érdemes a Notification Hubsot használni?
A Notification Hubs megszünteti az összes olyan összetett szolgáltatásokkal, társított engedélyezése leküldéses saját. A többplatformos, kibővített leküldéses értesítési infrastruktúrát leküldéses kapcsolatos kódok csökkenti, és egyszerűbbé teszi a kiszolgáló. A Notification hubs használatával eszközök felelősek csupán a PNS-leírók regisztrálásáért regisztrálása egy központi, amíg a háttérrendszer üzeneteket küld a felhasználóknak vagy érdeklődési köröknek, az alábbi ábrán látható módon:

![][1]

A Notification hubs a használatra kész leküldéses motor a következő előnyökkel jár:

* **Kereszt-platformok**

  * Minden, iOS, Android, Windows, és Kindle és Baidu fő leküldéses platformokat is támogat.
  * Platform-specifikus vagy platformfüggetlen formátumokban nincs platform-specifikus feladata az összes platformhoz leküldéses közös felületet.
  * Eszköz az egyik helyen kezelheti.
* **Kereszt-háttérkiszolgálókon**
  
  * Felhőalapú vagy helyszíni
  * .NET, Node.js, Java, stb.
* **Sokféle kézbesítési minta**:

  * *Egy vagy több platformra szórási*: azonnal elküldheti eszközök millióira egyetlen API-hívással platformon.
  * *Eszköz leküldése*: az egyes eszközökre értesítéseket célba.
  * *Felhasználói leküldése*: címkék és a sablonok funkció segítségével egy felhasználó összes platformfüggetlen-eszközök eléréséhez.
  * *Dinamikus címkékkel rendelkező szegmens leküldéses*: címkék szolgáltatás segít szegmens eszközök és az igényeknek megfelelő rájuk leküldéses e küld egy szegmens vagy szegmensek (pl. active és életét Budapest nem új felhasználók) kifejezés. Helyett átjárón pub-sub, frissítheti az eszközt címkék bárhonnan és bármikor.
  * *Honosított leküldéses*: funkciójával sablonok honosítási elérése a háttéralkalmazás kódjának módosítása nélkül.
  * *Csendes leküldéses*: is lehetővé teszi a leküldéses mintát csendes értesítések küldését az eszközök és kiváltó bizonyos ponttá vagy a műveletek befejeződnek.
  * *Ütemezett leküldéses*: bármikor küldjön értesítéseket is ütemezheti.
  * *Leküldéses közvetlen*: az szolgáltatás regisztrálása eszközök kihagyhatja, és közvetlenül a batch-leküldéses eszköz leírók listájára.
  * *Személyre szabott leküldéses*: eszköz leküldéses változók segítségével küld az eszközre vonatkozó testreszabott leküldéses értesítések az egyéni kulcs-érték párokat.
* **Részletes telemetria**
  
  * Általános leküldéses, eszköz, hiba és művelet telemetriai adatokat az Azure portálon és szoftveresen érhető el.
  * Üzenet telemetriai adatok száma a szolgáltatás sikeresen kötegelés a leküldéses értesítések ki a kezdeti kérés hívásából származó követi nyomon, minden leküldéses.
  * Platform Notification System visszajelzés platformmegbízhatósági értesítési rendszere hibakeresés segítésére összes visszajelzései kommunikál.
* **Méretezhetőség** 
  
  * Gyors üzenetek küldése nélkül újratervezni vagy az eszköz horizontális eszközök millióira.
* **Biztonság**

  * Közös hozzáférésű Jogosultságkód (SAS) vagy összevont hitelesítés.

## <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps-integráció
A különböző Azure-szolgáltatásokon elérhető zökkenőmentes és egységes élmény érdekében az [App Service Mobile Apps] a leküldéses értesítések beépített támogatását nyújtja a Notification Hubs használatával. Az [App Service Mobile Apps] jól skálázható, világszerte elérhető, vállalati fejlesztők és rendszerintegrátorok számára készült mobilalkalmazás-fejlesztő platform, amely számos lehetőséget kínál a mobilos fejlesztésekhez.

A Mobile Apps-fejlesztők a következő munkafolyamatoknál használhatják a Notification Hubs szolgáltatást:

1. Eszköz PNS-leírójának lekérése
2. Eszköz regisztrálása a Notification Hubs kényelmes Mobile Apps-ügyfél SDK regisztrációs API-n keresztül
   * Vegye figyelembe, hogy a Mobile Apps biztonsági okokból eltávolítja az összes regisztrációs címkét. Közvetlenül a háttérrendszerről használhatja a Notification Hubst a címkék és eszközök társításához.
3. Értesítések küldése az alkalmazás háttérrendszeréből a Notification Hubs használatával

Íme néhány hasznos szolgáltatás a fejlesztők számára, amelyeket ez az integráció tett elérhetővé:

* **Mobile Apps ügyfél SDK-k**: A többplatformos SDK-k egyszerű API-k biztosítanak a regisztrációhoz, és a kapcsolódó értesítési központtal a mobilalkalmazáshoz automatikusan kommunikál. A fejlesztőknek nem kell átrágniuk magukat a Notification Hubs hitelesítő adatain, és egy másik szolgáltatást használniuk.

  * *Felhasználói leküldése*: az SDK-k automatikusan felcímkézik az adott eszközt a Mobile Apps által hitelesített felhasználói Azonosítóval engedélyezése a felhasználóknak küldött leküldéses.
  * *Eszköz leküldése*: az SDK-k automatikusan segítségével a Mobile Apps telepítési Azonosítót GUID-ként regisztrálja a Notification Hubs, így a fejlesztőknek nem kell több szolgáltatásbeli GUID azonosítót fenntartaniuk.
* **Telepítési modell**: Mobile Apps a Notification Hubs legújabb leküldési modelljével mutatja az egy JSON-telepítésben, amely igazodik a leküldéses értesítési szolgáltatásokhoz, és könnyen használható az eszközök leküldéses tulajdonságait.
* **Rugalmasság**: fejlesztők mindig választhatják a Notification hubs közvetlen mellett az integráció megvalósítása után is fog működni.
* **Integrált felhasználói felület az [Azure-portálon]**: a leküldési funkciót egy olyan képességet a Mobile Apps vizuálisan jelzi, és a fejlesztők könnyedén használhatják a kapcsolódó értesítési központon keresztül Mobile Apps.

## <a name="next-steps"></a>Következő lépések
A Notification Hubsról a következő témakörökben talál további információt:

* **[Hogyan használják az ügyfelek a Notification Hubs szolgáltatást]**
* **[A Notification Hubs szolgáltatással kapcsolatos oktatóanyagok és útmutatók]**
* **Notification Hubs használatának első lépéseit oktatóanyagok**: [iOS], [Android], [univerzális Windows-], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Hogyan használják az ügyfelek a Notification Hubs szolgáltatást]: http://azure.microsoft.com/services/notification-hubs
[A Notification Hubs szolgáltatással kapcsolatos oktatóanyagok és útmutatók]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[univerzális Windows-]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure-portálon]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
