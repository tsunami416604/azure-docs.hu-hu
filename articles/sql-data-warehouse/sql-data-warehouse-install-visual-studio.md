---
title: A Visual Studio és az SSDT telepítése SQL Data Warehouse |} Microsoft Docs
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a2f01424dedb977000d0e4150f4a31c1a9a21cfb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790644"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>A Visual Studio és az SSDT telepítése SQL Data Warehouse
Alkalmazások fejlesztéséhez az SQL Data Warehouse, azt javasoljuk, a Visual Studio legújabb verziójának SQL Server Data Tools (SSDT) legújabb verziójával.  A Visual Studio 2013 Update 5 és az SSDT együttes használata is támogatott a visszamenőleges kompatibilitás érdekében.  

Visual Studio és az SSDT együttes használata lehetővé teszi az SQL Server Object Explorer segítségével vizuálisan megismerkedhet a táblákat, nézeteket, tárolt eljárásokat és sok más objektumot az SQL Data Warehouse, továbbá lekérdezéseket is futtathat.

> [!NOTE]
> Az SQL Data Warehouse még nem támogatja a Visual Studio-adatbázisprojekteket. Ez a funkció a rendszeres frissítések fogadásához adjon szavazhatnak [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>1. lépés: A Visual Studio telepítése
Az alábbi hivatkozásokból letöltése és telepítése a Visual Studio. Ha már rendelkezik a Visual Studio 2013 vagy újabb verziója, továbbléphet a 2. lépés, az SSDT telepítése.

1. [Töltse le a Visual Studio][].
2. Kövesse a [Visual Studio telepítése] [ Installing Visual Studio] útmutatót az MSDN Webhelyén, és válassza az alapértelmezett konfigurációkat.

## <a name="step-2-install-ssdt"></a>2. lépés: Az SSDT telepítése
A Visual Studióban az SSDT telepítése, ha bejelöli a Visual studióban az SSDT-frissítéseket az alábbiak szerint.

1. A Visual Studióban kattintson a **eszközök** / **bővítmények és frissítések...** / **Frissítések**
2. Válassza a **Product Updates** (Termékfrissítések) lehetőséget, majd keresse a **Microsoft SQL Server Update for database tooling** (Microsoft SQL Server frissítése adatbáziseszközökkel) elemet

Ha nem található frissítés, valószínűleg a legújabb verziót használja.  Annak megerősítéséhez, hogy az SSDT telepítve van, kattintson a **Help** / **About Microsoft Visual Studio** (Súgó > A Microsoft Visual Studio névjegye) lehetőségre, és keresse meg az SQL Server Data Toolst a listában. Az SSDT legújabb verziója a 14.0.60525.0-s verzió. Ha a telepítését nem érhető el a Visual Studióból, ellátogathat a [SSDT letöltése] [ SSDT Download] lapon töltse le és az SSDT manuális telepítése.

## <a name="next-steps"></a>További lépések
Most, hogy az SSDT legújabb verzióját, készen áll [csatlakozás] [ connect] az SQL Data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Töltse le a Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
