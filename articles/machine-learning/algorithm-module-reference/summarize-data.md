---
title: Adatok összegzése
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban található összegző adatok modult az adatkészlet oszlopaihoz tartozó alapszintű leíró statisztikai jelentés létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492578"
---
# <a name="summarize-data"></a>Adatok összegzése

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Az összesítő adat modul használatával olyan szabványos statisztikai mértékeket hozhat létre, amelyek leírják a bemeneti tábla minden oszlopát.

Az összegző statisztika akkor hasznos, ha meg szeretné ismerni a teljes adatkészlet jellemzőit. Előfordulhat például, hogy tudnia kell a következőket:

- Hány hiányzó érték van az egyes oszlopokban?
- Hány egyedi érték van a funkció oszlopban?
- Mi az egyes oszlopok átlagos és szórása?

A modul kiszámítja az egyes oszlopok fontos pontszámait, és a bemenetként megadott összes változó (adatoszlop) összesítő statisztikájának sorát adja vissza.

## <a name="how-to-configure-summarize-data"></a>Az adatösszesítés konfigurálása  

1. Adja hozzá az **összesítő** adatmodult a folyamathoz. Ez a modul a Designer **statisztikai függvények** kategóriájában található meg.

1. Kapcsolja be azt az adatkészletet, amelyhez jelentést szeretne készíteni.

    Ha csak néhány oszlopra szeretne jelentést készíteni, használja az oszlopok [kiválasztása az adatkészlet](select-columns-in-dataset.md) modulban lehetőséget, hogy az oszlopok egy részhalmazát feldolgozza.

1. Nincs szükség további paraméterekre. Alapértelmezés szerint a modul a bemenetként megadott összes oszlopot elemzi, az oszlopok értékeinek típusától függően pedig az eredmények szakaszban leírtak szerint a megfelelő statisztikai adatokat [jeleníti](#results) meg.

1. Futtassa a folyamatot, vagy kattintson a jobb gombbal a modulra, és válassza a **kijelölt futtatása**lehetőséget.

## <a name="results"></a>Results (Eredmények)

A modul jelentései a következő statisztikákat is tartalmazhatják. 

|Oszlop neve|Leírás|
|------|------|  
|**Funkció**|Az oszlop neve|
|**Száma**|Az összes sor száma|
|**Egyedi értékek száma**|Egyedi értékek száma az oszlopban|
|**Hiányzó értékek száma**|Egyedi értékek száma az oszlopban|
|**Min**|Legalacsonyabb érték az oszlopban|  
|**Max**|Legmagasabb érték az oszlopban|
|**Értem**|Az összes oszlop értékének középértéke|
|**Középérték szórása**|Oszlop értékeinek szórása|
|**1. kvartilis**|Érték az első kvartilis|
|**Medián**|Medián oszlop értéke|
|**harmadik kvartilis**|Érték a harmadik kvartilis|
|**Mód**|Oszlop értékeinek módja|
|**Tartomány**|A maximális és a minimális értékek közötti értékek számát jelző egész szám|
|**Minta eltérése**|Az oszlop eltérése; Lásd: Megjegyzés|
|**Minta szórása**|Oszlop szórása; Lásd: Megjegyzés|
|**Minta torzítása**|Az oszlop torzítása; Lásd: Megjegyzés|
|**Minta csúcsosságát**|Csúcsosságát az oszlophoz; Lásd: Megjegyzés|
|**P 0.5**|0,5% percentilis|
|**P1**|1% percentilis|
|**P5**|5%-os percentilis|
|**P95**|95% percentilis|
|**P 99,5**|99,5% percentilis |

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) .  
