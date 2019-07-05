---
title: 'Csatlakozás az adatokat: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan és a csatlakozás az Azure Machine Learning szolgáltatáshoz való csatlakozás adatmodul adatkészletek egyesíteni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518013"
---
# <a name="join-data"></a>Adatok összekapcsolása

Ez a cikk ismerteti, hogyan használható a **adatait** modul az Azure Machine Learning szolgáltatás vizuális felületen egyesíteni a két adatkészletet használ egy adatbázis-stílusú join művelet.  

## <a name="how-to-configure-join-data"></a>Csatlakozás adatok konfigurálása

Illesztés végrehajtásához két adatkészletet azok kell kapcsolatos legfontosabb oszlop szerint. Több oszlop használatával összetett kulcsok is támogatottak. 

1. Az adatkészletek szeretné egyesíteni, és húzza adja hozzá a **csatlakozzon adatok** modult is futtathatja a kísérletet. 

    A modul talál a **adatátalakítás** kategória alatt **adatkezelési**.

1. Csatlakozás az adatkészletek a **csatlakozzon adatok** modul. 
 
1. Válassza ki **Oszlopválasztás indítása** kulcs oszlopok kiválasztásához. Fontos, hogy a bal és jobb bemenet oszlopok kiválasztása.

    Egyetlen kulcs:

    Válassza ki mindkét bemenet egyetlen kulcsoszlop.
    
    Egy összetett kulcs:

    Válassza ki az összes kulcsoszlopot a bal oldali bemeneti és a jobb oldali bemeneti ugyanabban a sorrendben. A **adatait** modul csatlakozni fog a táblákat, ha az összes kulcsoszlopot felel meg. A beállítás **engedélyezi az ismétlődő elemeket, és őrizze meg a kijelölt oszlopok sorrendjét** , ha az oszlopok sorrendje nem ugyanaz, mint az eredeti tábla. 

    ![oszlop-választó](media/module/join-data-column-selector.png)


1. Válassza ki a **és nagybetűk megkülönböztetése** beállítást, ha meg szeretné őrizni a szöveges oszlop csatlakozzon a nagybetűk. 
   
1. Használja a **csatlakozás típusa** adja meg, hogyan össze kell vonni az adatkészletek a legördülő listából.  
  
    * **Belső illesztés**: Egy *belső illesztés* a leggyakoribb join művelet. Csak akkor, ha a Kulcsoszlopok az értékek megegyeznek a kombinált sorokat ad vissza.  
  
    * **Bal külső illesztéssel**: A *bal külső illesztéssel* értéket ad vissza a bal oldali tábla összes sorát a sorok csatlakoztatott. Ha a bal oldali táblában egy sor egyező sorok a jobb oldali táblában van, akkor a visszaadott sor származó a jobb oldali tábla összes oszlopának hiányzó értékeket tartalmaz. Megadhat egy helyettesítő értéket a hiányzó értékek is.  
  
    * **Teljes külső illesztés**: A *teljes külső illesztés* a bal oldali tábla összes sorának visszaadása (**tábla1**) és a jobb oldali tábla (**table2**).  
  
         Minden egyes bármelyik táblában lévő sorokat, amelyek nincsenek egyező sorok a más az eredmény tartalmazza a hiányzó értékeket tartalmazó sor.  
  
    * **Left Join pontosvesszővel**: A *left join félig* adja vissza a bal oldali tábla csak az értékeket a Kulcsoszlopok értékek egyeznek.  

1. A beállítás **jobb oldali kulcs oszlopok megtartása a csatlakoztatott tábla**:

    * Válassza ki ezt a beállítást, tekintse meg a kulcsokat, mind a bemeneti táblából.
    * Törli a jelölést, csak a visszaadandó kulcsoszlopai a bal oldali bemeneti.

1. Futtassa a kísérletet, vagy jelölje ki a kívánt adatait és a kiválasztott **kiválasztott futtatás** , az összekapcsolás végrehajtása.

1. Az eredmények megtekintéséhez kattintson a jobb gombbal a **csatlakozzon adatok** > **eredmények adatkészlet** > **Visualize**.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 