---
title: 'Partíció és minta: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a partíció- és minta modul használata az Azure Machine Learning szolgáltatás egy adatkészleten mintavételi végrehajtásához vagy partíció létrehozásához az adatkészletből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029280"
---
# <a name="partition-and-sample-module"></a>Partíció és minta modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használja, egy adatkészleten mintavételi végrehajtásához vagy partíció létrehozásához az adatkészletből.

A machine Learning alkalmazásával, mivel lehetővé teszi, hogy az adatkészlet méretének csökkentése a méretarány értékek fenntartása mellett egyre népszerűbb eszköz adatokra mintavétel alkalmazva. Ez a modul támogatja a több kapcsolódó feladatok, amelyek fontosak a machine Learning szolgáltatásban: 

- Az adatok elosztják a részletezés az azonos méretű több struktúrát. 

    Használhat a partíciók kereszt-ellenőrzési vagy esetben véletlenszerű csoportokhoz rendelésére.

- Csoportok és a egy adott csoportból adatok majd használata az adatok elkülönítését. 

    Után véletlenszerűen különböző csoportokhoz rendelése esetek, szüksége lehet a szolgáltatások, amelyek csak egy csoportba tartozó módosítása.

- Mintavétel. 

    Százalékos arányát az adatok kinyerése, véletlenszerű mintavételi a alkalmazni, vagy válasszon egy oszlopot az adatkészlet terheléselosztási használni, és végezze el a rétegzett mintavételi az értékeket.

- Egy tesztelési kisebb adatkészlet létrehozását. 

    Ha nagy mennyiségű adatot, akkor érdemes használni, csak az első *n* sorok és a kísérlet, majd váltson a teljes adatkészlet használatával, ha a modell beállítása közben. Mintavételi használatával hozzon létre s kisebb adatkészlet használatra fejlesztés alatt.

## <a name="configure-partition-and-sample"></a>Konfigurálja a partíció- és minta

Ez a modul több módszert is támogat, az adatok megosztásának partíciókra vagy mintavételi. Először válassza ki a módszert, és ezután további beállítások megadása a metódus szükséges.

- Fej
- Mintavételezés
- Kevesebb modellrészt hozzárendelése
- Válassza ki a modellrészek

### <a name="get-top-n-rows-from-a-dataset"></a>FELSŐ N sorok beolvasása egy adatkészletből

Ez a mód használatával csak az első *n* sorok. Ez a beállítás akkor hasznos, ha szeretné tesztelni a sorok kis számú kísérlet, és nem kell az elosztott terhelésű vagy az adatgyűjtés az bármely módon az adatokat.

1. Adja hozzá a **partíciót és minta** modult a kísérletvászonra a felületén, és kapcsolódjon az adatkészlet.  

2. **Partíció vagy minta mód**: Ezt a beállítást **fő**.

3. **Válassza ki a sorok számát**: Írja be a visszaadandó sorok száma.

    A megadott sorok száma nem negatív egész számnak kell lennie. Ha a kijelölt sorok száma nem haladja meg az adatkészlet sorainak számával, a teljes adatkészletet ad vissza.

4. Futtassa a kísérletet.

A modul csak a megadott számú sort tartalmazó egyetlen adatkészlet jelenít meg. A sorok mindig olvasható az adatkészlet tetején.

### <a name="create-a-sample-of-data"></a>Hozzon létre egy minta az adatok

Ezt a beállítást támogatja az egyszerű véletlenszerű mintavételi vagy műanyaggal rétegezett véletlenszerű mintavétel. Ez akkor hasznos, ha szeretne létrehozni a teszteléshez kisebb reprezentatív minta adatkészlet.

1. Adja hozzá a **partíciót és minta** modult a kísérletvászonra, és kapcsolódjon az adatkészletet.

2. **Partíció vagy minta mód**: Állítsa a bestattempt értékre **mintavételi**.

3. **Mintavételi ráta**: Írjon be egy 0 és 1 közötti értéket. Ez az érték határozza meg a százalékos aránya a forrásadatkészlet szerepelnie kell a kimeneti adatkészlet sorait.

    Ha azt szeretné, csak az eredeti adathalmazból felét, írja be például `0.5` jelzi, hogy a mintavételi ráta legyen 50 %-át.

    A bemeneti adatkészlet sorait összekeverve, és szelektív elhelyezi a kimeneti adatkészlet a megadott arány alapján.

4. **Mintavételi véletlenszám-generálás kezdőértéke**: Szükség esetén írja be egy egész számot egy kezdeti érték adatokként.

    Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorokat kell osztani azonos módon minden alkalommal. Az alapértelmezett érték 0, a rendszeróra alapján jön létre, hogy a kiindulási kezdőérték jelentése. Ez némileg különböző eredményekhez vezethet a kísérlet futtatásakor.

5. **Mintavételi rétegzett split**: Válassza ezt a lehetőséget, ha fontos, hogy az adatkészlet sorainak egyenletesen kell osztani az egyes kulcsoszlop mintavétel előtt.

    A **mintavételi csoportosítási oszlopot**, válassza ki az egyetlen *strata oszlop* az adatkészlethez való osztás esetén használandó. Az adatkészlet sorainak majd megoszlanak a következőképpen:

    1. Az összes bemeneti sorok (rétegzett) a megadott strata oszlopban szereplő értékek szerint vannak csoportosítva.

    2. Sorok vannak összekeverve egyes csoportokban.

    3. Minden csoport külön-külön kerül a kimeneti adatkészlet a megadott arány kielégítése érdekében.


6. Futtassa a kísérletet.

    Ezzel a beállítással a modul jelenít meg, amely tartalmazza az adatok egy reprezentatív mintavételi egyetlen adatkészlet. A fennmaradó, unsampled része az adatkészlet nem kimeneti. 

## <a name="split-data-into-partitions"></a>Adatok felosztása partíciók

Használja ezt a beállítást, ha meg szeretné részekre az adatkészlet az adatok. Ez a beállítás akkor is hasznos, ha azt szeretné, hozzon létre egy egyéni modellrészt kereszt-ellenőrzési vagy a sorok felosztása több csoportot.

1. Adja hozzá a **partíciót és minta** modult a kísérletvászonra, és kapcsolódjon az adatkészletet.

2. A **partíció vagy minta mód**válassza **Modellrész hozzárendelése**.

3. **Használja a particionáláskor helyettesítő**: Válassza ezt a lehetőséget, ha azt szeretné, hogy újra üzembe a készlet újra sorainak a mintavételezett sor. Ennek eredményeképpen az ugyanabban a sorban hozzárendelni kívánt több modellrész.

    Ha nem használ helyettesítő (az alapértelmezett beállítás), a mintavételezett sor van nem kerüljenek vissza a sorainak újra a készlet. Ennek eredményeképpen csak egyetlen lépésből minden egyes sor rendelhető.

4. **Véletlenszerű split**:  Válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok modellrész véletlenszerűen hozzárendelését.

    Ha nem ezt a beállítást, a Ciklikus időszeleteléses metódussal modellrész sorok vannak rendelve.

5. **Véletlenszám-generálás kezdőértéke**: Szükség esetén írja be az egész használandó alapérték. Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorokat kell osztani azonos módon minden alkalommal. Ellenkező esetben az alapértelmezett érték a 0 azt jelenti, hogy egy véletlenszerű kezdőérték kezdve használható.

6. **Adja meg a partitioner módszert**: Megadhatja, hogy hogyan kell elosztani az egyes partíciók az adatokat használja ezeket a beállításokat:

    - **Partíció egyenletesen**: Használja ezt a beállítást, hogy az egyes partíciók ugyanannyi sort helyezze. A kimeneti partíciók számának megadásához írja be az egész szám a **egyenletesen ossza fel kevesebb modellrészt adjon meg** szövegmezőben.

    - **A testre szabott aránykülönbségek partíció**: Ez a beállítás használatával adja meg az egyes partíciók méretét, egy vesszővel tagolt lista formájában.

        Például három partíciókat, az első partíció 50 %-át az adatokat tartalmazó és a többi két partíció minden tartalmazó 25 %-át az adatokat, kattintson a **vesszővel elválasztott listája aránykülönbségek** szövegmezőbe, és Ezek a számok írja be: `.5, .25, .25`

        Hozzá kell adnia az összes partícióméretei összege legfeljebb pontosan 1.

        - Ha ad meg, hogy számok, akár hozzáadott **1-nél kisebb**, egy külön partíciót hoz létre, a fennmaradó sorok tárolásához. Például írja be a.2 és.3, harmadik értéket partíció jön létre, amely az összes sor fennmaradó 50 %-os tárol.

        - Ha ad meg, hogy számok, akár hozzáadott **1-nél több**, hibaüzenet jelenik meg, a kísérlet futtatásakor.

7. **Rétegzett split**: Válassza ezt beállítást, ha azt szeretné, a sorok műanyaggal kell rétegezett mikor felosztása, és válassza a _strata oszlop_.

8. Futtassa a kísérletet.

    Ezzel a beállítással a modul jelenít meg több adatkészlet, a megadott szabályok használata particionált.

### <a name="use-data-from-a-predefined-partition"></a>Egy előre meghatározott partíció származó adatok használata  

Ezt a beállítást használják egy adatkészlet rendelkezik osztani több partíciót, és most szeretné betölteni mindegyik partíció viszont a további elemzés vagy feldolgozásra.

1. Adja hozzá a **partíciót és minta** modult a kísérletvászonra.

2. Csatlakoztathatja azt egy korábbi példánya kimenete **partíciót és minta**. Annak a példánynak kell használta a **Modellrész hozzárendelése** létrehozni az egyes partíciók száma.

3. **Partíció vagy minta mód**: Válassza ki **Modellrészek válasszon**.

4. **Adja meg, amely lehet mintát venni a modellrészek**: Válasszon ki egy partíciót, írja be az index használatához. Partíció indexek olyan 1-alapú. Például ha az adatkészlet osztva három részből áll, a partíciók kellene az indexek, 1, 2 és 3.

    Írja be az index érvénytelen értéket, ha egy tervezési idejű hiba jelenik meg: "Hiba 0018: Adatkészlet érvénytelen adatokat tartalmaz."

    Mellett az adatkészlet csoportosítást modellrész, elkülönítheti a az adatkészlet két csoportra: egy cél modellrészek, és minden mást. Ehhez írja be az index egyetlen lépésből, és válassza ki a lehetőséget, hogy **válassza ki a kijelölt modellrészek halmaza**, a megadott modellrészek mindent, de az adatok beolvasásához.

5. Több partíció dolgozik, ha hozzá kell adnia a további példányait a **partíciót és minta** modul egyes partíciók kezelésére.

    Például tegyük fel, korábban particionált betegek használatával kor négy modellrész be. Négy példányt kell minden egyes modellrészek dolgozni, a **partíciót és minta** modult, és mindegyik esetében választhatja ki egy másik modellrészek alább látható módon. Már nem helyes-e használni a **Modellrész hozzárendelése** kimeneti közvetlenül.  

    [![Partíció és minta](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Futtassa a kísérletet.

    Ezzel a beállítással a modul csak a modellrészek rendelt sorokat tartalmazó egyetlen adatkészlet jelenít meg.

> [!NOTE]
>  Nem tekintheti meg a modellrészek jelölések közvetlenül; Ezek meg adva, csak a metaadatok között.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 