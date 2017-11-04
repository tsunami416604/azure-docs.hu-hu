---
title: "Az Azure Search szolgáltatás REST API-verzió 2016 09-01. dátumú előnézeti |} Microsoft Docs"
description: "Az Azure Search szolgáltatás REST API-verzió 2016 09-01. dátumú előnézeti például szinonimák és moreLikeThis keresések kísérleti funkciót tartalmaz."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2016 09-01. dátumú előnézeti
Ez a cikk a referenciadokumentációt tartalmaz `api-version=2016-09-01-Preview`. Ez az előnézet kibővíti az aktuális általánosan elérhető verzió [api-version = 2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), a következő kísérleti funkciók megadásával:

* [Szinonimák API](search-synonyms.md) szinonimát maps, és bontsa ki a keresés.
* [`moreLikeThis`lekérdezésparaméter](search-more-like-this.md) , amelyek megfelelnek egy adott dokumentum a dokumentumok kereséséhez.

Győződjön meg arról, amelyekre a minta API-verzió `api-version=2016-09-01-Preview` ezek a kísérleti funkciók kipróbálásához. A következő példa bemutatja, hogyan előzetes api-verzió van megadva egy több-szerű a lekérdezés létrehozása során.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. **Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.**

Az Azure Search szolgáltatás több verzióban érhető el. Tekintse meg [keresési Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) részleteiről.
