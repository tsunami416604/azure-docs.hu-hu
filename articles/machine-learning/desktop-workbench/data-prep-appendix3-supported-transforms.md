---
title: Adatok előkészítése az Azure Machine Learning adatok átalakítások használata |} Microsoft Docs
description: Ez a cikk érhető el az Azure Machine Learning adatok előkészítése átalakítások teljes listáját tartalmazza.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9cd6e6cdf8d8dc2b1872d6f7b76821122fd427fa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Adatok előkészítése az Azure Machine Learning adatok átalakítások használata

A *átalakítási* az Azure Machine Learning adatforrástól adott formátumban, az adatokat (például az adattípus módosítása) egy műveletet hajtja végre és majd létrehozza az adatokat az új formátumban. Minden egyes átalakító saját felület és a viselkedés rendelkezik. Több átalakítások együtt láncolás keresztül az adatfolyam lépéseit, által összetett és ismételhető átalakítások végezheti el az adatokat. Ez az az alapvető adatok előkészítése funkciókat biztosítanak.

Az alábbiakban érhető el az Azure Machine Learning átalakításokat. 

## <a name="column-selection"></a>Oszlop kiválasztása 
A listában szereplő átalakítások számos működik, vagy csak egy oszlop, vagy sok. Több oszlop kijelöléséhez használja a Ctrl billentyűt. Jelöljön ki egy oszlopot, használja a Shift billentyűt.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>A fő vagy a rács fejléc átalakítások 
Hozzáférés a főmenü átalakítások opciójából alakítja. Kattintson a jobb gombbal az oszlop nevét, a program az adatrács szerint kiválaszthatja a átalakításokat. Ha több oszlop van kijelölve, egy átalakítások menüben kattintson a jobb gombbal valamelyiket biztosít.

A helyi menüben csak a kiválasztott típusú érvényes átalakítások jeleníti meg. A Főmenü összes átalakításokat tesz lehetővé, de letiltja a nem megfelelő a kijelölt oszlopokban.

Kattintson a jobb gombbal egy cella egy szűk részhalmaza a környezetfüggő átalakítások áll rendelkezésre. Ezek átalakítások másolatok, cserélje le, és szűréséhez. Ezek a típus-kompatibilis, adatok, úgy, hogy egy oszlophoz eltérő számú oszlop beállítások.

## <a name="derive-column-by-example"></a>Oszlopok származtatása példa alapján
A transzformáció segítségével leszármazottja, egy vagy több meglévő oszlopok egy olyan új oszlop létrehozása. A transzformáció bemeneti ellenőrzi, hogy a bemeneti (kijelölt) oszlopok és a fenti példában, és majd határozza meg a kívánt kimeneti az új oszlopban. 

A transzformáció, válasszon egy vagy több oszlopot. Vegyen fel egy új (üres) származtatott oszlopot példa alapján. Írja be a kívánt a származtatott oszlop (feltéve, hogy más oszlopokat is meg van származtatva), és a "által példa" technológia megkísérli adja meg az oszlopban levő összes többi cellák példát. 

Bonyolult példákat szükség lehet adjon meg egynél több példát. Ehhez az szükséges, jelöljön ki egy másik cellát, és írja be egy másik példa.

A "Által példa" technológiát használja az a kijelölt oszlopok például szerinti meghatározásához. Nincs kiválasztott oszlop. Ehhez a transzformációhoz meghívásakor, ha az aktuális sor valamennyi cellájában szolgálnak. Csak a szükséges oszlopok kiválasztása pontosabb eredményekre vezet.

Kiválaszthatja az oszlopokat az átalakítás meghívása előtt. Az átalakítási szerkesztő megnyitásakor jelölőnégyzeteket minden oszlop tetején jelzi a bemeneteként kijelölt milyen oszlopokat. Adja hozzá, vagy a kijelölt oszlop eltávolítása az oszlopfejlécre a jelölőnégyzetek használatával.

Részletes leírását a **származtatott oszlop példa alapján** átalakító mellett további minták, lásd: [Célosztályából oszlop példa hivatkozással](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Példa alapján osztott oszlop
A transzformáció oszlop vesz igénybe, és a "Által példa" motor használata esetén megkísérli az adott oszlop felosztásához *n* más oszlopokat is. Az automatikus felosztása a későbbi létrehozott oszlopok futtathatja.

Részletes leírását a **osztott oszlop példa alapján** átalakító mellett további minták, lásd: [osztott oszlop példa hivatkozással](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>JSON kiterjesztése

A transzformáció lehetővé teszi egy érvényes JSON-szöveg oszloppal kibontásával több oszlopba.

További részletes leírását a **bontsa ki a JSON** átalakító mellett további minták, lásd: [bontsa ki a JSON-hivatkozás](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Példa oszlopok egyesítése

A transzformáció több oszlop értékeinek kombinálásával ad hozzá egy olyan új oszlop. 

Részletes leírását a **példa oszlopok egyesítése** átalakító mellett további minták, lásd: [egyesítése oszlopok példa hivatkozással](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Ismétlődő oszlop
A transzformáció lehetővé teszi egy vagy több kijelölt oszlopok és által biztosított minden más néven az eredeti oszlopnév származó pontos másolatát.

## <a name="text-clustering"></a>Fürtszolgáltatás szöveg 
A transzformáció úgy van kialakítva, hogy inkonzisztens értékek, amelyek azonos legyen, és egy csoportba.  

Az ehhez a transzformációhoz csak egy oszlop értékének a hasonló elemek elemzése és fürtök csoportosítva. Az egyes fürtökön nincs kanonikus érték, amellyel az érték, amely a felváltja a fürt példányainak és összes példány értéket. Teljes fürtök távolítható el, és a kanonikus érték szerkeszthető. Példányok távolíthatók el egy adott fürt. A csoport példányokhoz fürtbe használt hasonlóság pontszám küszöbérték szűrő módosítható.

Alapértelmezés szerint az ehhez a transzformációhoz minden fürt példány értéket lecseréli a kanonikus érték az adott fürtben, az új értékeket tartalmazó új oszlop létrehozása. Az adatfolyam a későbbi használatra is hozzáadhat a hasonlóság pontszám minden példány egy olyan új oszlop, (amelyek elnevezheti).

## <a name="replace-values"></a>Cserélje le az értékeket
A transzformáció segítségével egy karakterlánc lecserélheti egy másik. Lehet, hogy a forrás karakterláncot egy részleges karakterlánc- vagy egy teljes cella, és a helyettesítő csak egy oszlop, vagy sok is alkalmazható. A keresési karakterláncot támogatja, mint rendszeres karakterek különleges karaktereket is keres. 

## <a name="replace-na-values"></a>NA értékek cseréje
A különféle NA helyett használhatja a transzformáció (nincs, NA, null, NaN stb.), vagy üres karakterláncot cserélje való egységes egyetlen értéket. A transzformáció támogatja egy vagy több oszlopot, és csak felsorolt, ha egy oszlop van kijelölve. Nincs a fő átalakító menü nem szerepelnek oszlopok kijelölése esetén.

## <a name="replace-missing-values"></a>Cserélje le a hiányzó értékek
A transzformáció egyetlen értéket lecseréli hiányzó adatait, és egy vagy több oszlop támogatja. A transzformáció csak szerepel a listában, ha egy oszlop van kijelölve. Nincs a fő átalakító menü nem szerepelnek oszlopok kijelölése esetén.

## <a name="replace-error-values"></a>Hibaértékek cseréje
A transzformáció egyetlen értéket lecseréli hibákat, és egy vagy több oszlop támogatja. A transzformáció csak szerepel a listában, ha egy oszlop van kijelölve. Nincs a fő átalakító menü nem szerepelnek oszlopok kijelölése esetén.

## <a name="trim-string"></a>Trim karakterlánc
A transzformáció Eltávolítja a kezdő és záró "whitespace" karakterek (beleértve a szóközöket, lapokat, stb.) egy vagy több oszlop.

## <a name="adjust-precision"></a>Pontosság beállítása
A transzformáció beállítja az egy numerikus oszlopot a tizedesjegyek számát.

## <a name="rename-column"></a>Oszlop átnevezése
A transzformáció megváltozik a kijelölt oszlop neve. Is hívhat meg azt az oszlop fejlécére beágyazott oszlop nevére kattintanak.

## <a name="remove-column"></a>Oszlop eltávolítása
A transzformáció Eltávolítja a kijelölt oszlopokat, és csak egy oszlop, vagy sok működik. 

## <a name="keep-column"></a>Oszlop megtartása
A transzformáció csak a kijelölt oszlopok tartja, és csak egy oszlop, vagy sok működik.

## <a name="handle-path-column"></a>Elérési út oszlop kezelése
A fájl importálásakor, elérési út oszlop automatikusan hozzáadódik az adatkészlet által a **adatforrás hozzáadása** szolgáltatás. A teljesen minősített fájlnév összekapcsolja az adatok-készletének elérési útját tartalmazza. A transzformáció hozzáadása vagy eltávolítja a felesleges oszlopába adatkészletből.

## <a name="convert-field-type-to-numeric"></a>Mező típusa átalakítása numerikus
A transzformáció numerikus típusú oszlop módosításait. Az elválasztó nem egész adatokat is megadhat. Alapértelmezés szerint a transzformáció elválasztó nem figyelmeztet, ezért a **szerkesztése** menüpont meghívni a szerkesztőt. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="convert-field-type-to-date"></a>Az átalakítás Date értékre mező típusa
A transzformáció dátum típusú oszlop módosításait. Egy alapértelmezett dátum/idő formátumot használja, de a felülbírálható `strftime` irányelvek. Is is illesztenie egy rögzített dátum tartalmazó time típusú értékek.

Alapértelmezés szerint a transzformáció formátum nem figyelmeztet, ezért a **szerkesztése** menüpont meghívni a szerkesztő eredő lépésben. A transzformáció csak egy oszlop, vagy sok működik.

9-22-1677 között csak dátumok és a 4-11-2262 konvertálható dátum adattípusú.

## <a name="convert-field-type-to-boolean"></a>Logikai érték mező típusa átalakítása
A transzformáció igaz/hamis típusú oszlop módosításait. Támogatja a több érték leképezése igaz vagy hamis értéket, és szerkesztheti a leképezések. Egy konstans, amely leképezhető értékek, amelyek nem léteznek a igaz/hamis hozzárendelési táblák a is támogatja. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="convert-field-type-to-string"></a>Mező típusa karakterlánccá konvertálni
A transzformáció karakterlánc típusú oszlop változik, és csak egy oszlop, vagy sok működik.

## <a name="convert-unix-timestamp-to-datetime"></a>Az átalakítás DateTime értékre Unix időbélyeg
A transzformáció tisztában van azzal a Unix időbélyeg-formátumnak, még akkor is, ha az adatok karakterláncként jelzi. Az időbélyeg megfelelően átalakítja egy dátum adattípusú adatok előkészítése során.

## <a name="filter"></a>Szűrés
A transzformáció támogatja a szűrést, a sorok egy vagy több oszlopban levő értékek alapján. A szűrőfeltételek függnek adatokat az egyes oszlopok, karakterlánc típusú oszlopokra szűrhet "contains" vagy "nem tartalmaz." Számoszlopaira "nagyobb, mint" vagy "kisebb, mint" feltételek szerint szűrhetők.

Ha a **szűrő** átalakító meghívták a főmenüből, vagy kattintson a jobb gombbal egy oszlop fejlécében, a oszthatja ketté a hibás sorok be egy másik adatfolyam lehetőség áll rendelkezésre. A fő adatfolyam folytatódik a szűrt **a** sort, és egy új adatfolyam jön létre, amely szűrve voltak összes sort tartalmazza **kimenő**.

## <a name="use-first-row-as-headers"></a>Első sor használata fejlécként
A transzformáció elősegíti az első sor adatkészletből kell lennie az oszlopok neveit. Ha valamelyik oszlop nincs adatok első sorának, a név megadása automatikusan generált. Ehhez a transzformációhoz használatával azt jelenti, hogy az adatok importálását kezdődik 2. sor, a lehető legrövidebb időn belül. Attól függően a **Skip sorok** beállítás, az importálás elindíthatja akár további le a következő adatkészletben. Is használhatja, távolítsa el az előléptetést, és csak az automatikusan generált nevek használatával.

## <a name="join"></a>Csatlakozás
A transzformáció két adatfolyamok csatlakozás együtt használatos. Kiválaszthatja, hogy mely kimeneti eredményének csatlakozási: az illesztés, az "bal oldali" sikertelen az illesztés, vagy a "jobbra" sikertelen az illesztés sorát sorát sikeres sorát.

A **illesztési** átalakító egyetlen adatfolyam indul, és adott adatfolyam választja az illesztés oldalán. Ez akkor választhatja ki, a másik oldalon csatlakozási egy másik folyamatábrája kéri. A két adatfolyamok kijelölése után a transzformáció bemeneti való csatlakozáshoz meg kell jelölni az illesztés mindkét oldalán levő egyetlen oszlophoz van szükség. Ha az illesztés több oszlop van szüksége, egy származtatott oszlop létrehozása, csak az illesztés használt összefűzött, új oszlop létrehozása az átalakítás megkezdése előtt. A transzformáció bemeneti megkísérli a javaslat illesztési kulcsokat, és ha lehetséges, a származtatott oszlop automatikusan generálhat.

Az illesztés befejezése után, a csatlakozási Sankey diagramnézet akkor jelenik meg. Az egyes sorok szélességét jelöli, hogy a csatlakozási folyamat részeként való áthaladás sorok száma. A jobb oldali panelen, kiválaszthatja a sikeres sorok, a bal oldali sikertelen sorokat, vagy a jobb oldali sor kizárólag sikertelen. Választhatja azt is, egy fiókirodának.

## <a name="append-rows"></a>Sor hozzáfűzése
A transzformáció adatainak áttelepítését egy másik adatfolyam a jelenlegivel fűzi hozzá. Leképezi a pozíció az új sorok felvételének végén az oszlopok.

## <a name="append-columns"></a>Oszlopok hozzáfűzése
A transzformáció hozzáfűzi a jelenlegivel egy másik adatfolyam az új oszlopokat. Jobb hozzáadja az oszlopokat. Azt nem történt kísérlet egy sor adatok sor.

## <a name="summarize"></a>Összefoglalója
A transzformáció kiszámítja az összesítések egy vagy több kijelölt oszlopokban szereplő egyedi értékek kombinációjához. 

Az aggregátumok támogatott: száma, SUM, MIN, MAX, VARIANCIA, és SZÓRÁSNÁL. Az adott oszlop aggregátumok szűri a program csak az adattípus érvényes összesítések. Összesítések nem alkalmazható le vannak tiltva. Például nem számítható ki a KÖZÉPÉRTÉK a karakterlánc oszlop, de a minimális és maximális számítási lehet.

A szerkesztő nem érhető el, húzza az oszlopfejléc a panel bal felső ahol összesítse oszlopok jelennek meg. A panel egy hierarchikus, így mindent egymásba ágyazott összesítések. A szerkesztő panel jobb felső sarokban a mely összesítés oszlop alkalmazandó kiválasztására használjuk. Egyetlen oszlop egy vagy több alkalommal összesíthetők. Miután legalább egy összesítés van kiválasztva, a rács jobb alsó előnézetét jeleníti meg az adatokat összesített formában. 

A transzformáció csatlakoztatja, akkor egy `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Távolítsa el az ismétlődő
Egy vagy több kiválasztott oszlop ismétlődő értékek szerepelnek a transzformáció Eltávolítja a teljes sor. Nem szerepelnek oszlopok közül választ, ha a csak eltávolítani sorai azokat, ahol az összes oszlop értékei azonos.

## <a name="sort"></a>Rendezés
A transzformáció rendezi az adatokat. A rendezés csak egy oszlop, vagy sok hajtható végre, és minden oszlop rendezhetők (alapértelmezett) növekvő vagy csökkenő (amelyet a szerkesztő).

A transzformáció csatlakoztatja, akkor egy `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Kimeneti átalakítások
A következő átalakítások kimeneti adatokat. Több írási blokkok írja ki az adatok különböző időpontokban egyetlen folyamat lehet.

### <a name="write-to-csv"></a>CSV-fájlba írása
A transzformáció az adatfolyam a jelenlegi ponttól írja ki az adatok CSV formátumban. Azt szabályozza, a helyre (helyi vagy távoli) és a fájl körül különböző beállításokat.

### <a name="write-to-parquet"></a>Parquet írása
A transzformáció Parquet formában adatai ír, az aktuális pontjáról az adatfolyam. Azt szabályozza, a helyre (helyi vagy távoli) és a fájl körül különböző beállításokat.

## <a name="script-based-transforms"></a>Parancsprogram-alapú átalakítások
A következő átalakító nem található az alaptermékben működésének végrehajtásához használja a parancsfájl (Python). 

>[!NOTE]
>Mielőtt bármely ezek is megjelennek, olvassa el [használatával Python bővítési](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Oszlop (parancsfájl) hozzáadása
A transzformáció oszlopot ad hozzá egy Python-kifejezés az adatokat.
További információkért lásd: [való származtatás új oszlopok minta Python kódját](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Speciális szűrési (parancsfájl)
A transzformáció Python szintű Sorszűrő használja.
További információkért lásd: [példa szűrőkifejezéseket](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Átalakítás Adatfolyamblokk (parancsfájl)
A transzformáció Python vonatkozik a teljes adatkészletet.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

A transzformáció is alkalmazhat Python teljes adat partíción.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Írási Adatfolyamblokk (parancsfájl)
A transzformáció Python írja ki a teljes adatkészletet használ.
További információkért lásd: [minta Python a való származtatás új oszlopok](data-prep-appendix9-sample-destination-connections-python.md).



