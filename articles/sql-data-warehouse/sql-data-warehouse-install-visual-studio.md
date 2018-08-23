---
title: A Visual Studio és az SSDT telepítése SQL Data warehouse-hoz |} A Microsoft Docs
description: A Visual Studio és az SQL Server Development Tools (SSDT) telepítése az Azure SQL Data Warehouse-hoz
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 11e4e6e887f7c182c1c321d8837e11f07afc2f6b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444725"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>A Visual Studio és az SSDT telepítése SQL Data warehouse-hoz
Alkalmazások fejlesztéséhez az SQL Data warehouse-ba, javasoljuk a Visual Studio legújabb verziójának használata az SQL Server Data Tools (SSDT) legújabb verzióját.  A Visual Studio 2013 Update 5 és az SSDT együttes használata is támogatott a visszamenőleges kompatibilitás érdekében.  

A Visual Studio és az SSDT együttes használata lehetővé teszi, hogy az SQL Server Object Explorer használatával vizuálisan táblák, nézetek, a tárolt eljárásokat és sok más objektumot az SQL Data Warehouse bemutatása, valamint a lekérdezések futtatása.

> [!NOTE]
> Az SQL Data Warehouse még nem támogatja a Visual Studio-adatbázisprojekteket. A funkció a rendszeres frissítések fogadásához szavazhat [UserVoice-on].
> 
> 

## <a name="step-1-install-visual-studio"></a>1. lépés: A Visual Studio telepítése
Az alábbi hivatkozásokból töltse le és telepítse a Visual Studióban. Ha már rendelkezik Visual Studio 2013 vagy újabb verziója, továbbléphet a 2. lépésben, az SSDT telepítése.

1. [A Visual Studio letöltése][].
2. Kövesse a [Installing Visual Studio] [ Installing Visual Studio] útmutatót az MSDN Webhelyén, és válassza az alapértelmezett konfigurációkat.

## <a name="step-2-install-ssdt"></a>2. lépés: Az SSDT telepítése
A Visual Studióban az SSDT telepítése, először ellenőrizze a Visual studióban az SSDT frissítés a következő lépésekkel.

1. A Visual Studióban kattintson a **eszközök** / **bővítmények és frissítések...** / **Frissítések**
2. Válassza a **Product Updates** (Termékfrissítések) lehetőséget, majd keresse a **Microsoft SQL Server Update for database tooling** (Microsoft SQL Server frissítése adatbáziseszközökkel) elemet

Ha nem található frissítés, valószínűleg a legújabb verziót használja.  Annak megerősítéséhez, hogy az SSDT telepítve van, kattintson a **Help** / **About Microsoft Visual Studio** (Súgó > A Microsoft Visual Studio névjegye) lehetőségre, és keresse meg az SQL Server Data Toolst a listában. Az SSDT legújabb verziója a 14.0.60525.0-s verzió. Ha telepítésére nem érhető el a Visual Studióból, ellátogathat a [az SSDT letöltése] [ SSDT Download] lapon töltse le és az SSDT manuális telepítése.

## <a name="next-steps"></a>További lépések
Most, hogy az SSDT legújabb verzióját, készen áll [csatlakozás] [ connect] az SQL Data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[A Visual Studio letöltése]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice-on]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
