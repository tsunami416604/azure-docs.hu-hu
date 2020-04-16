---
title: Rendezés
description: Az Azure Synapse SQL által támogatott rendezési típusok
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431539"
---
# <a name="database-collation-support-for-synapse-sql"></a>A Synapse SQL adatbázis-rendezési támogatása

A rendezések biztosítják a karakteralapú adattípusok területi beállítását, kódlapját, rendezési sorrendjét és karakterérzékenységi szabályait. A kiválasztás után az összes olyan oszlop és kifejezés, amely rendezési adatokat igényel, örökli a kiválasztott rendezést az adatbázis-beállítástól. Az alapértelmezett öröklődés felülbírálható, ha egy karakteralapú adattípushoz külön rendezést ad meg.

Új SQL-készlet-adatbázis létrehozásakor módosíthatja az alapértelmezett adatbázis-rendezést az Azure Portalról. Ez a funkció még könnyebbé teszi egy új adatbázis létrehozását a 3800 támogatott adatbázis-illesztések egyikével.

Megadhatja az alapértelmezett Synapse SQL on-demand adatbázis-rendezés létrehozásakor a CREATE DATABASE utasítás használatával.

## <a name="changing-collation"></a>Az illesztés módosítása
Az SQL-készletadatbázis alapértelmezett illesztésének módosításához egyszerűen frissítse a rendezési mezőt a kiépítési környezetben. Ha például a kis- és nagybetűk megkülönböztetésére szeretné módosítani az alapértelmezett rendezést, egyszerűen nevezze át a rendezést SQL_Latin1_General_CP1_CI_AS SQL_Latin1_General_CP1_CS_AS. 

Az SQL on-demand adatbázis alapértelmezett illesztésének módosításához használhatja az ALTER DATABASE utasítást.

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

Ezenkívül az SQL-készlet nem támogatja a következő rendezési típusokat:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Az aktuális illesztés ellenőrzése
Az adatbázis aktuális illesztésének ellenőrzéséhez futtassa a következő T-SQL kódrészletet:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Ha a "Rendezés" tulajdonságparaméterként megfelel, a DatabasePropertyEx függvény a megadott adatbázis aktuális illesztését adja vissza. Az MSDN AdatbázisTulajdonságEx függvényéről többet is megtudhat.

## <a name="next-steps"></a>További lépések

Az SQL-készlet és az SQL on-demand ajánlott eljárásokról az alábbi cikkekben talál további információkat:

- [Gyakorlati tanácsok az SQL-készlethez](best-practices-sql-pool.md)
- [Gyakorlati tanácsok az SQL igény szerinti](best-practices-sql-on-demand.md)


