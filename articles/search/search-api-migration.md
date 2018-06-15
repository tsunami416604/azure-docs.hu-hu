---
title: A legújabb Azure Search szolgáltatás REST API-verzióra való frissítést |} Microsoft Docs
description: A legújabb Azure Search szolgáltatás REST API-verzióra való frissítést
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3848f317fd6d760961756f132edf9cbcb5f431ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181970"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>A legújabb Azure Search szolgáltatás REST API-verzióra való frissítést
Előző verziójának használata a [Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/), ez a cikk segít frissíteni az alkalmazást a legújabb használandó általánosan elérhető API-verzió, 2017-11 – 11.

A REST API verziója 2017-11-11 korábbi verzióihoz képest végrehajtott egyes módosításokat tartalmazza. Ezek a legtöbbször visszamenőlegesen kompatibilis, így a kód módosítása csak csatlakozhatnak, attól függően, hogy milyen verziójú használata előtt érdemes beállítani. Lásd: [frissítésének lépései](#UpgradeSteps) útmutatást a kód módosítása az új API-verzió használatára.

> [!NOTE]
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>2017-11-11-es verzió újdonságai
Verzió 2017-11-11 a legújabb általánosan elérhető az Azure Search szolgáltatás REST API kiadásában. Az API-verzió az új funkciói a következők:

* [Szinonimák](search-synonyms.md). Az új szinonimák funkció lehetővé teszi a megfelelő feltételek megadásához, és bontsa ki a lekérdezés hatókörét.
* [Támogatja a szöveges BLOB hatékonyan indexelő](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Az új `text` jelentősen elemzése az Azure Blob indexelők mód növeli az indexelési teljesítmény.
* [Statisztika API szolgáltatás](https://aka.ms/azure-search-stats). Aktuális használati és korlátai által megszabott erőforrások megtekintése az Azure Search az új API-t.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítésének lépései
Ha egy GA verzióra frissít, 2015-02-28 vagy 2016-09-01, valószínűleg nem kell módosítani a kód nem változtatja meg. A csak helyzetekben, ahol kódot módosítani szeretne, amikor:

* A kód sikertelen lesz, amikor egy API-válaszból ismeretlen tulajdonságok vissza. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja-tulajdonságok nem értelmezi.
* A kód továbbra is fennáll az API-kérelmek és megpróbálná elküldeni ezeket az új API-verzióra. Például Ez azért fordulhat elő, ha az alkalmazás továbbra is fennáll, a Search API által visszaadott folytatási jogkivonatok (további információkért tekintse meg `@search.nextPageParameters` a a [keresési API-referencia](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Ha bármelyik ilyen helyzetekben vonatkozik, majd esetleg ennek megfelelően módosítania kell a kódot. Ellenkező esetben nem végez módosítást kell szükséges kivéve, ha el szeretné indítani a használatával a [új szolgáltatások](#WhatsNew) 2017-11-11 verzió.

Ha előzetes api-verzióval frissít, a fenti is érvényes, de is figyelembe kell venni, hogy bizonyos előzetes verziójú funkciók nem érhetők el a 2017-11-11-es verzió:

* Az Azure Blob Storage indexelő támogatása CSV-fájlok és a JSON-tömbök tartalmazó BLOB.
* "Több a" lekérdezések

Ha a kódot használja ezeket a funkciókat, csak akkor térnek át 2017-11-11 eltávolítása a használata nélkül.

> [!IMPORTANT]
> Adjon ne feledje, az előzetes API-k tesztelésében és értékelésében készült, és nem használható üzemi környezetben.
> 
> 

## <a name="conclusion"></a>Összegzés
Ha további információk az Azure Search szolgáltatás REST API van szüksége, tekintse meg a közelmúltban frissített [API-referencia](https://docs.microsoft.com/rest/api/searchservice/) az MSDN Webhelyén.

Azure Search szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megkérdezi a Súgó a [Azure Search MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) vagy [StackOverflow](http://stackoverflow.com/). Ha Azure Search kapcsolatos kérdést a StackOverflow van feltenni, ügyeljen arra, hogy a címke `azure-search`.

Köszönjük, hogy az Azure Search használatával!

