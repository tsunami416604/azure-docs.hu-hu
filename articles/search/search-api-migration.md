---
title: "Az Azure Search szolgáltatás REST API verziója 2016-09-01 frissítése |} Microsoft Docs"
description: "Az Azure Search szolgáltatás REST API verziója 2016-09-01 frissítése"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Az Azure Search szolgáltatás REST API verziója 2016-09-01 frissítése
2015-02-28 verzió vagy 2015-02-28-előzetes verziója használata a [Azure Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx), ez a cikk segít frissíteni az alkalmazást a következő általánosan elérhető API-verzió 2016-09-01 használatára.

A REST API verziója 2016-09-01 korábbi verzióihoz képest végrehajtott egyes módosításokat tartalmazza. Ezek a legtöbbször visszamenőlegesen kompatibilis, így a kód módosítása csak csatlakozhatnak, attól függően, hogy milyen verziójú használata előtt érdemes beállítani. Lásd: [frissítésének lépései](#UpgradeSteps) útmutatást a kód módosítása az új API-verzió használatára.

> [!NOTE]
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>2016-09-01 verzió újdonságai
Verzió 2016-09-01 a második általánosan elérhető az Azure Search szolgáltatás REST API kiadása. Az API-verzió az új funkciói a következők:

* [Egyéni lekérdezések](https://aka.ms/customanalyzers), amelyek lehetővé teszik, hogy a folyamat alakításával előállított szöveges példánycímkének és kereshető jogkivonatokba vezérlését.
* [Az Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) és [Azure Table Storage](search-howto-indexing-azure-tables.md) indexelők, amelyek segítségével könnyen adatokat importálhat az Azure storage Azure Search ütemezés vagy igény szerinti.
* [Hozzárendelések mezőben](search-indexer-field-mappings.md), amelyek lehetővé teszik, hogy hogyan indexelők adatimportálás Azure Search szolgáltatásba történő testreszabásához.
* ETag-EK, amelyek lehetővé teszik, hogy a definíciók indexek, az indexelők és az adatforrások frissítése párhuzamossági szálbiztos módon. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítésének lépései
Ha 2015-02-28 verzióról frissít, valószínűleg nem kell módosítani a kód nem változtatja meg. A csak helyzetekben, ahol kódot módosítani szeretne, amikor:

* A kód sikertelen lesz, amikor egy API-válaszból ismeretlen tulajdonságok vissza. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja-tulajdonságok nem értelmezi.
* A kód továbbra is fennáll az API-kérelmek és megpróbálná elküldeni ezeket az új API-verzióra. Például Ez azért fordulhat elő, ha az alkalmazás továbbra is fennáll, a Search API által visszaadott folytatási jogkivonatok (további információkért tekintse meg `@search.nextPageParameters` a a [keresési API-referencia](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Ha bármelyik ilyen helyzetekben vonatkozik, majd esetleg ennek megfelelően módosítania kell a kódot. Ellenkező esetben nem végez módosítást kell szükséges kivéve, ha el szeretné indítani a használatával a [új szolgáltatások](#WhatsNew) verzió 2016-09-01.

Ha verzió 2015-02-28-Preview frissít, a fenti is érvényes, de is figyelembe kell venni, hogy bizonyos előzetes verziójú funkciók nem érhetők el verziójában 2016-09-01:

* Az Azure Blob Storage indexelő támogatása CSV-fájlok és a JSON-tömbök tartalmazó BLOB.
* Szinonimák
* "Több a" lekérdezések

Ha a kódot használja ezeket a funkciókat, csak akkor térnek át 2016-09-01 eltávolítása a használata nélkül.

> [!IMPORTANT]
> Adjon ne feledje, az előzetes API-k tesztelésében és értékelésében készült, és nem használható üzemi környezetben.
> 
> 

## <a name="conclusion"></a>Összegzés
Ha további információk az Azure Search szolgáltatás REST API van szüksége, tekintse meg a közelmúltban frissített [API-referencia](https://msdn.microsoft.com/library/azure/dn798935.aspx) az MSDN Webhelyén.

Azure Search szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megkérdezi a Súgó a [Azure Search MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) vagy [StackOverflow](http://stackoverflow.com/). Ha Azure Search kapcsolatos kérdést a StackOverflow van feltenni, ügyeljen arra, hogy a címke `azure-search`.

Köszönjük, hogy az Azure Search használatával!

