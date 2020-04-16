---
title: Adatfolyam leképezése Ablakátalakítás
description: Azure Data Factory leképezési adatfolyam-átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 67e966e3b3dd274e993797ed37e17c5490d632c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416493"
---
# <a name="azure-data-factory-window-transformation"></a>Az Azure Data Factory ablakának átalakítása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az ablakátalakítás az a hely, ahol az adatfolyamok oszlopainak ablakalapú összesítését definiálhatja. A Kifejezésszerkesztőben különböző típusú összesítéseket határozhat meg, amelyek adatokon vagy időablakokon (SQL OVER záradék) alapulnak, például LEAD, LAG, NTILE, CUMEDIST, RANK stb.). A kimenetben egy új mező jön létre, amely tartalmazza ezeket az összesítéseket. Választható csoportonkénti mezőket is megadhat.

![Ablakbeállítások](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Felett
Állítsa be az ablakátalakítás oszlopadatainak particionálását. Az SQL-egyenérték az ```Partition By``` SQL Over záradéka. Ha számításokat szeretne létrehozni, vagy a particionáláshoz használandó kifejezést szeretne létrehozni, ezt úgy teheti meg, hogy az oszlop neve fölé viszi az egérmutatót, és kiválasztja a "számított oszlop" lehetőséget.

![Ablakbeállítások](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Rendezés
Az Over záradék másik része ```Order By```a . Ezzel beállítja az adatok rendezési sorrendjét. Ebben az oszlopmezőben a rendezéshez kifejezést is létrehozhat egy számítási értékhez.

![Ablakbeállítások](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Tartomány szerint
Ezután állítsa az ablakkeretet Határtalan vagy kötött. Kötetlen ablakkeret beállításához állítsa a csúszkát mindkét végén határtalan ra. Ha a Nem kötött és az Aktuális sor közötti beállítást választja, akkor be kell állítania az Eltolás kezdő és záró értékét. Mindkét érték pozitív egész szám lesz. Az adatokból relatív számokat vagy értékeket használhat.

Az ablakcsúszkának két értéket kell beállítania: az aktuális sor előtti értékeket és az aktuális sor utáni értékeket. A Kezdő és záró eltolás megegyezik a csúszka két választójával.

![Ablakbeállítások](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Ablakoszlopok
Végül a Kifejezésszerkesztő vel definiálja az adatablakokkal használni kívánt összesítéseket, például RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG stb.

![Ablakbeállítások](media/data-flow/windows7.png "windows 7")

Az ADF adatfolyam-kifejezési nyelvében a Kifejezésszerkesztőn keresztül használható összesítési és analitikus függvények teljes listája a következő: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>További lépések

Ha egyszerű csoportosulást keres, használja az [Összesítő transzformációt](data-flow-aggregate.md)
