---
title: Offline adatszinkronizálás az Azure Mobile Apps |} A Microsoft Docs
description: Fogalmi referenciája és az offline adatok szinkronizálási szolgáltatás az Azure Mobile Apps áttekintése
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471033"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban
## <a name="what-is-offline-data-sync"></a>Mi az offline adatszinkronizálás?
Offline adatszinkronizálás egy ügyfél- és SDK-szolgáltatás, az Azure Mobile Apps, amely megkönnyíti a fejlesztők számára a hálózati kapcsolat nélkül működik olyan alkalmazásokat hozhat létre.

Amikor az alkalmazás kapcsolat nélküli üzemmódban van, továbbra is létrehozhat és módosíthatja az adatokat, amelyek a helyi tárolóba lesznek mentve. Az alkalmazást újra online állapotba kerül, ha azt képes szinkronizálni az Azure Mobile Apps-háttéralkalmazás helyi módosításokat. A funkció is támogatja az ütközések észlelése, ha ugyanazt a rekordot az ügyfél és a háttérkiszolgálón is módosul. Ütközések lehet kezelni, a kiszolgáló vagy az ügyfél.

Offline szinkronizálás több előnye van:

* Kiszolgáló adatainak helyi eszközön történő gyorsítótárazásával javíthatja az alkalmazás reakciókészségét
* Hálózati problémák esetén, robusztus alkalmazásokat hozhat létre
* Lehetővé teszi a végfelhasználók számára, hogy hozzon létre és módosíthatók az adatok akkor is, ha nem hálózati lehet hozzáférni, alig vagy egyáltalán nem összefüggő forgatókönyvek támogatása
* Szinkronizálja az adatokat több eszközön, és két eszköz által módosított ugyanazt a rekordot ütközések észlelése
* A nagy késésű vagy forgalmi díjas hálózatok hálózati használatának korlátozása

Az alábbi oktatóanyagok bemutatják, hogyan offline szinkronizálást adhat a mobil ügyfélprogramok Azure Mobile Apps segítségével:

* [Android: Offline szinkronizálás engedélyezése]
* [Az Apache Cordova: Offline szinkronizálás engedélyezése](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: offline szinkronizálás engedélyezése]
* [Xamarin IOS-es: offline szinkronizálás engedélyezése]
* [Xamarin Android: Offline szinkronizálás engedélyezése]
* [Xamarin.Forms: Kapcsolat nélküli szinkronizálás – engedélyezése](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzális Windows Platform: Offline szinkronizálás engedélyezése]

## <a name="what-is-a-sync-table"></a>Mit jelent a szinkronizálás tábla?
A "/ táblák" végpont elérésére, az Azure Mobile ügyfél SDK-k biztosítanak felületek például `IMobileServiceTable` (.NET client SDK) vagy `MSTable` (IOS-es ügyfél). Ezen API-k közvetlenül csatlakozhat az Azure Mobile Apps-háttéralkalmazás, és a meghiúsuljon, ha az ügyféleszköz nincs hálózati kapcsolat.

Offline használatra támogatása érdekében az alkalmazás használja, a *szinkronizálási tábla* API-k, például `IMobileServiceSyncTable` (.NET client SDK) vagy `MSSyncTable` (IOS-es ügyfél). Az összes szinkronizálási esetében működik az azonos CRUD-műveletek (létrehozás, Olvasás, Update, Delete) tábla API-k, most olvasni, kivéve vagy írni egy *helyi tároló*. Minden olyan szinkronizálási tábla műveletek végrehajtása előtt inicializálni kell a helyi tárolóban.

## <a name="what-is-a-local-store"></a>Mit jelent a helyi tárolóból?
Egy helyi tárolóban a adatmegőrző réteget az ügyféleszközön. Az Azure Mobile Apps-ügyfél SDK-k adjon meg egy helyi alapértelmezett megvalósítása. A Windows, a Xamarin és az Android SQLite alapul. Az iOS-legfontosabb adatainak alapul.

A Windows Phone-vagy a Microsoft Store az SQLite-alapú megvalósítás használatához szüksége egy SQLite-bővítmény telepítése. További információkért lásd: [Univerzális Windows Platform: Offline szinkronizálás engedélyezése]. Android és IOS rendszerű szállításra az eszköz operációs rendszere, az SQLite verziójával, így azt nem hivatkozhat saját SQLite verzióját.

A fejlesztők saját helyi tárolóhoz is megvalósíthatja. Például ha szeretné a mobil ügyfelekből a titkosított formában tárolja az adatokat, megadhatja titkosítási SQLCipher használó helyi tároló.

## <a name="what-is-a-sync-context"></a>Mi az egy szinkronizálási környezetet?
A *szinkronizálási környezet* egy mobilügyfél-objektumhoz társított (például `IMobileServiceClient` vagy `MSClient`) és szinkronizálási táblák végzett módosításokat követi nyomon. A szinkronizálási környezetet fenntart egy *művelet várólistára*, amely tartja a CUD műveleteket (létrehozás, frissítés, Törlés) rendezett listáját, amelyek később küldi a kiszolgálóra.

A szinkronizálási környezetet használni például az inicializálási metódusa társítva egy helyi tárolóban `IMobileServicesSyncContext.InitializeAsync(localstore)` a a [.NET ügyféloldali SDK-val].

## <a name="how-sync-works"></a>Hogyan offline szinkronizálás használata
Szinkronizálási táblák használata esetén az Ügyfélkód szabályozza, amikor helyi módosítások szinkronizálva van-e az Azure Mobile Apps-háttéralkalmazást. Semmi addig, amíg nincs egy hívás érkezik, a háttérkiszolgáló *leküldéses* helyi módosításokat. Hasonlóképpen, a helyi tárolóban van feltöltve új csak akkor, ha van egy hívás *lekéréses* adatokat.

* **Leküldéses**: leküldéses a szinkronizálási környezet egy olyan műveletet, és minden CUD módosításokat küld az utolsó leküldéses óta. Ne feledje, hogy azt nem lehet elküldeni csak egyes táblákat módosításokat, mert ellenkező esetben műveletek sikerült elküldeni az üzemen kívüli. Leküldéses REST-hívások, az Azure Mobile Apps-háttéralkalmazás, amely ezután módosítja a server-adatbázis egy sorozatát hajtja végre.
* **Lekéréses**: lekéréses tábla alapon történik, és csak a kiszolgáló adatainak egy része egy lekérdezése szabhatók. Az Azure Mobile ügyfél SDK-k majd beszúrja az eredményül kapott adatokat a helyi tárolóban.
* **Implicit Leküldések**: egy lekéréses hajtja végre a függőben lévő helyi frissítést tartalmazó tábla szemben, ha a lekéréses először hajt végre egy `push()` a szinkronizálási környezetben. A leküldéses minimálisra már várólistára helyezett módosításokat, a kiszolgáló közötti ütközéseket.
* **A növekményes szinkronizálás**: az első paraméter a pull művelet egy *lekérdezésnév* csak az ügyfélen használt. Ha egy nem null értékű lekérdezési nevet használja, az Azure Mobile SDK hajt végre egy *növekményes szinkronizálás*. Minden alkalommal, amikor egy lekéréses művelet készletet ad vissza, az eredmények, a legújabb `updatedAt` adott eredményhalmaz időbélyege az SDK-t helyi rendszer táblákban vannak tárolva. Későbbi lekéréses műveletek csak a rekordok beolvasása után az időbélyeg.

  Növekményes szinkronizálás használatára, a kiszolgáló kell visszaadnia jelentéssel bíró `updatedAt` értéket, majd is támogatnia kell ezt a mezőt a rendezés. Az SDK-t a saját rendezés a updatedAt mezőt ad hozzá, mivel nem használhatja azonban a lekéréses lekérdezés, amely rendelkezik a saját `orderBy` záradékban.

  A lekérdezés neve mellett dönt karakterlánc lehet, de az egyes logikai lekérdezések az alkalmazásában egyedinek kell lennie.
  Ellenkező esetben különböző lekéréses műveletek esetleges ugyanazzal az időbélyeggel növekményes szinkronizálást, és a lekérdezések hibás eredményeket is.

  Ha a lekérdezés egy paraméterrel rendelkezik, egyedi lekérdezés neve létrehozásának egyik módja az foglalják magukban a paraméter értéke.
  Például ha szűrést végez a felhasználói azonosítóját, a lekérdezés neve lehet módon (C#-ban):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Ha azt szeretné, a növekményes szinkronizálás kikapcsolásához, `null` , a lekérdezés azonosítóját. Ebben az esetben az összes rekord lekérése megtörténik a minden meghívásához `PullAsync`, amelyek nem potenciálisan hatékony.
* **Végleges törlés**: törölheti a tartalmát a helyi tároló használata `IMobileServiceSyncTable.PurgeAsync`.
  Végleges törlés szükség lehet, ha elavult adatokat az ügyfél-adatbázisban, vagy ha szeretné elvetni az összes függőben lévő módosítást.

  Végleges törlésére törli a helyi tároló tábla. Ha műveletek várnak az adatbázishoz való szinkronizálás, a kiürítés kivételt jelez, kivéve, ha a *kényszerített kiürítés* paraméter értéke.

  Az elavult adatok az ügyfélen például tegyük fel, hogy a "teendőlista" példában Device1 csak lekéri az elemek nem fejeződtek be. A todoitem "Vásárlása tej" van megjelölve a kiszolgálón egy másik eszköz befejeződött. Azonban Device1 továbbra is, a "Vásárlás tej" todoitem helyi tároló mert csak, van lehetőség elemek, amelyek nincsenek megjelölve befejeződött. Végleges törlésére törli az elavult elem.

## <a name="next-steps"></a>További lépések
* [iOS: offline szinkronizálás engedélyezése]
* [Xamarin IOS-es: offline szinkronizálás engedélyezése]
* [Xamarin Android: Offline szinkronizálás engedélyezése]
* [Univerzális Windows Platform: Offline szinkronizálás engedélyezése]

<!-- Links -->
[.NET ügyféloldali SDK-val]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Offline szinkronizálás engedélyezése]: app-service-mobile-android-get-started-offline-data.md
[iOS: offline szinkronizálás engedélyezése]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin IOS-es: offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzális Windows Platform: Offline szinkronizálás engedélyezése]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
