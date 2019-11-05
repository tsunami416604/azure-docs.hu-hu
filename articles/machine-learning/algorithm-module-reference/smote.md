---
title: ARCUL ütötte
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a ARCUL ütötte modult a Azure Machine Learning szolgáltatásban az adathalmazban előforduló kis előfordulási példák számának növelésére a túlmintavételezés használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516187"
---
# <a name="smote"></a>ARCUL ütötte

Ez a cikk azt ismerteti, hogyan használható a **arcul ütötte** modul a Azure Machine learning Designerben (előzetes verzió) a gépi tanuláshoz használt adatkészletben lévő, az általa képviselt esetek számának növelésére. A ARCUL ütötte jobb módszer a ritka esetek számának növelésére, mint a meglévő esetek egyszerű duplikálása.  

 A ARCUL ütötte modult egy *kiegyensúlyozatlan*adatkészlethez kell kötni. Számos oka lehet annak, hogy az adathalmazok kiegyensúlyozva lehetnek: a megcélzott kategória lehet ritka a populációban, vagy ha az adatok gyűjtése nehéz lehet. A ARCUL ütötte-et általában akkor használja, ha az elemezni kívánt *osztályt* a rendszer jelöli. 
  
 A modul egy adatkészletet ad vissza, amely tartalmazza az eredeti mintákat, valamint egy további szintetikus kisebbségi mintát, a megadott százaléktól függően.  
  
### <a name="more-about-smote"></a>További információ a ARCUL ütötte

A **arcul ütötte** a *szintetikus kisebbségek Túlmintavételezésének technikáját*jelenti. Ez egy statisztikai módszer az adatkészletben lévő esetek számának kiegyensúlyozott módon történő növelésére.  A modul úgy működik, hogy új példányokat hoz létre a meglévő, bemenetként megadott kisebbségi esetekből. A ARCUL ütötte ezen implementációja **nem** módosítja a többségi esetek számát.

Az új példányok nem csupán másolatok a meglévő kisebbségi esetekről; Ehelyett az algoritmus mintákat vesz az egyes célcsoportok és a hozzájuk *tartozó szomszédok számára,* és olyan új példákat hoz létre, amelyek a cél eset funkcióit ötvözik a szomszédaival. Ez a megközelítés növeli az egyes osztályok által elérhető funkciókat, és általánosabban teszi a mintákat.
  
A ARCUL ütötte a teljes adatkészletet bemenetként veszi fel, de csak a kisebbségi esetek százalékos arányát növeli. Tegyük fel például, hogy egy kiegyensúlyozatlan adatkészlettel rendelkezik, ahol az esetek mindössze 1%-ában az A célként megadott érték (a kisebbségi osztály) és az esetek 99%-a "B" értékkel rendelkezik. Ha a kisebbségi esetek százalékos arányát az előző százalékban kétszer szeretné megnövelni, a modul tulajdonságainál a 200 értéket kell megadnia **arcul ütötte százalékban** .  
  
## <a name="examples"></a>Példák  

Azt javasoljuk, hogy a **arcul ütötte** -t kis adatkészlettel próbálja meg használni a működésének megtekintéséhez. Az alábbi példa a Azure Machine Learning Designerben elérhető véradási adatkészletet használja.
  
Ha felveszi az adatkészletet egy folyamatba, és az adatkészlet kimenetén található **Megjelenítés** elemre kattint, láthatja, hogy az adatkészletben szereplő 748-es sorok vagy esetek száma 570 eset (76%) 0. osztály és 178 eset (24%) 1. osztály. Bár ez nem borzasztóan kiegyensúlyozatlan, az 1. osztály a vért adományozó személyeket jelöli, így ezek a sorok tartalmazzák a modellezni kívánt *szolgáltatás területét* .
 
Az esetek számának növeléséhez a következő módon állíthatja be a **arcul ütötte százalékos**értékét a 100 többszörösével:

||0\. osztály|1\. osztály|teljes|  
|-|-------------|-------------|-----------|  
|Eredeti adatkészlet<br /><br /> (a **arcul ütötte-értékkel** egyenértékű = **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Arcul ütötte százalék** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Arcul ütötte százalék** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**Arcul ütötte százalék** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  Az ARCUL ütötte-t használó esetek számának növelése nem garantált, hogy pontosabb modelleket hozzon létre. Próbálja ki a folyamatot különböző százalékokkal, különböző szolgáltatáskészletekkel és a legközelebbi szomszédok különböző számával, hogy megtekintse, hogyan befolyásolja a modell az esetek hozzáadását.  
  
## <a name="how-to-configure-smote"></a>A ARCUL ütötte konfigurálása
  
1.  Adja hozzá a ARCUL ütötte modult a folyamathoz. A modult az Adatátalakítási modulok területen találja a manipuláció kategóriában.

2. Kapcsolódjon a fokozni kívánt adatkészlethez. Ha meg szeretné adni az új esetek létrehozásához szükséges lemezterületet, vagy csak bizonyos oszlopokat használ, vagy ha kizárja néhányat, a **arcul ütötte**használata előtt a használni kívánt oszlopok elkülönítéséhez használja az [adatkészletben szereplő oszlopok kiválasztása lehetőséget](select-columns-in-dataset.md) .
  
    Ellenkező esetben a **arcul ütötte** használatával létrehozott új esetek az **összes** bemenetként megadott oszlopon alapulnak. A szolgáltatás oszlopainak legalább egy oszlopa numerikus.
  
3.  Győződjön meg arról, hogy a címkét vagy a cél osztályt tartalmazó oszlop van kiválasztva.  A **arcul ütötte** csak bináris címkéket fogad.
  
4.  A **arcul ütötte** modul automatikusan azonosítja a kisebbségi osztályt a Label (címke) oszlopban, majd beolvassa a kisebbségi osztály összes példáját. Az összes oszlopnak nem lehet NaN.
  
5.  A **arcul ütötte százalékos értéke** beállításban adjon meg egy egész számot, amely a kimeneti adatkészletben a kisebbségi esetek céljának százalékos arányát jelzi. Példa:  
  
    - **0** (%) értéket kell beírnia. A ARCUL ütötte modul pontosan ugyanazt az adatkészletet adja vissza, amelyet bemenetként adott meg, és nem ad hozzá új kisebbségi eseteket. Ebben az adatkészletben az osztály aránya nem módosult.  
  
    -   **100** (%) értéket kell beírnia. A ARCUL ütötte-modul új, kisebb előfordulási eseteket hoz létre, és az eredeti adatkészletben azonos számú kisebbségi esetet ad hozzá. Mivel a ARCUL ütötte nem növekszik a többségi esetek száma, az egyes osztályokba tartozó esetek aránya már megváltozott.  
  
    -   **200** (%) értéket kell beírnia. A modul megduplázza a kisebbségi esetek százalékos arányát az eredeti adatkészlethez képest. Ez azt eredményezi, hogy az előzőekben leírtak szerint kétszer annyi kisebbségi ügyet **tartalmaz** .  Az adatkészlet mérete inkább úgy növekszik, hogy a többségi esetek száma azonos maradjon, és a kisebbségi esetek száma megnő, amíg meg nem felel a kívánt százalékos értéknek.  
  
    > [!NOTE]
    > A ARCUL ütötte százalékban csak a 100 többszörösét használja.

6.  A **legközelebbi szomszédok** lehetőség használatával határozza meg a arcul ütötte algoritmus által az új esetek kiépítésekor használt szolgáltatás területének méretét. A *legközelebbi szomszéd* egy adatsora (egy eset), amely hasonló a megcélzott esetekhez. A két eset közötti távolságot a rendszer az összes funkció súlyozott vektorának kombinálásával méri.  
  
     + A legközelebbi szomszédok számának növelésével több esetben is kap funkciókat.
     + A legközelebbi szomszédok számának alacsony értékre állításával olyan szolgáltatásokat használhat, amelyek az eredeti mintában jobban hasonlítanak.  
  
7. Adjon meg egy értéket a **véletlenszerű mag** szövegmezőben, ha ugyanazt az eredményt szeretné biztosítani ugyanazon folyamat ugyanazon adatcsatornán keresztül futtatva. Ellenkező esetben a modul egy véletlenszerű magot generál a Processzor órajelének értéke alapján a folyamat telepítésekor, ami némileg eltérő eredményeket eredményezhet a futtatások során.

8. A folyamat futtatása.  
  
     A modul kimenete egy adatkészlet, amely az eredeti sorokat, valamint néhány, a kisebbségi esetekkel felvett sort tartalmaz.  

## <a name="technical-notes"></a>Technikai megjegyzések

+ A **arcul ütötte** modult használó modellek közzétételekor távolítsa el a **arcul ütötte** a prediktív folyamatból, mielőtt webszolgáltatásként közzéteszi.  Ennek az az oka, hogy a ARCUL ütötte célja a modell fejlesztése a képzés során, és nem a pontozási célra készült.  Hibaüzenet jelenhet meg, ha egy közzétett prediktív folyamat tartalmazza a ARCUL ütötte modult.

+ A ARCUL ütötte alkalmazása előtt gyakran jobb eredményeket érhet el, ha hiányzó érték tisztítását vagy más átalakításokat alkalmaz az adatjavításhoz. 

+ Egyes kutatók megvizsgálták, hogy a ARCUL ütötte hatékony-e a nagy dimenziós vagy ritkán használt adatokat illetően, például a szöveges besorolásban vagy a genomikai adatkészletekben. Ez a dokumentum jól összefoglalja a ARCUL ütötte alkalmazásának hatásait és elméleti érvényességét: [Blagus és LUSA: arcul ütötte for nagy dimenziós osztály – kiegyensúlyozatlan adatmennyiség](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Ha a ARCUL ütötte nem érvényes az adatkészletben, akkor érdemes lehet más megközelítéseket is figyelembe venni a kisebbségi esetek túlmintavételezéséhez vagy a többségi esetek mintavételezéséhez, valamint a tanulók által közvetlenül a fürtözést segítő együttes módszerek létrehozásához. poggyász vagy adaptív növelés.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

