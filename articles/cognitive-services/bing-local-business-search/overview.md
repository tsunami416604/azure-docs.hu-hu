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
ms.openlocfilehash: 0006761126eb1d561da7eeff97e8a9928d62ddb0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478939"
---
# <a name="what-is-bing-local-business-search"></a>Mi az a Bing helyi üzleti keresés?
A Bing Helyi üzleti keresési API egy RESTful szolgáltatás, amely lehetővé teszi az alkalmazások számára, hogy keresési lekérdezések alapján információkat találjanak a helyi vállalkozásokról. Például, `q=<business-name> in Redmond, Washington`vagy `q=Italian restaurants near me`. 

## <a name="features"></a>Szolgáltatások
| Szolgáltatás | Leírás |  
| -- | -- | 
| [Helyi vállalkozások és helyszínek keresése](quickstarts/local-quickstart.md) | A Bing helyi vállalati keresési API honosított találatokat kap egy lekérdezésből. Az eredmények között szerepel a vállalkozás webhelyének URL-címe, valamint a megjelenített szöveg, telefonszám és földrajzi hely, beleértve a GPS-koordinátákat, a várost, az utcacímet. |  
| [Helyi eredmények szűrése földrajzi határokkal](specify-geographic-search.md) | Adja hozzá a koordinátákat keresési paraméterekként, hogy a találatokat egy adott földrajzi területre korlátozza, amelyet kör vagy négyzethatároló keret határoz meg. | 
| [Helyi üzleti eredmények szűrése kategória szerint](local-categories.md) | Helyi üzleti eredmények keresése kategória szerint. Ez a beállítás fordított IP-hely vagy a hívó GPS-koordinátáit használja a helyi eredmények különböző üzleti kategóriákban történő visszaküldéséhez.|

## <a name="workflow"></a>Munkafolyamat
Hívja meg a Bing Helyi üzleti keresési API-t bármely olyan programozási nyelvről, amely HTTP-kéréseket tud kezdeményezni, és elemezheti a JSON-válaszokat. Ez a szolgáltatás a REST API használatával érhető el.
 
1. Hozzon létre egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Bing Keresési API-khoz való hozzáféréssel. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot.](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)   
2. Az URL-cím kódolja `q=""` a keresési kifejezéseket a lekérdezési paraméterhez. Például `q=nearby+restaurant` vagy `q=nearby%20restaurant`. Ha szükséges, állítsa be a tördelést is. 
3. Kérelem küldése [a Bing helyi vállalati keresési API-nak](quickstarts/local-quickstart.md) 
4. Elemezd a JSON-választ 

> [!NOTE]
> Jelenleg a Helyi Üzleti Keresés: 
> * Csak a `en-US` piacot támogatja. 
> * Nem támogatja a Bing Autosuggest-t. 

## <a name="next-steps"></a>További lépések
- [Lekérdezés és válasz](local-search-query-response.md)
- [Helyi üzleti keresés rövid útmutató](quickstarts/local-quickstart.md)
- [Local Business Search API-referencia](local-search-reference.md)
- [Követelmények használata és megjelenítése](use-display-requirements.md)
