---
title: 'Adatok normalizálása: Modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Adatok normalizálása modult az Azure Machine Learningben egy adatkészlet *normalizálással történő átalakításához.Ismerje*meg, hogyan használhatja az adatok normalizálása modult az Azure Machine Learning ben normalizálás a normalizálás ..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477527"
---
# <a name="normalize-data-module"></a>Adatok modul normalizálása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal *normalizálással*alakíthatja át az adatkészletet.

Normalizálás olyan technika, gyakran alkalmazott részeként adatok előkészítése gépi tanulás. A normalizálás célja, hogy az adatkészlet numerikus oszlopainak értékeit úgy módosítsa, hogy közös léptéket használjon, anélkül, hogy torzítanák az értéktartományok közötti különbségeket, vagy elveszítenék az adatokat. Bizonyos algoritmusok esetében is szükség van az adatok megfelelő modellezésére.

Tegyük fel például, hogy a bemeneti adatkészlet egy 0 és 1 közötti értékekkel rendelkező oszlopot, egy másik pedig 10 000 és 100 000 közötti értékeket tartalmazó oszlopot tartalmaz. A számok *léptékének* nagy különbsége problémákat okozhat, amikor megpróbálja kombinálni az értékeket szolgáltatásként a modellezés során.

*A normalizálás* elkerüli ezeket a problémákat azáltal, hogy olyan új értékeket hoz létre, amelyek fenntartják az általános eloszlást és a méretarányokat a forrásadatokban, miközben a modellben használt összes numerikus oszlopra alkalmazott léptékben tartják az értékeket.

Ez a modul számos lehetőséget kínál a numerikus adatok átalakítására:

- Az összes értéket 0-1 léptékre módosíthatja, vagy úgy alakíthatja át az értékeket, hogy abszolút értékek helyett percentilis rangként képviseli őket.
- Normalizálást alkalmazhat egyetlen oszlopra vagy ugyanazon adatkészlet több oszlopára.
- Ha meg kell ismételnie a folyamatot, vagy ugyanazokat a normalizálási lépéseket kell alkalmaznia más adatokra, a lépéseket normalizálási transzformációként mentheti, és alkalmazhatja más, azonos sémával rendelkező adatkészletekre.

> [!WARNING]
> Egyes algoritmusok megkövetelik, hogy az adatok normalizálódnak a modell betanítása előtt. Más algoritmusok saját adatméretezést vagy normalizálást hajtanak végre. Ezért ha egy gépi tanulási algoritmust választ egy prediktív modell létrehozásához, győződjön meg róla, hogy tekintse át az algoritmus adatkövetelményeit, mielőtt normalizálást alkalmazna a betanítási adatokra.

##  <a name="configure-normalize-data"></a>Adatok normalizálása

Ezzel a modullal egyszerre csak egy normalizálási módszert alkalmazhat. Ezért a program ugyanazt a normalizálási módszert alkalmazza az összes kijelölt oszlopra. A különböző normalizálási módszerek használatához használja az **Adatok normalizálása második példányát.**

1. Adja hozzá az **Adatok normalizálása** modult a folyamathoz. A modul az Azure Machine Learning, **az Adatok átalakítása**, a Méretezés és **csökkentése** kategóriában.

2. Olyan adatkészlet csatlakoztatása, amely az összes szám legalább egy oszlopát tartalmazza.

3. Az Oszlopkijelölő vel kiválaszthatja a normalizálandó numerikus oszlopokat. Ha nem választja ki az egyes oszlopokat, alapértelmezés szerint a bemenet **összes** numerikus szövegoszlopa szerepel, és ugyanaz takarja a normalizálási folyamatot az összes kijelölt oszlopra. 

    Ez furcsa eredményekhez vezethet, ha olyan numerikus oszlopokat is felsogat, amelyeket nem szabad normalizálni! Mindig gondosan ellenőrizze az oszlopokat.

    Ha a rendszer nem észlel numerikus oszlopokat, ellenőrizze az oszlop metaadatait, és ellenőrizze, hogy az oszlop adattípusa támogatott numerikus típus-e.

    > [!TIP]
    > Annak érdekében, hogy egy adott típusú oszlopok bevitelre legyenek megadva, próbálja meg használni az [Oszlopok kijelölése az adatkészletben](./select-columns-in-dataset.md) modult az **adatok normalizálása**előtt.

4. **0 használata állandó oszlopokhoz, ha be van jelölve:** Jelölje be ezt a beállítást, ha bármely numerikus oszlop egyetlen változatlan értéket tartalmaz. Ez biztosítja, hogy az ilyen oszlopok at ne használják a normalizálási műveletekben.

5. Az **Átalakítási módszer** legördülő listából válasszon egyetlen matematikai függvényt, amelyet az összes kijelölt oszlopra alkalmazni szeretne. 
  
    - **Zscore**: Az összes értéket z-score-ra konvertálja.
    
      Az oszlopban lévő értékek a következő képlettel alakulnak át:  
  
      ![normalizálás z&#45;pontszámokkal](media/module/aml-normalization-z-score.png)
  
      Az átlagot és a szórást minden oszlopra külön kell kiszámítani. A sokaság szórását kell használni.
  
    - **MinMax**: A min-max normalizáló lineárisan átméretezi az összes funkciót a [0,1] intervallumra.
    
      A [0,1] intervallumra történő átméretezés az egyes funkciók értékeinek eltolásával történik, hogy a minimális érték 0 legyen, majd elosztjuk az új maximális értékkel (ami az eredeti maximális és a minimális értékek közötti különbség).
      
      Az oszlopban lévő értékek a következő képlettel alakulnak át:  
  
      ![normalizálás a min&#45;max funkcióval](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logisztika**: Az oszlopban lévő értékek a következő képlettel alakulnak át:

      ![képlet a logisztika függvényáltali normalizáláshoz](media/module/aml-normalization-logistic.png "AML_normalization-logisztikai")  
  
    - **LogNormal**: Ez a beállítás az összes értéket lognormális skálává alakítja.
  
      Az oszlopban lévő értékek a következő képlettel alakulnak át:
  
      ![képletnapló&#45;normál eloszlás](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Itt μ és σ az eloszlás paraméterei, amelyeket az adatokból empirikusan, maximális valószínűségbecslésként, minden egyes oszlopra külön-külön számítunk ki.  
  
    - **TanH**: Minden érték hiperbolikus érintővé alakul át.
    
      Az oszlopban lévő értékek a következő képlettel alakulnak át:
    
      ![normalizálás a tanh funkcióval](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Küldje el a folyamatot, vagy kattintson duplán az **Adatok normalizálása** modulra, és válassza a **Kijelölt futtatás lehetőséget.** 

## <a name="results"></a>Results (Eredmények)

A **Normalize Data** modul két kimenetet hoz létre:

- Az átalakított értékek megtekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Visualize parancsot.**

    Alapértelmezés szerint az értékek a helyükön alakulnak át. Ha az átalakított értékeket az eredeti értékekkel szeretné összehasonlítani, az [Oszlopok hozzáadása](./add-columns.md) modullal egyesítheti újra az adatkészleteket, és egymás mellett tekintheti meg az oszlopokat.

- Ha menteni szeretné az átalakítást, hogy ugyanazt a normalizálási módszert alkalmazhassa egy másik adatkészletre, jelölje ki a modult, és válassza az **Adatkészlet regisztrálása lehetőséget** a jobb oldali panel **Kimenetek** lapján.

    Ezután betöltheti a mentett átalakításokat a bal oldali navigációs ablak **Átalakítások** csoportjából, és alkalmazhatja azt egy azonos sémával rendelkező adatkészletre az [Átalakítás alkalmazása segítségével.](apply-transformation.md)  


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 