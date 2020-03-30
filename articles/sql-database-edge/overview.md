---
title: Mi az Azure SQL Database Edge? | Microsoft Docs
description: További információ az Azure SQL Database Edge-ről
keywords: bevezetés az SQL database edge,mi az sql adatbázis szélén, sql adatbázis szélén áttekintés
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246704"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Mi az Azure SQL Database Edge előzetes verzió?

Az Azure SQL Database Edge Preview egy optimalizált relációs adatbázis-motor, amely IoT és IoT Edge-telepítésekhez készült. Lehetővé teszi, hogy nagy teljesítményű adattárolási és -feldolgozási réteget hozzon létre az IoT-alkalmazások és -megoldások számára. Az Azure SQL Database Edge lehetővé teszi a relációs és nem relációs adatok, például a JSON, a graph és az idősorozat-adatok streamelését, feldolgozását és elemzését, ami a különböző modern IoT-alkalmazások számára a megfelelő választássá teszi.

Az Azure SQL Database Edge a [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)legújabb verzióira épül, amely iparágvezető teljesítményt, biztonságot és lekérdezésfeldolgozási lehetőségeket biztosít. Mivel az Azure SQL Database Edge ugyanarra a motorra épül, mint az SQL Server és az Azure SQL Database, ugyanazt a T-SQL programozási felületet biztosítja, amely megkönnyíti és gyorsabbá teszi az alkalmazások vagy megoldások fejlesztését, ugyanakkor az alkalmazásokat az IoT Edge-eszközök, adatközpontok és a felhő közötti hordozhatóság.

## <a name="deployment-models"></a>Telepítési modellek

Az Azure SQL Database Edge elérhető az Azure Marketplace-en, és az [Azure IoT Edge](../iot-edge/about-iot-edge.md)moduljaként telepíthető. További információ: [Deploy Azure SQL Database Edge](deploy-portal.md).<br>

![SQL Database Edge – áttekintő diagram](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Az SQL Database Edge kiadásai

Az SQL Database Edge három különböző kiadással vagy szoftvercsomaggal érhető el. Ezek a kiadások azonos funkciókészletekkel rendelkeznek, és csak a használati jogok és az általuk támogatott cpu/memória mennyisége tekintetében különböznek egymástól.

   |**Csomag**  |**Leírás**  |
   |---------|---------|
   |Az Azure SQL Database Edge fejlesztője  |  Fejlesztési csak sku, minden SQL Database Edge tároló legfeljebb 4 mag és 32 GB memória  |
   |Azure SQL Database Edge    |  Éles környezetben minden SQL Database Edge-tároló legfeljebb 8 magra és 64 GB memóriára korlátozódik. |

## <a name="pricing-and-availability"></a>Árak és elérhetőség

Az Azure SQL Database jelenleg előzetes verzióban érhető el. A díjszabásról és az elérhetőségről az [Azure SQL Database Edge című témakörben](https://azure.microsoft.com/services/sql-database-edge/)talál további információt.

> [!IMPORTANT]
> Az Azure SQL Database Edge és az SQL Server közötti szolgáltatáskülönbségek, valamint az Azure SQL Database Edge különböző beállításai közötti különbségek megértéséhez olvassa el az [SQL Database Edge-adatbázis szolgáltatásai című témakört.](https://azure.microsoft.com/services/sql-database-edge/)

## <a name="streaming-capabilities"></a>Streamelési lehetőségek  

Az Azure SQL Database Edge beépített streamelési lehetőségeket biztosít a valós idejű elemzéshez és az összetett eseményfeldolgozáshoz. A streamelési funkció az [Azure Stream Analytics-nek](../stream-analytics/stream-analytics-introduction.md) megfelelő konstrukciók használatával épül fel, és hasonló képességeket biztosít, mint [az Azure Stream Analytics az IoT Edge-en.](../stream-analytics/stream-analytics-edge.md)

Az Azure SQL Database Edge streamelési motorja alacsony késleltetésű, rugalmassági, hatékony sávszélesség-kihasználási és megfelelőségi célokra készült.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Gépi tanulási és mesterséges intelligencia képességek

Az Azure SQL Database Edge beépített gépi tanulási és elemzési képességeket biztosít a nyílt formátumú ONNX (Open Neural Network Exchange) futásidő integrálásával, amely lehetővé teszi a mélytanulási és neurális hálózati modellek cseréjét a különböző keretrendszerek között. Az ONNX-szel kapcsolatos további információkért lásd [itt](https://onnx.ai/). Az ONNX futásidejű biztosítja a rugalmasságot a modellek fejlesztéséhez az Ön által választott nyelven vagy eszközökben, amelyek et aztán átlehet alakítani AZ ONNX formátumra az SQL Database Edge-en belüli végrehajtáshoz. További információ: [Machine Learning and Artificial Intelligence with ONNX in SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Az Azure SQL Database Edge szolgáltatás sal

Az Azure SQL Database Edge egyszerűbbé és hatékonyabbá teszi az alkalmazások fejlesztését és karbantartását. A felhasználók az összes ismerős eszközt és készséget használhatják az IoT Edge igényeinek megfelelő nagyszerű alkalmazások és megoldások létrehozásához. A felhasználó az SQL Database Edge-ben fejleszthet az alábbi eszközökkel:

- [Az Azure Portal](https://portal.azure.com/) – Egy webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – Ingyenes, letölthető ügyfélalkalmazás bármely SQL-infrastruktúra kezeléséhez, az SQL Server kiszolgálótól az SQL Database-ig.
- [SQL Server Data Tools a Visual Studio-](/sql/ssdt/download-sql-server-data-tools-ssdt/) Ingyenes, letölthető ügyfélalkalmazás SQL Server relációs adatbázisok, SQL-adatbázisok, Integrációs szolgáltatások csomagjai, Analysis Services-adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – Egy ingyenes, letölthető, platformfüggetlen adatbázis-kezelő eszköz az adatok professzionális segítségével a Microsoft család helyszíni és felhőalapú adatplatformok Windows, MacOS és Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Ingyenes, letölthető, nyílt forráskódú kódszerkesztő Windows, macOS és Linux rendszerhez. Támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse lekérdezéséhez.


## <a name="next-steps"></a>További lépések

- A díjszabással és a rendelkezésre állással kapcsolatos részletekről az [Azure SQL Database Edge című témakörben talál.](https://azure.microsoft.com/services/sql-database-edge/)
- Az Azure SQL Database Edge előfizetéséhez való engedélyezéséhez.
- Első lépésekhez olvassa el az alábbi témaköröket:
  - [SQL Database Edge telepítése az Azure Portalon keresztül](deploy-portal.md)
  - [Gépi tanulás és mesterséges intelligencia az SQL Database Edge-el](onnx-overview.md)
