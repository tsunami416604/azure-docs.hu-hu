---
title: Az Azure Data Factory-folyamat forrás adatátalakítás leképezése
description: Az Azure Data Factory-folyamat forrás adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: e33606e0b5be0db5306cfd42f87baf59671eb2a8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451729"
---
# <a name="mapping-data-flow-source-transformation"></a>A folyamat forrás átalakítását leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az adatforrás-átalakítás konfigurálása ahhoz, hogy az adatokat a data flow-bA használni kívánt adatforrást. Előfordulhat, hogy egynél több forrás átalakító tartalmaz egyetlen adatfolyam. Mindig kezdeni tervezése az adatáramlás a forrás.

> [!NOTE]
> Minden adatfolyam kell legalább egy adatforrás-átalakítás. Tetszőleges számú további források, amennyi az átalakítási művelet végrehajtásához szükséges hozzá. Csatlakozhat, és a egy illesztési vagy Union átalakítás forrásokat.

![Átalakítási beállítások a forrás](media/data-flow/source.png "forrás")

Minden forrás adatfolyam átalakítása pontosan egy Data Factory-adatkészlet, az alakzat és írni vagy olvasni az adatok helyét meghatározó társítva kell lennie. A forrás listák fájl és a helyettesítő karakterek használatával egyszerre több fájl használatához.

## <a name="data-flow-staging-areas"></a>Adatfolyam átmeneti területekből

Az adatfolyam minden Azure-ban "átmeneti" adatkészletek együttműködik. Ezek az adatok folyamat adatkészletek a data-átalakításokat átmeneti adatok használhatók. A Data Factory csaknem 80 különböző natív összekötőket hozzáféréssel rendelkezik. Ezek más forrásokból származó adatokat tartalmazza, az adatfolyam, első lépés az adatok be egy olyan adatfolyam adatkészlet átmeneti területet a másolási tevékenység használatával.

## <a name="options"></a>Beállítások

### <a name="allow-schema-drift"></a>Séma eltéréseket engedélyezése
Jelölje be a séma eltéréseket engedélyezése, ha a forrás oszlopok gyakran változik. Ez a beállítás lehetővé teszi az összes bejövő mezőket a forrásból a fogadóba átalakítások funkción keresztül.

### <a name="validate-schema"></a>Séma érvényesítése

![Nyilvános adatforrás](media/data-flow/source1.png "1 nyilvános adatforrás")

Ha a forrásadatok bejövő verziója nem egyezik a megadott séma, majd az adatokat a folyamat végrehajtása sikertelen lesz.

### <a name="sampling"></a>Mintavételezés
Mintavételi használatával a forrás sorok számának korlátozásához.  Ez akkor hasznos, amikor szüksége van a forrásadatok egy minta csak teszteléshez és hibakereséshez.

## <a name="define-schema"></a>Séma megadása

![Forrás-átalakítás](media/data-flow/source2.png "2 forrás")

Minden forrás fájltípus esetében, amelyek nem típusos (azaz egybesimított fájlok ellentétben a Parquet-fájlokat) meg kell határozni az adattípusok Itt minden mező esetében az adatforrás-átalakítás. Ezt követően módosíthatja az oszlopnevek válassza ki az átalakítás és a egy származtatott oszlop átalakítása az adattípusokat. 

![Forrás-átalakítás](media/data-flow/source003.png "adattípusok")

Szigorú típusmegadású forrásokhoz módosíthatja az adattípusok későbbi válassza átalakításban. 

### <a name="optimize"></a>Optimalizálás

![Forrás-partíciók](media/data-flow/sourcepart.png "particionálása")

Az adatforrás-átalakítás optimalizálása lapon látni fogja a "Forrás" nevű particionálási típust. Ez lesz csak világos mentése az Azure SQL DB kijelölése után a forrásként. Ennek az oka az ADF párhuzamosíthatja hajtsa végre az Azure SQL DB adatforrás-lekérdezéseket nagy kapcsolatokat szeretne.

Az SQL DB forrás az adatok particionálása nem kötelező, de akkor hasznos, ha nagy lekérdezéseket. Erre két lehetősége van:

### <a name="column"></a>Oszlop

Válasszon ki egy oszlopot partícióhoz a a forrástáblában. A kapcsolatok maximális számát is meg kell adni.

### <a name="query-condition"></a>Lekérdezési feltétel

Igény szerint kiválaszthatja a lekérdezés alapján kapcsolatok particionálásához. E beállítás használata esetén egyszerűen írja be a WHERE predikátum tartalmát. Azaz > 1980 év

## <a name="source-file-management"></a>Forrás fájlok kezelése
![Az új forrásbeállítások](media/data-flow/source2.png "új beállításai")

* Helyettesítő elérési útja, amely megfelel a mintának a forrásmappa fájlok sorozatát válasszon. Ez a művelet felülírja az összes fájl, amely az adatkészlet definícióját állított be.
* Fájlok listája. Ugyanaz, mint egy fájl beállítása. Egy szöveges fájl relatív elérési út feldolgozandó fájlok listáját a létrehozott mutasson.
* Tároló neve oszlop egy oszlop adatait a forrásból a fájl nevét tárolja. Adjon meg egy új nevet a fájl karakterlánc tárolására.
* A befejezésekor (választhat az adatokat a folyamat végrehajtása után ne történjen semmi a forrás-fájllal, a forrás törölhető(k), vagy helyezze át a forrás-fájlokat. Az áthelyezési utakat relatív elérési utakat.

### <a name="sql-datasets"></a>SQL Datasets

Mint a forrás Azure SQL Database vagy Azure SQL DW használ, amikor kell további beállításokat.

* Lekérdezés: Adja meg a forrás SQL-lekérdezést. Lekérdezés beállítása felülírja a választotta, az adatkészlet bármely táblázat. Vegye figyelembe, hogy az Order By záradékok nem támogatottak-e meg a itt.

* Köteg mérete: Adja meg a Köteg mérete nagy mennyiségű adat álló batch méretű olvasott be.

> [!NOTE]
> A művelet beállítások csak akkor futnak, amikor a folyamat futásának (folyamat hibakeresési vagy futtatás végrehajtási) végrehajtása az adatfolyam használata az adatok folyamat végrehajtása tevékenység a folyamat. NEM hajtható végre fájlműveleteket, adatfolyam hibakeresési módban.

### <a name="projection"></a>Vetület

![Leképezés](media/data-flow/source3.png "leképezése")

Az adatkészletek sémák hasonlóan, a leképezés forrás határozza meg az adatokat tartalmazó oszlopok, az adattípusok és a forrásadatok adatformátumok a célnyelven. Ha nincs meghatározott sémája tartalmazó szövegfájl, kattintson a "Észlelése adattípus" ADF minta és az adattípusok kikövetkeztetni bejelentkezési kísérletet tehet fel. Beállíthatja az alapértelmezett adatok formátumai automatikus észlelés a "Definiálása alapértelmezett formátum" gomb használatával. Módosíthatja az oszlop adattípusait az ezt követő származtatott oszlopot az átalakítás. Az oszlopok neveit az válassza átalakítás használatával módosíthatók.

![Az alapértelmezett formátumok](media/data-flow/source2.png "alapértelmezett formátumok")

## <a name="next-steps"></a>További lépések

Készítsen az adatátalakítás az [származtatott oszlopot](data-flow-derived-column.md) és [kiválasztása](data-flow-select.md).
