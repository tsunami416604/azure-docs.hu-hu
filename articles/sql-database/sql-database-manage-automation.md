---
title: Azure Automation használatával Azure SQL-adatbázisok kezelése |} A Microsoft Docs
description: Ismerje meg hogyan az Azure Automation szolgáltatás nagy mennyiségű Azure SQL Database-adatbázisok kezeléséhez használható.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3360482c77dd262a81bebb885c95f72616c7764a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159533"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Azure Automation használatával Azure SQL Database adatbázisok felügyelete
Ez az útmutató vezet be, az Azure Automation szolgáltatást, és hogyan használható az Azure SQL-adatbázisok kezelésének egyszerűsítéséhez.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Az Azure Automation](https://azure.microsoft.com/services/automation/) leegyszerűsíti a felhőfelügyelet a folyamatok automatizálása révén az Azure-szolgáltatások. Az Azure Automationnel, hosszan futó, manuális, sok hibalehetőséget rejtő és gyakran ismételt feladatok automatizálható a megbízhatóság, hatékonyságot és a szervezet teljesítések idejét.

Az Azure Automation segítségével egy rendkívül megbízható és magas rendelkezésre állású munkafolyamat-végző összetevőjével, amely az igényeinek, ha a szervezet növekedésével skálázható. Az Azure Automation szolgáltatás folyamatokat is lehet kezdődjön manuálisan, 3. rendszerekben, vagy rendszeres időközönként, hogy a feladatok fordulhat elő, pontosan akkor, ha szükséges.

Csökkentheti üzemeltetési terheit és szabadítson fel informatikai fejlesztési és üzemeltetési csapatának munkára, így üzleti érték a felhőfelügyeleti feladatokat automatikusan Azure Automation által futtatandó való váltással.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hogyan segít az Azure Automation kezelése az Azure SQL Database-adatbázisok?
Az Azure SQL Database használatával kezelhetők az Azure Automationben a [Azure SQL Database PowerShell parancsmagjait](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#sql) , amelyek elérhetők a [Azure PowerShell-eszközök](/powershell/azure/overview). Az Azure Automation a ezeket az Azure SQL Database PowerShell parancsmagjait elérhető, beépített rendelkezik, így a SQL DB-felügyeleti feladatokat a szolgáltatáson belül is végrehajthatja. Ezek a parancsmagok az Azure Automation parancsmagjaival más Azure-szolgáltatások, az összetett feladatok automatizálása Azure-szolgáltatások és a 3. fél rendszerek párosítása is történik.

Az Azure Automation is képes kommunikálni az SQL-kiszolgálók közvetlenül, PowerShell-lel SQL-parancsok kiadásával.

A [Azure Automation forgatókönyv-katalógusában](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) automatizálásának Azure SQL Database-adatbázisok, a más Azure-szolgáltatások és a 3. fél rendszerek felügyeleti csoport és a Közösség runbookok termék különböző tartalmaz. A katalógus forgatókönyveinek a következők:

* [SQL Server-adatbázis SQL-lekérdezések futtatásához](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Vertikális skálázása a (felfelé vagy lefelé) az Azure SQL Database egy ütemezés szerint](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Csonkolása egy SQL-táblára, ha az adatbázis megközelíti a maximális méretét](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Az Azure SQL Database tábláiban indexelése, ha azok a nagy mértékben töredezettek](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Automation, és hogyan használható az Azure SQL-adatbázisok kezelését, az alábbi hivatkozásokból tudhat meg többet az Azure Automationben.

* [Azure Automation áttekintése](../automation/automation-intro.md)
* [Az első runbookom](../automation/automation-first-runbook-graphical.md)
* [Az Azure Automation tanulási térkép](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Az Azure Automation: Az SQL Agent a felhőben](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

