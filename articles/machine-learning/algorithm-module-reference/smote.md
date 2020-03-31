---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a SMOTE-modult az Azure Machine Learningben az adatkészletalacsony előfordulási gyakoriságú példáiszámának növeléséhez túlmintavételezéssel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477459"
---
# <a name="smote"></a>SMOTE

Ez a cikk ismerteti, hogyan használhatja a SMOTE-modul t az Azure Machine Learning designerben (előzetes verzió) az alulreprezentált esetek számának növeléséhez egy gépi tanuláshoz használt adatkészletben. A SMOTE jobb módja a ritka esetek számának növelésének, mint a meglévő esetek egyszerű megkettőzése.  

Az SMOTE modult kiegyensúlyozatlan adatkészlethez *csatlakoztatja.* Számos oka lehet annak, hogy egy adatkészlet kiegyensúlyozatlan. Előfordulhat például, hogy a megcélzott kategória ritka a populációban, vagy az adatok gyűjtése nehéz lehet. Általában akkor használja a SMOTE-t, ha az elemezni kívánt *osztály* alulreprezentált. 
  
A modul az eredeti mintákat tartalmazó adatkészletet ad vissza. A megadott százalékos értéktől függően számos szintetikus kisebbségi mintát is visszaad.  
  
## <a name="more-about-smote"></a>SMOTE – További információk

A szintetikus kisebbségi túlmintavételezési technika (SMOTE) egy statisztikai technika az adatkészletben lévő esetek számának kiegyensúlyozott növelésére. A modul úgy működik, hogy új példányokat hoz létre a meglévő kisebbségi esetekből, amelyeket bemenetként szállít. Az SMOTE e végrehajtása *nem* változtatja meg a többségi esetek számát.

Az új esetek nem csupa kisebbségi esetek másolatai. Ehelyett az algoritmus mintát vesz a *jellemzőtér* minden célosztály és a legközelebbi szomszédok. Az algoritmus ezután új példákat hoz létre, amelyek egyesítik a céleset jellemzőit a szomszédok jellemzőivel. Ez a megközelítés növeli az egyes osztályok számára elérhető funkciókat, és általánosabbá teszi a mintákat.
  
A SMOTE a teljes adatkészletet bemenetként veszi fel, de csak a kisebbségi esetek százalékos arányát növeli. Tegyük fel például, hogy van egy kiegyensúlyozatlan adatkészlet, ahol az esetek mindössze 1 százaléka rendelkezik Az A célértékkel (a kisebbségi osztály), és az esetek 99 százaléka B értékkel rendelkezik. Ha a kisebbségi esetek százalékos arányát az előző százalék kétszeresére szeretné növelni, a modul tulajdonságaiközött **200-at** kell megadnia az **SMOTE-százalékhoz.**  
  
## <a name="examples"></a>Példák  

Azt javasoljuk, hogy próbálja meg a SMOTE egy kis adatkészlettel, hogy hogyan működik. A következő példa az Azure Machine Learning tervezőjében elérhető véradási adatkészletet használja.
  
Ha hozzáadja az adatkészletet egy folyamathoz, és az adatkészlet kimenetén a Visualize lehetőséget **választja,** láthatja, hogy az adatkészlet 748 sora vagy esete közül 570 eset (76 százalék) a Class 0, és 178 eset (24 százalék) az 1- es osztályhoz tartoz. Bár ez az eredmény nem rettenetesen kiegyensúlyozatlan, class 1 képviseli az emberek, akik vért adományozott, így ezek a sorok tartalmazzák a *jellemző helyet,* hogy szeretné modell.
 
Az esetek számának növeléséhez az **SMOTE százalék**értékét a 100 többszöröseinek használatával állíthatja be az alábbiak szerint:

||0. osztály|1. osztály|összesen|  
|-|-------------|-------------|-----------|  
|Eredeti adatkészlet<br /><br /> (egyenértékű az **SMOTE százalékkal** = **0)**|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**SMOTE százalék** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE százalék** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**SMOTE százalék** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Az esetek számának növelése a SMOTE használatával nem garantált, hogy pontosabb modelleket készítsen. Próbálja meg a különböző százalékokkal, különböző funkciókészletekkel és a legközelebbi szomszédok különböző számaival rendelkező csővezetékeket, hogy lássa, hogyan befolyásolja az esetek hozzáadása a modellt.  
  
## <a name="how-to-configure-smote"></a>Az SMOTE konfigurálása
  
1.  Adja hozzá az SMOTE modult a folyamathoz. A modul az **Adatátalakítási modulok csoportban**található, a **Manipuláció** kategóriában.

2. Csatlakoztassa a kiemelni kívánt adatkészletet. Ha meg szeretné adni az új esetek létrehozásának jellemzőterét, akár csak bizonyos oszlopok használatával, akár néhány kizárásával, használja az Oszlopok kijelölése az [Adatkészlet modulban.](select-columns-in-dataset.md) Ezután elkülönítheti a használni kívánt oszlopokat az SMOTE használata előtt.
  
    Ellenkező esetben az új esetek létrehozása a SMOTE-n keresztül a bemenetként megadott *összes* oszlopon alapul. A jellemzőoszlopok legalább egy oszlopa numerikus.
  
3.  Győződjön meg arról, hogy a címkét vagy célosztályt tartalmazó oszlop ki van jelölve. A SMOTE csak bináris címkéket fogad el.
  
4.  Az SMOTE modul automatikusan azonosítja a kisebbségi osztályt a címkeoszlopban, majd megkapja a kisebbségi osztály összes példáját. Nem lehet minden oszlopban NaN érték.
  
5.  A **SMOTE százalékos beállításban** adjon meg egy egész számot, amely a kisebbségi esetek célszázalékát jelzi a kimeneti adatkészletben. Példa:  
  
    - A **0**értéket adja meg. Az SMOTE modul pontosan ugyanazt az adatkészletet adja vissza, amelyet bemenetként megadott. Nem ad hozzá új kisebbségi ügyeket. Ebben az adatkészletben az osztályarány nem változott.  
  
    - Adja meg **a 100**. A SMOTE modul új kisebbségi eseteket hoz létre. Ugyanannyi kisebbségi esetet ad hozzá, mint az eredeti adatkészletben. Mivel a SMOTE nem növeli a többségi esetek számát, az egyes csoportok eseteinek aránya megváltozott.  
  
    - Adja meg **200**. A modul megduplázza a kisebbségi esetek százalékos arányát az eredeti adatkészlethez képest. Ez *nem* eredményez kétszer annyi kisebbségi esetet, mint korábban. Ehelyett az adatkészlet mérete növekszik oly módon, hogy a többségi esetek száma ugyanaz marad. A kisebbségi esetek száma addig növekszik, amíg meg nem egyezik a kívánt százalékos értékkel.  
  
    > [!NOTE]
    > Az SMOTE-százalékhoz csak a 100 többszörösét használja.

6.  A **Legközelebbi szomszédok száma** beállítással meghatározhatja az SMOTE algoritmus által az új esetek létrehozásához használt jellemzőterület méretét. A legközelebbi szomszéd egy olyan adatsor (eset), amely hasonlít a célesethez. Bármely két eset közötti távolságot az összes jellemző súlyozott vektorainak kombinálásával mérik.  
  
    + A legközelebbi szomszédok számának növelésével több esetből kapsz funkciókat.
    + Ha alacsonyan tartja a legközelebbi szomszédok számát, olyan funkciókat használ, amelyek jobban hasonlítanak az eredeti mintában szereplő funkciókra.  
  
7. Adjon meg egy értéket a **Véletlen mag** mezőben, ha ugyanazokat az eredményeket szeretné biztosítani ugyanazon folyamat futtatásain, ugyanazzal az adattal. Ellenkező esetben a modul egy véletlenszerű magot hoz létre a processzor óraértékei alapján, amikor a folyamat telepítve van. A generációs egy véletlenszerű mag okozhat kissé eltérő eredményeket fut.

8. Küldje el a folyamatot.  
  
   A modul kimenete egy adatkészlet, amely tartalmazza az eredeti sorokat, valamint számos hozzáadott sort kisebbségi esetekkel.  

## <a name="technical-notes"></a>Technikai megjegyzések

+ Amikor egy **smote-modult** használó modellt tesz közzé, távolítsa el a **SMOTE-t** a prediktív folyamatból, mielőtt webes szolgáltatásként közzétenné. Ennek az az oka, hogy a SMOTE célja, hogy javítsa a modell a képzés során, nem a pontozás. Előfordulhat, hogy hibaüzenet jelenik meg, ha egy közzétett prediktív folyamat tartalmazza a SMOTE modult.

+ Gyakran jobb eredményeket érhet el, ha törli a hiányzó értékeket, vagy más átalakításokat alkalmaz az adatok javítására a SMOTE alkalmazása előtt. 

+ Egyes kutatók megvizsgálták, hogy a SMOTE hatékony-e a nagydimenziós vagy ritka adatokon, például a szövegosztályozásban vagy a genomikai adatkészletekben használt adatokon. Ez a dokumentum jó összefoglalót ad a SMOTE alkalmazásának hatásairól és elméleti érvényességéről ilyen esetekben: [Blagus és Lusa: SMOTE a nagy dimenziójú osztálykiegyensúlyozatlan adatok esetében](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Ha az SMOTE nem hatékony az adatkészletben, más, esetleg figyelembe vennie érdemes megközelítések a következők:
  + A kisebbségi esetek túlmintavételezésének vagy a többségi esetek alulmintavételezésének módszerei.
  + Együttes technikák, amelyek segítik a tanulót közvetlenül segítségével klaszterek, zsákolás, vagy adaptív fellendítése.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

