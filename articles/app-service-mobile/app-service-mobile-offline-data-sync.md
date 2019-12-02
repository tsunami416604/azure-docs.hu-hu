---
title: Offline adatszinkronizálás
description: Az Azure Mobile Apps offline adatszinkronizálási funkciójának fogalmi referenciája és áttekintése
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 9238ebd06a4aa532d20a2a98499963a75780f025
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668419"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Offline adatszinkronizálás az Azure Mobile Appsban

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="what-is-offline-data-sync"></a>Mi az offline adatszinkronizálás?
Az offline adatszinkronizálás az Azure-Mobile Apps ügyfél-és kiszolgálói SDK-funkciója, amely megkönnyíti a fejlesztők számára, hogy hálózati kapcsolat nélkül működő alkalmazásokat hozzanak létre.

Ha az alkalmazás kapcsolat nélküli módban van, akkor továbbra is létrehozhat és módosíthat egy helyi tárolóba mentett adatmennyiséget. Amikor az alkalmazás ismét online állapotba kerül, szinkronizálhatja a helyi módosításokat az Azure Mobile apps-háttérrel. A szolgáltatás emellett támogatja az ütközések észlelését is, ha ugyanaz a rekord módosul az ügyfélen és a háttérben is. Az ütközések ezután a kiszolgálón vagy az ügyfélen is kezelhetők.

Az offline szinkronizálás számos előnnyel jár:

* Az alkalmazások rugalmasságának javítása a kiszolgáló adatgyorsítótárazásával az eszközön helyileg
* Olyan robusztus alkalmazásokat hozhat létre, amelyek hálózati problémák esetén is hasznosak
* A végfelhasználók számára lehetővé teszi az adatok létrehozását és módosítását akkor is, ha nincs hálózati hozzáférés, kis vagy semmilyen kapcsolattal nem rendelkező támogatási forgatókönyvek
* Adatok szinkronizálása több eszköz között, és ütközések észlelése, ha ugyanazt a rekordot két eszköz módosítja
* Hálózati használat korlátozása nagy késésű vagy mért hálózatokon

Az alábbi oktatóanyagok azt mutatják be, hogyan lehet offline szinkronizálást hozzáadni a mobil ügyfelekhez az Azure Mobile Apps használatával:

* [Android: offline szinkronizálás engedélyezése]
* [Apache Cordova: offline szinkronizálás engedélyezése](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: offline szinkronizálás engedélyezése]
* [Xamarin iOS: offline szinkronizálás engedélyezése]
* [Xamarin Android: offline szinkronizálás engedélyezése]
* [Xamarin. Forms: offline szinkronizálás engedélyezése](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzális Windows-platform: offline szinkronizálás engedélyezése]

## <a name="what-is-a-sync-table"></a>Mi az a szinkronizálási táblázat?
Az "/Tables" végpont eléréséhez az Azure Mobile Client SDK-k olyan felületeket biztosítanak, mint a `IMobileServiceTable` (.NET Client SDK) vagy `MSTable` (iOS-ügyfél). Ezek az API-k közvetlenül csatlakoznak az Azure Mobile apps-háttérhöz, és sikertelenek lesznek, ha az ügyfél nem rendelkezik hálózati kapcsolattal.

Az offline használat támogatásához az alkalmazásnak Ehelyett a *Sync Table* API-kat (például `IMobileServiceSyncTable` (.net Client SDK) vagy `MSSyncTable` (iOS-ügyfél) kell használnia. Az összes azonos szifilisz-művelet (létrehozás, olvasás, frissítés, törlés) a Sync Table API-k használatával működik, kivéve, ha egy *helyi áruházba*olvas vagy ír. A szinkronizálási tábla műveleteinek elvégzése előtt inicializálni kell a helyi tárolót.

## <a name="what-is-a-local-store"></a>Mi a helyi tároló?
A helyi tároló az ügyfél eszközén az adatmegőrzési réteg. Az Azure Mobile Apps ügyfél SDK-k alapértelmezett helyi tároló-implementációt biztosítanak. Windows, Xamarin és Android rendszeren az SQLite-alapú. IOS rendszeren az alapértékeken alapul.

Ha Windows Phone-telefon vagy Microsoft Store SQLite-alapú megvalósítását szeretné használni, telepítenie kell egy SQLite-bővítményt. További információt a [univerzális Windows-platform: offline szinkronizálás engedélyezése]című témakörben talál. Az Android és az iOS rendszerű eszközök az operációs rendszerhez tartozó SQLite-verzióval rendelkeznek, ezért nem szükséges a saját SQLite-verziójára hivatkozni.

A fejlesztők saját helyi áruházat is alkalmazhatnak. Ha például titkosított formátumban szeretné tárolni az adattárolást a mobil ügyfélen, megadhat egy helyi tárolót, amely a SQLCipher-t használja a titkosításhoz.

## <a name="what-is-a-sync-context"></a>Mi a szinkronizálási környezet?
A *szinkronizálási környezet* egy mobil ügyfél-objektumhoz (például `IMobileServiceClient` vagy `MSClient`) van társítva, és nyomon követi a szinkronizálási táblákon végrehajtott módosításokat. A szinkronizálási környezet fenntart egy *műveleti várólistát*, amely megtartja a kiszolgálón később eljuttatott CUD-műveletek (létrehozás, frissítés, törlés) rendezett listáját.

Egy helyi tároló társítva van a szinkronizálási környezettel egy inicializálási módszer, például a [.net Client SDK]`IMobileServicesSyncContext.InitializeAsync(localstore)` használatával.

## <a name="how-sync-works"></a>Az offline szinkronizálás működése
A szinkronizálási táblázatok használatakor az ügyfél kódja szabályozza, hogy a helyi módosítások hogyan legyenek szinkronizálva egy Azure Mobile apps-háttérrel. A rendszer semmit nem küld a háttérbe, amíg a helyi módosítások *leküldése* meghívást nem végez. Hasonlóképpen, a helyi tároló csak akkor töltődik fel új adatokkal, ha hívása van az adatok *lekérésére* .

* **Push**: a push egy művelet a szinkronizálási környezetben, és az utolsó leküldése óta minden CUD-változást elküld. Vegye figyelembe, hogy csak az egyes táblák módosításait lehet elküldeni, mert az egyéb műveleteket nem lehet sorrendben elküldeni. A push több REST-hívást hajt végre az Azure Mobile apps-háttérre, ami viszont módosítja a kiszolgáló-adatbázist.
* **Pull**: a lekérést a rendszer táblázatos alapon hajtja végre, és testreszabhatja egy lekérdezéssel, hogy csak a kiszolgáló-adathalmazt adja vissza. Az Azure Mobile Client SDK-k ezután beszúrják az eredményül kapott adattárakat a helyi tárolóba.
* **Implicit leküldés**: Ha a lekérést egy függőben lévő helyi frissítéssel rendelkező táblán hajtja végre, a lekéréses szolgáltatás először végrehajt egy `push()` a szinkronizálási környezetben. Ez a leküldés segít csökkenteni a már várólistára helyezett és a kiszolgáló új adatait érintő ütközéseket.
* **Növekményes szinkronizálás**: a lekéréses művelet első paramétere a csak az ügyfélen használt *lekérdezés neve* . Ha nem null értékű lekérdezési nevet használ, az Azure Mobile SDK *növekményes szinkronizálást*hajt végre. Minden alkalommal, amikor egy lekéréses művelet eredményeket ad vissza, a rendszer az SDK helyi rendszertábláiban tárolja az adott eredményhalmaz legutóbbi `updatedAt` időbélyegét. Az ezt követő lekérési műveletek csak az időbélyeg utáni rekordokat kérik le.

  A növekményes szinkronizálás használatához a kiszolgálónak értelmes `updatedAt` értékeket kell visszaadnia, és a mező szerinti rendezést is támogatnia kell. Mivel azonban az SDK hozzáadja a saját rendezését a updatedAt mezőben, nem használhat olyan lekéréses lekérdezést, amely rendelkezik saját `orderBy` záradékkal.

  A lekérdezés neve bármely kiválasztott sztring lehet, de egyedinek kell lennie az alkalmazás minden logikai lekérdezéséhez.
  Ellenkező esetben a különböző lekéréses műveletek felülírhatják a növekményes szinkronizálás időbélyegét, és a lekérdezések helytelen eredményeket adhatnak vissza.

  Ha a lekérdezés paraméterrel rendelkezik, az egyedi lekérdezési név létrehozásának egyik módja a paraméter értékének beépítése.
  Ha például a felhasználóazonosító-t szűri, a lekérdezés neve a következő lehet (a C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Ha szeretné letiltani a növekményes szinkronizálást, adja át `null` lekérdezési AZONOSÍTÓjának. Ebben az esetben a rendszer minden rekordot beolvas minden `PullAsync`re, ami potenciálisan nem hatékony.
* Törlés: törölheti a helyi tároló tartalmát `IMobileServiceSyncTable.PurgeAsync`használatával.
  Előfordulhat, hogy a kiürítés akkor szükséges, ha elavult az adatbázis, vagy ha el szeretné vetni az összes függőben lévő módosítást.

  A kiürítés törli a táblát a helyi tárolóból. Ha vannak olyan műveletek, amelyek a kiszolgáló adatbázisával való szinkronizálásra várnak, a kiürítés kivételt jelez, kivéve, ha a *kényszerített kiürítési* paraméter be van állítva.

  Az ügyfél elavult adatokra Példaként tegyük fel, hogy a "Todo List" példában a Device1 csak a nem befejezett elemeket kéri le. A "Buy Milk" todoitem egy másik eszköz jelöli meg a kiszolgálón. Azonban a Device1 továbbra is a "Buy Milk" todoitem a helyi tárolóban, mert csak olyan elemeket húz le, amelyek nincsenek megjelölve készként. A kiürítés törli ezt az elavult tételt.

## <a name="next-steps"></a>Következő lépések
* [iOS: offline szinkronizálás engedélyezése]
* [Xamarin iOS: offline szinkronizálás engedélyezése]
* [Xamarin Android: offline szinkronizálás engedélyezése]
* [Univerzális Windows-platform: offline szinkronizálás engedélyezése]

<!-- Links -->
[.NET Client SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: offline szinkronizálás engedélyezése]: app-service-mobile-android-get-started-offline-data.md
[iOS: offline szinkronizálás engedélyezése]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzális Windows-platform: offline szinkronizálás engedélyezése]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
