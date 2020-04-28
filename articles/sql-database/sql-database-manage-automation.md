---
title: Adatbázisok kezelése Azure Automation
description: Ismerje meg, hogyan használható a Azure Automation szolgáltatás az Azure SQL Database-adatbázisok nagy léptékű kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822810"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure SQL Database-adatbázisok kezelése Azure Automation használatával

Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként használható az Azure SQL-adatbázisok kezelésének egyszerűbbé tételéhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a hosszan futó, a manuális, a hibákra hajlamos és a gyakran ismétlődő feladatok használatával automatizálható a szervezet megbízhatóságának, hatékonyságának és időpontjának növelése.

A Azure Automation magas megbízhatóságot és magas rendelkezésre állást biztosító munkafolyamat-végrehajtó motort biztosít, amely a szervezet növekedésének megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse a működési terhelést, és szabadítson fel informatikai/DevOps-személyzetet, hogy az üzleti értékeket felhasználva a Felhőbeli felügyeleti feladatok automatikus futtatásával Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hogyan segíthet Azure Automation az Azure SQL Database-adatbázisok kezelésében?

A Azure SQL Database Azure Automation a [Azure PowerShell eszközökben](/powershell/azure/overview)elérhető [Azure SQL Database PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) használatával kezelhetők. Azure Automation ezekkel a Azure SQL Database PowerShell-parancsmagokkal elérhetők a dobozból, így az összes SQL-adatbázis-felügyeleti feladatot elvégezheti a szolgáltatáson belül. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső rendszerek között.

A Azure Automation az SQL-kiszolgálókkal való közvetlen kommunikációt is lehetővé teszi az SQL-parancsok a PowerShell használatával történő kiadásával.

A [Azure Automation runbook](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) -katalógus számos termék-és közösségi runbookok tartalmaz, amelyekkel automatizálhatja az Azure SQL Database-adatbázisok, más Azure-szolgáltatások és külső rendszerek felügyeletét. A gyűjtemény runbookok a következők:

- [SQL-lekérdezések futtatása SQL Server adatbázison](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Függőlegesen (felfelé vagy lefelé) egy Azure SQL Database ütemezett méretezése](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [SQL-tábla csonkítása, ha az adatbázisa a maximális méretet közelíti meg](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Táblázatok indexelése egy Azure SQL Database, ha nagyon töredezettek](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Azure Automation alapjait és az Azure SQL Database-adatbázisok felügyeletének módját, az alábbi hivatkozásokat követve további információkat tudhat meg a Azure Automationról.

- [Az Azure Automation áttekintése](../automation/automation-intro.md)
- [Az első runbookom](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: az SQL-ügynök a felhőben](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 