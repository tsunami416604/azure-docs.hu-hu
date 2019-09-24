---
title: Mi az az Azure Notification Hubs?
description: Ismerje meg, hogyan bővítheti lehetőségeit az Azure Notification Hubs használatával hozzáadott leküldéses értesítési képességekkel.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f92c9ac6942bfad8df57bb3887d1ba5b7c7190af
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213104"
---
# <a name="what-is-azure-notification-hubs"></a>Mi az az Azure Notification Hubs?

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A Notification Hubs vállalati és végfelhasználói célokra is egyaránt nagyszerűen használható. Íme, néhány példaforgatókönyv:

- Legfrissebb híreket tartalmazó értesítések küldése felhasználók millióinak minimális késéssel.
- Helyalapú kuponok küldése az ez iránt érdeklődő felhasználói szegmenseknek.
- Eseményekkel kapcsolatos értesítések küldése felhasználóknak vagy csoportoknak média-/sport-/pénzügyi/játékalkalmazásokban.
- Promóciós tartalmak leküldése alkalmazásokba marketingcéllal az ügyfelek bevonása érdekében.
- Felhasználók értesítése olyan vállalati eseményekről, mint az új üzenetek vagy munkaelemek.
- Kódok küldése többtényezős hitelesítéshez.

## <a name="what-are-push-notifications"></a>Mik azok a leküldéses értesítések?

A leküldéses értesítések az alkalmazások közötti kommunikáció egyik formája, ahol a Mobile apps felhasználói értesítést kapnak bizonyos szükséges információkról, általában egy előugró ablakban vagy egy mobileszközön. A felhasználók általában az üzenet megtekintésére vagy elvetésére választanak; az előbbi megnyithatja az értesítést küldő mobil alkalmazást. Egyes értesítéseket a háttérben az alkalmazásnak a jelenetek mögötti feldolgozásához és a teendők eldöntéséhez kell elvégeznie.

A leküldéses értesítések fontos részei a végfelhasználói alkalmazásoknak az alkalmazással kapcsolatos marketingtevékenységek elősegítésében és az alkalmazáshasználat növelésében, illetve a vállalati alkalmazásoknak a naprakész üzleti információk kommunikálásában. Ez a legjobb alkalmazás-felhasználó kommunikáció, mivel ez energiahatékony a mobileszközök számára, rugalmasan használható az értesítések küldéséhez, és akkor érhető el, ha a megfelelő alkalmazások nem aktívak.

Az alábbi témakörökben tekinthet meg további információt a leküldéses értesítésekkel kapcsolatban néhány népszerű platform esetén:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Hogyan működnek a leküldéses értesítések?

A leküldéses értesítéseket a rendszerek platformspecifikus, *Platform Notification System* (PNS) nevű infrastruktúrákon keresztül küldik el. Ezek az infrastruktúrák lecsupaszított leküldéses funkciókat kínálnak, amelyekkel üzeneteket kézbesítenek egy adott leíróval rendelkező eszközöknek, és nem biztosítanak közös felületet. Ha értesítést szeretne küldeni minden ügyfélnek az alkalmazások Android-, iOS-és Windows-verzióiban, a fejlesztőnek külön kell működnie Apple Push Notification Service (APNS), a Firebase Cloud Messaging (FCM) és a Windows Notification Service (WNS) szolgáltatással.

A leküldés lényegében a következőképpen működik:

1. Egy alkalmazás úgy dönt, hogy szeretne értesítést kapni, így kapcsolatba lép a PNS, ahol az alkalmazás fut, és egyedi és ideiglenes leküldéses leírót kér. A leíró típusa a rendszertől függ (például a WNS URI-ket használ, míg a APNS tokeneket használ).
2. Az ügyfélalkalmazás ezt a leírót az alkalmazás-háttér vagy a szolgáltató tárolja.
3. Leküldéses értesítés küldéséhez az alkalmazás háttere a leíró használatával kapcsolatba lép a PNS egy adott ügyfélalkalmazás megcélzásához.
4. A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![A leküldéses értesítés munkafolyamata](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>A leküldéses értesítések kihívásai

A PNS-ek hatékonyak. Számos teendőt hagynak ugyanakkor az alkalmazásfejlesztőre még a gyakori leküldéses értesítési forgatókönyvek, például a leküldéses értesítések szegmentált felhasználóknak való szórásos küldésének megvalósítása esetén is.

Az értesítések leküldéséhez összetett infrastruktúrára van szükség, amely nem kapcsolódik az alkalmazás fő üzleti logikájához. Néhány infrastrukturális kihívás:

- **Platformfüggőség**
  - A háttérrendszer összetett és nehezen kezelhető platformtól függő logikát igényel, hogy az értesítéseket különböző platformokon lévő eszközökre küldje el, mivel a PNSes nem egységesek.
- **Méretezés**
  - A PNS-irányelvek szerint az eszközök jogkivonatait az alkalmazás minden indításakor frissíteni kell. A háttérrendszer nagy mennyiségű forgalmat és adatbázis-hozzáférést biztosít a tokenek naprakészen tartásához. Ha az eszközök száma több száz, ezres vagy millióra nő, az infrastruktúra létrehozásának és fenntartásának díja nagy.
  - A legtöbb PNS nem támogatja a több eszközre küldött szórásos küldést. Egy millió eszközre való szórásos küldés egy millió, PNS-ekre küldött hívást eredményez. Ekkora forgalomra való méretezés minimális késéssel egyáltalán nem egyszerű feladat.
- **Útválasztás**
  - Jóllehet a PNS-ekkel küldhet értesítéseket az eszközökre, a legtöbb alkalmazás értesítéseinek célpontjai felhasználók vagy érdeklődési körök. A háttérrendszernek nyilvántartás kell fenntartania, hogy az eszközöket érdeklődési körökhöz, felhasználókhoz, tulajdonságokhoz stb. társítsa. Ez a terhelés hozzáadódik az alkalmazások piacra kerülési idejéhez és fenntartási költségeihez.

## <a name="why-use-azure-notification-hubs"></a>Miért érdemes az Azure Notification Hubs szolgáltatást használni?

Notification Hubs kiküszöböli a saját alkalmazás-háttérbeli értesítések küldéséhez kapcsolódó összes bonyolultságot. A szolgáltatás többplatformos, kibővített leküldéses értesítési infrastruktúrája csökkenti a leküldéses értesítésekkel kapcsolatos kódolást, valamint egyszerűsíti a háttérrendszert. A Notification Hubs segítségével az eszközök csak a PNS-leíróknak egy központban való regisztrálásáért, a háttérrendszer pedig az üzenetek felhasználóknak vagy érdekcsoportoknak való elküldéséért felelős, ahogy az alábbi ábrán is látható:

![Értesítési központ ábrája](./media/notification-hubs-overview/notification-hub-diagram.png)

A Notification Hubs egy használatra kész leküldéses értesítési alrendszer az alábbi előnyökkel:

- **Platformfüggetlen**
  - Támogatja az összes jelentős leküldési platformot, beleértve az iOS, az Android, a Windows, valamint a Kindle és a Baidu platformot.
  - Közös felület, ahonnan leküldéses értesítéseket küldhet minden platformra platformspecifikus vagy platformfüggetlen formátumban platformspecifikus munka nélkül.
  - Eszközleírók kezelése egy helyen.
- **Háttérrendszertől független**
  - Felhőalapú vagy helyszíni.
  - .NET, Node. js, Java, Python stb.
- **Sokféle kézbesítési minta**
  - Közvetítés egy vagy több platformra: Egyetlen API-hívással azonnal továbbíthat több millió eszközre több platformon.
  - Leküldés az eszközre: Az értesítéseket az egyes eszközökre is megcélozhatja.
  - Leküldés a felhasználónak: A címkék és sablonok funkció segítségével elérheti a felhasználók összes platformfüggetlen eszközét.
  - Leküldéses szegmens dinamikus címkékkel: A címkék funkció segítséget nyújt az eszközök szegmentálásához és az igényeknek megfelelő leküldéshez, függetlenül attól, hogy egy szegmensre vagy szegmensek kifejezésére küld (például aktív és Seattle-ben nem új felhasználó). Mivel nincs a közzétételi-feliratkozási (pub-sub) modellre lenne korlátozva, az eszközcímkéket bárhol és bármikor frissítheti.
  - Honosított leküldés: A sablonok funkcióval a háttérrendszer-kód befolyásolása nélkül érheti el a honosítást.
  - Csendes leküldés: Engedélyezheti a leküldéses lekéréses mintát, ha csendes értesítéseket küld az eszközökre, és elindítja őket bizonyos lekérések vagy műveletek végrehajtásához.
  - Ütemezett leküldés: A beütemezett értesítéseket bármikor elküldheti.
  - Közvetlen leküldés: Kihagyhatja az eszközök regisztrálását a Notification Hubs szolgáltatással, és közvetlenül a Batch leküldheti az eszközökön lévő fogópontok listáját.
  - Személyre szabott leküldés: Az eszköz leküldéses változói lehetővé teszik, hogy az eszközre jellemző személyre szabott leküldéses értesítéseket egyedi kulcs-érték párokkal küldje
- **Részletes telemetria**
  - Az általános leküldési, eszköz-, hiba- és műveleti telemetria az Azure Portalon és szoftveresen is elérhető.
  - Az üzenetszintű telemetriai adatok nyomon követik az összes leküldést a kezdeti kérelemhívástól a Notification Hubs szolgáltatás általi sikeres kötegelt leküldésig.
  - A Platform Notification System-visszajelzések a Platform Notification Systems minden visszajelzését kommunikálják, hogy segítsék a hibakeresést.
- **Méretezhetőség**
  - Gyors üzeneteket küldhet eszközök millióinak új architektúrára való áttérés vagy az eszközök horizontális skálázása nélkül.
- **Biztonság**
  - Közös hozzáférésű jogosultságkód (SAS) vagy összevont hitelesítés.

## <a name="next-steps"></a>További lépések

Ismerkedés az értesítési központ létrehozásával és használatával a [következő oktatóanyag segítségével: Leküldéses értesítések küldése](notification-hubs-android-push-notification-google-fcm-get-started.md)a Mobile Applications-nek.

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: https://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: https://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: https://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: https://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: https://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[App Service Mobile Apps]: https://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
