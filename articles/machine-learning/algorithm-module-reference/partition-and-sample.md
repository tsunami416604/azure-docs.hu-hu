---
title: 'Partíció és minta: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban található partíciós és minta modult mintavételezésre egy adatkészleten, vagy partíciókat hozhat létre az adatkészletből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 79cd6fe9156a785d82e303007d02ce58506dcfcf
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128550"
---
# <a name="partition-and-sample-module"></a>Partíció és minta modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal mintavételezést végezhet egy adatkészleten, vagy partíciókat hozhat létre az adatkészletből.

A mintavétel fontos eszköz a gépi tanulásban, mivel lehetővé teszi az adatkészlet méretének csökkentését az értékek azonos arányának fenntartása mellett. Ez a modul több kapcsolódó feladatot is támogat, amelyek fontosak a gépi tanulásban: 

- Az adatait több, azonos méretű alszakaszra oszthatja. 

    Használhatja a partíciókat a kereszt-ellenőrzéshez, illetve az esetek véletlenszerű csoportokba való hozzárendelését is.

- Az adatok csoportokba való elválasztása, majd egy adott csoport adatainak használata. 

    Az esetek különböző csoportokhoz való véletlenszerű hozzárendelését követően előfordulhat, hogy módosítania kell a csak egy csoporthoz társított szolgáltatásokat.

- Mintavételi. 

    Kinyerheti az adatok százalékos arányát, véletlenszerű mintavételezést alkalmazhat, vagy kiválaszthatja az adatkészlet kiegyensúlyozásához használandó oszlopot, és rétegzett mintavételezést hajthat végre az értékein.

- Kisebb adatkészlet létrehozása teszteléshez. 

    Ha sok adat van, érdemes csak az első *n* sort használni a kísérlet beállításakor, majd a modell létrehozásakor váltson a teljes adatkészlet használatára. A mintavétel használatával kisebb adatkészletet is létrehozhat a fejlesztéshez.

## <a name="configure-partition-and-sample"></a>Partíció és minta konfigurálása

Ez a modul több módszer használatát teszi lehetővé az adatpartíciók és a mintavételezések felosztásához. Először válassza ki a metódust, majd adja meg a metódus által igényelt további beállításokat.

- Átjáró
- Mintavételezés
- Hozzárendelés a kidobásokhoz
- Kidobás

### <a name="get-top-n-rows-from-a-dataset"></a>ELSŐ N sor beolvasása egy adatkészletből

Ezzel a móddal csak az első *n* sor kerül be. Ez a beállítás akkor lehet hasznos, ha kis számú sorban szeretné tesztelni a kísérletet, és nincs szükség arra, hogy az adat kiegyensúlyozott legyen, vagy bármilyen módon lehessen mintát venni.

1. Adja hozzá a **partíció és a minta** modult a kísérlethez a felületen, és kapcsolja össze az adatkészletet.  

2. **Partíció vagy minta mód**: Állítsa be ezt a beállítást a **Head**értékre.

3. **A kijelölni kívánt sorok száma**: Adja meg a visszaadni kívánt sorok számát.

    A megadott sorok számának nem negatív egész számnak kell lennie. Ha a kiválasztott sorok száma nagyobb, mint az adatkészlet sorainak száma, a rendszer a teljes adathalmazt adja vissza.

4. Futtassa a kísérletet.

A modul egyetlen adatkészlet kimenetét jeleníti meg, amely csak a megadott számú sort tartalmazza. A sorok mindig az adatkészlet elejétől lesznek beolvasva.

### <a name="create-a-sample-of-data"></a>Adatminta létrehozása

Ez a beállítás támogatja az egyszerű véletlenszerű mintavételezést vagy a rétegzett véletlenszerű mintavételt. Ez akkor hasznos, ha kisebb reprezentatív minta adatkészletet szeretne létrehozni teszteléshez.

1. Adja hozzá a **partíció és a minta** modult a kísérlethez, és kapcsolja össze az adatkészletet.

2. **Partíció vagy minta mód**: Állítsa be a **mintavételezést**.

3. **Mintavételezési sebesség**: Adjon meg egy 0 és 1 közötti értéket. Ez az érték határozza meg a forrás adatkészletből a kimeneti adatkészletbe foglalandó sorok százalékos arányát.

    Ha például csak az eredeti adatkészlet felét szeretné használni, írja be `0.5` , hogy a mintavételezési sebesség 50% legyen.

    A bemeneti adatkészlet sorait a rendszer a megadott arány szerint rendezi és szelektíven helyezi el a kimeneti adatkészletbe.

4. **Véletlenszerű magok mintavételezéshez**: Szükség esetén egy egész számot is megadhat, amelyet a mag értékként kíván használni.

    Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Az alapértelmezett érték 0, ami azt jelenti, hogy a rendszer órája alapján hozza létre a kezdő magot. Ez némileg eltérő eredményeket eredményezhet, amikor a kísérletet futtatja.

5. **Rétegzett felosztás mintavételezéshez**: Akkor válassza ezt a lehetőséget, ha fontos, hogy az adatkészlet sorait egyenletesen ossza el a mintavételezés előtt egy fontos oszlop alapján.

    A **mintavételezési kulcs oszlopához**válassza ki az adatkészlet felosztásához használni kívánt egyetlen *rétegbeli oszlopot* . Az adatkészlet sorai a következőképpen oszlanak meg:

    1. Az összes bemeneti sor (rétegzett) a megadott rétegek oszlop értékei szerint csoportosítva van.

    2. A sorok az egyes csoportokon belül vannak megkeverve.

    3. Az egyes csoportok szelektíven hozzáadódnak a kimeneti adatkészlethez, hogy megfeleljenek a megadott aránynak.


6. Futtassa a kísérletet.

    Ezzel a beállítással a modul egyetlen adatkészletet ad eredményül, amely az adatokat reprezentáló mintavételt tartalmaz. Az adatkészlet fennmaradó, nem mintavételezés alatt álló része nem kimenet. 

## <a name="split-data-into-partitions"></a>Az adatfelosztás partícióra

Akkor használja ezt a beállítást, ha az adatkészletet az adat részhalmazára szeretné osztani. Ez a beállítás akkor is hasznos, ha egyéni számú kitöltést szeretne létrehozni a kereszt-ellenőrzéshez, vagy több csoportba kell osztania a sorokat.

1. Adja hozzá a **partíció és a minta** modult a kísérlethez, és kapcsolja össze az adatkészletet.

2. A **partíció vagy a minta mód**esetében válassza **a hozzárendelés a**kidobáshoz lehetőséget.

3. **Csere használata a particionáláskor**: Válassza ezt a lehetőséget, ha azt szeretné, hogy a mintában szereplő sor visszakerüljön a sorok készletére a lehetséges újrafelhasználáshoz. Ennek eredményeképpen előfordulhat, hogy ugyanaz a sor több bedobáshoz is hozzá van rendelve.

    Ha nem használ helyettesítő elemet (ez az alapértelmezett beállítás), a rendszer nem helyezi vissza a mintavételezési sort a lehetséges újrahasznosítási sorok készletében. Ennek eredményeképpen minden egyes sor csak egy összecsukott elemhez rendelhető hozzá.

4. **Randomizált felosztás**:  Válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok véletlenszerűen legyenek kiosztva a kidobáshoz.

    Ha nem jelöli be ezt a beállítást, a sorok a ciklikus multiplexelés használatával lesznek kiosztva.

5. **Véletlenszerű mag**: Szükség esetén egy egész számot is megadhat, amelyet a mag értékként kíván használni. Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Ellenkező esetben az alapértelmezett 0 érték azt jelenti, hogy a rendszer véletlenszerű kezdő magot használ.

6. **A particionáló módszer meghatározása**: Adja meg, hogy az egyes partíciók milyen módon legyenek kiosztva az adatsorokkal:

    - **Egyenletes partíció**: Ezzel a beállítással megegyező számú sort helyezhet el az egyes partíciókban. A kimeneti partíciók számának megadásához adjon meg egy egész számot a kidobások számának megadása a szövegmezőbe **egyenletes felosztáshoz** .

    - **Partíció testreszabott arányokkal**: Ezzel a beállítással adhatja meg az egyes partíciók méretét vesszővel tagolt listaként.

        Ha például három partíciót szeretne létrehozni, az első olyan partícióval, amely az adatok 50%-át tartalmazza, és az adatok 25%-át tartalmazó fennmaradó két partíciót, akkor kattintson a **vesszővel** elválasztott arányok listájára, és írja be a következő számokat:`.5, .25, .25`

        Az összes partíciós méret összegének pontosan 1 értékkel kell rendelkeznie.

        - Ha 1-nél **kisebb**számot ad meg, a fennmaradó sorok tárolására külön partíció jön létre. Ha például a .2 és a .3 értékeket adja meg, a rendszer egy harmadik partíciót hoz létre, amely az összes sor fennmaradó 50 százalékát tárolja.

        - Ha **több mint 1**értéket ad meg, akkor a kísérlet futtatásakor hiba lép fel.

7. **Rétegzett felosztás**: Válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok a felosztáskor rétegzett legyenek, majd válassza a _rétegek oszlopot_.

8. Futtassa a kísérletet.

    Ezzel a beállítással a modul több adatkészletet fog kiadni, a megadott szabályok alapján particionálva.

### <a name="use-data-from-a-predefined-partition"></a>Előre definiált partíció adatainak használata  

Ezt a beállítást akkor használja a rendszer, ha több partícióra osztott egy adatkészletet, és most további elemzésre vagy feldolgozásra szeretné betölteni az egyes partíciókat.

1. Adja hozzá a **partíciót és a minta** modult a kísérlethez.

2. Kapcsolja össze a **partíció és a minta**előző példányának kimenetével. A példánynak a **hozzárendelés** a kidobáshoz lehetőséget kell használnia bizonyos számú partíció létrehozásához.

3. **Partíció vagy minta mód**: Válassza a **pick fold**lehetőséget.

4. **Határozza meg, hogy melyik kidobásból kell mintát**venni: Válassza ki a használni kívánt partíciót az indexének beírásával. A partíciós indexek 1-alapúak. Ha például három részre osztotta az adatkészletet, akkor a partíciók az 1, 2 és 3 indexekkel rendelkezhetnek.

    Ha érvénytelen indexet adott meg, a rendszer tervezési idejű hibát eredményez: "Hiba 0018: Az adatkészlet érvénytelen adatokat tartalmaz. "

    Az adatkészlet összevonása mellett az adatkészletet két csoportba is elkülönítheti: a cél kidobását és minden mást. Ehhez írja be az egyetlen fold indexét, majd válassza ki a **kiválasztott fold komplement**(kiválasztás) elemét, hogy mindent, de a megadott foldban lévő összes információt beolvassa.

5. Ha több partíciót is dolgozik, az egyes partíciók kezeléséhez a **partíció és a minta** modul további példányait is fel kell vennie.

    Tegyük fel például, hogy a korábban particionált pácienseket négy, a kor használatával összehajtogatjuk. Az egyes összecsukás esetén a **partíció és a minta** modul négy példányát kell használnia, és mindegyikben az alább látható módon egy másikat kell kiválasztania. Nem helyes, hogy közvetlenül a **hozzárendelést** használja a kimenet kidobásához.  

    [![Partíció és minta](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Futtassa a kísérletet.

    Ezzel a beállítással a modul egyetlen adatkészlet kimenetét jeleníti meg, amely csak az ehhez a foldhoz rendelt sorokat tartalmazza.

> [!NOTE]
>  A fold megjelöléseit nem lehet közvetlenül megtekinteni; csak a metaadatokban jelennek meg.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 