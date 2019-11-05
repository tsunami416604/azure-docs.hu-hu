---
title: 'Partíció és minta: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning a partíció és a minta modult az adatkészletek mintavételezésének végrehajtásához, vagy az adatkészlet partícióinak létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 659dcae0bee0fe1015ba6225797c31bad438013f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497717"
---
# <a name="partition-and-sample-module"></a>Partíció és minta modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal mintavételezést végezhet egy adatkészleten, vagy partíciókat hozhat létre az adatkészletből.

A mintavétel fontos eszköz a gépi tanulásban, mivel lehetővé teszi az adatkészlet méretének csökkentését az értékek azonos arányának fenntartása mellett. Ez a modul több kapcsolódó feladatot is támogat, amelyek fontosak a gépi tanulásban: 

- Az adatait több, azonos méretű alszakaszra oszthatja. 

    Használhatja a partíciókat a kereszt-ellenőrzéshez, illetve az esetek véletlenszerű csoportokba való hozzárendelését is.

- Az adatok csoportokba való elválasztása, majd egy adott csoport adatainak használata. 

    Az esetek különböző csoportokhoz való véletlenszerű hozzárendelését követően előfordulhat, hogy módosítania kell a csak egy csoporthoz társított szolgáltatásokat.

- Mintavételi. 

    Kinyerheti az adatok százalékos arányát, véletlenszerű mintavételezést alkalmazhat, vagy kiválaszthatja az adatkészlet kiegyensúlyozásához használandó oszlopot, és rétegzett mintavételezést hajthat végre az értékein.

- Kisebb adatkészlet létrehozása teszteléshez. 

    Ha sok adat van, érdemes csak az első *n* sort használni a folyamat beállítása során, majd a modell létrehozásakor váltson a teljes adatkészlet használatára. A mintavétel használatával kisebb adatkészletet is létrehozhat a fejlesztéshez.

## <a name="configure-partition-and-sample"></a>Partíció és minta konfigurálása

Ez a modul több módszer használatát teszi lehetővé az adatpartíciók és a mintavételezések felosztásához. Először válassza ki a metódust, majd adja meg a metódus által igényelt további beállításokat.

- Head
- Mintavételezés
- Hozzárendelés a kidobásokhoz
- Kidobás

### <a name="get-top-n-rows-from-a-dataset"></a>ELSŐ N sor beolvasása egy adatkészletből

Ezzel a móddal csak az első *n* sor kerül be. Ez a beállítás akkor hasznos, ha egy folyamatot kis számú sorban szeretne tesztelni, és nincs szükség az adat kiegyensúlyozására vagy bármilyen módon történő mintavételezésére.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz a csatolón, és kapcsolódjon az adatkészlethez.  

2. **Partíciós vagy minta mód**: ezt a beállítást állítsa a **Head**értékre.

3. **Kijelölni kívánt sorok száma**: írja be a visszaadni kívánt sorok számát.

    A megadott sorok számának nem negatív egész számnak kell lennie. Ha a kiválasztott sorok száma nagyobb, mint az adatkészlet sorainak száma, a rendszer a teljes adathalmazt adja vissza.

4. A folyamat futtatása.

A modul egyetlen adatkészlet kimenetét jeleníti meg, amely csak a megadott számú sort tartalmazza. A sorok mindig az adatkészlet elejétől lesznek beolvasva.

### <a name="create-a-sample-of-data"></a>Adatminta létrehozása

Ez a beállítás támogatja az egyszerű véletlenszerű mintavételezést vagy a rétegzett véletlenszerű mintavételt. Ez akkor hasznos, ha kisebb reprezentatív minta adatkészletet szeretne létrehozni teszteléshez.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz, és kapcsolódjon az adatkészlethez.

2. **Partíció vagy minta mód**: ezt állítsa be a **mintavételezéshez**.

3. **Mintavételezési sebesség**: adjon meg egy 0 és 1 közötti értéket. Ez az érték határozza meg a forrás adatkészletből a kimeneti adatkészletbe foglalandó sorok százalékos arányát.

    Ha például csak az eredeti adatkészlet felét szeretné használni, írja be a `0.5`t, hogy jelezze, a mintavételi sebességnek 50%-nak kell lennie.

    A bemeneti adatkészlet sorait a rendszer a megadott arány szerint rendezi és szelektíven helyezi el a kimeneti adatkészletbe.

4. **Véletlenszerű magok mintavételezéshez**: opcionálisan adjon meg egy egész számot, amelyet a rendszer a magok értékének megadására használ.

    Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Az alapértelmezett érték 0, ami azt jelenti, hogy a rendszer órája alapján hozza létre a kezdő magot. Ez némileg eltérő eredményeket eredményezhet a folyamat minden egyes futtatásakor.

5. **Rétegzett felosztás mintavételezéshez**: akkor válassza ezt a beállítást, ha fontos, hogy az adatkészlet sorait egyenletesen kell megosztani a mintavétel előtt egy fontos oszlop szerint.

    A **mintavételezési kulcs oszlopához**válassza ki az adatkészlet felosztásához használni kívánt egyetlen *rétegbeli oszlopot* . Az adatkészlet sorai a következőképpen oszlanak meg:

    1. Az összes bemeneti sor (rétegzett) a megadott rétegek oszlop értékei szerint csoportosítva van.

    2. A sorok az egyes csoportokon belül vannak megkeverve.

    3. Az egyes csoportok szelektíven hozzáadódnak a kimeneti adatkészlethez, hogy megfeleljenek a megadott aránynak.


6. A folyamat futtatása.

    Ezzel a beállítással a modul egyetlen adatkészletet ad eredményül, amely az adatokat reprezentáló mintavételt tartalmaz. Az adatkészlet fennmaradó, nem mintavételezés alatt álló része nem kimenet. 

## <a name="split-data-into-partitions"></a>Az adatfelosztás partícióra

Akkor használja ezt a beállítást, ha az adatkészletet az adat részhalmazára szeretné osztani. Ez a beállítás akkor is hasznos, ha egyéni számú kitöltést szeretne létrehozni a kereszt-ellenőrzéshez, vagy több csoportba kell osztania a sorokat.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz, és kapcsolódjon az adatkészlethez.

2. A **partíció vagy a minta mód**esetében válassza **a hozzárendelés a kidobáshoz**lehetőséget.

3. **Csere használata a particionáláskor**: válassza ezt a lehetőséget, ha azt szeretné, hogy a mintában szereplő sor visszakerüljön a sorok készletére a lehetséges újrafelhasználáshoz. Ennek eredményeképpen előfordulhat, hogy ugyanaz a sor több bedobáshoz is hozzá van rendelve.

    Ha nem használ helyettesítő elemet (ez az alapértelmezett beállítás), a rendszer nem helyezi vissza a mintavételezési sort a lehetséges újrahasznosítási sorok készletében. Ennek eredményeképpen minden egyes sor csak egy összecsukott elemhez rendelhető hozzá.

4. **Randomizált felosztás**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok véletlenszerűen legyenek kiosztva a kidobáshoz.

    Ha nem jelöli be ezt a beállítást, a sorok a ciklikus multiplexelés használatával lesznek kiosztva.

5. **Véletlenszerű mag**: igény szerint adjon meg egy egész számot, amelyet a mag értékként kíván használni. Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Ellenkező esetben az alapértelmezett 0 érték azt jelenti, hogy a rendszer véletlenszerű kezdő magot használ.

6. **Adja meg a particionáló metódust**: jelezze, hogy az egyes partíciók milyen módon legyenek kiosztva az adatsorokkal:

    - **Partíció egyenletes**használata: ezzel a beállítással az egyes partíciókban megegyező számú sort helyezhet el. A kimeneti partíciók számának megadásához adjon meg egy egész számot a **kidobások számának megadása a szövegmezőbe egyenletes felosztáshoz** .

    - **Partíció testreszabott arányokkal**: ezzel a beállítással megadhatja az egyes partíciók méretét vesszővel tagolt listaként.

        Ha például három partíciót szeretne létrehozni, az első partíciót, amely az adatok 50%-át tartalmazza, valamint az adatok 25%-át tartalmazó fennmaradó két partíciót, akkor kattintson a **vesszővel elválasztott arányok listájára** , és írja be a következő számokat: @no_ _t_1_`.5, .25, .25`

        Az összes partíciós méret összegének pontosan 1 értékkel kell rendelkeznie.

        - Ha 1-nél **kisebb**számot ad meg, a fennmaradó sorok tárolására külön partíció jön létre. Ha például a .2 és a .3 értékeket adja meg, a rendszer egy harmadik partíciót hoz létre, amely az összes sor fennmaradó 50 százalékát tárolja.

        - Ha **több mint 1**értéket ad meg, akkor a folyamat futtatásakor hiba lép fel.

7. **Rétegzett felosztás**: válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok a felosztáskor rétegzett legyenek, majd válassza a _rétegek oszlopot_.

8. A folyamat futtatása.

    Ezzel a beállítással a modul több adatkészletet fog kiadni, a megadott szabályok alapján particionálva.

### <a name="use-data-from-a-predefined-partition"></a>Előre definiált partíció adatainak használata  

Ezt a beállítást akkor használja a rendszer, ha több partícióra osztott egy adatkészletet, és most további elemzésre vagy feldolgozásra szeretné betölteni az egyes partíciókat.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz.

2. Kapcsolja össze a **partíció és a minta**előző példányának kimenetével. A példánynak a hozzárendelés a **Kidobáshoz** lehetőséget kell használnia bizonyos számú partíció létrehozásához.

3. **Partíció vagy minta mód**: válassza a **pick fold**lehetőséget.

4. **Adja meg, hogy melyik összecsukott mintát**szeretné kiválasztani: válassza ki a használni kívánt partíciót az indexének beírásával. A partíciós indexek 1-alapúak. Ha például három részre osztotta az adatkészletet, akkor a partíciók az 1, 2 és 3 indexekkel rendelkezhetnek.

    Ha érvénytelen indexet adott meg, a rendszer tervezésidejű hibát jelez: "hiba 0018: az adatkészlet érvénytelen adatokat tartalmaz."

    Az adatkészlet összevonása mellett az adatkészletet két csoportba is elkülönítheti: a cél kidobását és minden mást. Ehhez írja be az egyetlen fold indexét, majd válassza ki a **kiválasztott fold komplement**(kiválasztás) elemét, hogy mindent, de a megadott foldban lévő összes információt beolvassa.

5. Ha több partíciót is dolgozik, az egyes partíciók kezeléséhez a **partíció és a minta** modul további példányait is fel kell vennie.

    Tegyük fel például, hogy a korábban particionált pácienseket négy, a kor használatával összehajtogatjuk. Az egyes összecsukás esetén a **partíció és a minta** modul négy példányát kell használnia, és mindegyikben az alább látható módon egy másikat kell kiválasztania. Nem helyes, hogy közvetlenül a hozzárendelést használja a kimenet **kidobásához** .  

    [![partíció és minta](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. A folyamat futtatása.

    Ezzel a beállítással a modul egyetlen adatkészlet kimenetét jeleníti meg, amely csak az ehhez a foldhoz rendelt sorokat tartalmazza.

> [!NOTE]
>  A fold megjelöléseit nem lehet közvetlenül megtekinteni; csak a metaadatokban jelennek meg.

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 