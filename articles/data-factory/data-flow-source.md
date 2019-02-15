---
title: Az Azure Data Factory-folyamat forrás adatátalakítás leképezése
description: Az Azure Data Factory-folyamat forrás adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35f4e794caf84aba860b98e68eadcdcd88e77952
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271870"
---
# <a name="azure-data-factory-mapping-data-flow-source-transformation"></a>Az Azure Data Factory-folyamat forrás adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az adatforrás-átalakítás konfigurálása ahhoz, hogy az adatokat a data flow-bA használni kívánt adatforrást. Előfordulhat, hogy az egyetlen adatfolyam átalakítása 1-nél több forrás. Mindig kezdeni tervezése az adatáramlás a forrás.

> [!NOTE]
> Minden adatfolyam kell legalább egy adatforrás-átalakítás. Tetszőleges számú további források, amennyi szükséges hozzáadása

![Átalakítási beállítások a forrás](media/data-flow/source.png "forrás")

Az adatfolyam forrás ADF pontosan egy adathalmazt, amely meghatározza az alakzat és írni vagy olvasni az adatok helyének társítva kell lennie.

## <a name="data-flow-staging-areas"></a>Adatfolyam átmeneti területekből

Vonal a üzemel az ADF adatfolyam rendelkezik 5 elsődleges "átmeneti" területekre az adatok csatlakoztatva átalakításokat hajthattak végre Azure-ban: Azure Blob, ADLS Gen 1, ADLS Gen 2, Azure SQL DB, and Azure SQL DW. Az ADF csaknem 80 különböző natív összekötőket hozzáféréssel rendelkezik, ezért ezeket más adatforrásokat tartalmazza azokat az adatfolyam, első lépés az adatok be egy olyan öt elsődleges adatfolyam átmeneti területet először a másolási tevékenység használatával:

## <a name="options"></a>Beállítások

### <a name="allow-schema-drift"></a>Séma eltéréseket engedélyezése
Jelölje be a séma eltéréseket engedélyezése, ha a forrás oszlopok gyakran változik. Ez a beállítás lehetővé teszi az összes bejövő mezőket a forrásból a fogadóba átalakítások funkción keresztül.

### <a name="fail-if-columns-in-the-dataset-are-not-found"></a>Sikertelen, ha az adatkészletben lévő oszlopok nem találhatók.
Ezt a lehetőséget egy forrás séma érvényesítése sikertelen lesz, az adatfolyam Ha, amelyek várhatóan a forrásból származó oszlopok nem szerepelnek, kényszerítése.

### <a name="sampling"></a>Mintavételezés
Mintavételi használatával a forrás sorok számának korlátozásához.  Ez akkor hasznos, amikor szüksége van a forrásadatok egy minta csak teszteléshez és hibakereséshez.

### <a name="define-schema"></a>Séma megadása

![Forrás-átalakítás](media/data-flow/source2.png "2 forrás")

### <a name="you-can-modify-the-name-of-the-source-columns-and-their-associated-data-types"></a>Módosíthatja a forrásoszlopokat és az azokhoz kapcsolódó adatok típusokat neve

Minden forrás fájltípus esetében, amelyek nem típusos (azaz egybesimított fájlok ellentétben a Parquet-fájlokat) meg kell határozni az adattípusok Itt minden mező esetében az forrás átalakítást, nem pedig az adatkészletben.

Ha nem látja az oszlopok nevét és típusát az adatfolyam az, akkor valószínű, mert Ön nem határozzák meg azokat a fogadó séma megadása szakaszában. Ha nem használja az adatok Flow séma eltéréseket kezelését csak kell.

A "Schema megadása" Itt az adatforrás-átalakítás lapján látható, amelyen beállíthatja az adattípusok és formátumok:

![Forrás-átalakítás](media/data-flow/source003.png "adattípusok")

### <a name="optimize"></a>Optimalizálás

![Forrás-partíciók](media/data-flow/sourcepart.png "particionálása")

Az adatforrás-átalakítás optimalizálása lapon látni fogja a "Forrás" nevű particionálási típust. Ez lesz csak világos mentése az Azure SQL DB kijelölése után a forrásként. Ennek az oka az ADF párhuzamosíthatja hajtsa végre az Azure SQL DB adatforrás-lekérdezéseket nagy kapcsolatokat szeretne.

Az SQL DB forrás az adatok particionálása nem kötelező, de akkor hasznos, ha nagy lekérdezéseket. Erre két lehetősége van:

### <a name="column"></a>Oszlop

Válasszon ki egy oszlopot partícióhoz a a forrástáblában. A kapcsolatok maximális számát is meg kell adni.

### <a name="query-condition"></a>Lekérdezési feltétel

Igény szerint kiválaszthatja a lekérdezés alapján kapcsolatok particionálásához. E beállítás használata esetén egyszerűen írja be a WHERE predikátum tartalmát. Azaz > 1980 év
