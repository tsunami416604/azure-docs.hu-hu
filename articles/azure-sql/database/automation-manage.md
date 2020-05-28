---
title: Adatbázisok kezelése Azure Automation
description: Ismerje meg, hogyan használható a Azure Automation szolgáltatás a Azure SQL Database nagy léptékű kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4b5378b5df36c158c3f401f214730b4f493f5def
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043610"
---
# <a name="managing-azure-sql-database-using-azure-automation"></a>Azure SQL Database kezelése Azure Automation használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb.md)]

Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként lehet egyszerűsíteni az adatbázisok kezelését az Azure SQL Database-ben.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a hosszan futó, a manuális, a hibákra hajlamos és a gyakran ismétlődő feladatok használatával automatizálható a szervezet megbízhatóságának, hatékonyságának és időpontjának növelése. További információ az első lépésekről: [Azure Automation intro](../../automation/automation-intro.md)

A Azure Automation magas megbízhatóságot és magas rendelkezésre állást biztosító munkafolyamat-végrehajtó motort biztosít, amely a szervezet növekedésének megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse a működési terhelést, és szabadítson fel informatikai/DevOps-személyzetet, hogy az üzleti értékeket felhasználva a Felhőbeli felügyeleti feladatok automatikus futtatásával Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-database"></a>Hogyan lehet Azure Automation segíteni a Azure SQL Database kezelésében

A Azure SQL Database Azure Automation a [Azure PowerShell eszközökben](/powershell/azure/overview)elérhető [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) használatával kezelhetők. Azure Automation ezen Azure SQL Database PowerShell-parancsmagok elérhetők a dobozból, így az összes SQL Database felügyeleti feladatot elvégezheti a szolgáltatáson belül. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső rendszerek között.

A Azure Automation az SQL-kiszolgálókkal való közvetlen kommunikációt is lehetővé teszi az SQL-parancsok a PowerShell használatával történő kiadásával.

A runbook és modul-galériák [Azure Automation](../../automation/automation-runbook-gallery.md) a Microsoft és a Közösség különböző runbookok, amelyeket importálhat Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Azure Automation alapjait, és azt, hogy miként használható a Azure SQL Database kezelésére, kövesse az alábbi hivatkozásokat, amelyekkel többet is megtudhat a Azure Automationről.

- [Az Azure Automation áttekintése](../../automation/automation-intro.md)
- [Az első runbookom](../../automation/learn/automation-tutorial-runbook-graphical.md)
