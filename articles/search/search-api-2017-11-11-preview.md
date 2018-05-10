---
title: Az Azure Search szolgáltatás REST API-verzió 2017 11-11 – előzetes verzió |} Microsoft Docs
description: Az Azure Search szolgáltatás REST API-verzió 2017 11-11-előzetes verzió például szinonimák és moreLikeThis keresések kísérleti funkciót tartalmaz.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2017 11-11 – előzetes verzió
Ez a cikk ismerteti a `api-version=2017-11-11-Preview` Azure Search szolgáltatás REST API-t a következő kísérleti szolgáltatásokat nyújtó verziója:

+ [Kognitív keresési](cognitive-search-concept-intro.md), egy új dúsító funkció az Azure Search indexelő, amely nem szöveges források és magánháztartás szöveg átalakítása azt a teljes szöveges kereshető tartalom az Azure Search rejtett információkat talál.

A minta API megegyezik az általánosan elérhető API-t a következő két műveletek kivételével:

+ [Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)](ref-create-skillset.md)
+ [Hozzon létre indexelőt (api-version = 2017-11-11 – előzetes verzió)](ref-create-indexer.md)

Ügyeljen arra, hogy a cél API előzetes verzióját `api-version=2017-11-11-Preview` előzetes kiadású szolgáltatások kiértékelése során. A következő példa szintaxist API előzetes verzióját hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. **Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.**

Az Azure Search szolgáltatás több verzióban érhető el. Tekintse meg [API-verziók](search-api-versions.md) részleteiről.
