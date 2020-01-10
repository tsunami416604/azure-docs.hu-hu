---
title: Képességek alaphelyzetbe állítása (API-Version = 2019-050 -06-Preview)
titleSuffix: Azure Cognitive Search
description: A növekményes bővítéshez használt előnézet REST API a készségkészlet teljes vagy részleges újrafeldolgozásához van szükség.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832158"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Képességek alaphelyzetbe állítása (API-Version = 2019-05 -06 – előzetes verzió)

> [!IMPORTANT] 
> A növekményes gazdagodás jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.

A **készségek alaphelyzetbe állítása** kérelem meghatározza, hogy mely szaktudást kell feldolgozni a következő indexelő futtatáskor. Olyan indexelő esetében, amelyeknél engedélyezve van a gyorsítótárazás, explicit módon megkérheti a feldolgozást az indexelő által nem észlelhető szaktudás-frissítésekhez. Ha például külső módosításokat hajt végre, például egy egyéni szakértelemre való átállítást, ezzel az API-val újra futtathatja a képességet. Az olyan kimenetek, mint például a Knowledge Store vagy a Search index, a gyorsítótárból származó újrafelhasználható adatok és az új tartalom alapján frissülnek.

A meglévő [készségkészlet](https://docs.microsoft.com/rest/api/searchservice/create-skillset) alaphelyzetbe ÁLLÍTHATJA http Put használatával, és megadhatja a kérelem URI-ja által frissítendő készségkészlet nevét. A kérelemben az **API-Version = 2019-05 -06-Preview verziót** kell használnia.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Másik lehetőségként a POST paranccsal helyezze el az indexelő nevét a kérelem törzsébe:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Kérésfejlécek  

 A következő táblázat ismerteti a kötelező és választható kérelmek fejléceit.  

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Beállítás `application/json`|  
|*API-kulcs:*|Kötelező. A `api-key` a keresési szolgáltatáshoz való kérelem hitelesítésére szolgál. Ez egy karakterlánc-érték, amely egyedi a szolgáltatás számára. A **készségkészlet alaphelyzetbe állítására** vonatkozó kérésnek tartalmaznia kell egy `api-key` fejlécet, amely a felügyeleti kulcshoz van beállítva (a lekérdezési kulcs helyett).|  

Szükség van a szolgáltatás nevére is a kérelem URL-címének létrehozásához. A szolgáltatás nevét és `api-key` a Azure Portal szolgáltatás áttekintés lapjáról is lekérheti. Lásd: [Azure Cognitive Search-szolgáltatás létrehozása](https://docs.microsoft.com/azure/search/search-create-service-portal) az oldalhoz navigációs Súgó.  

## <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa  

A kérelem törzse a szaktudás nevét tartalmazó tömb.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Válasz  

Állapotkód: 204 nincs tartalom a sikeres válaszhoz. 

## <a name="see-also"></a>Lásd még:

+ [Keresési REST API-k](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP-állapotkódok](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [AI-dúsítás áttekintése](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Gyorsítótárazás és növekményes növelés konfigurálása](search-howto-incremental-index.md)
+ [Növekményes dúsítás](cognitive-search-incremental-indexing-conceptual.md)