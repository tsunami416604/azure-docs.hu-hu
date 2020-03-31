---
title: 'Partíció és minta: Modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Partíció és minta modul az Azure Machine Learning mintavételezés egy adatkészleten, vagy partíciókat az adatkészletből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477510"
---
# <a name="partition-and-sample-module"></a>Partíció- és mintamodul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

A Partíció és a Minta modul segítségével mintavételezést végezhet egy adatkészleten, vagy partíciókat hozhat létre az adatkészletből.

A mintavételezés fontos eszköz a gépi tanulásban, mivel lehetővé teszi az adatkészlet méretének csökkentését, miközben megtartja az azonos arányú értékeket. Ez a modul számos kapcsolódó feladatot támogat, amelyek fontosak a gépi tanulásban: 

- Az adatok felosztása azonos méretű több alszakaszra. 

  Használhatja a partíciókkereszt-ellenőrzés, vagy az esetek hozzárendelése véletlenszerű csoportokhoz.

- Az adatok csoportokra bontása, majd egy adott csoport adatainak lebontása. 

  Miután véletlenszerűen hozzárendelte az eseteket a különböző csoportokhoz, előfordulhat, hogy módosítania kell a csak egy csoporthoz társított funkciókat.

- Mintavételi. 

  Kinyerheti az adatok egy százalékát, alkalmazhat véletlenszerű mintavételezést, vagy kiválaszthat egy oszlopot az adatkészlet kiegyensúlyozásához, és rétegzett mintavételezést hajthat végre az értékein.

- Kisebb adatkészlet létrehozása tesztelésre. 

  Ha sok adattal rendelkezik, érdemes lehet csak az első *n* sorokat használni a folyamat beállítása kor, majd a modell létrehozásakor váltson a teljes adatkészlet használatára. Mintavételezési segítségével is létrehozhat egy kisebb adatkészletet a fejlesztéshez.

## <a name="configure-the-module"></a>A modul konfigurálása

Ez a modul a következő módszereket támogatja az adatok partíciókra való felosztására vagy mintavételezésre. Először válassza ki a módszert, majd adja meg a metódus által igényelt további beállításokat.

- Head
- Mintavételezés
- Hozzárendelés a hajtásokhoz
- Pick fold (pick fold)

### <a name="get-top-n-rows-from-a-dataset"></a>TOP N sorok beolvasása adatkészletből

Ebben a módban csak az első *n* sorokat kaphatja meg. Ez a beállítás akkor hasznos, ha egy folyamatot kis számú sorban szeretne tesztelni, és nincs szüksége az adatok kiegyensúlyozottságára vagy mintavételezésére.

1. Adja hozzá a **partíciót és a mintamodult** a folyamathoz a kapcsolatban, és csatlakoztassa az adatkészletet.  

1. **Partíció vagy minta mód**: Állítsa ezt a beállítást **a Fej**.

1. **A kijelölandó sorok száma**: Adja meg a visszaadandó sorok számát.

   A sorok számának nem negatív egész számnak kell lennie. Ha a kijelölt sorok száma nagyobb, mint az adatkészlet sorainak száma, a teljes adatkészlet et adja vissza.

1. Küldje el a folyamatot.

A modul egyetlen adatkészletet ad ki, amely csak a megadott számú sort tartalmazza. A sorok mindig az adatkészlet tetejéről kerülnek beolvasva.

### <a name="create-a-sample-of-data"></a>Adatminta létrehozása

Ez a beállítás támogatja az egyszerű véletlenszerű mintavételt vagy a rétegzett véletlenszerű mintavételt. Ez akkor hasznos, ha kisebb reprezentatív minta adatkészletet szeretne létrehozni tesztelésre.

1. Adja hozzá a **partíciót és a mintamodult** a folyamathoz, és csatlakoztassa az adatkészletet.

1. **Partíció vagy minta mód**: Állítsa ezt a beállítást **mintavételi gombra.**

1. **Mintavételi arány:** Adjon meg egy 0 és 1 közötti értéket. ez az érték a forrásadatkészlet sorainak azt a százalékát adja meg, amelyet a kimeneti adatkészletben fel kell tüntetni.

   Ha például az eredeti adatkészletnek csak a `0.5` felét szeretné, adja meg azt a jelzést, hogy a mintavételi arány nak 50 százaléknak kell lennie.

   A bemeneti adatkészlet sorai a megadott aránynak megfelelően összekeverednek és szelektíven kerülnek a kimeneti adatkészletbe.

1. **Véletlenszerű vetőmag mintavételhez:** Tetszés szerint adjon meg egy egész szám, amelyet vetőmagértékként szeretne használni.

   Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok at minden alkalommal ugyanúgy osszák. Az alapértelmezett érték **0**, ami azt jelenti, hogy a rendszeróra alapján kezdőmag jön létre. Ez az érték a folyamat minden egyes futtatásakor némileg eltérő eredményekhez vezethet.

1. **Rétegzett felosztás mintavételezéshez:** Válassza ezt a lehetőséget, ha fontos, hogy az adatkészlet sorait a mintavétel előtt néhány kulcsoszlop egyenletesen ossza fel.

   A **Mintavételezési kulcs rétegződési oszlopához**válasszon ki egy *rétegoszlopot,* amelyet az adatkészlet felosztásakor kell használnia. Az adatkészlet sorai ezután a következőképpen oszlanak meg:

   1. Az összes bemeneti sort a megadott rétegoszlop értékei csoportosítják (rétegezik).

   1. A sorok at az egyes csoportokon belül keverik.

   1. Minden csoport szelektíven hozzáadódik a kimeneti adatkészlethez, hogy megfeleljen a megadott aránynak.


1. Küldje el a folyamatot.

   Ezzel a beállítással a modul egyetlen adatkészletet ad ki, amely az adatok reprezentatív mintavételezését tartalmazza. Az adatkészlet fennmaradó, nem mintavételezett része nem lesz kimenet. 

## <a name="split-data-into-partitions"></a>Adatok felosztása partíciókra

Akkor használja ezt a beállítást, ha az adatkészletet az adatok részhalmazaira szeretné osztani. Ez a beállítás akkor is hasznos, ha egyéni számú hajtogatási összehajthatót szeretne létrehozni keresztellenőrzésre, vagy több csoportra szeretné osztani a sorokat.

1. Adja hozzá a **partíciót és a mintamodult** a folyamathoz, és csatlakoztassa az adatkészletet.

1. **Partíció vagy minta módban**válassza a Hozzárendelés a **hajtáshoz**lehetőséget.

1. **Csere használata a particionálásban:** Válassza ezt a lehetőséget, ha azt szeretné, hogy a mintavételezett sor visszakerüljön a sorok készletébe, hogy esetlegesen újra felhasználhassa. Ennek eredményeképpen ugyanaz a sor több hajtáshoz is hozzárendelhető.

   Ha nem használja a csere (az alapértelmezett beállítás), a mintavételezett sor nem kerül vissza a készlet sorok esetleges újrafelhasználása. Ennek eredményeképpen minden sor csak egy hajtáshoz rendelhető hozzá.

1. **Randomizált felosztás:** Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok véletlenszerűen legyenek hozzárendelve a behajthatókhoz.

   Ha nem jelöli be ezt a beállítást, a sorok a ciklikus multiplexelés módszerrel vannak hozzárendelve a behajtáshoz.

1. **Véletlenszerű vetőmag:** Tetszés szerint adjon meg egy egész szintet, amelyet magértékként szeretne használni. Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok at minden alkalommal ugyanúgy osszák. Ellenkező esetben a **0** alapértelmezett érték azt jelenti, hogy véletlenszerű kezdőmag lesz használva.

1. **Adja meg a particionáló metódust:** Adja meg, hogyan szeretné felosztani az adatokat az egyes partíciók között az alábbi beállításokkal:

   - **Partíció egyenletesen**: Ezzel a beállítással azonos számú sort helyezhet el minden partíción. A kimeneti partíciók számának megadásához írjon be egy egész számot a Felosztáshoz a felosztáshoz használható **hajtások számámezőbe.**

   - **Testreszabott arányokkal rendelkező partíció:** Ezzel a beállítással az egyes partíciók méretét vesszővel tagolt listaként adhatja meg.

     Tegyük fel például, hogy három partíciót szeretne létrehozni. Az első partíció az adatok 50 százalékát fogja tartalmazni. A fennmaradó két partíció mindegyike az adatok 25 százalékát fogja tartalmazni. A **Vesszővel elválasztott arányok listája** mezőbe írja be a következő számokat: **.5, .25, .25**.

     Az összes partícióméret összegének pontosan 1-et kell hozzáadnia.

     Ha olyan számokat ad meg, amelyek *legfeljebb 1 értéket*adnak ki, a fennmaradó sorok tárolására egy további partíció jön létre. Ha például a .2 és **a .3** értékeket adja meg, egy harmadik partíció jön létre az összes sor fennmaradó 50 százalékának tárolására. **.3**
     
     Ha olyan számokat ad meg, amelyek *legfeljebb 1 értéket*adnak ki, a folyamat futtatásakor hiba lép fel.

1. **Rétegzett felosztás**: Válassza ezt a lehetőséget, ha felosztáskor rétegezi a sorokat, majd válassza ki a _rétegoszlopot._

1. Küldje el a folyamatot.

   Ezzel a beállítással a modul több adatkészletet ad ki. Az adatkészletek particionálva a megadott szabályok szerint.

### <a name="use-data-from-a-predefined-partition"></a>Előre definiált partícióadatainak használata  

Akkor használja ezt a beállítást, ha egy adatkészletet több partícióra osztott fel, és most minden partíciót be szeretne tölteni további elemzés vagy feldolgozás céljából.

1. Adja hozzá a **partíciót és a mintamodult** a folyamathoz.

1. Csatlakoztassa a modult a partíció **és a minta**egy korábbi példányának kimenetéhez. A példánynak a **Hozzárendelés a hajtáshoz** lehetőséget kellett használnia bizonyos számú partíció létrehozásához.

1. **Partíció vagy minta mód:** Válassza **a Pick Fold**lehetőséget.

1. **Adja meg, hogy melyik hajtásból kell mintát venni:** Az index megadásával válassza ki a használni kívánt partíciót. A partícióindexek 1-alapúak. Ha például az adatkészletet három részre osztja, a partíciók 1, 2 és 3 indexet kapnak.

   Ha érvénytelen indexértéket ad meg, a következő tervezési idejű hiba lép fel: "0018 hiba: Az adatkészlet érvénytelen adatokat tartalmaz."

   Az adatkészlet hajtások szerinti csoportosítása mellett az adatkészletet két csoportra bonthatja: a célhajtásra és minden másra. Ehhez adja meg az index egyetlen szeres, majd válassza ki a lehetőséget **Pick kiegészíti a kiválasztott szeres,** hogy mindent, de az adatokat a megadott szeres.

1. Ha több partícióval dolgozik, hozzá kell adnia a partíció és a **mintamodul** további példányait az egyes partíciók kezeléséhez.

   A második sorban lévő **Partíció és Minta** modul például a **Hajtogatáshoz hozzárendelés,** a harmadik sorban pedig a **Pick Fold**elem.   

   ![Partíció és minta](./media/module/partition-and-sample.png)

1. Küldje el a folyamatot.

   Ezzel a beállítással a modul egyetlen adatkészletet ad ki, amely csak az adott hajtáshoz rendelt sorokat tartalmazza.

> [!NOTE]
>  A hajtásjelölések nem tekinthetők meg közvetlenül. Csak a metaadatokban vannak jelen.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 