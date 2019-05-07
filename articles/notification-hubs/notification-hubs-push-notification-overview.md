---
title: Mi az az Azure Notification Hubs?
description: Ismerje meg, hogyan bővítheti lehetőségeit az Azure Notification Hubs használatával hozzáadott leküldéses értesítési képességekkel.
author: jwargo
manager: patniko
editor: spelluru
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
ms.author: jowargo
ms.openlocfilehash: 03d4c269f76a89c43dec253367d07f3bf71a06d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141215"
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

Leküldéses értesítések az alkalmazás közötti felhasználó kommunikációs egy képernyő, ahol bizonyos kívánt információ általában a előugró üzenet vagy párbeszédpanel a mobileszközökön a mobile apps felhasználóinak értesítést kap. Felhasználók megtekintéséhez, vagy hagyja figyelmen kívül az üzenetet; általában kiválasztása az előbbi megnyílik a mobilalkalmazásban, amely az értesítés közölt kiválasztása. Egyes értesítéseket nem rendelkeznek – az alkalmazás a háttérben dolgozza fel, és döntse el, mi a teendő a háttérben-i.

A leküldéses értesítések fontos részei a végfelhasználói alkalmazásoknak az alkalmazással kapcsolatos marketingtevékenységek elősegítésében és az alkalmazáshasználat növelésében, illetve a vállalati alkalmazásoknak a naprakész üzleti információk kommunikálásában. A legjobb alkalmazást és a felhasználók kommunikáció, mert energiahatékony mobileszközök esetén az értesítések feladói, rugalmas és érhető el, ha a kapcsolódó alkalmazások nem aktívak.

Az alábbi témakörökben tekinthet meg további információt a leküldéses értesítésekkel kapcsolatban néhány népszerű platform esetén:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](https://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Hogyan működnek a leküldéses értesítések?

A leküldéses értesítéseket a rendszerek platformspecifikus, *Platform Notification System* (PNS) nevű infrastruktúrákon keresztül küldik el. Ezek az infrastruktúrák lecsupaszított leküldéses funkciókat kínálnak, amelyekkel üzeneteket kézbesítenek egy adott leíróval rendelkező eszközöknek, és nem biztosítanak közös felületet. Értesítés küldése minden ügyfél számára az Android, iOS és az alkalmazás Windows verziói között, a fejlesztői együttműködve kell az Apple Push Notification Service(APNS), a Firebase Cloud Messaging(FCM) és a Windows értesítési Service(WNS) külön-külön.

A leküldés lényegében a következőképpen működik:

1. Egy alkalmazás úgy dönt, hogy értesítést szeretne kapni, így azt PNS kapcsolatba lép a célplatformhoz, ahol az alkalmazás fut, és a egy egyedi és ideiglenes leküldéses leíró kérelmek szeretné. A leíró típusa a rendszertől függ (például WNS használ URI-k közben APNS tokeneket használ).
2. Az ügyfélalkalmazás ezt az azonosítót a háttéralkalmazás vagy a szolgáltató tárolja.
3. Leküldéses értesítés küldése, az alkalmazási háttérrendszer kapcsolatba lép a PNS-leírójának célja egy adott ügyfél-alkalmazás használatával.
4. A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![A leküldéses értesítés munkafolyamata](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>A leküldéses értesítések kihívásai

A PNS-ek hatékonyak. Számos teendőt hagynak ugyanakkor az alkalmazásfejlesztőre még a gyakori leküldéses értesítési forgatókönyvek, például a leküldéses értesítések szegmentált felhasználóknak való szórásos küldésének megvalósítása esetén is.

Az értesítések leküldéséhez összetett infrastruktúrára van szükség, amely nem kapcsolódik az alkalmazás fő üzleti logikájához. Néhány infrastrukturális kihívás:

- **Platformfüggőség**
  - A háttérrendszer szükséges összetett és nehezen fenntartható platformfüggő logikai értesítések küldéséhez, PNSes vannak nem egységes eszközöket a különböző platformokon.
- **Méretezés**
  - A PNS-irányelvek szerint az eszközök jogkivonatait az alkalmazás minden indításakor frissíteni kell. A háttérben a nagy mennyiségű forgalmat és az adatbázis a hozzáférést csak a tokenek naprakészen foglalkozik. Az eszközök száma nő, több száz, több ezer vagy több millió, a költség, létrehozását és az infrastruktúra karbantartását esetén nagy.
  - A legtöbb PNS nem támogatja a több eszközre küldött szórásos küldést. Egy millió eszközre való szórásos küldés egy millió, PNS-ekre küldött hívást eredményez. Ekkora forgalomra való méretezés minimális késéssel egyáltalán nem egyszerű feladat.
- **Útválasztás**
  - Jóllehet a PNS-ekkel küldhet értesítéseket az eszközökre, a legtöbb alkalmazás értesítéseinek célpontjai felhasználók vagy érdeklődési körök. A háttérrendszernek nyilvántartás kell fenntartania, hogy az eszközöket érdeklődési körökhöz, felhasználókhoz, tulajdonságokhoz stb. társítsa. Ez a terhelés hozzáadódik az alkalmazások piacra kerülési idejéhez és fenntartási költségeihez.

## <a name="why-use-azure-notification-hubs"></a>Miért érdemes az Azure Notification Hubs szolgáltatást használni?

A Notification Hubs megszünteti az értesítések leküldése a saját alkalmazását a háttérrendszer társított összes hagyhatják. A szolgáltatás többplatformos, kibővített leküldéses értesítési infrastruktúrája csökkenti a leküldéses értesítésekkel kapcsolatos kódolást, valamint egyszerűsíti a háttérrendszert. A Notification Hubs segítségével az eszközök csak a PNS-leíróknak egy központban való regisztrálásáért, a háttérrendszer pedig az üzenetek felhasználóknak vagy érdekcsoportoknak való elküldéséért felelős, ahogy az alábbi ábrán is látható:

![Értesítési központ ábrája](./media/notification-hubs-overview/notification-hub-diagram.png)

A Notification Hubs egy használatra kész leküldéses értesítési alrendszer az alábbi előnyökkel:

- **Platformfüggetlen**
  - Támogatja az összes jelentős leküldési platformot, beleértve az iOS, az Android, a Windows, valamint a Kindle és a Baidu platformot.
  - Közös felület, ahonnan leküldéses értesítéseket küldhet minden platformra platformspecifikus vagy platformfüggetlen formátumban platformspecifikus munka nélkül.
  - Eszközleírók kezelése egy helyen.
- **Háttérrendszertől független**
  - Felhőalapú vagy helyszíni.
  - .NET, Node.js, Java stb.
- **Sokféle kézbesítési minta**
  - Egy vagy több platformon szórás: Azonnal elküldheti eszközök millióira egyetlen API hívással platformon.
  - Továbbítsa az eszköz: Megcélozhatja azokat az egyes eszközökre értesítéseket.
  - Küldje le a felhasználó számára: Címkékkel és sablonokkal funkciók segítenek elérni egy felhasználó összes többplatformos eszközei.
  - Küldje le a szegmens megjelölhető dinamikus címkékkel: Címkék szolgáltatás segítségével, szükség szerint leküldéses őket és szegmens-eszközök egy szegmens vagy egy kifejezés a szegmenseket (például aktív és életét Seattle nem új felhasználó) küld-e. Mivel nincs a közzétételi-feliratkozási (pub-sub) modellre lenne korlátozva, az eszközcímkéket bárhol és bármikor frissítheti.
  - Leküldéses: Sablonok funkció segítségével honosítási elérése a háttérkód módosítása nélkül.
  - Csendes leküldéses: Beavatkozás nélküli értesítések küldését az eszközeire és aktiválása bizonyos lekéri vagy a műveletek befejeződnek, engedélyezheti a leküldéses minta.
  - Ütemezett leküldés: Bármikor küldjön értesítéseket is ütemezhető.
  - Közvetlen lehívás: A Notification Hubs szolgáltatással való regisztrálásáról eszközök kihagyhatja, és közvetlenül batch-leküldéses eszközleírók listájára.
  - Személyre szabott leküldéses: Eszköz leküldéses változók segítségével küld az eszközre vonatkozó személyre szabott leküldéses értesítések az egyéni kulcs-érték párokat.
- **Részletes telemetria**
  - Az általános leküldési, eszköz-, hiba- és műveleti telemetria az Azure Portalon és szoftveresen is elérhető.
  - Az üzenetszintű telemetriai adatok nyomon követik az összes leküldést a kezdeti kérelemhívástól a Notification Hubs szolgáltatás általi sikeres kötegelt leküldésig.
  - A Platform Notification System-visszajelzések a Platform Notification Systems minden visszajelzését kommunikálják, hogy segítsék a hibakeresést.
- **Méretezhetőség**
  - Gyors üzeneteket küldhet eszközök millióinak új architektúrára való áttérés vagy az eszközök horizontális skálázása nélkül.
- **Biztonság**
  - Közös hozzáférésű jogosultságkód (SAS) vagy összevont hitelesítés.

## <a name="next-steps"></a>További lépések

Ismerkedés a létrehozása és a egy értesítési központ használatával a következő a [oktatóanyag: Leküldéses értesítések küldése a mobilalkalmazások](notification-hubs-android-push-notification-google-fcm-get-started.md).

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
