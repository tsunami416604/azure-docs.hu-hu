---
title: "Adatok előkészítése az Azure Machine Learning adatok átalakítások használata |} Microsoft Docs"
description: "Ez a cikk érhető el az Azure Machine Learning adatok prep átalakítások teljes listáját tartalmazza"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: 4331bb4a16d56f027dd63a97868822fa6ecc92d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Adatok előkészítése az Azure Machine Learning adatok átalakítások használata

A *átalakítási* az Azure Machine Learning adatforrástól adott formátumban, az adatokat (például az adattípus módosítása) egy műveletet hajtja végre és majd létrehozza az adatokat az új formátumban. Minden egyes átalakító saját felület és a viselkedés rendelkezik. Akkor is láncolt több átalakítások együtt keresztül lépéseket az adatfolyamban, lehetővé téve az adatokon összetett és ismételhető átalakításokat hajthattak végre. Ez az az alapvető adatok előkészítése funkciókat biztosítanak.

Elérhető az Azure Machine Learning átalakítások listája a következő: 

## <a name="column-selection"></a>Oszlop kiválasztása 
A következő listában szereplő átalakítások számos csak egy oszlop, vagy sok működnek. Több oszlop kijelöléséhez használja a **Ctrl** ; kulcs, vagy jelöljön ki egy oszlopot, használja a **Shift** kulcs.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Átalakítja az fő menü és/vagy a rács fejlécéből 
A átalakítások parancsa a fő átalakítások is elérhetők. Kattintson a jobb gombbal az oszlop nevét, a program az adatrács átalakítások is lehet kiválasztani. Ha több oszlop van kijelölve, majd kattintson a jobb gombbal valamelyiket biztosít egy átalakítások menüben.

Csak a kiválasztott típusú érvényes átalakítások érhető el a helyi menüben. A Főmenü összes átalakításokat tesz lehetővé, de letiltja a átalakítások, amely nem kapcsolódik a kijelölt oszlopokat a.

Kattintson a jobb gombbal egy cella egy szűk részhalmaza a környezetfüggő átalakítások áll rendelkezésre. Ezek átalakítások másolatok, cserélje le, és szűréséhez. Ezek átalakítások adattípus tisztában, úgy, hogy egy oszlophoz eltérő számú oszlop beállítások.

## <a name="derive-column-by-example"></a>Példa alapján oszlop származtatni
A transzformáció lehetővé teszi, hogy a leszármazottja egy vagy több meglévő oszlop, mint egy olyan új oszlop létrehozása. A átalakítások ellenőrzi, hogy a bemeneti (kijelölt) oszlopok és a fenti példában, és majd határozza meg az új oszlopba desire kimenetét. 

A transzformáció, válasszon egy vagy több oszlopot. Vegyen fel egy új (üres) származtatott oszlopot példa alapján. Írja be a származtatott oszlop (feltéve, hogy más oszlopokat is meg van származtatva) és a "által példa" technológia megkísérli adja meg az oszlopban levő összes többi cellák szeretne példát. 

Bonyolult példákért lehet szükséges adjon meg egynél több példát. Ehhez az szükséges, jelöljön ki egy másik cellát, és írja be egy másik példa.

A "Által példa" technológiát használja az a kijelölt oszlopok például szerinti meghatározásához. Nincs kiválasztott oszlop. Ehhez a transzformációhoz meghívásakor, ha az aktuális sor valamennyi cellájában szolgálnak. Csak a szükséges oszlopok kiválasztása pontosabb eredményekre vezet.

Kiválaszthatja az oszlopokat az átalakítás meghívása előtt. Az átalakítási szerkesztő el lett indítva, miután a jelölőnégyzeteket minden oszlop tetején jelzi, bemeneteként kiválasztott milyen oszlop. Adja hozzá, vagy a kijelölt oszlop eltávolítása az oszlopfejlécre a jelölőnégyzetek használatával.

Részletesebb leírását **származtatott oszlop szerint példa** átalakító, további mintákat, valamint tekintse meg: [Célosztályából oszlop alapján – példa](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Példa alapján osztott oszlop
A transzformáció oszlop vesz igénybe, és a "Által példa" használata megkísérli az adott oszlop felosztásához  *n*  más oszlopokat is. Az automatikus vegyes futtassa a következő létrehozott oszlopokon lehetőség.

Részletesebb leírását **minta alapján oszlop felosztása** átalakító mellett további minták, lásd: [osztott oszlop alapján – példa](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>JSON kiterjesztése

A transzformáció lehetővé teszi egy érvényes JSON-szöveg oszloppal kibontásával több oszlopba.

Részletesebb leírását **bontsa ki a JSON** átalakító mellett további minták, lásd: [bontsa ki a JSON-hivatkozás](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Példa oszlopok egyesítése

A transzformáció lehetővé teszi, hogy adjon hozzá egy olyan új oszlop több oszlop értékeinek kombinálásával. 

Részletesebb leírását **példa oszlopok egyesítése** átalakító, további mintákat, valamint tekintse meg: [egyesítése oszlopok alapján – példa](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Ismétlődő oszlop
A transzformáció lehetővé teszi egy vagy több kijelölt oszlopok és által biztosított minden más néven az eredeti oszlopnév származó pontos másolatát.

## <a name="text-clustering"></a>Fürtszolgáltatás szöveg 
A transzformáció úgy van kialakítva, hogy inkonzisztens értékek, amelyek azonos legyen, és egy csoportba.  

A transzformáció használatakor csak egy oszlop értékének hasonlóság az elemzett és fürtök csoportosítva. Az egyes fürtökön kanonikus értéket, amely az értéket, amelyet a felváltja a fürt összes példánya, és a példány értéke van. Teljes fürtök távolítható el és szerkeszthetik a kanonikus érték. Példányok távolíthatók el egy adott fürt. Emellett a hasonlóság pontszám küszöbérték szűrő csoport példányokhoz fürtbe használt módosítható.

Alapértelmezés szerint az ehhez a transzformációhoz minden fürt példány értéket lecseréli a kanonikus érték az adott fürtben, az új értékeket tartalmazó új oszlop létrehozása. A rendszer is hozzá lehet adni a hasonlóság pontszám, minden egyes előfordulás esetén egy olyan új oszlop, (amelyek elnevezheti) a használatával engedélyezhető az adatfolyam később.

## <a name="replace-values"></a>Cserélje le az értékeket
A transzformáció lehetővé teszi, hogy egy karakterlánc másik le kell cserélni. Lehet, hogy a forrás karakterláncot egy részleges karakterlánc- vagy egy teljes cella; a csere csak egy oszlop, vagy sok is alkalmazható. A keresési karakterláncot támogatja a speciális karakterek, valamint rendszeres karakterek keresése. 

## <a name="replace-na-values"></a>NA értékek cseréje
A transzformáció lehetővé teszi, hogy a különböző különféle NA (nincs, NA, null, NaN stb.) vagy az üres karakterláncokat, hogy azok összhangban egyetlen értéket kell cserélni. A transzformáció támogatja az egy vagy több oszlopot. A transzformáció csak egy oszlop van kijelölve, és nincs jelen a fő átalakító menü amikor nincs kiválasztott oszlop szerepel.

## <a name="replace-missing-values"></a>Cserélje le a hiányzó értékek
A transzformáció hiányzó adatok egyetlen értéket lecseréli. A transzformáció támogatja az egy vagy több oszlopot. A transzformáció csak egy oszlop van kijelölve, és nincs jelen a fő átalakító menü amikor nincs kiválasztott oszlop szerepel.

## <a name="replace-error-values"></a>Hibaértékek cseréje
A transzformáció hibák egyetlen értéket lecseréli. A transzformáció támogatja az egy vagy több oszlopot. A transzformáció csak egy oszlop van kijelölve, és nincs jelen a fő átalakító menü amikor nincs kiválasztott oszlop szerepel.

## <a name="trim-string"></a>Trim karakterlánc
A transzformáció Eltávolítja a kezdő és záró "whitespace" karakterek (szóközöket, lapokon *stb*), egy vagy több oszlop.

## <a name="adjust-precision"></a>Pontosság beállítása
A transzformáció lehetővé teszi egy numerikus oszlopot a tizedesjegyek számának beállítása.

## <a name="rename-column"></a>Oszlop átnevezése
A transzformáció megváltozik a kijelölt oszlop neve. A transzformáció is meghívott beágyazottan történjen-e az oszlop fejlécére kattintva az oszlop neve.

## <a name="remove-column"></a>Oszlop eltávolítása
A transzformáció Eltávolítja a kijelölt oszlop. A transzformáció csak egy oszlop, vagy sok működik. 

## <a name="keep-column"></a>Oszlop megtartása
A transzformáció tartja csak a kijelölt oszlop. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="handle-path-column"></a>Elérési út oszlop kezelése
A fájl importálásakor elérési út oszlop automatikusan hozzáadódik a dataset adatforrás hozzáadása varázslóval. A teljesen minősített fájl elérési útját a dataset összekapcsolja tartalmaz. A transzformáció hozzáadása vagy, hogy további oszlop eltávolítása az adatkészletet.

## <a name="convert-field-type-to-numeric"></a>Mező típusa átalakítása numerikus
A transzformáció numerikus típusú oszlop módosításait. Az elválasztó is megadhat, ha nem egész adatok. Alapértelmezés szerint a transzformáció elválasztó nem figyelmeztet, használja a **szerkesztése** menüpont meghívni a szerkesztőt. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="convert-field-type-to-date"></a>Az átalakítás Date értékre mező típusa
A transzformáció dátum típusú oszlop módosításait. Egy alapértelmezett dátum/idő formátumot használja, de előfordulhat, hogy karakterlánccal bírálható felül `strftime` irányelvek. Akkor is illesztenie egy rögzített dátum tartalmazó time típusú értékek.

A transzformáció nem figyelmeztet a formátum alapértelmezés szerint használja a **szerkesztése** menüpont meghívni a szerkesztő eredő lépésben. A transzformáció csak egy oszlop, vagy sok működik.

9-22-1677 között csak dátumok és a 4-11-2262 konvertálható dátum adattípusú.

## <a name="convert-field-type-to-boolean"></a>Logikai érték mező típusa átalakítása
A transzformáció igaz/hamis típusú oszlop módosításait. A transzformáció támogatja több érték leképezése igaz vagy hamis értéket, és lehet szerkeszteni a leképezések. Egy konstans, amely leképezhető értékek, amelyek nem szerepelnek a true/false értéket eredményező leképezési táblák is támogatja. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="convert-field-type-to-string"></a>Mező típusa karakterlánccá konvertálni
A transzformáció karakterlánc típusú oszlop módosításait. A transzformáció csak egy oszlop, vagy sok működik.

## <a name="convert-unix-timestamp-to-datetime"></a>Az átalakítás DateTime értékre Unix időbélyeg
A transzformáció tisztában van azzal a Unix időbélyeg-formátumnak, még akkor is, ha az adatok egy karakterláncból, és megfelelően alakít az adatok prep dátum típusra.

## <a name="filter"></a>Szűrés
A transzformáció támogatja a szűrést, a sorok egy vagy több oszlopban levő értékek alapján. A szűrőfeltételek az oszlopok, lehetővé téve a szűrő karakterlánc típusú oszlopokra "contains" vagy "nem tartalmaz" adattípus függenek. Számokat tartalmazó oszlop szerint "nagyobb, mint" szűrhetők "kisebb, mint" feltételeket.

Meghívásakor a szűrő átalakítása a főmenübe, vagy kattintson a jobb gombbal az oszlop fejlécében, oszthatja ketté a hibás sorok be egy másik adatfolyam beállítás érhető el. A fő adatfolyam folytatódik a szűrt **a** sort, és egy új adatfolyam jön létre, amely szűrve voltak összes sort tartalmazza **kimenő**.

## <a name="use-first-row-as-headers"></a>Első sor használata fejlécként
A transzformáció elősegíti az első sor adatkészletből kell lennie az oszlopok neveit. Ha valamelyik oszlop első sorában nincs adatokat, majd nevét jön létre automatikusan. Ehhez a transzformációhoz használatával azt jelenti, hogy az adatok importálását kezdődik 2. sor, a lehető legrövidebb időn belül. Attól függően, hogy a sorok kihagyása beállítást, az importálás a következő adatkészletben kezdheti akár további le. Azt is használható az előléptetés eltávolítása, és csak az automatikusan létrehozott névvel rendelkeznek.

## <a name="join"></a>Csatlakozás
A transzformáció két adatfolyamok csatlakozás együtt használatos. Kiválaszthatja, melyik kimeneti csatlakozási legyen az eredmény: sikeres sorait a való csatlakozást, a "bal oldali" sikertelen az illesztés, vagy a "jobbra" sikertelen az illesztés sorát sorát.

Az illesztési varázsló elindítása a egyetlen az adatfolyam, és adott adatfolyam választja az illesztés egy oldalán. Majd megkérdezi a másik oldalon csatlakozási egy másik folyamatábrája. A két adatfolyamok kijelölése után a varázsló való csatlakozáshoz meg kell jelölni a közös mindkét oldalon levő egyetlen oszlophoz van szükség. Ha a csatlakozás igényel, több oszlopot, majd származtatott oszlop létrehozása csak az illesztési (összefűzött) új oszlop létrehozása varázsló elindítása előtt. A varázsló megpróbálja illesztési kulcsok javaslat, és ha lehetséges létre automatikusan a származtatott oszlop.

Az illesztés befejezését követően a csatlakozási Sankey diagramnézet akkor jelenik meg. Az egyes sorok szélességét jelöli, hogy a csatlakozási folyamat részeként való áthaladás sorok száma. A jobb oldali panelen válassza ki a sikeres sorok, a bal oldali sikertelen vagy kizárólag sikertelen jobb teszi lehetővé. Akkor is csak egy ág kiválasztása.

## <a name="append-rows"></a>Sor hozzáfűzése
A transzformáció adatainak áttelepítését egy másik adatfolyam a jelenlegivel fűzi hozzá. Leképezi a pozíció az új sorok felvételének végén az oszlopok.

## <a name="append-columns"></a>Oszlopok hozzáfűzése
A transzformáció hozzáfűzi a jelenlegivel egy másik adatfolyam az új oszlopokat. Hozzáadja az új oszlopok jobb; azt nem történt kísérlet egy sor adatok sor.

## <a name="summarize"></a>Összefoglalója
A transzformáció kiszámítja az összesítések egy vagy több kijelölt oszlopokban szereplő egyedi értékek kombinációjához. 

Az aggregátumok támogatott: száma, SUM, MIN, MAX, VARIANCIA, és SZÓRÁSNÁL. Az adott oszlop aggregátumok szűri a program csak a meglévők közül, amelyek érvényesek az adattípus. Összesítések nem alkalmazható le vannak tiltva. Például nem számítható ki a középérték a karakterlánc oszlop, de a minimális és maximális számítási lehet.

A szerkesztő elérhetővé válik, húzza az oszlopfejléc a panel a bal felső. Ha az oszlopok összesítse jelennek meg. A panel egy hierarchikus, így a beágyazott összesítéseknek elvégzéséhez. A szerkesztő panel jobb felső sarokban lévő oszlop alkalmazandó mely összesítés kiválasztására szolgál. Egyetlen oszlop egy vagy több alkalommal összesíthetők. Legalább egy összesítés van kiválasztva, ha a rács jobb alsó előnézetét jeleníti meg az adatokat összesített formában. 

A transzformáció csatlakoztatja, akkor egy `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Távolítsa el az ismétlődő
A transzformáció Eltávolítja a teljes sor, ha ismétlődő értékek vannak egy vagy több kijelölt oszlopok. Ha nem szerepelnek oszlopok közül választ, akkor a csak sorok, amelyekre a rendszer eltávolítja azokat, ahol az összes oszlop értékei azonos.

## <a name="sort"></a>Rendezés
A transzformáció rendezi az adatokat. A rendezés csak egy oszlop, vagy sok hajtható végre, és minden oszlop rendezhetők (alapértelmezett) növekvő vagy csökkenő (módosíthatja a szerkesztő).

A transzformáció csatlakoztatja, akkor egy `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Kimeneti átalakítások
A következő átalakítások kimeneti adatokat. Lehetséges, hogy több írási blokkok írja ki az adatok különböző időpontokban tennie egyetlen folyamatában.

### <a name="write-to-csv"></a>CSV-fájlba írása
A transzformáció az adatfolyam a jelenlegi ponttól írja ki az adatok CSV formátumban. Lehetővé teszi a vezérlő (helyi vagy távoli) helyének és különböző beállításokat a fájl körül.

### <a name="write-to-parquet"></a>Parquet írása
A transzformáció Parquet formában adatai ír, az aktuális pontjáról az adatfolyam. Lehetővé teszi a vezérlő (helyi vagy távoli) helyének és különböző beállításokat a fájl körül.

## <a name="script-based-transforms"></a>Parancsprogram-alapú átalakítások
A következő átalakító nem található az alaptermékben működésének végrehajtásához használja a parancsfájl (Python). Ezek átalakítások alkalmazása előtt olvassa el a [használatával Python bővítési](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Oszlop (parancsfájl) hozzáadása
A transzformáció lehetővé teszi, hogy egy oszlopot fel kell venni egy Python-kifejezés az adatokat.
További információkért lásd: [való származtatás új oszlopok minta Python kódját](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Speciális szűrési (parancsfájl)
A transzformáció lehetővé teszi, hogy a Python szintű Sorszűrő kellene írni.
További információkért lásd: [példa szűrőkifejezéseket](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Átalakítás Adatfolyamblokk (parancsfájl)
A transzformáció lehetővé teszi, hogy a teljes adatkészlet alkalmazandó Python.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Átalakítás Adatfolyamblokk (parancsfájl)
A transzformáció lehetővé teszi, hogy a Python egy teljes adat partíció alkalmazandó.
További információkért lásd: [példa átalakító adatfolyam átalakítások](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Írási Adatfolyamblokk (parancsfájl)
A transzformáció lehetővé teszi, hogy a Python írása, és a teljes adatkészlet alkalmazandó.
További információkért lásd: [minta Python a való származtatás új oszlopok](data-prep-appendix9-sample-destination-connections-python.md).



