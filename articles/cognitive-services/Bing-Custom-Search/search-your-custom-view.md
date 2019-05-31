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
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 9c0c4fc21f6f4b23b14c8020ecd7e95c63dedcbf
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388553"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>A Bing Custom Search példány meghívása a portálról

Miután konfigurálta az egyéni keresési funkciót, tesztelheti, a Bing Custom Search belül [portál](https://customsearch.ai). 

![a Bing egyéni keresés portál képernyőképe](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Hozzon létre egy keresési lekérdezést 

Miután bejelentkezett, a Bing Custom Search [portál](https://customsearch.ai), válassza ki a keresési példányát, és kattintson a **éles** lapon. A **végpontok**, válasszon ki egy API-végpontot (például a webes API). Az előfizetés határozza meg, milyen végpontokat jelennek meg.

Hozzon létre egy keresési lekérdezést, adja meg a paraméterértékeket a végponthoz. Vegye figyelembe, hogy a paraméterek megjelennek a portálon, válassza ki a végpont függően változhat. Tekintse meg a [Custom Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) további információt. Az előfizetést, a keresési példányát használja, adja hozzá a megfelelő előfizetést kulcsot, és frissítse a megfelelő piaci és/vagy nyelvi paramétereket.

Néhány fontos paraméter alatt van:


|Paraméter  |Leírás  |
|---------|---------|
|Lekérdezés     | Keresse meg a keresett kifejezés. Webes, kép, videó és automatikus kiegészítési végpontokhoz csak érhető el. |
|Egyéni konfiguráció azonosítója | A kiválasztott egyéni keresési példány konfiguráció azonosítója. Ez a mező csak olvasható. |
|Market     | A piacon, amely be fogja származik. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok érhető el.        |
|Előfizetői azonosító | Az előfizetési kulcsot, a teszteléséhez. A legördülő listából válasszon ki egy kulcsot, vagy manuálisan adjon meg egy másikat.          |

Kattintson a **további paraméterek** tárja fel a következő paraméterekkel:  

|Paraméter  |Leírás  |
|---------|---------|
|Safe Search     | Felnőtt tartalom vonatkozó szűrésére használt szűrő. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok érhető el.        |
|A felhasználói felület nyelve    | A felhasználói felület karakterláncok használt nyelv. Például, ha engedélyezi a képek és videók tárolt felhasználói felületén a **kép** és **videó** lapokon megadott nyelv használata.        |
|Count     | A válaszban visszaadandó találatok száma. Csak a webes, kép- és végpontok esetén érhető el.         |
|Offset    | A kihagyandó eredmények visszaküldése előtt a keresési eredmények számát. Csak a webes, kép- és végpontok esetén érhető el.        |
    
Az összes szükséges beállítások megadása után kattintson a **hívás** a JSON-válasz megtekintéséhez a jobb oldali ablaktáblán. Ha a felhasználói felület üzemeltetett végpontot, az alsó ablaktáblán tesztelheti a keresési funkciót.

## <a name="change-your-bing-custom-search-subscription"></a>A Bing egyéni Keresés előfizetés módosítása

Az előfizetés nélkül hozzon létre egy új példányt a Bing Custom Search-példányhoz társított módosíthatja. Ahhoz, hogy az API-hívások küldött és a egy új előfizetésen felüli, a Bing Custom Search új erőforrás létrehozása az Azure Portalon. Az új előfizetési kulcsot használja az API-kérelmek és a példány egyéni konfiguráció azonosítója.

## <a name="next-steps"></a>További lépések

- [Hívja meg az egyéni nézet a C# használatával](./call-endpoint-csharp.md)
- [Az egyéni nézet Java-hívás](./call-endpoint-java.md)
- [Hívja meg az egyéni nézet és NodeJs](./call-endpoint-nodejs.md)
- [Hívja meg az egyéni nézet a Pythonnal](./call-endpoint-python.md)

- [Hívja meg az egyéni nézet a C# SDK-val](./sdk-csharp-quick-start.md)
