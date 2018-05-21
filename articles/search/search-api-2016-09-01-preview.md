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
ms.openlocfilehash: e5cd7fcd0c853f03dbafb4d95b8459dcc83aecdf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2016 09-01. dátumú előnézeti
Ez a cikk felsorolja az előnézeti funkciókat `api-version=2016-09-01-Preview`. Ez az előnézet kibővíti az előző általánosan elérhető verzió [api-version = 2016-09-01](https://docs.microsoft.com/rest/api/searchservice), a következő kísérleti funkciók megadásával:

* [`moreLikeThis` lekérdezésparaméter](search-more-like-this.md) , amelyek megfelelnek egy adott dokumentum a dokumentumok kereséséhez. Ez a szolgáltatás le lett korábbi előzetes verziójú funkciók. Ha ez az API korábbi verziójával api-hívás, továbbra is használhatja.


## <a name="how-to-call-a-preview-api"></a>Hogyan hívhatja meg a minta API

A következő példa bemutatja, hogyan előzetes api-verzió van megadva egy több-szerű a lekérdezés létrehozása során.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. **Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.**

Az Azure Search szolgáltatás több verzióban érhető el. Tekintse meg [keresési Service Versioning](https://docs.microsoft.com/azure/search/search-api-versions) részleteiről.
