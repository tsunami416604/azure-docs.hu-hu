---
title: 'Adatok manuális bevitele: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Adatok bevitele manuálisan modult az Azure Machine Learningben egy kis adatkészlet létrehozásához értékek beírásával. Az adatkészlet több oszlopot is tartalmazhat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367516"
---
# <a name="enter-data-manually-module"></a>Adatok bevitele kézi modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Az **Adatok bevitele manuálisan** modulsegítségével értékek beírásával kis adatkészletet hozhat létre. Az adatkészlet több oszlopot is tartalmazhat.
  
Ez a modul olyan esetekben lehet hasznos, mint például:  
  
- Kis értékhalmaz létrehozása tesztelésre.  
- Címkék rövid listájának létrehozása.  
- Az adatkészletbe beszúrandó oszlopnevek listájának beírása.

## <a name="create-a-dataset"></a>Adatkészlet létrehozása 
  
1. Adja hozzá a [Manuális adatok bevitele](./enter-data-manually.md) modult a folyamathoz. Ez a modul az Azure Machine Learning **adatbeviteli és kimeneti** kategóriájában található. 
  
1. A **DataFormat (DataFormat)** mezőben válasszon az alábbi lehetőségek közül. Ezek a beállítások határozzák meg, hogyan kell elemezni a megadott adatokat. Az egyes formátumok követelményei nagymértékben eltérnek egymástól, ezért mindenképpen olvassa el a kapcsolódó témaköröket.  
  
   - **ARFF**: A Weka által használt attribútum-kapcsolatú fájlformátum.   
   - **CSV**: Vesszővel tagolt értékek formátuma. További információt a [Konvertálás CSV-vé című témakörben talál.](./convert-to-csv.md)    
   - **SVMLight**: A Vowpal Wabbit és más gépi tanulási keretrendszerek által használt formátum.    
   - **TSV**: Tabulátor-elválasztott értékek formátuma.

   Ha olyan formátumot választ, amely nem felel meg a formátumspecifikációknak, futásidejű hiba lép fel.
  
1. Kattintson az **Adatok** szövegmezőbe az adatok bevitelének megkezdéséhez. A következő formátumok különös figyelmet igényelnek:  
  
   - **CSV**: Ha több oszlopot szeretne létrehozni, vesszővel tagolt szövegbe illeszteni, vagy több oszlopot írjon be a mezők közötti vesszővel.
  
     Ha a **HasFejléc** lehetőséget választja, az első értéksort használhatja oszlopfejlécként.  
  
     Ha nem jelöli be ezt a beállítást, a program az oszlopneveket (Col1, Col2 stb.) használja. A [Metaadatok szerkesztése](./edit-metadata.md)segítségével később oszlopok neveit is hozzáadhatja vagy módosíthatja.  
  
   - **TSV**: Több oszlop létrehozásához, tabulátortag szövegbe beillesztése vagy több oszlop beírása a mezők közötti tabulátorok használatával.  
  
     Ha a **HasFejléc** lehetőséget választja, az első értéksort használhatja oszlopfejlécként.  
  
     Ha nem jelöli be ezt a beállítást, a program az oszlopneveket (Col1, Col2 stb.) használja. A [Metaadatok szerkesztése](./edit-metadata.md)segítségével később oszlopok neveit is hozzáadhatja vagy módosíthatja.  
  
   - **ARFF**: Illessze be a meglévő ARFF formátumú fájlt. Ha közvetlenül írja be az értékeket, ügyeljen arra, hogy a választható fejléc- és attribútummezőket az adatok elejére vegye fel. 

     Egy egyszerű listához például a következő fejléc- és attribútumsorok adhatók hozzá. Az oszlop fejléce a . `SampleText` Ne feledje, hogy a Karakterlánc-típus nem támogatott.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Az SVMLight formátum használatával írja be vagy illessze be az értékeket.  
  
     A következő minta például a véradási adatkészlet első pár sorát jelöli SVMLight formátumban:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Az Adatok [bevitele manuálisan modul futtatásakor](./enter-data-manually.md) ezek a sorok oszlopok és indexértékek adatkészleté alakulnak az alábbiak szerint:  
  
     |Col1|Col2|Oszlop3|4. oszlop|Címkék|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Új sor indításához jelölje ki az Enter billentyűt minden sor után.      
     
   Ha több üres záró sor hozzáadásához az Enter multiple times lehetőséget választja, az üres sorok törlődnek vagy le lesznek vágva.  
  
   Ha hiányzó értékekkel rendelkező sorokat hoz létre, később bármikor kiszűrheti őket.  
  
1. Csatlakoztassa a kimeneti portot más modulokhoz, és futtassa a folyamatot.  
  
   Az adatkészlet megtekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés parancsot.**

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 