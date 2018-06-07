---
title: Azure SQL Database-adatbázisok Azure Automation kezelése |} Microsoft Docs
description: További tudnivalók hogyan az Azure Automation szolgáltatás Azure SQL-adatbázisok léptékű kezelésére használható.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: a45a3e7e8c4381aa7df15628bfc57f5ad4f94100
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647601"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure SQL Database-adatbázisok Azure Automation kezelése
Az útmutatóból megismerheti az Azure Automation szolgáltatás, és hogyan használható egyszerűbbé teheti az Azure SQL-adatbázisok kezelését.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation szolgáltatásbeli](https://azure.microsoft.com/services/automation/) egy Azure szolgáltatás felhő kezelésüket folyamatok automatizálása révén. Azure Automation használ, hosszan futó, manuális, hibákhoz vezethet, és gyakran ismétlődő feladatok automatizálhatók megbízhatóságát, hatékonyságát és a szervezet hamarabb növelése érdekében.

Azure Automation szolgáltatásbeli egy magas rendelkezésre állású és nagymértékben megbízható munkafolyamat-végrehajtási motorjának, amely az igényeinek, ha a szervezet növekedésének megfelelően méretezi biztosít. Az Azure Automationben folyamatok is lehet kezdődött el manuálisan, a 3. fél rendszerek vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan, ha szükséges.

Működési munkaterhelés csökkentése és az szabadítson fel informatikai / azáltal, hogy a felhő felügyeleti feladatok automatikusan Azure Automation által futtatandó value DevOps személyzet üzleti hozzáadó munkahelyi összpontosíthat.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hogyan segíthet az Azure Automation Azure SQL-adatbázisok kezelése?
Az Azure SQL Database az Azure Automationben használatával kezelhető a [Azure SQL Database PowerShell parancsmagjait](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) , amelyek elérhetők a [Azure PowerShell eszközök](/powershell/azure/overview). Azure Automation a elérhető Azure SQL Database PowerShell parancsmagok alapesetben rendelkezik, így az SQL-adatbázis a felügyeleti feladatokat a szolgáltatáson belül végezheti el. Ezek a parancsmagok az Azure Automationben más Azure-szolgáltatások, Azure-szolgáltatások és a 3. fél rendszerek között összetett feladatok automatizálása a parancsmagjaival is párosítható.

Azure Automation szolgáltatásbeli is van lehetősége, kiszolgálókkal való kommunikációhoz SQL közvetlenül, PowerShell-lel SQL-parancsok beírásával.

A [Azure Automation-runbook gyűjtemény](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) termék csoportját, és a Közösség runbookok Ismerkedés az Azure SQL-adatbázisok, más Azure-szolgáltatások és a 3. fél rendszerek felügyeletének automatizálására különböző tartalmaz. Gyűjteményelem forgatókönyvek a következők:

* [SQL Server-adatbázis SQL-lekérdezések futtatásához](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Függőleges méretezni a (felfelé vagy lefelé) Azure SQL adatbázis ütemezés szerint](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Egy SQL-tábla csonkolni, ha az adatbázis megközelíti a maximális méretét](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Azure SQL adatbázis táblák index, ha azok nagy mértékben töredezettek](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Automation, és hogyan használható az Azure SQL-adatbázisok kezelése alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Automation.

* [Azure Automation – áttekintés](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
* [Azure Automation tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation szolgáltatásbeli: Az SQL Agent a felhőben](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

