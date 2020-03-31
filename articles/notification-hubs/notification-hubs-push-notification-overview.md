---
title: Mi az az Azure Notification Hubs?
description: Ismerje meg, hogyan bővítheti lehetőségeit az Azure Notification Hubs használatával hozzáadott leküldéses értesítési képességekkel.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: f5b59d1669d89c73b93199d1f833da149003d399
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159336"
---
# <a name="what-is-azure-notification-hubs"></a>Mi az az Azure Notification Hubs?

Az Azure Értesítési központok egy könnyen használható és skálázható leküldéses motort biztosítanak, amely lehetővé teszi, hogy értesítéseket küldjön bármely platformra (iOS, Android, Windows stb.) bármely háttérrendszerről (felhőből vagy helyszíni rendszerről). A Notification Hubs vállalati és végfelhasználói célokra is egyaránt nagyszerűen használható. Íme, néhány példaforgatókönyv:

- Legfrissebb híreket tartalmazó értesítések küldése felhasználók millióinak minimális késéssel.
- Helyalapú kuponok küldése az ez iránt érdeklődő felhasználói szegmenseknek.
- Eseményekkel kapcsolatos értesítések küldése felhasználóknak vagy csoportoknak média-/sport-/pénzügyi/játékalkalmazásokban.
- Promóciós tartalmak leküldése alkalmazásokba marketingcéllal az ügyfelek bevonása érdekében.
- A felhasználók értesítése vállalati eseményekről, például új üzenetekről és munkaelemekről.
- Kódok küldése többtényezős hitelesítéshez.

## <a name="what-are-push-notifications"></a>Mik azok a leküldéses értesítések?

A leküldéses értesítések az alkalmazások közötti kommunikáció olyan formáját alkotják, amelyben a mobilalkalmazások felhasználói értesítést kapnak bizonyos kívánt információkról, általában egy előugró ablakban vagy egy mobileszközön lévő párbeszédpanelen. A felhasználók általában úgy döntenek, hogy megtekintik vagy elutasítják az üzenetet; az előbbi kiválasztása megnyitja az értesítést közrei mobilalkalmazást. Egyes értesítések némák - a színfalak mögött kézbesítve az alkalmazás számára, hogy feldolgozza és eldöntse, mit tegyen.

A leküldéses értesítések fontos részei a végfelhasználói alkalmazásoknak az alkalmazással kapcsolatos marketingtevékenységek elősegítésében és az alkalmazáshasználat növelésében, illetve a vállalati alkalmazásoknak a naprakész üzleti információk kommunikálásában. Ez a legjobb alkalmazás-felhasználó kommunikáció, mert energiatakarékos a mobileszközök, rugalmas az értesítések feladói, és elérhető, ha a megfelelő alkalmazások nem aktívak.

> [!NOTE]
> Az Azure Notification Hubs hivatalosan nem támogatja a VOIP-leküldéses értesítéseket; azonban [ez a cikk ismerteti, hogyan használhatja az APNS VOIP-értesítések et](voip-apns.md) az Azure Értesítési központokon keresztül.

Az alábbi témakörökben tekinthet meg további információt a leküldéses értesítésekkel kapcsolatban néhány népszerű platform esetén:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-do-push-notifications-work"></a>Hogyan működnek a leküldéses értesítések?

A leküldéses értesítéseket a rendszerek platformspecifikus, *Platform Notification System* (PNS) nevű infrastruktúrákon keresztül küldik el. Alapvető leküldéses funkciókat kínálnak, hogy üzenetet juttassanak el egy adott fogantyúval rendelkező eszközre, és nincs közös felületük. Ha értesítést szeretne küldeni az alkalmazás Android, iOS és Windows verzióiban lévő összes ügyfélnek, a fejlesztőnek külön kell működnie az Apple Push Notification Service (APNS), a Firebase Cloud Messaging (FCM) és a Windows értesítési szolgáltatás (WNS) szolgáltatással.

A leküldés lényegében a következőképpen működik:

1. Egy alkalmazás értesítést szeretne kapni, ezért kapcsolatba lép a PNS-sel azon a célplatformon, amelyen az alkalmazás fut, és egyedi és ideiglenes leküldéses leírót kér. A leíró típusa a rendszertől függ (például a WNS URI-kat használ, míg az APNS jogkivonatokat).
2. Az ügyfélalkalmazás tárolja ezt a leírót az alkalmazás háttérrendszerében vagy szolgáltatójában.
3. Leküldéses értesítés küldéséhez az alkalmazás háttérrendszere kapcsolatba lép a PNS-sel a leíró használatával egy adott ügyfélalkalmazás célzásához.
4. A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![A leküldéses értesítés munkafolyamata](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>A leküldéses értesítések kihívásai

A PNS-ek hatékonyak. Számos teendőt hagynak ugyanakkor az alkalmazásfejlesztőre még a gyakori leküldéses értesítési forgatókönyvek, például a leküldéses értesítések szegmentált felhasználóknak való szórásos küldésének megvalósítása esetén is.

Leküldéses értesítések küldése összetett infrastruktúrát igényel, amely nem kapcsolódik az alkalmazás fő üzleti logikájához. Az infrastrukturális kihívások a következők:

- **Platformfüggőség**
  - A háttérrendszer összetett és nehezen karbantartható platformfüggő logikát igényel, hogy értesítéseket küldjön a különböző platformokon lévő eszközökre, mivel a PNS-ek nincsenek egységesek.
- **Skála**
  - PNS-irányelvek szerint az eszközjogkivonatokat minden alkalmazásindításkor frissíteni kell. A háttérprogram nagy mennyiségű forgalommal és adatbázis-hozzáféréssel foglalkozik, csak azért, hogy a jogkivonatok naprakészek maradjanak. Ha az eszközök száma százra, ezrekre vagy milliókra nő, az infrastruktúra létrehozásának és karbantartásának költsége hatalmas.
  - A legtöbb PNS nem támogatja a több eszközre küldött szórásos küldést. Egy millió eszközre való szórásos küldés egy millió, PNS-ekre küldött hívást eredményez. Ekkora forgalomra való méretezés minimális késéssel egyáltalán nem egyszerű feladat.
- **Útválasztás**
  - Bár a PNS-ek lehetővé teszik az eszközökre irányuló üzenetek küldését, a legtöbb alkalmazásértesítés felhasználókat vagy érdekcsoportokat céloz meg. A háttérrendszernek létre kell vetnie egy rendszerleíró adatbázist az eszközök érdekcsoportokhoz, felhasználókhoz, tulajdonságokhoz stb. Ez a többletköltség növeli az alkalmazás piaci és karbantartási költségeit.

## <a name="why-use-azure-notification-hubs"></a>Miért érdemes az Azure Notification Hubs szolgáltatást használni?

Az értesítési központok kiküszöbölik a leküldéses értesítések saját küldésének bonyolultságát az alkalmazás háttérrendszeréről. A szolgáltatás többplatformos, kibővített leküldéses értesítési infrastruktúrája csökkenti a leküldéses értesítésekkel kapcsolatos kódolást, valamint egyszerűsíti a háttérrendszert. A Notification Hubs segítségével az eszközök csak a PNS-leíróknak egy központban való regisztrálásáért, a háttérrendszer pedig az üzenetek felhasználóknak vagy érdekcsoportoknak való elküldéséért felelős, ahogy az alábbi ábrán is látható:

![Értesítési központ ábrája](./media/notification-hubs-overview/notification-hub-diagram.png)

Az Értesítési központok a használatra kész leküldéses motor, amely a következő előnyökkel jár:

- **Platformfüggetlen**
  - Támogatás az összes főbb push platformok.
  - Közös felület, ahonnan leküldéses értesítéseket küldhet minden platformra platformspecifikus vagy platformfüggetlen formátumban platformspecifikus munka nélkül.
  - Eszközleírók kezelése egy helyen.
- **Háttérrendszertől független**
  - Felhőben vagy a helyszínen.
  - .NET, Node.js, Java, Python stb.
- **Sokféle kézbesítési minta**
  - Egy vagy több platformra való közvetítés: Egyetlen API-hívással azonnal több millió eszközre sugározhat különböző platformokon.
  - Leküldés eszközökre: Az értesítésekkel adott eszközöket célozhat meg.
  - Leküldéses a felhasználóhoz: A címkék és sablonok segítségével elérheti a felhasználó platformfüggetlen eszközeit.
  - Push to segment dinamikus címkék: A címkék funkció segít szegmens eszközök és nyomja őket az Ön igényeinek megfelelően, hogy küld egy szegmensben, vagy egy kifejezés a szegmensek (Például az aktív és él Seattle-ben NEM új felhasználó). Ahelyett, hogy csak a közzététel-előfizetés, frissítheti eszköz címkék bárhol és bármikor.
  - Honosított leküldéses: A sablonok funkció segít elérni a lokalizációt anélkül, hogy befolyásolná a háttérkódot.
  - Csendes leküldés: Engedélyezheti a leküldés és lekérés (push-to-pull) mintát, így az eszközökön csendes értesítések küldésével aktiválhatja bizonyos lekérések vagy műveletek végrehajtását.
  - Ütemezett leküldéses: Az értesítéseket bármikor elküldheti.
  - Közvetlen leküldés: Kihagyhatja az eszközök regisztrációját a Notification Hubs szolgáltatásban, és közvetlenül kötegelt értesítéseket küldhet le eszközleírók egy listájára.
  - Személyre szabott leküldéses: Az eszközleküldéses változók segítségével egyedi kulcsérték-párokkal küldhet eszközspecifikus, személyre szabott leküldéses értesítéseket.
- **Részletes telemetria**
  - Általános leküldéses, eszköz-, hiba- és műveleti telemetriai adatok érhetők el az Azure Portalon és programozott módon.
  - Az üzenetenkénti telemetriai adatok nyomon követik a kezdeti kéréshívásból az értesítési központok szolgáltatásba irányuló leküldéses leküldéses minden leküldéses leküldéses.
  - A platformértesítési rendszer visszajelzései közlik a PNS-ek összes visszajelzését a hibakeresés elősegítésére.
- **Méretezhetőség**
  - Gyors üzeneteket küldhet több millió eszköznek anélkül, hogy újratervezne vagy eszközszilánkoket hozna.
- **Biztonság**
  - Közös hozzáférésű jogosultságkód (SAS) vagy összevont hitelesítés.

## <a name="next-steps"></a>További lépések

Az értesítési központ létrehozásának és használatának első lépéseinek végrehajtásához kövesse az [értesítések mobilalkalmazásokba való leküldésével](notification-hubs-android-push-notification-google-fcm-get-started.md) foglalkozó oktatóanyagot.

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
