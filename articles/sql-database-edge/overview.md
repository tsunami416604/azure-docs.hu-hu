---
title: Mi az Azure SQL Database Edge? | Microsoft Docs
description: Tudnivalók az Azure SQL Database Edge-ről
keywords: az SQL Database Edge bemutatása, mi az SQL Database Edge, az SQL Database Edge áttekintése
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9cbfc17e7412b4d30f082354996721ee7b5d6d5b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514003"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Mi az Azure SQL Database Edge Preview?

Az Azure SQL Database Edge Preview egy optimalizált, a IoT és a IoT Edge üzemelő példányokhoz igazított, összehasonlítható adatbázis-motor. Lehetőséget biztosít nagy teljesítményű adattárolási és feldolgozási réteg létrehozására a IoT alkalmazások és megoldások számára. Az Azure SQL Database Edge lehetővé teszi a kapcsolatok és a nem rokonok, például a JSON, a Graph és az idősoros adatsorozatok továbbítását, feldolgozását és elemzését, így a különböző modern IoT-alkalmazások számára is megfelelő választás.

Azure SQL Database Edge az [Microsoft SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)legújabb verziójára épül, amely piacvezető teljesítményt, biztonsági és lekérdezés-feldolgozási képességeket biztosít. Mivel az Azure SQL Database Edge ugyanarra a motorra épül, mint a SQL Server és a Azure SQL Database, ugyanazt a T-SQL programozási felületet biztosítja, amely megkönnyíti és gyorsabbá teszi az alkalmazások és megoldások fejlesztését, és ezzel egyidejűleg lehetővé teszi az alkalmazás IoT Edge eszközök, adatközpontok és a felhő közötti hordozhatóság.

## <a name="deployment-models"></a>Üzembe helyezési modellek

Azure SQL Database Edge elérhető az Azure piactéren, és modulként üzembe helyezhető [Azure IoT Edgehoz](../iot-edge/about-iot-edge.md). További információ: [Azure SQL Database Edge üzembe helyezése](deploy-portal.md).<br>

![SQL Database Edge – áttekintő diagram](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL Database Edge kiadásai

Az SQL Database Edge három különböző kiadással vagy szoftver csomaggal érhető el. Ezek a kiadások azonos szolgáltatáskészlet-készlettel rendelkeznek, és csak a használati jogosultságuk, valamint az általuk támogatott CPU-/memória-mennyiség tekintetében térnek el egymástól.

   |**Csomag**  |**Leírás**  |
   |---------|---------|
   |Fejlesztői  |  Csak fejlesztési SKU, az alább említett standard SKU által megszabott szabályozási korlátokkal |
   |Standard   |  A standard csomag legfeljebb 4 CPU-t és akár 32 GB memóriát támogat a SQL Database Edge-tárolóhoz. |
   |Prémium    |  A prémium SKU legfeljebb 8 magot és akár 64 GB memóriát támogat a SQL Database Edge-tárolóhoz. |

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Azure SQL Database jelenleg előzetes verzióban érhető el. További információ a díjszabásról és a rendelkezésre állásról: [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> A Azure SQL Database Edge és a SQL Server közötti különbségek megismeréséhez, valamint a különböző Azure SQL Database Edge-beállítások közötti különbségekről lásd: [SQL Database Edge Database-funkciók](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Folyamatos átviteli képességek  

Az Azure SQL Database Edge a valós idejű elemzések és az összetett események feldolgozásához biztosít beépített streaming képességeket. A folyamatos átviteli képesség ugyanazokat a szerkezeteket használja, mint a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) , és hasonló képességeket biztosít, mint [IoT Edge-eszközökön futó Azure stream Analytics](../stream-analytics/stream-analytics-edge.md).

Az Azure SQL Database Edge streaming Engine a kis késleltetésű, a rugalmasság, a sávszélesség és a megfelelőség hatékony kihasználására szolgál.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning és mesterséges intelligenciával kapcsolatos képességek

Az Azure SQL Database Edge beépített gépi tanulási és elemzési funkciókat biztosít a nyílt formátumú ONNX (nyílt neurális hálózati Exchange) futtatókörnyezet integrálásával, amely lehetővé teszi a mély tanulási és neurális hálózati modellek cseréjét a különböző keretrendszerek között. További információ a ONNX-ről: [itt](https://onnx.ai/). A ONNX Runtime lehetővé teszi, hogy a modelleket a kívánt nyelven vagy eszközökön fejlessze, és ezt követően a ONNX formátumra konvertálható legyen a SQL Database Edge-ben való végrehajtásra. További információ: [Machine learning és mesterséges intelligencia a ONNX SQL Database Edge-ben](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Azure SQL Database Edge használata

Az Azure SQL Database Edge egyszerűbbé és hatékonyabbá teszi az alkalmazások fejlesztését és karbantartását. A felhasználók az összes ismerős eszközt és szaktudást kihasználva nagyszerű alkalmazásokat és megoldásokat hozhatnak létre a IoT Edge igényeinek megfelelően. A felhasználó az alábbi eszközökkel fejlesztheti SQL Database Edge-ben:

- [A Azure Portal](https://portal.azure.com/) webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – ingyenes, letölthető ÜGYFÉLALKALMAZÁS bármely SQL-infrastruktúra kezelésére, SQL Serverról a SQL Databasere.
- [SQL Server Data Tools a Visual Studióban](/sql/ssdt/download-sql-server-data-tools-ssdt/) – ingyenes, letölthető ügyfélalkalmazás SQL Server-alapú, SQL Database-adatbázisok, Integration Services-csomagok, Analysis Services adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – ingyenes, letölthető, platformfüggetlen adatbázis-eszköz, amely a helyszíni és a Felhőbeli adatplatformok Microsoft-családját használja Windows, MacOS és Linux rendszereken.
- [Visual Studio Code](https://code.visualstudio.com/docs) – ingyenes, letölthető, nyílt forráskódú Kódszerkesztő Windows, MacOS és Linux rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database és Azure SQL Data Warehouse lekérdezéséhez.


## <a name="next-steps"></a>További lépések

- A díjszabással és a rendelkezésre állással kapcsolatos részletekért lásd: [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Az előfizetéshez tartozó Azure SQL Database Edge engedélyezése iránti kérelem.
- Első lépésként tekintse meg a következőket:
  - [SQL Database Edge üzembe helyezése Azure Portal](deploy-portal.md)
  - [Machine Learning és mesterséges intelligencia SQL Database Edge-vel](onnx-overview.md)
