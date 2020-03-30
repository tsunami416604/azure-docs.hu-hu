---
title: Adatok összegzése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az Adatok összegzése modult az Azure Machine Learningben egy adatkészlet oszlopaihoz alapvető leíró statisztikai jelentés létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477442"
---
# <a name="summarize-data"></a>Adatok összegzése

Ez a cikk ismerteti az Azure Machine Learning designer modulját (előzetes verzió).

Az Adatok összegzése modul segítségével hozzon létre egy sor szabványos statisztikai mértékeket, amelyek leírják a bemeneti tábla egyes oszlopait.

Az összegző statisztikák akkor hasznosak, ha meg szeretné érteni a teljes adatkészlet jellemzőit. Előfordulhat például, hogy tudnia kell:

- Hány hiányzó érték van az egyes oszlopokban?
- Hány egyedi érték van egy jellemzőoszlopban?
- Mi az egyes oszlopok átlaga és szórása?

A modul kiszámítja az egyes oszlopok fontos pontszámait, és minden bemenetként megadott változóhoz (adatoszlophoz) egy sor összefoglaló statisztikát ad vissza.

## <a name="how-to-configure-summarize-data"></a>Az adatok összegzése beállítás  

1. Adja hozzá az **Adatok összegzése** modult a folyamathoz. Ezt a modult a **tervező Statisztikai függvények** kategóriájában találja.

1. Kapcsolja össze azt az adatkészletet, amelyhez jelentést szeretne létrehozni.

    Ha csak néhány oszlopról szeretne jelentést tenni, az [Adatkészlet oszlopok kijelölése](select-columns-in-dataset.md) modullal vetítheti ki az oszlopok egy részhalmazát.

1. Nincs szükség további paraméterekre. Alapértelmezés szerint a modul elemzi a bemenetként megadott összes oszlopot, és az oszlopokban lévő értékek típusától függően az [Eredmények](#results) szakaszban leírtak szerint egy megfelelő statisztikai készletet ad ki.

1. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A modulból származó jelentés a következő statisztikákat tartalmazhatja. 

|Oszlop neve|Leírás|
|------|------|  
|**Szolgáltatás**|Az oszlop neve|
|**Darabszám**|Az összes sor száma|
|**Egyedi értékek száma**|Egyedi értékek száma az oszlopban|
|**Hiányzó értékek száma**|Egyedi értékek száma az oszlopban|
|**Min**|Az oszlop legalacsonyabb értéke|  
|**Max**|Az oszlop legmagasabb értéke|
|**Jelent**|Az összes oszlopérték átlaga|
|**Átlagos eltérés**|Az oszlopértékek átlagos eltérése|
|**1. Kvartilis**|Érték az első kvartilisnél|
|**Medián**|Medián oszlopérték|
|**3. Kvartilis**|Érték a harmadik kvartilisnél|
|**Mód**|Az oszlopértékek módja|
|**Tartomány**|Egész szám, amely a maximális és a legkisebb értékek közötti értékek számát jelöli|
|**Mintavariancia**|Oszlop varianciája; lásd: Megjegyzés|
|**Minta szórása**|Az oszlop szórása; lásd: Megjegyzés|
|**Minta ferdesége**|Az oszlop ferdesége; lásd: Megjegyzés|
|**Minta Kurtosis**|Kurtózis oszlophoz; lásd: Megjegyzés|
|**P0.5**|0,5% percentilis|
|**P1**|1% percentilis|
|**P5**|5% percentilis|
|**95. p.**|95% percentilis|
|**99,5 p.5**|99,5% percentilis |

## <a name="technical-notes"></a>Technikai megjegyzések

- Nem numerikus oszlopok esetén csak a Darabszám, az Egyedi értékek száma és a Hiányzó értékek száma értékeit számítja ki a program. Más statisztikák esetén null értéket ad vissza a függvény.

- A logikai értékeket tartalmazó oszlopok feldolgozása a következő szabályok szerint:

    - A Min számításakor logikai ÉS alkalmazásra kerül.
    
    - A Max érték számításakor logikai VAGY
    
    - A Tartomány számításakor a modul először azt ellenőrzi, hogy az oszlopban szereplő egyedi értékek száma 2-nek felel-e meg.
    
    - Bármely lebegőpontos számítást igénylő statisztika számításakor a True értékeket a függvény 1,0-ként, a Hamis értékeket pedig 0,0-ként kezeli.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md)  
