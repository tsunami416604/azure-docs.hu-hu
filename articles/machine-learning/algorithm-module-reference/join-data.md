---
title: 'Csatlakozás az adatkezeléshez: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan egyesítheti az adatkészleteket az Azure Machine Learning csatlakoztatási csatlakozás adatmoduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 11/19/2019
ms.openlocfilehash: b07bde671be73af2a351353d9794907972a022e7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232621"
---
# <a name="join-data"></a>Adatok összekapcsolása

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designer (előzetes verzió) **Csatlakozás** adatmodulja két adatkészlet egyesítéséhez egy adatbázis-stílusú csatlakozási művelettel.  

## <a name="how-to-configure-join-data"></a>Csatlakozási adatbevitel konfigurálása

Két adatkészlethez való csatlakozás végrehajtásához egy kulcs oszlopnak kell kapcsolódnia. A több oszlopot használó összetett kulcsok is támogatottak. 

1. Adja hozzá az egyesíteni kívánt adatkészleteket, majd húzza az **illesztési adat** modult a folyamatba. 

    A modult az **Adatátalakítási** kategóriában, a **manipuláció**szakaszban találja.

1. Csatlakoztassa az adatkészleteket az **adategyesítési** modulhoz. 
 
1. Válassza az **oszlop indítása lehetőséget** , hogy kiválassza a kulcs oszlop (oka) t. Ne felejtse el kiválasztani a bal és a jobb oldali bemenetek oszlopait.

    Egyetlen kulcs esetén:

    Válassza ki mindkét bemenet egyetlen kulcs oszlopát.
    
    Összetett kulcs esetén:

    Válassza ki az összes kulcsfontosságú oszlopot a bal oldali és a jobb oldali bemenetből ugyanabba a sorrendbe. Az **illesztési** adatmodul akkor csatlakozik a táblákhoz, amikor az összes kulcs oszlop egyezik. Ha az oszlop sorrendje nem egyezik meg az eredeti táblával, ellenőrizze, hogy az **Ismétlődések engedélyezése és az oszlopok megőrzése beállítás engedélyezve van-e** . 

    ![oszlop – választó](media/module/join-data-column-selector.png)


1. Jelölje be az **egyeztetési eset** beállítást, ha meg szeretné őrizni a kis-és nagybetűk megkülönböztetését egy szöveges oszlophoz való csatlakozáskor. 
   
1. Az **összekapcsolás típusa** legördülő listával adhatja meg, hogy az adatkészletek hogyan legyenek egyesítve.  
  
    * **Belső illesztés**: a leggyakoribb csatlakozási művelet a *belső* illesztés. Az egyesített sorokat csak akkor adja vissza, ha a kulcs oszlopainak értékei egyeznek.  
  
    * **Bal külső illesztés**: a bal oldali *külső illesztés* a bal oldali tábla összes sorához csatlakoztatott sorokat ad vissza. Ha a bal oldali tábla egyik sora nem rendelkezik egyező sorokkal a jobb oldali táblában, a visszaadott sor a jobb oldali táblából származó összes oszlophoz hiányzó értékeket tartalmaz. A hiányzó értékekhez helyettesítő értéket is megadhat.  
  
    * **Teljes külső illesztés**: a *teljes külső illesztés* a bal oldali táblából (**tábla1**) és a jobb oldali táblából (**table2**) származó összes sort adja vissza.  
  
         A másik tábla minden olyan sorához, amely nem rendelkezik egyező sorokkal, az eredmény tartalmaz egy sort, amely a hiányzó értékeket tartalmazza.  
  
    * **Bal oldali félig illesztés**: a *bal oldali fél* csak a bal oldali tábla értékeit adja vissza, ha a kulcs oszlopainak értéke megegyezik.  

1. A **jobb oldali kulcsok oszlopai az összekapcsolt táblában**:

    * Ezzel a beállítással mindkét bemeneti táblából megtekintheti a kulcsokat.
    * Törölje a jelölést, hogy csak a bal oldali bemenetből származó kulcs oszlopokat adja vissza.

1. Futtassa a folyamatot, vagy válassza ki az összekapcsolási modult, és a kiválasztott **Futtatás** elemre kattintva végezze el a csatlakozást.

1. Az eredmények megtekintéséhez kattintson a jobb gombbal az **illesztési adatokat** > **results adatkészlet** > **Megjelenítés**elemre.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 