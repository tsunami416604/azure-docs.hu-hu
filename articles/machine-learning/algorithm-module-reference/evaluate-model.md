---
title: 'Modell kiértékelése: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Modell kiértékelése modul t az Azure Machine Learning egy betanított modell pontosságának mérésére.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456404"
---
# <a name="evaluate-model-module"></a>Modell modul kiértékelése

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal mérheti a betanított modell pontosságát. Egy modellből létrehozott pontszámokat tartalmazó adatkészletet ad meg, és a **Modell kiértékelése** modul kiszámítja az iparági szabványnak megfelelő kiértékelési metrikákkészletét.
  
 A **Modell kiértékelése** által visszaadott mérőszámok a kiértékelt modell típusától függenek:  
  
-   **Osztályozási modellek**    
-   **Regressziós modellek**  
-   **Fürtözési modellek**  


> [!TIP]
> Ha ön új -hoz minta értékelés, mi ajánl a video sor mellett Dr. István Elston, részeként -ból [gépi tanulás fogás](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) -ból EdX. 


A Modell kiértékelése modult háromféleképpen **használhatja:**

+ Pontszámok generálása a betanítási adatok felett, és a modell értékelése ezen pontszámok alapján
+ Pontszámok létrehozása a modellen, de hasonlítsa össze ezeket a pontszámokat egy fenntartott tesztkészlet pontszámaival
+ Két különböző, de kapcsolódó modell pontszámainak összehasonlítása ugyanazon adathalmaz használatával

## <a name="use-the-training-data"></a>A betanítási adatok használata

Modell kiértékeléséhez olyan adatkészletet kell csatlakoztatnia, amely bemeneti oszlopok és pontszámok készletét tartalmazza.  Ha más adat nem áll rendelkezésre, használhatja az eredeti adatkészletet.

1. Csatlakoztassa a [score modell](./score-model.md) **pontozott adatkészletkimenetét** a **Kiértékelés modell**bemenetével. 
2. Kattintson **a Modell kiértékelése** modulra, és futtassa a folyamatot a kiértékelési pontszámok létrehozásához.

## <a name="use-testing-data"></a>Tesztelési adatok használata

A gépi tanulás gyakori forgatókönyve az eredeti adatkészlet betanítási és tesztelési adatkészletek, a [Split](./split-data.md) modul vagy a [partíció és minta](./partition-and-sample.md) modul külön. 

1. Csatlakoztassa a [score modell](score-model.md) **pontozott adatkészletkimenetét** a **Kiértékelés modell**bemenetével. 
2. Csatlakoztassa a tesztelési adatokat tartalmazó Osztott adatok modul kimenetét a **Kiértékelés modell**jobb oldali bemenetéhez.
2. Kattintson **a Modell kiértékelése** modulra, és válassza a **Kiválasztott futtatás lehetőséget** a kiértékelési pontszámok létrehozásához.

## <a name="compare-scores-from-two-models"></a>Két modell pontszámainak összehasonlítása

A pontszámok második készletét is csatlakoztathatja a **Modell kiértékelése beállításhoz.**  A pontszámok lehetnek ismert eredményekkel rendelkező megosztott kiértékelési készlet, vagy ugyanazon adatok egy másik modellből származó eredmények készlete.

Ez a funkció azért hasznos, mert könnyen összehasonlíthatja az ugyanazon adatokon lévő két különböző modell eredményeit. Vagy összehasonlíthatja a pontszámok két különböző fut ugyanazon az adatokon keresztül különböző paraméterekkel.

1. Csatlakoztassa a [score modell](score-model.md) **pontozott adatkészletkimenetét** a **Kiértékelés modell**bemenetével. 
2. Csatlakoztassa a második modell score modell moduljának kimenetét a **Kiértékelés modell**jobb oldali bemenetéhez.
3. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A **Modell kiértékelése futtatása**után kattintson a jobb gombbal a modulra, és válassza a **Kiértékelés eredményeinek megjelenítését** az eredmények megtekintéséhez.

Ha adatkészleteket csatlakoztat a **Modell kiértékelése**mindkét bemenetéhez, az eredmények mindkét adathalmaz, vagy mindkét modell metrikákat tartalmaznak.
A bal oldali porthoz csatolt modell vagy adatok először a jelentésben jelennek meg, majd az adatkészlet vagy a jobb oldali porthoz csatolt modell mutatói jelennek meg.  

Az alábbi kép például két fürtözési modell eredményeit mutatja, amelyek ugyanazon adatokra épültek, de különböző paraméterekkel.  

![Összehasonlítás2Modellek](media/module/evaluate-2-models.png)  

Mivel ez egy fürtözési modell, a kiértékelési eredmények eltérnek, mint ha két regressziós modell pontszámait hasonlítja össze, vagy két besorolási modellt. A teljes bemutató azonban ugyanaz. 

## <a name="metrics"></a>Mérőszámok

Ez a szakasz a **modell kiértékelése**által támogatott modellek adott típusaira adott metrikákat ismerteti:

+ [osztályozási modellek](#metrics-for-classification-models)
+ [regressziós modellek](#metrics-for-regression-models)
+ [fürtözési modellek](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Osztályozási modellek metrikák

A besorolási modellek kiértékelésekor a következő mutatókjelennek meg.
  
-   **A pontosság** méri a besorolási modell jóságát, mint a valódi eredmények arányát az összes esethez képest.  
  
-   **A precizitás** a valódi eredmények aránya az összes pozitív eredményhez viszonyítva.  
  
-   **A visszahívás** a modell által visszaadott összes helyes eredmény törtrésze.  
  
-   **Az F-pontszám** a pontosság és a visszahívás súlyozott átlagaként kerül kiszámításra 0 és 1 között, ahol az ideális F-pontszám érték 1.  
  
-   **Az AUC** az y tengelyen valódi pozitívokkal ábrázolt ív alatti területet, az x tengelyen pedig az álpozitívokat méri. Ez a metrika azért hasznos, mert egyetlen számot biztosít, amely lehetővé teszi a különböző típusú modellek összehasonlítását.  
  
- **Az átlagos naplóveszteség** egyetlen pontszám, amelyet a rossz eredmények büntetésének kifejezésére használnak. A számítás két valószínűségi eloszlás – az igaz és a modellben lévő közötti különbség.  
  
- **A betanítási napló elvesztése** egyetlen pontszám, amely az osztályozó előnyét képviseli egy véletlenszerű előrejelzéssel szemben. A naplóveszteség a modell bizonytalanságát méri, összehasonlítva az általa a címkékben lévő ismert értékekkel (alapigazság) kiadó valószínűségekkel. A modell egészének naplóveszteségét szeretné minimalizálni.

### <a name="metrics-for-regression-models"></a>Regressziós modellek metrikák
 
A regressziós modellekhez visszaadott metrikák a hiba összegének becslésére szolgálnak.  A modell akkor tekinthető jól az adatoknak, ha a megfigyelt és az előre jelzett értékek közötti különbség kicsi. Azonban, ha megnézzük a minta a maradékok (a különbség bármely előre jelzett pont és a megfelelő tényleges érték) lehet mondani sokat a lehetséges elfogultság a modellben.  
  
 A következő mutatók a regressziós modellek kiértékeléséről vannak jelentve.
  
- **Az átlagos abszolút hiba (MAE)** azt méri, hogy az előrejelzések milyen közel vannak a tényleges eredményekhez; így az alacsonyabb pontszám jobb.  
  
- **A legfelső négyzetes hiba (RMSE)** egyetlen értéket hoz létre, amely összegzi a modellben lévő hibát. A különbség négyszögesítésével a metrika figyelmen kívül hagyja a túl-előrejelzés és az alul-előrejelzés közötti különbséget.  
  
- **A relatív abszolút hiba (RAE)** a várható és a tényleges értékek relatív abszolút különbsége; relatív, mert az átlagos különbséget elosztjuk a számtani középértékével.  
  
- **A relatív négyzetes hiba (RSE)** hasonlóképpen normalizálja az előre jelzett értékek teljes négyzetes hibáját a tényleges értékek teljes négyzetes hibájával való osztva.  
  

  
- A gyakran R<sup>2-nek</sup>nevezett **meghatározási együttható**a modell prediktív erejét 0 és 1 közötti értékként jelöli. A nulla azt jelenti, hogy a modell véletlenszerű (nem magyaráz semmit); 1 azt jelenti, hogy van egy tökéletes illeszkedést. Az R<sup>2</sup> értékek értelmezésénél azonban óvatosan kell eljárni, mivel az alacsony értékek teljesen normálisak lehetnek, és a magas értékek gyanúsak lehetnek.

###  <a name="metrics-for-clustering-models"></a>A fürtözési modellek metrikák

Mivel a fürtözési modellek sok tekintetben jelentősen eltérnek a besorolási és regressziós modellektől, [a Modell kiértékelése](evaluate-model.md) a fürtözési modellek eltérő statisztikáit is visszaadja.  
  
 A fürtözési modellhez visszaadott statisztikák leírják, hogy hány adatpont volt hozzárendelve az egyes fürtökhöz, a fürtök közötti elkülönítés mértéke, és hogy az adatpontok milyen szorosan vannak összefésülve az egyes fürtökön belül.  
  
 A fürtözési modell statisztikái átlagolása a teljes adatkészletre, további sorok pedig fürtenkénti statisztikákat tartalmaznak.  
  
A fürtözési modellek kiértékeléséhez a következő metrikák at jelentik.
    
-   Az Átlagos távolság az **Egyéb középponttól**oszlopban szereplő pontszámok azt jelzik, hogy a fürt egyes pontjai átlagosan milyen közel vannak az összes többi fürt centroidjaihoz.   

-   A **fürtközpontátlagos távolsága**oszlopban lévő pontszámok a fürt összes pontjának a fürt centroidhoz való közelségét jelölik.  
  
-   A **Pontok száma** oszlop azt mutatja, hogy hány adatpont volt hozzárendelve az egyes fürtökhöz, valamint az egyes fürtadatpontok teljes száma.  
  
     Ha a fürtökhöz rendelt adatpontok száma kisebb, mint a rendelkezésre álló adatpontok teljes száma, az azt jelenti, hogy az adatpontokat nem lehetett fürthöz rendelni.  
  
-   A maximális távolság a **fürtközponttól**oszlopban szereplő pontszámok az egyes pontok és az adott pont fürtjének centroidja közötti távolság okán vannak.  
  
     Ha ez a szám magas, azt jelentheti, hogy a fürt széles körben elszórtan. Tekintse át ezt a statisztikát a **fürtközponttól való átlagos távolsággal** együtt a fürt eloszlásának meghatározásához.   

-   Az eredmények egyes szakaszainak alján található **Kombinált kiértékelés** pontszám felsorolja az adott modellben létrehozott fürtök átlagos pontszámait.  
  

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 