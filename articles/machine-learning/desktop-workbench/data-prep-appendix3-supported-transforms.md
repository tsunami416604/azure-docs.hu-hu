---
title: Adatok átalakítások használata az Azure Machine Learning adat-előkészítési |} A Microsoft Docs
description: A cikk ismerteti az elérhető az Azure Machine Learning adat-előkészítési átalakítások teljes listáját.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: c47d9bc72ad1d197b5030076456f9dc9efc422bc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644996"
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Az Azure Machine Learning adat-előkészítési adatok átalakítások használata

A *átalakítása* az Azure Machine Learning használ fel az adatokat egy adott formátumban, az adatok (például az adattípus módosítása) egy olyan műveletet hajt végre, és majd hoz létre az új formátum az adatok. Minden átalakítási saját felület és a viselkedés rendelkezik. Láncolás – több átalakítások együtt az adatfolyam a lépések végrehajtásával, elvégezhet az adatokon összetett és megismételhető átalakításokat. Ez a alapvető fontosságú adatok előkészítési funkciókat.

Az alábbiakban az Azure Machine Learningben elérhető átalakításokat. 

## <a name="column-selection"></a>Oszlop kiválasztása 
Az átalakítások, a listában szereplő számos csak egy oszlop, vagy számos működik. Több oszlop kijelöléséhez használja a Ctrl billentyűt. Az oszlopok olyan tartományt válasszon, használja a Shift billentyűt.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>A főmenü vagy a rács fejléc-ből 
Hozzáférés a főmenü az átalakítások közül alakítja át. Kattintson a jobb gombbal az oszlop neve, az adatrácsban lehetőség kiválasztásával a átalakításokat. Ha több oszlop van kiválasztva, kattintson a jobb gombbal valamelyik biztosít egy átalakítások menü.

A helyi menüben csak a kiválasztott adattípus érvényes átalakítások jeleníti meg. A főmenü minden átalakítások kínál, de letiltja a nem megfelelő a kijelölt oszlopokban.

Környezetfüggő átalakítások részhalmazát kattintson a jobb gombbal egy cella érhető el. Ezek az átalakítások, csere, másolás és szűréséhez. Ezek a adatok típusa a DNSSEC használatát, így egy oszlopot a beállításai eltérnek a egy karakterláncokat tartalmazó oszlopot.

## <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján
Az átalakítási használatával leszármazottja, egy vagy több meglévő oszlopok egy olyan új oszlop létrehozása. Az átalakítás a bemeneti (kijelölt) oszlopok és a fenti példában, és ezután határozza meg a kívánt kimenetet az új oszlopban. 

Az átalakítási, válasszon egy vagy több oszlop. Adjon hozzá egy új (üres) származtatott oszlop példa alapján. Írja be egy példát, hogy milyen meg szeretné tekinteni a származtatott oszlop (feltéve, hogy a többi oszloptól leszármaztatott), és a "által példaként" technológia próbál adja meg az oszlopban az összes többi cella. 

Bonyolult példák szüksége lehet adjon meg egynél több példát. Ehhez jelöljön ki egy másik cellát, és írja be egy másik példát.

A "Által példaként" technológia a kijelölt oszlopok használja példaként szerinti határozza meg. Nincs kiválasztott oszlop. az átalakító meghívásakor, ha az aktuális sor összes cellát szolgálnak. Csak a szükséges oszlopokat kiválasztásával pontosabb eredmények vezet.

Az átalakítás meghívása előtt válassza ki az oszlopokat. Az átalakítási szerkesztő megnyitásakor jelölőnégyzeteket minden oszlop tetején adja meg, milyen oszlop ki van jelölve, bemenetként. Adja hozzá, vagy oszlopok eltávolítása a kijelölt jelölőnégyzetek használatával az oszlopfejlécre.

Részletesebb leírását a **származtatott oszlopok példa alapján** átalakítás, további minták, valamint lásd [példa referencia származtatása](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Oszlopok felosztása példa alapján
Az átalakítás vesz igénybe egy meglévő oszlopára, és a "Által példaként" motor használatával próbál az adott oszlop felosztása *n* többi oszlopot. Az ezt követő létrehozott oszlopok az automatikus split futtathatja.

További részletes ismertetése a **oszlopok felosztása példa alapján** átalakítás, további minták, valamint lásd [oszlopok felosztása példa alapján](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>JSON kiterjesztése

Az átalakítás lehetővé teszi, hogy több oszlopra érvényes JSON-szöveget tartalmazó oszlop kibontásával.

Részletesebb leírását a **bontsa ki a JSON** átalakítás, további minták, valamint lásd [bontsa ki a JSON-referenciáját](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Példa alapján oszlopok egyesítése

Az átalakítási érték több oszlopból kombinálásával ad hozzá egy olyan új oszlop. 

További részletes ismertetése a **oszlopok egyesítése példa alapján** átalakítás, további minták, valamint tekintse meg [oszlopok egyesítése példa alapján](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Ismétlődő oszlop
Az átalakítás lehetővé teszi egy vagy több kijelölt oszlopok és biztosítja az egyes egy új nevet az eredeti oszlopnév származó pontos másolatát.

## <a name="text-clustering"></a>Fürtszolgáltatás szöveg 
Az átalakítási célja inkonzisztens értékeit kell azonosnak lennie, és csoportosíthatja őket.  

Az ezen átalakítási csak egy oszlop értékei arclistát elemzése és fürtök vannak csoportosítva. Az egyes fürtökön nincs kanonikus értéket, amely az érték, amely kiváltja a fürtben szereplő összes példányt, és minden példány értékeit. Teljes fürtök távolítható el, és a canonical értéket szerkeszteni. Példányok egy adott fürt eltávolíthatók. A hasonlóság pontszám küszöbérték szűrőt, rendelkezik egy fürtbe csoport példányai is módosítható.

Alapértelmezés szerint a transzformáció lecseréli az összes fürt példány értékeket, hogy a fürt, az új értékeket tartalmazó új oszlop létrehozása, a canonical értékű. Egy olyan új oszlop (amelyek elnevezheti) használható az adatok a folyamat későbbi részében is megteheti a hasonlóság pontszám, minden példány esetében.

## <a name="replace-values"></a>Értékek lecserélése
Az átalakítási használatával egy karakterlánc lecserélheti egy másik. Lehet, hogy a forrás-karakterlánc részleges karakterlánc- vagy a teljes cella, és a helyettesítő csak egy oszlop, vagy számos is alkalmazható. A keresési karakterláncot a keresés, mint a normál karakterek speciális karaktereket is támogatja. 

## <a name="replace-na-values"></a>NA értékek lecserélése
A különféle formában előforduló NA helyett használhatja a transzformáció (Na, NA, null, NaN, stb.), vagy üres karakterláncok, így konzisztens egyetlen értéket lecseréli. Az átalakítási támogatja egy vagy több oszlop, és csak felsorolt, ha egy oszlop van kijelölve. Ez nem szerepel a fő átalakító menü amikor nincs kiválasztott oszlop.

## <a name="replace-missing-values"></a>Cserélje le a hiányzó értékek
Az átalakítási hiányzó adatokra cseréli egyetlen értéket, és a egy vagy több oszlop támogatja. Az átalakítási csak szerepel, ha egy oszlop van kijelölve. Ez nem szerepel a fő átalakító menü amikor nincs kiválasztott oszlop.

## <a name="replace-error-values"></a>Hibás értékek cseréje
Az átalakítási egyetlen értéket lecseréli az hibákat, és a egy vagy több oszlop támogatja. Az átalakítási csak szerepel, ha egy oszlop van kijelölve. Ez nem szerepel a fő átalakító menü amikor nincs kiválasztott oszlop.

## <a name="trim-string"></a>Trim karakterlánc
Az átalakítási eltávolítja a kezdő és záró "elválasztó karakter" karakterek (beleértve a szóközöket, lapok, stb.) egy vagy több oszlopból.

## <a name="adjust-precision"></a>Állítsa be a pontosság
Az átalakítási állítja be a számú tizedesjegyre kerekítenek egy numerikus oszlophoz.

## <a name="rename-column"></a>Oszlop átnevezése
Az átalakítási módosítja a kiválasztott oszlop neve. Is hajthatók, soron belüli, az oszlop fejlécére kattintva az oszlop neve.

## <a name="remove-column"></a>Odebrat Sloupec
Az átalakítási eltávolítja a kijelölt oszlopokat, és csak egy oszlop, vagy számos működik. 

## <a name="keep-column"></a>Oszlop megtartása
Az átalakítási tartja csak a kijelölt oszlopokat, és csak egy oszlop, vagy számos működik.

## <a name="handle-path-column"></a>Elérési út oszlop kezeléséhez
A fájl importálásakor egy elérési út oszlop automatikusan hozzáadódik az adatkészlet által a **adatforrás hozzáadása** funkció. A teljes fájlnév alkotó a data-készletének elérési útját tartalmazza. Az átalakítási ad hozzá, vagy az adathalmaz távolít el, hogy további oszlop.

## <a name="convert-field-type-to-numeric"></a>Mezőtípus átalakítása numerikus
Az átalakítási numerikus típusú oszlop változik. Az elválasztó nem egész adatokat is megadhat. Alapértelmezés szerint a transzformáció az elválasztó nem kér, ezért a **szerkesztése** menüpont a szerkesztő elindításához. Az átalakítási csak egy oszlop vagy sok működik.

## <a name="convert-field-type-to-date"></a>Mezőtípus átalakítása dátuma
Az átalakítás date típusú oszlop változik. Egy alapértelmezett dátum/idő formátumot használja, de használatával felülbírálhatók `strftime` irányelveknek. Akkor is egy rögzített dátum-idő értékek jogosultságokat is.

Alapértelmezés szerint az átalakítási formátuma nem kér, ezért a **szerkesztése** menüpont eredő lépésben a szerkesztő elindításához. Az átalakítási csak egy oszlop vagy sok működik.

Csak a 9-22-1677 közötti dátumok és 4-11-2262 konvertálható dátum adattípusú.

## <a name="convert-field-type-to-boolean"></a>Mezőtípus átalakítása logikai érték
Az átalakítási igaz/hamis típusú oszlop változik. Támogatja a több érték leképezése igaz vagy hamis értéket, és ezeket a leképezéseket szerkesztheti. Támogatja egy állandó, amelyhez leképezheti a true/false hozzárendelési táblák nem létező értékeket is. Az átalakítási csak egy oszlop vagy sok működik.

## <a name="convert-field-type-to-string"></a>Mezőtípus átalakítása karakterlánc
Az átalakítási karakterlánc típusú oszlop változik, és csak egy oszlop, vagy számos működik.

## <a name="convert-unix-timestamp-to-datetime"></a>Az átalakítás DateTime értékre Unix-időbélyege
Az átalakítási tisztában van azzal a Unix-időbélyeg formátuma, még akkor is, ha az adatok karakterláncként jelzi. Átalakítja a timestamp megfelelően az adat-előkészítési dátum típusra.

## <a name="filter"></a>Szűrés
Az átalakítási támogatja a szűrést, a sorok egy vagy több oszlopban szereplő értékek alapján. A szűrő feltételeinek függnek az egyes oszlopok adattípusát, úgy is szűrheti a karakterlánc típusú oszlopokra "contains" vagy "nem tartalmaz." Numerikus oszlopok "nagyobb" vagy "kisebb, mint" feltételek szerint szűrhetők.

Ha a **szűrő** átalakító meghívásainak fő menüjéből, vagy a jobb gombbal az oszlop fejlécében, egy másik data flow-bA a hibás sorok elágazásra lehetőség áll rendelkezésre. A fő adatok folyamat továbbra is a szűrt **a** sort, és a egy új adatfolyamot jön létre, amely tartalmazza az összes sort is szűrt **ki**.

## <a name="use-first-row-as-headers"></a>Első sor használata fejlécként
Az átalakítási elősegíti az első sor adatkészletből kell az oszlopok neveit. Egyes oszlopok nincs adatok első sora, ha egy név automatikusan létrehozott. Az átalakítási használata, az azt jelenti, hogy az adatok importálása a lehető leghamarabb ki kezdődik, 2. sor. Attól függően, a **sorok kihagyása** állítja, az importálás elindításához jobbat le az adatkészlet. Emellett ezzel eltávolítja az előléptetés, és csak az automatikusan generált nevek használata.

## <a name="join"></a>Csatlakozás
Az átalakítási csatlakozzon a két adatfolyam-gyűjteményre együtt használatos. Kiválaszthatja, hogy melyik kimenete az illesztés eredménye: a "bal oldali" sort a csatlakozás vagy a "megfelelő" sikertelen az illesztés sorait sikertelen csatlakozás sikeres sorait.

A **illesztési** átalakító egyetlen folyamat indul, és az illesztés egyik oldalán, az adatfolyam választja. Ezután kéri, hogy egy másik adatok áramlását a Csatlakozás másik oldalán válassza ki. Miután kiválasztotta a két folyamatok, az átalakítás csak egy oszlop, ki kell választani a csatlakozás az illesztés mindkét oldalán van szükség. Ha a join több oszlop van szüksége, származtatott oszlop létrehozása, hozzon létre egy új, összefűzött oszlop csak a join használható az átalakítás előtt. Az átalakítás próbál meg javaslat illesztési kulcsokat, és ha lehetséges, hozzon létre automatikusan a származtatott oszlop.

A csatlakozás után a JOIN Sankey diagram nézet jelenik meg. A sorok egymáshoz képest relatív szélességét a join folyamat része Mozgatott sorok számát jelöli. A jobb oldali panelen, kiválaszthatja a sikeres sorok, a bal oldali sort, vagy sikertelen kizárólag a sor jobb sikertelen. Azt is beállíthatja egy önálló fiókirodai.

## <a name="append-rows"></a>Sorok hozzáfűzése
Az átalakítási fűzi hozzá a másik az adatfolyam adatainak a jelenlegivel. Az oszlopok és az új sorok hozzáadása a végén alapján vannak leképezve.

## <a name="append-columns"></a>Oszlop hozzáfűzése
Az átalakítási fűzi hozzá a jelenlegivel új oszlopokat az adatok egy másik folyamat. Az új oszlopok hozzáadása a jobb oldalon. Nem, próbál adatokat sorok. sor.

## <a name="summarize"></a>Összegzés
Az átalakítási kiszámítja az összesítések egy vagy több kijelölt oszlopokban szereplő egyedi értékek kombinációjának. 

Az aggregátumok támogatott: száma, SUM, MIN, MAX, KÖZÉPÉRTÉK, VARIANCIA és szórás. Egy adott oszlop összesítések listája csak a alkalmazni az adattípus összesítések van szűrve. Összesítések nem alkalmazható le vannak tiltva. Például már nem egy karakterláncoszlop KÖZÉPÉRTÉKÉT számítja ki, de a minimális és a maximális számítási lehetséges.

A szerkesztő nem érhető el, húzza az oszlop fejlécére a panel tetején balra, hol jelennek meg, hogy az oszlopok. Ezen a panelen, hierarchikus, így az egymásba ágyazott összesítések teheti. A szerkesztő panel jobb felső sarokban válassza ki egy oszlopot a alkalmazni melyik összesítés szolgál. Egyetlen oszlop egy vagy több alkalommal összesíthetők. Miután legalább egy összesítés van kiválasztva, a rácshoz jobb alsó előnézetét jeleníti meg az adatokat összesített formában. 

Ez transformaci csatlakoztatja egy `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Ismétlődések eltávolítása
Az átalakítási eltávolítja az egész sort, ha található egy vagy több kiválasztott oszlop ismétlődő értékeket. Nincsenek oszlopok közül választ, a csak sorok eltávolítva-e azokról, ahol az oszlopértékeket azonosak.

## <a name="sort"></a>Rendezés
Az átalakítási rendezi az adatokat. A rendezés csak egy oszlop, vagy számos teheti meg, és minden oszlophoz lehet rendezni (alapértelmezett) növekvő vagy csökkenő (amely a szerkesztőből módosítható).

Ez transformaci csatlakoztatja egy `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Kimeneti átalakítások
A következő átalakítások kimeneti adatokat. Több írási blokkok írja az adatokat különböző időpontokban ki egyetlen folyamat lehet.

### <a name="write-to-csv"></a>CSV-fájlba írása
Az átalakítási ír ki az adatok CSV formátumban az aktuális időponthoz az adatfolyam. Azt szabályozza, a hely (helyi vagy távoli) és a fájl körül különböző beállításokat.

### <a name="write-to-parquet"></a>Írni a parquet eszközökben
Az átalakítási adatok Parquet formátumban ír, az adatfolyam az aktuális időponthoz. Azt szabályozza, a hely (helyi vagy távoli) és a fájl körül különböző beállításokat.

## <a name="script-based-transforms"></a>Parancsprogram-alapú átalakítások
A következő átalakítások parancsfájllal (Python) funkciók, amelyek nem található az alaptermékben végrehajtásához. 

>[!NOTE]
>Bármely, ezek az átalakítások használata előtt olvassa el a [Python használó kiterjeszthetőség](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>(Szkript) oszlop hozzáadása
Az átalakítási oszlopot ad hozzá az adatokat egy Python-kifejezés használatával.
További információkért lásd: [minta a Python-kód az új oszlopok származtatás](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Speciális szűrő (szkript)
Az átalakítási segítségével írhat egy Python-sor oldalszintű szűrő.
További információkért lásd: [példa szűrőkifejezésekben](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>(Szkript) adatfolyam átalakítása
Az átalakítás a teljes adatkészlet Python vonatkozik.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

Az átalakítási is alkalmazhat Python teljes partíción.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Írás (szkript) adatfolyam
Az átalakítási Python írja ki a teljes adatkészletet használja.
További információkért lásd: [minta Python for új oszlopok származtatás](data-prep-appendix9-sample-destination-connections-python.md).



