---
title: Rendezés – Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse támogatott rendezési típusok.
services: sql-data-warehouse
author: antvgski
manager: igorstan
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 598ad4c103035ec2d94015e1f8e12194d1b723fc
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935105"
---
# <a name="database-collation-support-for-azure-sql-data-warehouse"></a>Adatbázis-rendezési támogatás Azure SQL Data Warehouse

Új Azure SQL Data Warehouse-adatbázis létrehozásakor megváltoztathatja a Azure Portal alapértelmezett adatbázis-rendezését. Ezzel a képességgel még könnyebben hozhat létre új adatbázist az 3800 támogatott adatbázis-rendezések egyikével a SQL Data Warehousehoz.
A rendezések lehetővé teszik a területi beállítás, a kódlap, a rendezési sorrend és a karakteres adattípusok megkülönböztetésére vonatkozó szabályok megadását. Ha kiválasztotta, a rendezési adatokat igénylő összes oszlop és kifejezés örökli a kiválasztott rendezést az adatbázis-beállítástól. Az alapértelmezett öröklés felülbírálható úgy, hogy egy karakter alapú adattípus esetében explicit módon megadhat egy másik rendezést.

## <a name="changing-collation"></a>Rendezés módosítása
Az alapértelmezett rendezés módosításához egyszerűen frissítse a rendezési mezőt a létesítési élményben.

Ha például módosítani szeretné az alapértelmezett rendezést a kis-és nagybetűk megkülönböztetésére, egyszerűen átnevezheti a rendezést a SQL_Latin1_General_CP1_CI_AS és a SQL_Latin1_General_CP1_CS_AS között. 

## <a name="list-of-unsupported-collation-types"></a>Nem támogatott rendezési típusok listája
*   Japanese_Bushu_Kakusu_140_BIN
*   Japanese_Bushu_Kakusu_140_BIN2
*   Japanese_Bushu_Kakusu_140_CI_AI_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI
*   Japanese_Bushu_Kakusu_140_CI_AI_WS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AI
*   Japanese_Bushu_Kakusu_140_CS_AI_WS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*   Japanese_XJIS_140_BIN
*   Japanese_XJIS_140_BIN2
*   Japanese_XJIS_140_CI_AI_VSS
*   Japanese_XJIS_140_CI_AI_WS_VSS
*   Japanese_XJIS_140_CI_AI_KS_VSS
*   Japanese_XJIS_140_CI_AI_KS_WS_VSS
*   Japanese_XJIS_140_CI_AS_VSS
*   Japanese_XJIS_140_CI_AS_WS_VSS
*   Japanese_XJIS_140_CI_AS_KS_VSS
*   Japanese_XJIS_140_CI_AS_KS_WS_VSS
*   Japanese_XJIS_140_CS_AI_VSS
*   Japanese_XJIS_140_CS_AI_WS_VSS
*   Japanese_XJIS_140_CS_AI_KS_VSS
*   Japanese_XJIS_140_CS_AI_KS_WS_VSS
*   Japanese_XJIS_140_CS_AS_VSS
*   Japanese_XJIS_140_CS_AS_WS_VSS
*   Japanese_XJIS_140_CS_AS_KS_VSS
*   Japanese_XJIS_140_CS_AS_KS_WS_VSS
*   Japanese_XJIS_140_CI_AI
*   Japanese_XJIS_140_CI_AI_WS
*   Japanese_XJIS_140_CI_AI_KS
*   Japanese_XJIS_140_CI_AI_KS_WS
*   Japanese_XJIS_140_CI_AS
*   Japanese_XJIS_140_CI_AS_WS
*   Japanese_XJIS_140_CI_AS_KS
*   Japanese_XJIS_140_CI_AS_KS_WS
*   Japanese_XJIS_140_CS_AI
*   Japanese_XJIS_140_CS_AI_WS
*   Japanese_XJIS_140_CS_AI_KS
*   Japanese_XJIS_140_CS_AI_KS_WS
*   Japanese_XJIS_140_CS_AS
*   Japanese_XJIS_140_CS_AS_WS
*   Japanese_XJIS_140_CS_AS_KS
*   Japanese_XJIS_140_CS_AS_KS_WS
*   SQL_EBCDIC1141_CP1_CS_AS
*   SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Az aktuális rendezés ellenőrzése
Az adatbázis aktuális rendezésének ellenőrzését a következő T-SQL-kódrészlet futtatásával végezheti el: Válassza a DATABASEPROPERTYEX (DB_NAME (), "rendezés") rendezést; Ha a tulajdonság paraméterként a "rendezés" értéket adta át, a DatabasePropertyEx függvény a megadott adatbázis aktuális rendezését adja vissza. További információ a DatabasePropertyEx függvényről az MSDN webhelyen.

