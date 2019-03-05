---
title: Azure Search-index javaslattevők hozzáadása
description: Lehetővé teszi mezők gépelés közbeni lekérdezési műveletek végrehajtásához, ahol javasolt lekérdezések Azure Search-index mezőire szöveget alkotják.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308652"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Azure Search-index javaslattevők hozzáadása

A **javaslattevő** egy Azure Search-szerkezet támogatja a "Keresés –--beíráskor" [javaslatok](https://docs.microsoft.com/rest/api/searchservice/suggestions) funkció és a [automatikus kiegészítési funkciójának (előzetes verzió)](search-autocomplete-tutorial.md) funkció. A javaslatok API hívása előtt meg kell adnia egy **javaslattevő** egy index meghatározott mezőkre vonatkozó javaslatok engedélyezéséhez.

Bár egy **javaslattevő** néhány olyan tulajdonság tartozik, hogy az elsősorban, amelyhez engedélyezi a mezők gyűjteménye a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions). Például egy utazási alkalmazás érdemes a destinations, város és létesítmények typeahead keresésének lehetővé tételéhez. Ennek megfelelően minden három mezőt a mező gyűjtemény helyezkedik el.

Csak egyet használhat **javaslattevő** erőforrás minden index (pontosabban az egyik **javaslattevő** a a **javaslattevők** gyűjtemény).

## <a name="creating-a-suggester"></a>A javaslattevő létrehozása

Hozhat létre egy **javaslattevő** bármikor, de az index gyakorolt függően változik, a mező alapján.

+ A javaslattevő hozzá ugyanazt a frissítést részeként új mezők a legkevésbé ahhoz, hogy nincs indexkészítés nem szükséges.
+ Meglévő azonban módosítja a javaslattevő hozzáadott mezők kialakulhat egy teljesen újra kell építenie az indexet a mező definíciójában.

**Javaslattevők** leghatékonyabbak, ha annak alapján hozza létre a meghatározott dokumentumok helyett laza szavakat vagy kifejezéseket. A legjobb jelölt mezők, címek, nevek és más viszonylag rövid kifejezés, amely egy elem azonosíthatja. Kevésbé hatékony: ismétlődő mezőket, például a kategóriák és a címkéket, vagy nagyon hosszú mezők, például a leírásokat és megjegyzéseket mezőket.

A javaslattevő létrehozása után adja hozzá a [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) a lekérdezés logikai meghívni a szolgáltatást.

Tulajdonságok, amelyek meghatározzák egy **javaslattevő** többek között az alábbiak:

|Tulajdonság|Leírás|
|--------------|-----------------|
|`name`|Neve a **javaslattevő**. Nevét használja a **javaslattevő** hívásakor a [javaslatok &#40;Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|
|`searchMode`|A jelölt kifejezések keresésére használt stratégia. Mód csak a jelenleg támogatott `analyzingInfixMatching`, amely rugalmas megfelelő mondat eleji vagy mondat közepén hajt végre.|
|`sourceFields`|Egy vagy több mező, amelyek a forrás javaslatok tartalmának listáját. Csak típusú mezők `Edm.String` és `Collection(Edm.String)` lehet, hogy források javaslatokért. Csak állítsa be egyéni nyelvi elemzőt nem rendelkező mezők is használható. |

## <a name="suggester-example"></a>Javaslattevő példa
A **javaslattevő** része az index definícióját. Csak egy **javaslattevő** is szerepelhet a **javaslattevők** gyűjtemény a jelenlegi verzióban mellett a **mezők** gyűjtemény és **scoringProfiles**.

```
{
  "name": "hotels",
  "fields": [
    . . .
  ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ],
  "scoringProfiles": [
    . . .
  ]
}

```

## <a name="see-also"></a>Lásd még
[Index létrehozása &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[Index frissítése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[Javaslatok &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[Index operations &#40;Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  
[Azure Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
