---
title: 'Adja meg manuálisan az adatbevitelt: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja az adatok manuális megadása modult a Azure Machine Learning szolgáltatásban egy kisebb adatkészlet létrehozásához az értékek beírásával. Az adatkészlet több oszloppal is rendelkezhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: bd5fb636dbf79534e84a21a461f8912ceb6d9633
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693164"
---
# <a name="enter-data-manually-module"></a>Manuálisan adja meg az adatmodult

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy kis adatkészletet az értékek beírásával. Az adatkészlet több oszloppal is rendelkezhet.
  
Ez a modul a következő helyzetekben lehet hasznos:  
  
- Értékek kis halmazának létrehozása teszteléshez  
  
- Címkék rövid listájának létrehozása
  
- Az adatkészletbe beszúrandó oszlopnevek listájának beírása

## <a name="enter-data-manually"></a>Manuális adatbevitel 
  
1.  Adja hozzá az [adatbevitel manuálisan](./enter-data-manually.md) modult a folyamathoz. Ezt a modult a Azure Machine Learning **adatbevitel és kimenet** kategóriájában találja. 
  
2.  A **DataFormat**területen válassza az alábbi lehetőségek egyikét. Ezek a beállítások határozzák meg, hogy az Ön által megadott információ hogyan legyen elemezve. Az egyes formátumokra vonatkozó követelmények nagy mértékben különböznek, ezért mindenképpen olvassa el a kapcsolódó témaköröket.  
  
    -   **ARFF**. A WEKA által használt attribútum-rokon fájlformátum.   
  
    -   **CSV**. Vesszővel tagolt értékek formátuma További információ: [Konvertálás CSV](./convert-to-csv.md)-re.  
  
    -   **SVMLight**. A Vowpal Wabbit és más gépi tanulási keretrendszerek által használt formátum.  
  
    -   **TSV**. Tabulátorral tagolt értékek formátuma

     Ha olyan formátumot választ, amely nem felel meg a formátum specifikációjának, futásidejű hiba történik.
  
3.  Az adatbevitel megkezdéséhez **kattintson az** adatszövegmezőbe. A következő formátumok különleges figyelmet igényelnek:  
  
    - **CSV**: több oszlop létrehozásához illessze be a vesszővel tagolt szövegbe, vagy írjon be több oszlopot a mezők közötti vesszők használatával.
  
        Ha a **HasHeader** lehetőséget választja, az értékek első sorát használhatja oszlop fejlécként.  
  
        Ha kijelöli ezt a beállítást, a rendszer az oszlopok nevét, a Col1, a Col2 és így tovább lehetőséget használja. Az oszlopok nevét később is hozzáadhatja vagy módosíthatja a [metaadatok szerkesztése](./edit-metadata.md)paranccsal.  
  
    - **TSV**: több oszlop létrehozásához illessze be a tabulátorral tagolt szöveget, vagy írjon be több oszlopot a mezők közötti fülek használatával.  
  
        Ha a **HasHeader** lehetőséget választja, az értékek első sorát használhatja oszlop fejlécként.  
  
        Ha kijelöli ezt a beállítást, a rendszer az oszlopok nevét, a Col1, a Col2 és így tovább lehetőséget használja. Az oszlopok nevét később is hozzáadhatja vagy módosíthatja a [metaadatok szerkesztése](./edit-metadata.md)paranccsal.  
  
    -   **ARFF**: beillesztés meglévő ARFF-formátumú fájlba. Ha közvetlenül ír be értékeket, ügyeljen arra, hogy az adatok elején adja hozzá az opcionális fejlécet és a kötelező attribútum mezőket. 
    
        A következő fejléc-és attribútum-sorok például hozzáadhatók egy egyszerű listához. Az oszlop fejléce `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: írja be vagy illessze be az értékeket a SVMLight formátum használatával.  
  
        Az alábbi minta például a véradási adatkészlet első pár sorát jelöli a SVMight formátumban:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Az [adatok manuális megadása](./enter-data-manually.md) modul futtatásakor a rendszer ezeket a sorokat az oszlopok és az index értékeinek adatkészletére konvertálja az alábbiak szerint:  
  
        |Col1|Col2|Col3|Col4|Címkék|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Az új sor elindításához nyomja le az ENTER billentyűt az egyes sorok után.  
  
     **Ügyeljen rá, hogy az utolsó sor után nyomja le az ENTER billentyűt.** 
     
     Ha többször is megnyomja az ENTER billentyűt több üres záró sor hozzáadásához, a rendszer eltávolítja az utolsó üres sort, de a többi üres sort hiányzó értékként kezeli a rendszer.  
  
     Ha hiányzó értékekkel rendelkező sorokat hoz létre, akkor a későbbiekben bármikor szűrheti őket.  
  
5.  Kattintson a jobb gombbal a modulra, és válassza a **kijelölt futtatása** lehetőséget, hogy elemezze az adatokat, és betöltse azt a munkaterületre adatkészletként.  
  
     Az adatkészlet megtekintéséhez kattintson a kimeneti portra, majd válassza a **Megjelenítés**lehetőséget.  
## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 