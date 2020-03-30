---
title: Adatbázisok kezelése az Azure Automation segítségével
description: Ismerje meg, hogyan használható az Azure Automation szolgáltatás az Azure SQL-adatbázisok nagy méretekben történő kezelésére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822810"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure SQL-adatbázisok kezelése az Azure Automation használatával

Ez az útmutató bemutatja az Azure Automation szolgáltatást, és hogyan használható az Azure SQL-adatbázisok felügyeletének egyszerűsítésére.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?

Az [Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely leegyszerűsíti a felhőkezelést a folyamatautomatizálás révén. Az Azure Automation használatával a hosszú ideig futó, manuális, hibalehetőségeket rejtő és gyakran ismétlődő feladatok automatizálhatók a megbízhatóság, a hatékonyság és a szervezet értékének növelése érdekében.

Az Azure Automation nagy megbízhatóságú és magas rendelkezésre állású munkafolyamat-végrehajtási motort biztosít, amely a szervezet növekedésével az igényeinek megfelelően méretezhető. Az Azure Automationben a folyamatok manuálisan, külső rendszerek által vagy ütemezett időközönként is elrúghatók, így a feladatok szükség esetén pontosan megtörténnek.

Alacsonyabb működési többletterhelés, és szabadítsa fel az IT / DevOps személyzetet, hogy olyan munkára összpontosíthassanak, amely növeli az üzleti értéket azáltal, hogy a felhőkezelési feladatokat az Azure Automation automatikusan futtatja.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hogyan segíthet az Azure Automation az Azure SQL-adatbázisok kezelésében?

Az Azure SQL Database az Azure [PowerShell-eszközökben](/powershell/azure/overview)elérhető [Azure SQL Database PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) kezelhető az Azure Automationben. Az Azure Automation rendelkezik ezekkel az Azure SQL Database PowerShell-parancsmagokkal, amelyek a dobozból kivehetők, így a szolgáltatáson belül elvégezheti az összes SQL DB felügyeleti feladatot. Ezeket a parancsmagokat az Azure Automationben más Azure-szolgáltatások parancsmagjaival is párosíthatja, így automatizálhatja az összetett feladatokat az Azure-szolgáltatásokban és a külső rendszerekben.

Az Azure Automation a PowerShell használatával sql-parancsok kibocsátásával közvetlenül is képes kommunikálni az SQL-kiszolgálókkal.

Az [Azure Automation runbook-gyűjteményszámos](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) termékcsapatot és közösségi runbookot tartalmaz az Azure SQL-adatbázisok, más Azure-szolgáltatások és külső rendszerek felügyeletének automatizálásához. Galéria runbookok közé:

- [SQL-lekérdezések futtatása SQL Server-adatbázisban](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Az Azure SQL-adatbázis vertikális méretezése (fel- vagy le) ütemezés szerint](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [SQL-tábla csonkolása, ha az adatbázis mérete megközelíti a maximális méretét](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Táblák indexelése egy Azure SQL-adatbázisban, ha azok nagyon töredezettek](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az Azure Automation alapjait, és hogy miként használható az Azure SQL-adatbázisok kezeléséhez, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg az Azure Automationről.

- [Az Azure Automation áttekintése](../automation/automation-intro.md)
- [Az első runbookom](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Az SQL-ügynök a felhőben](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 