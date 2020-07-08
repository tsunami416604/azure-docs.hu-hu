---
title: Rendezés
description: Az Azure szinapszis SQL-ben támogatott rendezési típusok
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0e9d8048c88a5ef37df2fde1ab282a834b07228a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206342"
---
# <a name="database-collation-support-for-synapse-sql"></a>Adatbázis-rendezési támogatás a szinapszis SQL-hez

A rendezések lehetővé teszik a területi beállítás, a kódlap, a rendezési sorrend és a karakteres adattípusok megkülönböztetésére vonatkozó szabályok megadását. Ha kiválasztotta, a rendezési adatokat igénylő összes oszlop és kifejezés örökli a kiválasztott rendezést az adatbázis-beállítástól. Az alapértelmezett öröklés felülbírálható úgy, hogy egy karakter alapú adattípus esetében explicit módon megadhat egy másik rendezést.

Az adatbázis alapértelmezett rendezését az új SQL Pool-adatbázis létrehozásakor módosíthatja a Azure Portal. Ezzel a képességgel még könnyebben hozhat létre egy új adatbázist a 3800 támogatott adatbázis-rendezések egyikével.

Megadhatja az alapértelmezett szinapszis SQL on-demand adatbázis-rendezést a létrehozáskor az adatbázis létrehozása utasítás használatával.

## <a name="changing-collation"></a>Rendezés módosítása
Az SQL Pool-adatbázis alapértelmezett rendezésének módosításához egyszerűen frissítse a rendezési mezőt a létesítési élményben. Ha például meg szeretné változtatni az alapértelmezett rendezést a kis-és nagybetűk megkülönböztetésére, egyszerűen nevezze át a rendezést SQL_Latin1_General_CP1_CI_ASról SQL_Latin1_General_CP1_CS_ASra. 

Az SQL on-demand adatbázis alapértelmezett rendezésének módosításához használhatja az ALTER DATABASE utasítást.

## <a name="list-of-unsupported-collation-types"></a>Nem támogatott rendezési típusok listája
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Emellett az SQL-készlet nem támogatja a következő rendezési típusokat:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="checking-the-current-collation"></a>Az aktuális rendezés ellenőrzése
Az adatbázis aktuális rendezésének ellenőrzését a következő T-SQL-kódrészlet futtatásával végezheti el:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Ha a tulajdonság paraméterként a "rendezés" értéket adta át, a DatabasePropertyEx függvény a megadott adatbázis aktuális rendezését adja vissza. További információ a DatabasePropertyEx függvényről az MSDN webhelyen.

## <a name="next-steps"></a>További lépések

Az SQL Pool és az SQL on-demand ajánlott eljárásaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Ajánlott eljárások az SQL-készlethez](best-practices-sql-pool.md)
- [Ajánlott eljárások az SQL igény szerinti kezeléséhez](best-practices-sql-on-demand.md)


