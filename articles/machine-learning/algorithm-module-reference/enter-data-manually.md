---
title: 'Adja meg manuálisan az adatokat: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható az adatokat manuálisan adja meg a modul az Azure Machine Learning szolgáltatás egy kisméretű adatkészlet létrehozásához írja be az értékeket. Az adatkészlet több oszlopot is rendelkezhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028605"
---
# <a name="enter-data-manually-module"></a>Adja meg az adatokat manuálisan modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával hozzon létre egy kisebb adatkészletet írja be az értékeket. Az adatkészlet több oszlopot is rendelkezhet.
  
Ez a modul például a következő esetekben hasznos lehet:  
  
- Egy kis készletét tesztelési értékek generálása  
  
- Egy rövid lista a címkék létrehozása
  
- Írja be egy adatkészlet oszlopnevek listája

## <a name="enter-data-manually"></a>Adja meg manuálisan az adatok 
  
1.  Adja hozzá a [adatokat adja meg manuálisan](./enter-data-manually.md) modult a kísérletvászonra. Ez a modul a annak a **adatok bemeneti és kimeneti** kategória az Azure Machine Learningben. 
  
2.  A **DataFormat**, válassza ki az alábbi lehetőségek egyikét. Ezek a beállítások határozzák meg, hogyan kell értelmezni az Ön által megadott adatokat. Az egyes formátum követelményei nagy mértékben eltérnek egymástól, ezért mindenképpen olvassa el a kapcsolódó témaköröket.  
  
    -   **ARFF**. Az attribútum-relációs fájlformátumra, Weka használják.   
  
    -   **CSV**. Vesszővel tagolt formátumú. További információkért lásd: [átalakítás fürt megosztott Kötetévé](./convert-to-csv.md).  
  
    -   **SVMLight**. A Vowpal Wabbit és más gépi tanulási keretrendszereket által használt formátum.  
  
    -   **TSV**. Formátum tabulátorral tagolt értékek.

     Válasszon formátumot, és nem ad meg, amely megfelel a formátum specifikációk adatokat, ha egy futásidejű hiba történik.
  
3.  Kattintson a **adatok** szövegmezőbe kezdje bevinni az adatokat. A következő formátumok külön figyelmet igényel:  
  
    - **CSV**:  Több oszlop létrehozása, illessze be a vesszővel tagolt szöveg, vagy írja be a több oszlopait, vesszővel válassza el egymástól a mezők között.
  
        Ha a **HasHeader** beállítást, az értékek első sorát használhatja az oszlop fejlécére.  
  
        Ha törli ezt a beállítást, az oszlopok nevei, Col1, Col2, és így tovább, szolgálnak. Hozzáadhat vagy oszlop módosításához később a számítógépnevek [metaadatainak szerkesztése](./edit-metadata.md).  
  
    - **TSV**: Több oszlop létrehozása, illessze be a tabulátorral tagolt szöveg, vagy használja a füleket mezői között több oszlopba írja be.  
  
        Ha a **HasHeader** beállítást, az értékek első sorát használhatja az oszlop fejlécére.  
  
        Ha törli ezt a beállítást, az oszlopok nevei, Col1, Col2, és így tovább, szolgálnak. Hozzáadhat vagy oszlop módosításához később a számítógépnevek [metaadatainak szerkesztése](./edit-metadata.md).  
  
    -   **ARFF**:  Illessze be egy olyan meglévő ARFF fájlt. Ha elkezdi beírni a értékek közvetlenül, mindenképpen adja hozzá a fejléc nem kötelező és kötelező attribútum mezőket az adatok elején. 
    
        Ha például az alábbi fejléc és attribútum sort lehet hozzáadni egy egyszerű listát. Az oszlop fejlécére lenne `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Írja be vagy illessze be az értékeket a SVMLight formátum használatával.  
  
        Az alábbi minta például az első néhány sort az véradás adatkészlet SVMight formátumban jelöli:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Ha futtatja a [adatokat adja meg manuálisan](./enter-data-manually.md) modult, ezek a sorok konvertálja az oszlopok egy adatkészletet és az indexfrissítési értékek használata a következőképpen történik:  
  
        |Col1|Col2|Col3|Col4|Címkék|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Nyomja le az ENTER után minden sor egy új sort.  
  
     **Győződjön meg arról, a végső sor után lenyomja az ENTER billentyűt.** 
     
     Ha lenyomja az ENTER billentyűt többször hozzáadása több üres sor végén, a végső üres sor levágva törlődik, de más üres sor hiányzó értékeket számít.  
  
     A hiányzó sorokat hoz létre, ha mindig szűrheti őket később.  
  
5.  Kattintson a jobb gombbal a modult, és válassza ki **kijelölt futtatása** elemezheti az adatokat, és töltse be a munkaterület adatkészletként.  
  
     Az adatkészlet megtekintéséhez kattintson a kimeneti portra, és válassza ki **Visualize**.  
## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 