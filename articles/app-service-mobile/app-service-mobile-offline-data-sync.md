---
title: "Az Azure Mobile Apps az offline adatszinkronizálás |} Microsoft Docs"
description: "Fogalmi referenciája és a kapcsolat nélküli szinkronizálás funkció az Azure Mobile Apps áttekintése"
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 8e2bd755d14319f8c66f7ae7ec64fbd10801b39d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban
## <a name="what-is-offline-data-sync"></a>Mi az az offline adatszinkronizálás?
Offline adatszinkronizálás egy ügyfél és kiszolgáló SDK szolgáltatás az Azure Mobile Apps, amely megkönnyíti a fejlesztők számára, amelyek a hálózati kapcsolat nélkül működési-alkalmazásai létrehozására.

Ha az alkalmazás offline módban van, még mindig létrehozásához és módosításához adatait, amely a helyi tárolójába kerülnek. Az alkalmazást újra online állapotba kerül, ha azt képes szinkronizálni az Azure Mobile Apps-háttéralkalmazás helyi módosításokkal. A szolgáltatás is támogatja az ütközések észlelése megváltozásakor ugyanazt a bejegyzést az ügyfélen és a háttérkiszolgálón is. Ütközések lehet kezelni, vagy a kiszolgáló vagy az ügyfélen.

Kapcsolat nélküli szinkronizálás rendelkezik számos előnnyel jár:

* Alkalmazások válaszképességét javítása a kiszolgáló az adatokat az eszközön helyileg
* Továbbra is, ha hálózati problémák hasznos robusztus alkalmazások létrehozása
* Lehetővé teszi a végfelhasználók számára, hogy hozzon létre és módosíthatják az adatokat, akkor is, amikor nincs hálózati hozzáférés, forgatókönyveket támogató összefüggő kevéssé vagy egyáltalán ne
* Szinkronizálja az adatokat több eszközön, és ütközések észlelés, ha ugyanaz a bejegyzés módosul a két eszköz
* A nagy késleltetésű vagy forgalmi díjas hálózatokon hálózati használatának korlátozása

Az alábbi oktatóanyagok bemutatják, hogyan kapcsolat nélküli szinkronizálás hozzáadása az Azure Mobile Apps használata a mobil ügyfelek:

* [Android: Kapcsolat nélküli szinkronizálásának engedélyezése]
* [Apache Cordova: Kapcsolat nélküli szinkronizálásának engedélyezése](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: kapcsolat nélküli szinkronizálásának engedélyezése]
* [Xamarin iOS: kapcsolat nélküli szinkronizálásának engedélyezése]
* [Xamarin Android: Kapcsolat nélküli szinkronizálásának engedélyezése]
* [Xamarin.Forms: Engedélyezése kapcsolat nélküli szinkronizálás](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [univerzális Windows Platform: kapcsolat nélküli szinkronizálásának engedélyezése]

## <a name="what-is-a-sync-table"></a>Mi az a egy szinkronizálás tábla?
A "/ táblák" végpont szeretne használni, az Azure Mobile ügyfél SDK-k meg felületek például `IMobileServiceTable` (.NET SDK-ügyfél) vagy `MSTable` (iOS-ügyfél). Ezen API-k közvetlen csatlakoztatása az Azure Mobile Apps-háttéralkalmazás, és sikertelen lesz, ha az ügyféleszköz nincs hálózati kapcsolat.

Kapcsolat nélküli használatát támogatja, az alkalmazás inkább használjon a *szinkronizálási tábla* API-k, például a `IMobileServiceSyncTable` (.NET SDK-ügyfél) vagy `MSSyncTable` (iOS-ügyfél). Összes szinkronizálási dolgozhat ugyanazon a CRUD műveleteihez (létrehozási, olvasási, frissítési, törlési) tábla API-k, kivéve most azok olvasni vagy írni egy *helyi tároló*. A szinkronizálási tábla műveletek végrehajtása előtt a helyi tárolójába inicializálni kell.

## <a name="what-is-a-local-store"></a>Mi az a helyi tárolót?
A helyi tárolójába a adatmegőrző réteget az ügyféleszközön. Az Azure Mobile Apps-ügyfél SDK-k implementálásához alapértelmezett helyi tárolóból. A Windows, a Xamarin és az Android SQLite alapul. IOS az alapvető adatokon alapul.

A Windows Phone vagy Windows áruház 8.1 az SQLite-alapú megvalósítás használatához telepítendő egy SQLite-bővítmény. További információkért lásd: [univerzális Windows Platform: kapcsolat nélküli szinkronizálásának engedélyezése]. Android és iOS rendszerrel szállított egy SQLite verziója az eszköz operációs rendszere saját magát, így azt nem hivatkozhat saját SQLite verzióját.

A fejlesztők is megvalósíthatja a saját helyi tárolóból. Például ha szeretne az adatok tárolása a mobil ügyfélen titkosított formátumban, megadhat titkosításhoz SQLCipher használó helyi tároló.

## <a name="what-is-a-sync-context"></a>Mi az a szinkronizálási környezetet?
A *szinkronizálási környezetet* egy mobil ügyfél objektumhoz társított (például `IMobileServiceClient` vagy `MSClient`) és szinkronizáló táblákkal végzett módosításokat követi nyomon. A szinkronizálási környezetben kezeli az *művelet várólista*, amely tartja a rendezett listáját CUD műveleteket (létrehozás, frissítés, Törlés), amely későbbi küldi el a kiszolgálónak.

A szinkronizálás környezet használatával, mint egy inicializálási metódusa társítva a helyi tárolójába `IMobileServicesSyncContext.InitializeAsync(localstore)` a a [.NET ügyfél SDK].

## <a name="how-sync-works"></a>Hogyan kapcsolat nélküli szinkronizálás használata
Szinkronizálási táblák használata esetén az Ügyfélkód szabályozza, amikor változtatásokat szinkronizálva van-e az Azure Mobile Apps-háttéralkalmazás. Semmi sem küld a háttér csak akkor hívása *leküldéses* helyi módosításokkal. Hasonlóképpen, a helyi tárolójába fel van töltve az új adatokat csak akkor, ha egy hívás a *lekéréses* adatokat.

* **Leküldéses**: leküldéses művelet, a szinkronizálási környezetben, és minden CUD módosítások elküldi az utolsó leküldéses óta. Vegye figyelembe, hogy azt nem lehet elküldeni a csak egy egyedi tábla módosítása, mert ellenkező esetben műveletek sikerült elküldeni nem megfelelő sorrendben. Leküldéses hajtja végre az Azure Mobile Apps-háttéralkalmazás, amely pedig módosítja a server-adatbázis többi hívások sorozata.
* **Lekéréses**: lekéréses tábla alapon történik, és csak egy részét a kiszolgáló adatainak beolvasása lekérdezéssel testreszabhatók. Az Azure Mobile ügyfél SDK-k helyezze be a kapott adatokat a helyi tárolójába.
* **Implicit leküldéses értesítések**: lekérési egy táblázaton, amelyeknek helyi frissítések végrehajtása, ha a lehívásos először hajt végre egy `push()` sync-környezetében. A leküldéses csökkentheti módosításokat, amelyek már sorban áll, a kiszolgáló közötti ütközések.
* **A növekményes szinkronizálás**: az első paraméter a pull művelet egy *lekérdezésnév* csak az ügyfélen használt. Ha egy nem null értékű lekérdezés nevet használja, az Azure Mobile SDK hajt végre egy *növekményes szinkronizálás*. Minden alkalommal, amikor egy lekéréses művelet adja vissza készletként, az eredmények, a legújabb `updatedAt` adott eredményhalmazából időbélyeg az SDK helyi rendszer táblázatban tárolja. További lekéréses műveletek után az időbélyeg csak rekordok beolvasása.

  Növekményes szinkronizálás használatára, a kiszolgáló kell visszaadnia jelentéssel bíró `updatedAt` értéket, majd is támogatnia kell ezt a mezőt szerint rendezve. Azonban az SDK-val saját rendezési updatedAt mező ad hozzá, mivel nem használhat saját lekéréses lekérdezés `orderBy` záradékban.

  A lekérdezés neve mellett dönt karakterlánc lehet, de az alkalmazás minden logikai lekérdezés egyedinek kell lennie.
  Ellenkező esetben másik lekéréses műveletek felülírhatja az ugyanazon a növekményes szinkronizálás időbélyeg, és a lekérdezések visszaadhatják a megfelelő eredményeket.

  Ha a lekérdezés paraméterrel rendelkezik, egy hozzon létre egy egyedi lekérdezés neve módja átfogó a paraméter értékét.
  Például ha szűrt felhasználói azonosítóját, a lekérdezés neve lehet, az alábbiak szerint (a C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Ha azt szeretné, hogy tilthatják le a növekményes szinkronizálás, adja át `null` , a lekérdezés azonosítóját. Ebben az esetben az összes rekord rendszer kérdezi le, minden egyes hívásakor `PullAsync`, amely nem potenciálisan hatékony.
* **Kiürítése**: törölheti a tartalmát a helyi tárolójába használatával `IMobileServiceSyncTable.PurgeAsync`.
  Kiürítése akkor lehet szükség, ha elavult adatokat az ügyfél-adatbázisban, vagy ha függőben lévő módosítások elvetése.

  A kiürítési törli a tábla a helyi tárolóból. Ha nincsenek műveletek várnak az adatbázishoz való szinkronizálás, a kiürítés kivételt jelez, kivéve, ha a *kiürítése kényszerítése* paraméter értéke.

  Az elavult adatok az ügyfélen például tegyük fel, hogy a "teendőlista" példában Device1 csak kéri le. elemek nem fejeződtek be. Egy todoitem "Megvásárlása tej" van megjelölve a kiszolgálón egy másik eszköz befejeződött. Azonban Device1 még a "Vásárlás tej" todoitem helyi tárolóban levő, mert csak akkor van húzza nem teljes megjelölt elemek. A kiürítési törli az elavult elem.

## <a name="next-steps"></a>Következő lépések
* [iOS: kapcsolat nélküli szinkronizálásának engedélyezése]
* [Xamarin iOS: kapcsolat nélküli szinkronizálásának engedélyezése]
* [Xamarin Android: Kapcsolat nélküli szinkronizálásának engedélyezése]
* [univerzális Windows Platform: kapcsolat nélküli szinkronizálásának engedélyezése]

<!-- Links -->
[.NET ügyfél SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Kapcsolat nélküli szinkronizálásának engedélyezése]: app-service-mobile-android-get-started-offline-data.md
[iOS: kapcsolat nélküli szinkronizálásának engedélyezése]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: kapcsolat nélküli szinkronizálásának engedélyezése]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Kapcsolat nélküli szinkronizálásának engedélyezése]: app-service-mobile-xamarin-android-get-started-offline-data.md
[univerzális Windows Platform: kapcsolat nélküli szinkronizálásának engedélyezése]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
