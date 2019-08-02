---
title: A Visual Studio és a SSDT telepítése a SQL Data Warehousehoz | Microsoft Docs
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479490"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>A Visual Studio és a SSDT telepítése SQL Data Warehouse
A Visual Studio 2019 alkalmazással fejlesztheti SQL Data Warehouse. A SQL Data Warehouse jelenleg nem támogatja a Visual Studio 2019 SSDT. 

A Visual Studio és a SSDT segítségével a SQL Server Object Explorer segítségével megismerheti a táblákat, a nézeteket, a tárolt eljárásokat és számos további objektumot vizuálisan a SQL Data Warehouse. Lehetővé teszi a lekérdezések futtatását is.

> [!NOTE]
> Az SQL Data Warehouse még nem támogatja a Visual Studio-adatbázisprojekteket. A szolgáltatás rendszeres frissítéseinek fogadásához szavazzon a [UserVoice-on].
> 
> 

## <a name="step-1-install-visual-studio"></a>1\. lépés: A Visual Studio telepítése
A Visual Studio letöltéséhez és telepítéséhez kövesse az alábbi hivatkozásokat. Ha már telepítve van a Visual Studio 2013-es vagy újabb verziója, ugorjon a 2. lépésre, és telepítse a SSDT.

1. [A Visual Studio letöltése][].
2. Kövesse az [Installing Visual Studio][Installing Visual Studio] (A Visual Studio telepítése) útmutatót az MSDN webhelyén, és válassza az alapértelmezett konfigurációkat.

## <a name="step-2-install-ssdt"></a>2\. lépés: A SSDT telepítése
A Visual studióhoz készült SSDT telepítéséhez először tekintse meg a SSDT frissítését a Visual studióból a következő lépésekkel.

1. A Visual Studióban kattintson a **Tools** / Extensions and Updates elemre **...** / **Frissítések**
2. Válassza a **Product Updates** (Termékfrissítések) lehetőséget, majd keresse a **Microsoft SQL Server Update for database tooling** (Microsoft SQL Server frissítése adatbáziseszközökkel) elemet

Ha nem talál frissítést, telepítenie kell a legújabb verziót. Annak megerősítéséhez, hogy az SSDT telepítve van, kattintson a **Help** / **About Microsoft Visual Studio** (Súgó > A Microsoft Visual Studio névjegye) lehetőségre, és keresse meg az SQL Server Data Toolst a listában. Ha a telepítés lehetőség nem érhető el a Visual studióból, látogasson el a [SSDT letöltési][SSDT Download] oldalára, ahol manuálisan letöltheti és telepítheti a SSDT.

## <a name="next-steps"></a>További lépések
Most, hogy a SSDT legújabb verzióját használja, készen áll a SQL Data Warehouse [][connect] való kapcsolódásra.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[A Visual Studio letöltése]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice-on]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
