---
title: Az Azure Search szolgáltatás REST API-verzió 2016 09-01. dátumú előnézeti |} Microsoft Docs
description: Az Azure Search szolgáltatás REST API-verzió 2016 09-01. dátumú előnézeti például moreLikeThis keresések kísérleti funkciót tartalmaz.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2016 09-01. dátumú előnézeti
Ez a cikk a referenciadokumentációt tartalmaz `api-version=2016-09-01-Preview`. Ez az előnézet kibővíti az aktuális általánosan elérhető verzió [api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), a következő kísérleti funkciók megadásával:

* [`moreLikeThis` lekérdezésparaméter](search-more-like-this.md) , amelyek megfelelnek egy adott dokumentum a dokumentumok kereséséhez.

Győződjön meg arról, amelyekre a minta API-verzió `api-version=2016-09-01-Preview` ezek a kísérleti funkciók kipróbálásához. A következő példa bemutatja, hogyan előzetes api-verzió van megadva egy több-szerű a lekérdezés létrehozása során.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. **Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.**

Az Azure Search szolgáltatás több verzióban érhető el. Tekintse meg [keresési Service Versioning](https://docs.microsoft.com/azure/search/search-api-versions) részleteiről.
