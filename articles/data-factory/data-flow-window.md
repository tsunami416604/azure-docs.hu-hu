---
title: Az adatfolyam-ablak átalakításának leképezése
description: Azure Data Factory leképezési adatfolyam ablakának átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606226"
---
# <a name="azure-data-factory-window-transformation"></a>Azure Data Factory ablak átalakítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az ablak transzformációja az oszlopok ablakos összesítéseit fogja meghatározni az adatfolyamokban. A Kifejezésszerkesztő különböző típusú összesítéseket határozhat meg, amelyek az adat-vagy időablakokon alapulnak (SQL OVER záradék), például az ólom, a LAG, a NTILE, a CUMEDIST, a RANK stb.). A rendszer létrehoz egy új mezőt a kimenetben, amely tartalmazza ezeket az összesítéseket. A választható csoportosítási mezőket is felveheti.

![Ablak beállításai](media/data-flow/windows1.png "Windows 1")

## <a name="over"></a>Keresztül
Állítsa be az oszlopok adatparticionálását az ablak átalakításához. Az SQL-ekvivalens az ```Partition By``` SQL-ben az over záradékban található. Ha létre szeretne hozni egy számítást, vagy létre szeretne hozni egy kifejezést a particionáláshoz, akkor az oszlop neve fölé húzva, majd a "számított oszlop" lehetőséget kell választania.

![Ablak beállításai](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Rendezés
Az over záradék egy másik része a értékét állítja be ```Order By``` . Ezzel az adatrendezési sorrendet fogja beállítani. Ebben az oszlopban is létrehozhat egy kifejezést a rendezéshez.

![Ablak beállításai](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Tartomány:
Ezután állítsa az ablakkeret keret nélküliként vagy korlátként. A nem kötött ablakkeret beállításához állítsa a csúszkát úgy, hogy mindkét végén fel legyen kötve. Ha a nem kötött és az aktuális sor közötti beállítást választja, akkor a kezdő és a záró értékeket kell megadnia. Mindkét érték pozitív egész szám lesz. Az adatokból relatív számokat vagy értékeket is használhat.

Az ablak csúszka két beállított értékkel rendelkezik: az aktuális sor előtti értékek és az aktuális sor utáni értékek. A kezdő és a záró eltolás megegyezik a csúszkán található két választóval.

![Ablak beállításai](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Ablak oszlopai
Végül a Kifejezésszerkesztő segítségével definiálhatja azokat az összesítéseket, amelyeket használni szeretne az Adatablakok, például a RANK, a COUNT, a MIN, a MAX, a SŰRŰ rang, az ólom, a késés stb. alapján.

![Ablak beállításai](media/data-flow/windows7.png "Windows 7")

A kifejezés-szerkesztőben az ADF adatáramlás kifejezésének nyelvén használható összesítési és elemzési függvények teljes listája itt található: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>További lépések

Ha egy egyszerű csoportosítási összesítést keres, használja az [összesített transzformációt](data-flow-aggregate.md)
