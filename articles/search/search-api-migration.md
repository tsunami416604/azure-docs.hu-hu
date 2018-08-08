---
title: A legújabb Azure Search szolgáltatás REST API-verzióra való frissítést |} A Microsoft Docs
description: Az Azure Search szolgáltatás REST API legújabb verziójára
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 2efe7769f68988f3c0d52c8806b78c1b96d8c639
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620229"
---
# <a name="upgrading-to-the-latest-azure-search-service-rest-api-version"></a>Az Azure Search szolgáltatás REST API legújabb verziójára
Ha egy korábbi verzióját használja, a [Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/), ez a cikk segít frissíteni az alkalmazást a legújabb általánosan elérhető API-verzió, 2017-11-11.

REST API-verzió 2017-11-11 tartalmaz néhány módosítást korábbi verzióit. Ezek a leginkább visszamenőlegesen kompatibilis, így a kód módosítása csak minimális erőfeszítéssel, attól függően, melyik verziót használja előtt érdemes beállítani. Lásd: [frissítésére lépéseket](#UpgradeSteps) útmutatást a kód módosítása az új API-verzió használatára.

> [!NOTE]
> Az Azure Search-szolgáltatáspéldányhoz több REST API-verziók, többek között a legújabb buildszám támogatja. Továbbra is verzióját használja, amikor már nem a legújabb buildszám, de azt javasoljuk, hogy a legújabb verzió használatához kódok migrálása.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>2017-11-11-es verzió újdonságai
Verzió 2017-11-11 a legújabb Azure Search szolgáltatás REST API általánosan elérhető kiadásában. Az API-verzió az új funkciói a következők:

* [A szinonimák](search-synonyms.md). Az új szinonimák funkcióval egyenértékű kifejezések meghatározását, és bontsa ki a lekérdezés hatókörét.
* [Hatékonyan a szöveg-blobok indexelése támogatása](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). Az új `text` mód az Azure Blob-indexelők elemzés jelentősen javítja az indexelési teljesítmény.
* [Szolgáltatási statisztikák API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Az aktuális használatát és korlátait az erőforrások megtekintése az Azure Search szolgáltatásban az új API-val.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Ha egy GA verzióról frissít, 2015-02-28-as vagy 2016-09-01, valószínűleg nem kell semmilyen módosítást a kód más, a verziószám módosítása. A csak olyan helyzetekben, amelyben szükség lehet kódot, amikor:

* A kód sikertelen lesz, ha egy API-válaszban visszaadott ismeretlen tulajdonságait. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja-tulajdonságok, amely nem érthető.
* A kód továbbra is fennáll az API-kérések, és próbálja meg újból elküldeni őket az új API-verzióra. Például Ez azért fordulhat elő, ha az alkalmazás továbbra is fennáll, a Search API által visszaadott folytatási token (további információért keressen `@search.nextPageParameters` a a [Search API-referencia](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Ha ezekben a helyzetekben egyikét alkalmazza, majd szükség lehet, ennek megfelelően módosítania kell a kódot. Ellenkező esetben módosítása nélkül lehet szükség, ha el szeretné indítani a használatával a [új funkciók](#WhatsNew) a 2017-11-11-es verzió.

Ha frissíti az előzetes api-verzió, a fenti is vonatkozik, de is figyelembe kell venni, hogy bizonyos előzetes verziójú funkciók nem érhetők el a 2017-11-11-es verzió:

* Az Azure Blob Storage indexelő támogatása CSV-fájl és BLOB tartalmazó JSON-tömbök.
* "Több ilyen" lekérdezések

Ha a kódot használja ezeket a funkciókat, nem tudja frissíteni a 2017-11-11 anélkül, hogy eltávolítaná a használatát.

> [!IMPORTANT]
> . Ne feledje, az előzetes verziójú API-k, tesztelési és értékelési célokra szolgálnak, és nem éles környezetekben használható.
> 
> 

## <a name="conclusion"></a>Összegzés
Ha további részleteket az Azure Search szolgáltatás REST API használatával van szüksége, tekintse meg a nemrég frissített [API-referencia](https://docs.microsoft.com/rest/api/searchservice/) az MSDN Webhelyén.

Az Azure Search szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megszüntetését a Súgó a [Azure Search MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) vagy [StackOverflow](http://stackoverflow.com/). Ha a StackOverflow Azure Search névjegye Ön kérdéseket tehet fel, ellenőrizze, hogy a címke azt az `azure-search`.

Köszönjük, hogy az Azure Search használatával!

