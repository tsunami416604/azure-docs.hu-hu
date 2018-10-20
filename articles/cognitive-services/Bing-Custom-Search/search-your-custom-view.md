---
title: Keressen egy egyéni nézet – Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Keresés a webes egyéni nézetét ismerteti.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 15c5b3c58c4f3617111707ed82d031b67b6ad4c1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465135"
---
# <a name="call-your-custom-search"></a>Hívja meg az egyéni keresés

Keresési eredmények eléréséhez a példány a Custom Search API az első hívás előtt kell a Cognitive Services előfizetési kulcs beszerzése. Egyéni keresési API-kulcs lekéréséhez lásd: [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Próbálja ki!

Miután konfigurálta az egyéni keresési funkciót, a konfigurációt, az egyéni keresési portálon belül tesztelheti. 

1. Jelentkezzen be a [egyéni keresési](https://customsearch.ai).
2. Kattintson egy egyéni keresési példány példányok a listából.
3. Kattintson a **éles** fülre. 
4. Alatt a **végpontok** lapra, válassza ki a végpont (például a webes API). Az előfizetés határozza meg, milyen végpontokat jelennek meg (lásd: [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) az előfizetési lehetőségek). 
5. Adja meg a paraméterértékeket. 

    Az alábbiakban a lehetséges paramétereket is megadhatja a (a tényleges lista függ a kijelölt végpont). További információ ezekről a paraméterekről: [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) hivatkozást.

    - **Lekérdezés**: A keresési kifejezést. Csak a webes, kép, videó és automatikus kiegészítési végpontokhoz érhető el.
    - **Egyéni konfiguráció azonosítója**: a kiválasztott egyéni keresési példány konfiguráció azonosítója. Ez a mező csak olvasható.
    - **Piaci**: A piacon, honnan származnak az eredményeket. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok érhető el.
    - **Az előfizetői**: az előfizetési kulcsot, a teszteléséhez. Előfordulhat, hogy a legördülő listából válasszon ki egy kulcsot, vagy manuálisan adjon meg egy másikat.  
      
    Kattintson a **további paraméterek** tárja fel a következő paraméterekkel:  
      
    - **Biztonságos keresés**: használatával szűrhetők a felnőtt tartalom vonatkozó szűrő. Csak a webes, kép, videó és tárolt felhasználói felületén végpontok esetén érhető el.
    - **A felhasználói felület nyelvének**: A felhasználói felület karakterláncok használt nyelv. Például, ha engedélyezi a képek és videók tárolt felhasználói felületén a **kép** és **videó** lapokon megadott nyelv használata.
    - **Száma**: a válaszban visszaadandó találatok száma. Csak a webes, kép- és végpontok esetén érhető el.
    - **Eltolás**: előtt eredményt adnak vissza a keresési eredmények számát. Csak a webes, kép- és végpontok esetén érhető el.

6. Az összes szükséges beállítások megadása után kattintson a **hívás** a JSON-válasz megtekintéséhez a jobb oldali ablaktáblán. 

Ha a felhasználói felület üzemeltetett végpontot, az alsó ablaktáblán tesztelheti a keresési funkciót.

## <a name="next-steps"></a>További lépések

- [Hívja meg az egyéni nézet a C# használatával](./call-endpoint-csharp.md)
- [Az egyéni nézet Java-hívás](./call-endpoint-java.md)
- [Hívja meg az egyéni nézet és NodeJs](./call-endpoint-nodejs.md)
- [Hívja meg az egyéni nézet a Pythonnal](./call-endpoint-python.md)

- [Hívja meg az egyéni nézet a C# SDK-val](./sdk-csharp-quick-start.md)