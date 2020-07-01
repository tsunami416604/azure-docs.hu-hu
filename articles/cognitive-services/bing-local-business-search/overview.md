---
title: Mi az a Bing Local Business Search API?
titleSuffix: Azure Cognitive Services
description: A Bing Local Business Search API egy RESTful szolgáltatás, amellyel az alkalmazások a közeli helyekkel és vállalkozásokkal kapcsolatos információkat találhatnak keresési lekérdezések alapján.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 685ee0c616234563981e55f14213e424daae32f5
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611271"
---
# <a name="what-is-bing-local-business-search"></a>Mi a Bing helyi üzleti keresés?
A Bing local Business Search API egy REST-alapú szolgáltatás, amely lehetővé teszi az alkalmazások számára, hogy keresési lekérdezések alapján megtalálják a helyi vállalkozások adatait. Például:, `q=<business-name> in Redmond, Washington` vagy `q=Italian restaurants near me` . 

## <a name="features"></a>Szolgáltatások
| Szolgáltatás | Leírás |  
| -- | -- | 
| [Helyi vállalkozások és helyek keresése](quickstarts/local-quickstart.md) | A Bing local Business keresési API honosított eredményeket kap egy lekérdezésből. Az eredmények közé tartozik a vállalat webhelyének URL-címe, valamint a szöveg, a telefonszám és a földrajzi hely megjelenítése, beleértve a következőket: GPS koordináták, város, utca címe |  
| [Helyi eredmények szűrése földrajzi határokkal](specify-geographic-search.md) | Adja hozzá a koordinátákat keresési paraméterekként, hogy az eredményeket egy adott földrajzi területre korlátozza, amely egy kör alakú terület vagy a négyzetes határoló mező alapján van meghatározva. | 
| [Helyi üzleti eredmények szűrése kategóriánként](local-categories.md) | Keressen helyi üzleti eredményeket kategóriánként. Ez a beállítás a hívó fordított IP-címét vagy GPS-koordinátáit használja a honosított eredmények visszaadásához a különböző üzleti kategóriákban.|

## <a name="workflow"></a>Munkafolyamat
Hívja meg a Bing local Business Search API-t bármilyen programozási nyelvről, amely HTTP-kéréseket tesz elérhetővé, és elemezheti a JSON-válaszokat. Ez a szolgáltatás a REST API használatával érhető el.
 
1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , amely hozzáféréssel rendelkezik a Bing Search API-khoz. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).   
2. Az URL-cím kódolja a keresési kifejezéseket a `q=""` lekérdezési paraméterhez. Például `q=nearby+restaurant` vagy `q=nearby%20restaurant`. Szükség esetén állítsa be a tördelést is. 
3. Kérelem küldése [a Bing helyi üzleti keresési API-](quickstarts/local-quickstart.md) nak 
4. A JSON-válasz elemzése 

> [!NOTE]
> Jelenleg a helyi üzleti keresés: 
> * Csak a `en-US` piacot támogatja. 
> * A nem támogatja a Bing Autosuggest. 

## <a name="next-steps"></a>További lépések
- [Lekérdezés és válasz](local-search-query-response.md)
- [Helyi üzleti keresés – rövid útmutató](quickstarts/local-quickstart.md)
- [Local Business Search API-referencia](local-search-reference.md)
- [Követelmények használata és megjelenítése](use-display-requirements.md)
