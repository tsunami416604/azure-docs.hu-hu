---
title: Az Azure Data Factory-folyamat ablak adatátalakítás leképezése
description: Az Azure Data Factory-folyamat ablak adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861910"
---
# <a name="azure-data-factory-window-transformation"></a>Az Azure Data Factory ablak átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Az ablak átalakítás, ahol meghatározzuk Windows-alapú összesítések az oszlopok az adatfolyamban. A Kifejezésszerkesztő definiálható különböző típusú adat- vagy windows (SQL OVER záradékban) alapuló összesítések például az ÉRDEKLŐDŐ, KÉSÉS, NTILE, CUMEDIST, RANK, stb.). Egy új mezőt a kimenetben, amely tartalmazza az ezen összesítések jön létre. Választható csoportosítási mezők is használható.

![Időintervallum lehetőségei](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>felett
Állítsa be a particionálási oszlop adatait a ablak átalakítást. Az SQL-megfelelője a a ```Partition By``` az SQL-ben az Over záradékkal. Hozzon létre egy számítási, vagy hozzon létre egy kifejezést a particionálás használandó szeretné, ha ehhez az egérmutatót az oszlop neve, és válassza ki a "számított oszlop".

![Időintervallum lehetőségei](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Rendezés
Egy másik része az Over záradékkal beállítása a ```Order By```. Ez a adatok rendezési sorrendjét állítja be. A rendezési oszlop mezőben is létrehozhat egy calculate értéke egy kifejezés.

![Időintervallum lehetőségei](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Tartomány szerint
Következő lépésként állítsa a ablakkeret Unbounded vagy kötött. Egy kötetlen ablakkeret beállítása, állítsa a csúszkát Unbounded mindkét végén. Ha egy beállítás Unbounded és Current Row között, majd kell a eltolás kezdő és vége értékeket. Mindkét értéket pozitív egész számok lesznek. Relatív számokat vagy értékek is használható, ha az adatok alapján.

Az ablak csúszka beállítása két értéke van: az értékeket, mielőtt az aktuális sor és után az aktuális sor értékeit. A kezdő és záró eltolás megegyezik a csúszkán a két használhatók a választók.

![Időintervallum lehetőségei](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Ablak oszlopok
Végül használja a Kifejezésszerkesztő meghatározásához a használni kívánt adatokkal RANK, COUNT, MIN, például a windows MAX összesítések, SŰRŰ RANK, ÉRDEKLŐDŐ, KÉSÉS, stb.

![Időintervallum lehetőségei](media/data-flow/windows7.png "windows 7")

Összesítő és elemzési funkciók, hogy az ADF Data Flow kifejezés nyelvi a Kifejezésszerkesztő keresztül elérhető teljes listáját itt találhatók: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>További lépések

Ha egy egyszerű a group by összesítő keres, használja a [átalakítási összesítés](data-flow-aggregate.md)
