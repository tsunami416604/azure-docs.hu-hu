---
title: Egyéni nézet keresése – Bing egyéni keresés
titleSuffix: Azure Cognitive Services
description: Miután konfigurálta az egyéni keresési élményt, tesztelheti azt a Bing egyéni keresési portálon belül.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983112"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>A Bing egyéni keresési példányának hívása a portálról

Miután konfigurálta az egyéni keresési élményt, tesztelheti azt a Bing egyéni keresési [portálon](https://customsearch.ai)belül. 

![képernyőkép a Bing egyéni keresőportáljáról](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Keresési lekérdezés létrehozása 

Miután bejelentkezett a Bing egyéni keresési [portálra,](https://customsearch.ai)jelölje ki a keresési példányt, és kattintson az **Éles környezet** fülre. A **Végpontok**csoportban jelöljön ki egy API-végpontot (például webes API-t). Az előfizetés határozza meg, hogy mely végpontok jelennek meg.

Keresési lekérdezés létrehozásához adja meg a végpont paraméterértékeit. Vegye figyelembe, hogy a portálon megjelenő paraméterek a választott végponttól függően változhatnak. További információt az [egyéni keresési API-hivatkozásban](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) talál. A keresési példány által használt előfizetés módosításához adja hozzá a megfelelő előfizetési kulcsot, és frissítse a megfelelő piaci és/vagy nyelvi paramétereket.

Néhány fontos paraméter az alábbiakban:


|Paraméter  |Leírás  |
|---------|---------|
|Lekérdezés     | A keresett kifejezés. Csak webes, kép-, video- és automatikus javaslati végpontokhoz érhető el |
|Egyéni konfigurációazonosító | A kijelölt egyéni keresési példány konfigurációazonosítója. Ez a mező csak olvasható. |
|Piac     | Az eredmények ből származó piac. Csak a webes, képi, videó- és hosztolt felhasználói felület ivégpontok esetén érhető el.        |
|Előfizetői azonosító | A teszteléshez szükséges előfizetési kulcs. Kiválaszthat egy kulcsot a legördülő listából, vagy manuálisan is beírhat egyet.          |

A **További paraméterek gombra** kattintva a következő paraméterek jelennek meg:  

|Paraméter  |Leírás  |
|---------|---------|
|Biztonságos keresés     | A weblapok felnőtt tartalomra való szűrésére szolgáló szűrő. Csak a webes, képi, videó- és hosztolt felhasználói felület ivégpontok esetén érhető el. Ne feledje, hogy a Bing `moderate` egyéni `strict`videokeresés csak két értéket támogat: és a.        |
|Felhasználói felület nyelve    | A felhasználói felület karakterláncaihoz használt nyelv. Ha például engedélyezi a képeket és a videókat a Hosztolt felhasználói felületen, a **Kép** és **videó** fülek a megadott nyelvet használják.        |
|Darabszám     | A válaszban visszaadandó keresési eredmények száma. Csak webes, kép- és videovégpontokhoz érhető el.         |
|Eltolás    | A visszaadandó keresési eredmények száma az eredmények visszaadása előtt. Csak webes, kép- és videovégpontokhoz érhető el.        |
    
Miután megadta az összes szükséges beállítást, kattintson a **Hívás** gombra a JSON-válasz megtekintéséhez a jobb oldali ablaktáblában. Ha a Hosztolt felhasználói felület végpontját választja, az alsó ablaktáblában tesztelheti a keresési élményt.

## <a name="change-your-bing-custom-search-subscription"></a>A Bing egyéni keresési előfizetésének módosítása

A Bing egyéni keresési példányához társított előfizetést új példány létrehozása nélkül módosíthatja. Ha egy új előfizetésre szeretné elküldeni és felfizetni az API-hívásokat, hozzon létre egy új Bing egyéni keresési erőforrást az Azure Portalon. Használja az új előfizetési kulcsot az API-kérelmekben, valamint a példány egyéni konfigurációs azonosítóját.

## <a name="next-steps"></a>További lépések

- [Az egyéni nézet hívása C-vel #](./call-endpoint-csharp.md)
- [Az egyéni nézet hívása Java-val](./call-endpoint-java.md)
- [Az egyéni nézet hívása nodej-kkel](./call-endpoint-nodejs.md)
- [Az egyéni nézet hívása pythonnal](./call-endpoint-python.md)

- [Az egyéni nézet hívása a C# SDK-val](./sdk-csharp-quick-start.md)
