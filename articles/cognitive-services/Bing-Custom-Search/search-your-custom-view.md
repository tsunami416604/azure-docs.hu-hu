---
title: Keressen egy egyéni nézet – Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Keresés a webes egyéni nézetét ismerteti.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: f00a8a02ac7f9468516506b6e6a376209d24c270
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448884"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>A Bing Custom Search példány meghívása a portálról

Miután konfigurálta az egyéni keresési funkciót, tesztelheti, a Bing Custom Search belül [portál](https://customsearch.ai). 

![a Bing egyéni keresés portál képernyőképe](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Hozzon létre egy keresési lekérdezést 

Miután bejelentkezett, a Bing Custom Search [portál](https://customsearch.ai), válassza ki a keresési példányát, és kattintson a **éles** lapon. A **végpontok**, válasszon ki egy API-végpontot (például a webes API). Az előfizetés határozza meg, milyen végpontokat jelennek meg.

Hozzon létre egy keresési lekérdezést, adja meg a paraméterértékeket a végponthoz. Vegye figyelembe, hogy a paraméterek megjelennek a portálon, válassza ki a végpont függően változhat. Tekintse meg a[Custom Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) további információt. 

Néhány fontos paraméter alatt van:


|Paraméter  |Leírás  |
|---------|---------|
|Lekérdezés     | Keresse meg a keresett kifejezés. Webes, kép, videó és automatikus kiegészítési végpontokhoz csak érhető el. |
|Egyéni konfiguráció azonosítója | A kiválasztott egyéni keresési példány konfiguráció azonosítója. Ez a mező csak olvasható. |
|Piac     | A piacon, amely be fogja származik. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok érhető el.        |
|Előfizetői azonosító | Az előfizetési kulcsot, a teszteléséhez. A legördülő listából válasszon ki egy kulcsot, vagy manuálisan adjon meg egy másikat.          |

Kattintson a **további paraméterek** tárja fel a következő paraméterekkel:  

|Paraméter  |Leírás  |
|---------|---------|
|Biztonságos keresés     | Felnőtt tartalom vonatkozó szűrésére használt szűrő. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok érhető el.        |
|A felhasználói felület nyelve    | A felhasználói felület karakterláncok használt nyelv. Például, ha engedélyezi a képek és videók tárolt felhasználói felületén a **kép** és **videó** lapokon megadott nyelv használata.        |
|Darabszám     | A válaszban visszaadandó találatok száma. Csak a webes, kép- és végpontok esetén érhető el.         |
|Eltolás    | A kihagyandó eredmények visszaküldése előtt a keresési eredmények számát. Csak a webes, kép- és végpontok esetén érhető el.        |
    
Az összes szükséges beállítások megadása után kattintson a **hívás** a JSON-válasz megtekintéséhez a jobb oldali ablaktáblán. Ha a felhasználói felület üzemeltetett végpontot, az alsó ablaktáblán tesztelheti a keresési funkciót.

## <a name="next-steps"></a>További lépések

- [Hívja meg az egyéni nézet a C# használatával](./call-endpoint-csharp.md)
- [Az egyéni nézet Java-hívás](./call-endpoint-java.md)
- [Hívja meg az egyéni nézet és NodeJs](./call-endpoint-nodejs.md)
- [Hívja meg az egyéni nézet a Pythonnal](./call-endpoint-python.md)

- [Hívja meg az egyéni nézet a C# SDK-val](./sdk-csharp-quick-start.md)
