---
title: 'Adja meg manuálisan az adatbevitelt: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az adatok manuális megadása modult Azure Machine Learning egy kisebb adatkészlet létrehozásához az értékek beírásával. Az adatkészlet több oszloppal is rendelkezhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 736b128e5207e58220afa69b4cf5dc4e8fea4a26
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79140807"
---
# <a name="enter-data-manually-module"></a>Manuálisan adja meg az adatmodult

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy kis adatkészletet az értékek beírásával. Az adatkészlet több oszloppal is rendelkezhet.
  
Ez a modul a következő helyzetekben lehet hasznos:  
  
- Értékek kis halmazának létrehozása teszteléshez  
  
- Címkék rövid listájának létrehozása
  
- Az adatkészletbe beszúrandó oszlopnevek listájának beírása

## <a name="enter-data-manually"></a>Manuális adatbevitel 
  
1.  Adja hozzá az [adatbevitel manuálisan](./enter-data-manually.md) modult a folyamathoz. Ezt a modult a Azure Machine Learning **adatbevitel és kimenet** kategóriájában találja. 
  
2.  A **DataFormat**területen válassza az alábbi lehetőségek egyikét. Ezek a beállítások határozzák meg, hogy az Ön által megadott információ hogyan legyen elemezve. Az egyes formátumokra vonatkozó követelmények nagy mértékben különböznek, ezért mindenképpen olvassa el a kapcsolódó témaköröket.  
  
    -   **ARFF**: a WEKA által használt attribútum-rokon fájlformátum.   
  
    -   **CSV**: vesszővel tagolt értékek formátuma. További információ: [Konvertálás CSV](./convert-to-csv.md)-re.  
  
    -   **SVMLight**: a Vowpal Wabbit és más gépi tanulási keretrendszerek által használt formátum.  
  
    -   **TSV**: tabulátorral tagolt értékek formátuma.

     Ha olyan formátumot választ, amely nem felel meg a formátum specifikációjának, futásidejű hiba történik.
  
3.  Az adatbevitel megkezdéséhez **kattintson az** adatszövegmezőbe. A következő formátumok különleges figyelmet igényelnek:  
  
    - **CSV**: több oszlop létrehozásához illessze be a vesszővel tagolt szövegbe, vagy írjon be több oszlopot a mezők közötti vesszők használatával.
  
        Ha a **HasHeader** lehetőséget választja, az értékek első sorát használhatja oszlop fejlécként.  
  
        Ha kijelöli ezt a beállítást, a rendszer az oszlopok nevét, a Col1, a Col2 és így tovább lehetőséget használja. Az oszlopok nevét később is hozzáadhatja vagy módosíthatja a [metaadatok szerkesztése](./edit-metadata.md)paranccsal.  
  
    - **TSV**: több oszlop létrehozásához illessze be a tabulátorral tagolt szöveget, vagy írjon be több oszlopot a mezők közötti fülek használatával.  
  
        Ha a **HasHeader** lehetőséget választja, az értékek első sorát használhatja oszlop fejlécként.  
  
        Ha kijelöli ezt a beállítást, a rendszer az oszlopok nevét, a Col1, a Col2 és így tovább lehetőséget használja. Az oszlopok nevét később is hozzáadhatja vagy módosíthatja a [metaadatok szerkesztése](./edit-metadata.md)paranccsal.  
  
    -   **ARFF**: beillesztés meglévő ARFF-formátumú fájlba. Ha közvetlenül ír be értékeket, ügyeljen arra, hogy az adatok elején adja hozzá az opcionális fejlécet és a kötelező attribútum mezőket. 
    
        A következő fejléc-és attribútum-sorok például hozzáadhatók egy egyszerű listához. Az oszlop fejléce `SampleText`. Vegye figyelembe, hogy a karakterlánc típusa nem támogatott.
    
        ```text
        % Title: SampleText.ARFF
        % Source: Enter Data module
        @ATTRIBUTE SampleText NUMERIC
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
     
     Ha többször is megnyomja az ENTER billentyűt több üres záró sor hozzáadásához, az üres sorok el lesznek távolítva.  
  
     Ha hiányzó értékekkel rendelkező sorokat hoz létre, akkor a későbbiekben bármikor szűrheti őket.  
  
5.  Kapcsolja a kimeneti portot más modulokhoz, és futtassa a folyamatot.  
  
     Az adatkészlet megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés**lehetőséget.  
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 