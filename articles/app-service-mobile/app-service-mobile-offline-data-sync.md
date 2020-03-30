---
title: Kapcsolat nélküli adatszinkronizálás
description: Az Azure Mobile Apps offline adatszinkronizálási funkciójának általános áttekintése és áttekintése
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458749"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban

## <a name="what-is-offline-data-sync"></a>Mi az offline adatszinkronizálás?
Az offline adatszinkronizálás az Azure Mobile Apps ügyfél- és kiszolgálós SDK-szolgáltatása, amely megkönnyíti a fejlesztők számára a hálózati kapcsolat nélküli, működő alkalmazások létrehozását.

Ha az alkalmazás offline módban van, továbbra is létrehozhat és módosíthat adatokat, amelyeket a rendszer egy helyi tárolóba ment. Amikor az alkalmazás újra online állapotba kerül, szinkronizálhatja a helyi módosításokat az Azure Mobile App háttérrendszerével. A szolgáltatás támogatja az ütközések észlelését is, ha ugyanaz a rekord mind az ügyfélen, mind a háttérrendszeren módosul. Az ütközések ezután kezelhetők a kiszolgálón vagy az ügyfélen.

Az offline szinkronizálásnak számos előnye van:

* Az alkalmazás válaszképességének javítása a kiszolgálóadatok helyi gyorsítótárazásával az eszközön
* Robusztus alkalmazások létrehozása, amelyek hálózati problémák esetén is hasznosak maradnak
* Lehetővé teszi a végfelhasználók számára, hogy akkor is hozzanak létre és módosítsanak adatokat, ha nincs hálózati hozzáférés, támogatva azokat a forgatókönyveket, amelyek kevés vagy semmilyen kapcsolattal nem rendelkeznek
* Adatok szinkronizálása több eszközön, és ütközések észlelése, ha ugyanazt a rekordot két eszköz módosítja
* A hálózat használatának korlátozása nagy késleltetésű vagy forgalmi díjas hálózatokon

Az alábbi oktatóanyagok bemutatják, hogyan adhat hozzá offline szinkronizálást a mobilügyfelekhez az Azure Mobile Apps használatával:

* [Android: Offline szinkronizálás engedélyezése]
* [Apache Cordova: Offline szinkronizálás engedélyezése](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Offline szinkronizálás engedélyezése]
* [Xamarin iOS: Offline szinkronizálás engedélyezése]
* [Xamarin Android: Offline szinkronizálás engedélyezése]
* [Xamarin.Forms: Offline szinkronizálás engedélyezése](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Univerzális Windows-platform: Offline szinkronizálás engedélyezése]

## <a name="what-is-a-sync-table"></a>Mi az a szinkronizálási tábla?
A "/tables" végpont eléréséhez az Azure Mobile-ügyfél SDK-k biztosít felületek, például `IMobileServiceTable` (.NET ügyfél SDK) vagy `MSTable` (iOS-ügyfél). Ezek az API-k közvetlenül csatlakoznak az Azure Mobile App háttérrendszeréhez, és sikertelenek, ha az ügyféleszköz nem rendelkezik hálózati kapcsolattal.

Az offline használat támogatásához az alkalmazásnak ehelyett a *szinkronizálási tábla* API-jait kell használnia, például `IMobileServiceSyncTable` (.NET ügyfél SDK) vagy `MSSyncTable` (iOS-ügyfél). Minden egyes CRUD-művelet (Létrehozás, Olvasás, Frissítés, Törlés) a szinkronizálási tábla API-k ellen működik, kivéve most, hogy egy *helyi tárolóból*olvasnak vagy írnak. A szinkronizálási tábla műveletek végrehajtása előtt a helyi tárolót inicializálva kell végezni.

## <a name="what-is-a-local-store"></a>Mi az a helyi bolt?
A helyi tároló az ügyféleszközön lévő adatmegőrzési réteg. Az Azure Mobile Apps ügyfél SDK-k alapértelmezett helyi áruház-megvalósítást biztosítanak. Windows, Xamarin és Android, ez alapján SQLite. IOS rendszeren alapadatokon alapul.

Az SQLite-alapú implementáció Windows Phone-telefonon vagy Microsoft Store-ban való használatához telepítenie kell egy SQLite kiterjesztést. További információt az [Univerzális Windows-platform: Offline szinkronizálás engedélyezése]című témakörben talál. Android és iOS hajó egy változata SQLite a készülék operációs rendszer maga, így nem szükséges hivatkozni a saját verzióját SQLite.

A fejlesztők saját helyi áruházat is megvalósíthatnak. Ha például az adatokat titkosított formátumban szeretné tárolni a mobilügyfélen, megadhat egy helyi tárolót, amely az SQLCipher titkosítást használja.

## <a name="what-is-a-sync-context"></a>Mi az a szinkronizálási környezet?
A *szinkronizálási környezet* egy mobil ügyfélobjektumhoz (például `IMobileServiceClient` vagy `MSClient`) van társítva, és nyomon követi a szinkronizálási táblákkal végrehajtott módosításokat. A szinkronizálási környezet egy *műveleti várólistát*tart fenn, amely a kiszolgálónak később küldött CUD-műveletek (Létrehozás, Frissítés, Törlés) rendezett listáját tartalmazza.

A szinkronizálási környezethez egy inicializálási `IMobileServicesSyncContext.InitializeAsync(localstore)` módszer rel van társítva egy helyi tároló, például a .NET ügyfél SDK.A local store is associated with the sync context using a initialize method such as inicializálás method, such inicializálás method, such as [inicializálás]method, such inicializálás

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Az offline szinkronizálás működése
Szinkronizálási táblák használatakor az ügyfélkód szabályozza, ha a helyi módosítások szinkronizálása egy Azure Mobile App háttérrendszer. Semmi sem kerül elküldésre a háttérrendszernek, amíg nincs hívás a helyi módosítások *leküldésére.* Hasonlóképpen a helyi tároló csak akkor töltődik fel új adatokkal, ha hívás történik az adatok *lekérése.*

* **Push**: Push egy művelet a szinkronizálási környezetben, és elküldi az összes CUD változások, mivel az utolsó leküldéses. Ne feledje, hogy nem lehet csak egy adott tábla módosításait elküldeni, mert ellenkező esetben a műveletek nem sorrendben küldhetők el. A Push rest-hívások sorozatát hajtja végre az Azure Mobile App háttérrendszeréhez, ami viszont módosítja a kiszolgáló adatbázisát.
* **Lekéréses:** A lekéréses rendszer táblanként történik, és a lekérdezéssel testreszabható, hogy a kiszolgáló adatainak csak egy részét lehessen beolvasni. Az Azure Mobile-ügyfél SDK-k majd helyezze be az eredményül kapott adatokat a helyi tárolóba.
* **Implicit leküldések:** Ha egy lekéréses végrehajtása egy olyan táblához, `push()` amely függőben lévő helyi frissítéseket hajt végre, a lekéréseelőször végrehajtja a szinkronizálási környezetben. Ez a leküldéses segít minimalizálni a már várólistára helyezett módosítások és a kiszolgálóról származó új adatok közötti ütközéseket.
* **Növekményes szinkronizálás:** a lekéréses művelet első paramétere egy *olyan lekérdezésnév,* amelyet csak az ügyfélen használnak. Ha nem null lekérdezési nevet használ, az Azure Mobile SDK *növekményes szinkronizálást*hajt végre. Minden alkalommal, amikor egy lekéréses `updatedAt` művelet eredményhalmazt ad vissza, az eredményhalmaz legújabb időbélyege az SDK helyi rendszertábláiban tárolódik. Az ezt követő lekéréses műveletek csak rekordokat kérnek le az időbélyeg után.

  A növekményes szinkronizálás `updatedAt` használatához a kiszolgálónak értelmes értékeket kell visszaadnia, és támogatnia kell a mező szerint történő rendezést is. Mivel azonban az SDK hozzáadja a saját rendezését a updatedAt mezőhöz, nem használhat saját `orderBy` záradékkal rendelkező lekéréses lekérdezést.

  A lekérdezés neve tetszőleges karakterlánc lehet, de az alkalmazás minden logikai lekérdezéséhez egyedinek kell lennie.
  Ellenkező esetben a különböző lekéréses műveletek felülírhatják ugyanazt a növekményes szinkronizálási időbélyeget, és a lekérdezések helytelen eredményeket adhatnak vissza.

  Ha a lekérdezés nek van paramétere, az egyedi lekérdezésnév létrehozásának egyik módja a paraméter értékének beleépítése.
  Ha például a userid-ra szűr, a lekérdezés neve a következő lehet (C#-ban):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Ha le szeretné tiltani a növekményes szinkronizálást, adja át `null` a lekérdezésazonosítót. Ebben az esetben a rendszer minden rekordot lekér a híváskor, `PullAsync`ami potenciálisan nem hatékony.
* **Törlés**: A helyi áruház tartalmát törölheti a használatával. `IMobileServiceSyncTable.PurgeAsync`
  Tisztításra akkor lehet szükség, ha elavult adatok vannak az ügyféladatbázisban, vagy ha az összes függőben lévő módosítást el szeretné vetni.

  A kiürítés törli a táblát a helyi tárolóból. Ha vannak olyan műveletek, amelyek szinkronizálásra várnak a kiszolgálóadatbázissal, a kiürítés kivételt okoz, kivéve, ha az *erőkiírás* paraméter be van állítva.

  Az ügyfél elavult adatainak példájaként tegyük fel, hogy a "teendőlista" példában az Eszköz1 csak a nem befejezett elemeket kéri le. A "Tej vásárlása" todoitem-et egy másik eszköz tölti be a kiszolgálón. Azonban a Device1 még mindig a "Tej vásárlása" todoitem a helyi boltban, mert csak húzza elemeket, amelyek nincsenek megjelölve teljes. A kiürítés törli ezt az elavult elemet.

## <a name="next-steps"></a>További lépések
* [iOS: Offline szinkronizálás engedélyezése]
* [Xamarin iOS: Offline szinkronizálás engedélyezése]
* [Xamarin Android: Offline szinkronizálás engedélyezése]
* [Univerzális Windows-platform: Offline szinkronizálás engedélyezése]

<!-- Links -->
[.NET ügyfél SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Offline szinkronizálás engedélyezése]: app-service-mobile-android-get-started-offline-data.md
[iOS: Offline szinkronizálás engedélyezése]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Offline szinkronizálás engedélyezése]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Univerzális Windows-platform: Offline szinkronizálás engedélyezése]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
