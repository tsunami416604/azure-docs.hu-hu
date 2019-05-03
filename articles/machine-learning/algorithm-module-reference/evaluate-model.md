---
title: 'Modell értékelése: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a modell kiértékelése modul használata az Azure Machine Learning szolgáltatás a betanított modell pontosságát méréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028740"
---
# <a name="evaluate-model-module"></a>Model-modul kiértékelése

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével mérheti a betanított modell pontosságát. Egy adatkészlet jön létre egy olyan modellből pontszámok tartalmazó adnia, és a **Evaluate Model** modul kiszámítja az iparági szabványnak megfelelő értékelési mérőszámok készletét.
  
 A metrikák által visszaadott **Evaluate Model** függenek, hogy kipróbálja a modell típusa:  
  
-   **Képbesorolási modellek**    
-   **Regressziós modellek**    



> [!TIP]
> Ha most ismerkedik a modell értékelése, javasoljuk a videósorozat a vészhelyreállítás. Stephen Elston a részeként az [machine learning-tanfolyamot](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) az EdX. 


Háromféleképpen használja a **Evaluate Model** modul:

+ Pontszámok készítése a betanítási adatokon, és a pontok alapján a modell értékelése
+ Pontszámok a modell létrehozásához, de a pontszámok fenntartott tesztelési konfigurálásában a pontszámok összehasonlítása
+ Pontszámok használatával ugyanazokat az adatokat két különböző, de kapcsolódó modellek összehasonlítása

## <a name="use-the-training-data"></a>A betanítási adatok használata

A modell kiértékelése, csatlakoznia kell egy adatkészletet, amely tartalmazza a bemeneti oszlopai és pontszámait.  Ha nincs más adatok nem érhető el, az eredeti adatkészletre is használhatja.

1. Csatlakozás a **adatkészlet Pontozását** a kimeneti a [Score Model](./score-model.md) bemenetével **Evaluate Model**. 
2. Kattintson a **Evaluate Model** modult, majd futtassa a kísérletet, a kiértékelési eredmények létrehozásához.

## <a name="use-testing-data"></a>Tesztelési adatok használata

A machine Learning szolgáltatásban egy gyakori forgatókönyv, hogy az eredeti készlet szét a tanítási és egy tesztelési, használja a [Split](./split-data.md) modul, vagy a [partíciót és minta](./partition-and-sample.md) modul. 

1. Csatlakozás a **adatkészlet Pontozását** a kimeneti a [Score Model](score-model.md) bemenetével **Evaluate Model**. 
2. Csatlakozás a felosztási adatok modul jobb oldali bemenetével tesztelési adatokat tartalmazó kimenetét **Evaluate Model**.
2. Kattintson a **Evaluate Model** modult, majd válassza ki **kijelölt futtatása** a kiértékelési eredmények létrehozásához.

## <a name="compare-scores-from-two-models"></a>Pontszámok a két modell összehasonlítása

Is csatlakoztathat egy második együttesét a pontszámok **Evaluate Model**.  A pontszámok egy megosztott kiértékelési eredmények már ismert, vagy egy ugyanazokat az adatokat egy másik modell eredményeinek lehet.

Ez a funkció akkor hasznos, mivel könnyen összehasonlíthat ugyanazokat az adatokat a két különböző modell eredményeinek. Vagy előfordulhat, hogy hasonlítsa össze a két különböző futtatások pontszámok keresztül eltérő paraméterekkel rendelkező ugyanazokat az adatokat.

1. Csatlakozás a **adatkészlet Pontozását** a kimeneti a [Score Model](score-model.md) bemenetével **Evaluate Model**. 
2. A kimenet a második modell Score Model-modul csatlakozni a jobb oldali bemenetével **Evaluate Model**.
3. Kattintson a jobb gombbal **Evaluate Model**, és válassza ki **kijelölt futtatása** a kiértékelési eredmények létrehozásához.

## <a name="results"></a>Results (Eredmények)

Futtatása után **Evaluate Model**, kattintson a jobb gombbal a modult, és válassza ki **kiértékelésének eredménye** kattintva megjelenítjük az eredményeket. A következőket teheti:

+ Az eredmények mentése más eszközökkel könnyebben végezhet adatelemzést adatkészletként
+ Hozzon létre egy vizualizációt a felületen

Ha az adatkészletek csatlakozni mindkét bemenetei **Evaluate Model**, az eredményeket mind az adatkészlethez tartozó, vagy mindkét modellt metrikáit tartalmazza.
A modell vagy a bal oldali port csatolt adatok egyike jelenik meg először a jelentésben, a metrikák az adatkészlet követ, illetve a megfelelő portot a csatlakoztatott modell.  

Az alábbi képen például a két fürtözési modell, amely ugyanazokat az adatokat, de eltérő paraméterekkel rendelkező készült jelöli az eredmények összehasonlítása.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Mivel ez a fürtözési modell, a kiértékelési eredmények eltérőek, mint ha két regressziós modellt a pontszámok képest, illetve két képbesorolási modellek képest. Azonban az általános bemutató megegyezik. 

## <a name="metrics"></a>Mérőszámok

Ez a szakasz ismerteti a visszaadott használhatók a modellek különféle típusú metrikák **Evaluate Model**:

+ [képbesorolási modellek](#bkmk_classification)
+ [regressziós modellek](#bkmk_regression)

###  <a name="bkmk_classification"></a> Képbesorolási modellek metrikái

A következő metrikák szerepelnek a jelentésben képbesorolási modellek kiértékelése során. Modell összehasonlítására, ha azok rangsora szerint a mérőszám az értékeléshez választja.  
  
-   **Pontosság** méri a besorolási modell az időarány, amíg teljes esetben igaz eredmény, még kiadásában.  
  
-   **Pontosság** Igaz eredmény az időarány, amíg az összes pozitív eredmények felett van.  
  
-   **Idézze** van a százalékértékében mutatkozó a modell által visszaadott összes megfelelő eredmény.  
  
-   **F-pontszám** számítja ki, hogy a pontosság és a visszaírási 0 és 1, ahol az ideális F-pontszám értéke 1 közötti súlyozott átlagát.  
  
-   **AUC** mértékek a görbe alatti terület az ábrázolt igaz értékre, az y tengely és a hamis pozitív jelzések elkerülése az x tengelyen lévő pozitívok. Ez a metrika akkor hasznos, mert biztosít, amely lehetővé teszi a különböző típusú modell összehasonlítására egyetlen számmá.  
  
- **Átlagos veszteség log** a nem megfelelő eredmények a büntetés express segítségével egyetlen pontszámot. A különbség a két valószínűségi disztribúciók – igaz azt, és a egy, a modell, akkor a program.  
  
- **Napló adatveszteség képzési** egyetlen feltüntető pontszámok tartoznak az osztályozó által igénybe vett használhatóságát egy véletlenszerű előrejelzési van. A napló adatveszteség méri a modell a bizonytalanság összehasonlítja a valószínűségek, a címkék az ismert értékek (földön hiteles) adja vissza. A modell egy teljes log veszteség minimalizálása érdekében érdemes.

##  <a name="bkmk_regression"></a> Regressziós modellek metrikái
 
Metrikákat adja vissza, regressziós modellek általában tervezték, hogy a hiba-becslésére.  Egy modellt az adatok illeszkednek jól, ha a megfigyelt és az előre jelzett értékek közötti különbség az, kis minősül. Azonban megnézzük a például minta (bármely egy előre jelzett pont és a megfelelő tényleges érték közötti különbség) eláruljuk, sokkal kapcsolatos potenciális eltérés a modellben.  
  
 A következő metrikák szerepelnek a jelentésben, regressziós modellek értékeléséhez. Modell összehasonlítására, amikor azok rangsora szerint a mérőszám az értékeléshez választja.  
  
- **Jelenti azt, hogy abszolút hiba (MAE)** méri, hogy milyen közel az előrejelzés vannak, a tényleges eredmények; így alacsonyabb pontszám, annál jobb.  
  
- **Root mean squared hiba (Gyökátlagos)** hoz létre egyetlen érték, amely összefoglalja a hiba a modellben. Squaring a különbség az, amelyet a metrika nem veszik figyelembe az előny előrejelzési és korrigáljuk előrejelzési közötti különbség.  
  
- **Relatív abszolút hiba (RAE)** várt és tényleges érték; közötti relatív abszolút eltérés van relatív, mert az a különbség a számtani elosztja.  
  
- **Relatív hiba (RSE) négyzet** hasonlóképpen normalizálja az előre jelzett értékek teljes squared hiba a teljes squared hiba a tényleges értékek halmozódik.  
  
- **Jelenti azt, hogy a nulla egy hiba (MZOE)** jelzi, hogy az előrejelzés helyes volt-e vagy sem.  Más szóval: `ZeroOneLoss(x,y) = 1` amikor `x!=y`; egyébként `0`.
  
- **Együttható**gyakran nevezik R<sup>2</sup>, teljesítményéből prediktív modell 0 és 1 közötti értéket. Nulla érték azt jelenti, hogy a modell le véletlenszerű (ismerteti semmi); 1 azt jelenti, hogy nincs tökéletes választás. Azonban legyen óvatos az R értelmezése<sup>2</sup> értékeket, mivel lehet, hogy a kis-értékek teljesen normális, és nagy értékeket is lehet a gyanús.
  

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 