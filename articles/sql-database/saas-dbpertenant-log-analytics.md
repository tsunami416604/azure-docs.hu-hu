---
title: Az Azure Monitor naplói több-bérlős alkalmazással
description: Az Azure Monitor-naplók beállítása és használata több-bérlős Azure SQL Database SaaS-alkalmazással
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214491"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Az Azure Monitor-naplók beállítása és használata több-bérlős SQL Database SaaS-alkalmazással

Ebben az oktatóanyagban beállítja és használja az [Azure Monitor naplók](/azure/log-analytics/log-analytics-overview) rugalmas készletek és adatbázisok figyeléséhez. Ez az oktatóanyag a [Teljesítményfigyelés és -kezelés oktatóanyagra](saas-dbpertenant-performance-monitoring.md)épül. Bemutatja, hogyan használhatja az Azure Monitor naplók az Azure Portalon biztosított figyelésés és riasztás bővítéséhez. Az Azure Monitor naplói több ezer rugalmas készlet és több százezer adatbázis figyelését támogatják. Az Azure Monitor naplói egyetlen figyelési megoldást kínálnak, amely integrálhatja a különböző alkalmazások és Azure-szolgáltatások figyelését több Azure-előfizetésbe.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja az Azure Monitor naplóit.
> * Az Azure Monitor naplóival figyelheti a készleteket és az adatbázisokat.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis-bérlőnként alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis-bérlőnkénti alkalmazás telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Tekintse meg a [teljesítményfigyelési és -kezelési oktatóanyag](saas-dbpertenant-performance-monitoring.md) a SaaS-forgatókönyvek és minták, valamint hogyan befolyásolják a felügyeleti megoldás követelményeinek megvitatását.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Adatbázis- és rugalmaskészlet-teljesítmény figyelése és kezelése az Azure Monitor naplóival

Az Azure SQL Database figyelése és riasztása az Azure Portalon található adatbázisokban és készletekben érhető el. Ez a beépített figyelés és riasztás kényelmes, de erőforrás-specifikus is. Ez azt jelenti, hogy kevésbé alkalmas a nagy telepítések figyelésére, vagy egységes nézetet biztosít az erőforrások és az előfizetések között.

Nagy mennyiségű forgatókönyvek esetén használhatja az Azure Monitor naplók figyelése és riasztása. Az Azure Monitor egy különálló Azure-szolgáltatás, amely lehetővé teszi a potenciálisan számos szolgáltatás munkaterületén összegyűjtött naplók elemzését. Az Azure Monitor naplói beépített lekérdezési nyelvet és adatvizualizációs eszközöket biztosít, amelyek lehetővé teszik a működési adatelemzést. Az SQL Analytics-megoldás számos előre definiált rugalmas készletet és adatbázis-figyelést és riasztási nézeteket és lekérdezéseket biztosít. Az Azure Monitor naplók is biztosít egy egyéni nézet tervezője.

Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük. A Log Analytics-munkaterületeket és elemzési megoldásokat az Azure Portalon nyitják meg. Az Azure Portal az újabb hozzáférési pont, de előfordulhat, hogy az operations management suite portál mögött bizonyos területeken.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Teljesítménydiagnosztikai adatok létrehozása a bérlők munkaterhelésének szimulálásával 

1. A PowerShell ISE-ben nyissa meg *a .. WingtipTicketsSaaS-MultiTenantDb-master\\tanulási\\modulok teljesítményfigyelése és kezelése\\Demo-PerformanceMonitoringAndManagement.ps1 . \\* Tartsa nyitva ezt a parancsfájlt, mert előfordulhat, hogy az oktatóanyag során több terhelésgenerálási forgatókönyvet is futtatni szeretne.
1. Ha még nem tette meg, a bérlők egy kötegét kell kiépíteni, hogy a figyelési környezet érdekesebbé váltsa. Ez a folyamat néhány percet vesz igénybe.

   a. Állítsa be **$DemoScenario = 1**, _Bérlőtétel kiépítése_.

   b. A parancsfájl futtatásához és további 17 bérlő üzembe helyezéséhez nyomja le az F5 billentyűt.

1. Most indítsa el a terhelésgenerátort egy szimulált terhelés futtatásához az összes bérlőn.

    a. Állítsa **be $DemoScenario = 2**, Normál _intenzitási terhelés létrehozása (körülbelül 30 DTU)_.

    b. A parancsfájl futtatásához nyomja le az F5 billentyűt.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlőnkénti alkalmazásparancsfájlok beszereznie

A Wingtip Jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub tárház. A Wingtip Tickets PowerShell-parancsfájlok letöltésének és blokkolásának feloldásához az általános útmutatás ban talál [útmutatást.](saas-tenancy-wingtip-app-guidance-tips.md)

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>A Log Analytics munkaterület és az Azure SQL Analytics-megoldás telepítése és konfigurálása

Az Azure Monitor egy külön szolgáltatás, amelyet konfigurálni kell. Az Azure Monitor naplói naplóadatokat, telemetriai adatokat és metrikákat gyűjtenek a Log Analytics-munkaterületen. Az Azure más erőforrásaihoz hasonlóan létre kell hozni egy Log Analytics-munkaterületet. A munkaterületet nem kell ugyanabban az erőforráscsoportban létrehozni, mint az általa figyelt alkalmazásokat. Ennek gyakran teszi a legtöbb értelme mégis. A Wingtip jegyek alkalmazás egyetlen erőforráscsoport használatával győződjön meg arról, hogy a munkaterület törlődik az alkalmazással.

1. A PowerShell ISE-ben nyissa meg *a .. WingtipTicketsSaaS-MultiTenantDb-master\\tanulási\\modulok teljesítményfigyelése és felügyeleti\\naplóanalytics\\Demo-LogAnalytics.ps1 . \\*
1. A parancsfájl futtatásához nyomja le az F5 billentyűt.

Most már megnyithatja az Azure Monitor-naplókat az Azure Portalon. Néhány percet vesz igénybe a telemetriai adatok gyűjtése a Log Analytics-munkaterületen, és láthatóvá tétele. Minél hosszabb ideig hagyja el a rendszert diagnosztikai adatok gyűjtése, annál érdekesebb a tapasztalat. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>A Log Analytics munkaterület és az SQL Analytics-megoldás használata a készletek és adatbázisok figyelésére


Ebben a gyakorlatban nyissa meg a Log Analytics-munkaterületet az Azure Portalon az adatbázisok és készletek összegyűjtött telemetriai adatokból.

1. Keresse fel az [Azure Portalt](https://portal.azure.com). Válassza a **Minden szolgáltatás** lehetőséget a Log Analytics-munkaterület megnyitásához. Ezután keresse meg a Log Analytics.

   ![A Log Analytics munkaterületének megnyitása](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Válassza ki a _wtploganalytics&lt;&gt;nevű_munkaterületet.

1. **Válassza az Áttekintés lehetőséget** a logelemzési megoldás megnyitásához az Azure Portalon.

   ![Áttekintés](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Eltarthat néhány percig, amíg a megoldás aktívvá vált. 

1. Válassza ki az **Azure SQL Analytics** csempét a megnyitásához.

    ![Áttekintő csempe](media/saas-dbpertenant-log-analytics/overview.png)

1. A megoldás nézetei oldalra görgethetők, alul saját belső görgetősávjukkal. Szükség esetén frissítse az oldalt.

1. Az összefoglaló lap felfedezéséhez jelölje ki a csempéket vagy az egyes adatbázisokat a leásókezelő megnyitásához.

    ![Naplóelemzési irányítópult](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Az időtartomány módosításához módosítsa a szűrőbeállítást. Ehhez az oktatóanyaghoz válassza az **Utolsó 1 óra**lehetőséget.

    ![Időszűrő](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Válasszon ki egy adott adatbázist az adatbázis lekérdezéshasználatának és metrikáinak feltárásához.

    ![Adatbázis-elemzés](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. A használati mutatók megtekintéséhez görgessen jobbra az elemzési oldalon.
 
     ![Adatbázis-mutatók](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Görgessen balra az elemzési laphoz, és válassza ki a kiszolgálócsempét az **Erőforrás adatai** listában.  

    ![Erőforrás-információk listája](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Megnyílik egy lap, amely a kiszolgálón lévő készleteket és adatbázisokat jeleníti meg.

    ![Kiszolgáló készletekkel és adatbázisokkal](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Jelöljön ki egy készletet. A megnyíló készletlapon görgessen jobbra a készlet metrikáinak megtekintéséhez. 

    ![Készletmetrikák](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. A Log Analytics-munkaterületen válassza az **OMS-portál** lehetőséget a munkaterület megnyitásához.

    ![Log Analytics-munkaterület](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

A Log Analytics-munkaterületen további anno megismerheti a napló- és metrikaadatokat. 

Figyelés és riasztás az Azure Monitor naplók alapulnak lekérdezések a munkaterület i adatok, ellentétben az Azure Portalon az egyes erőforrásokon meghatározott riasztási. A riasztások lekérdezésekre alapozza, és egyetlen riasztást határozhat meg, amely az összes adatbázisra néz, ahelyett, hogy adatbázisonként egyet definiálna. A lekérdezéseket csak a munkaterületen elérhető adatok korlátozzák.

Ha többet szeretne tudni arról, hogy miként használhatja az Azure Monitor naplóklekérdezését és riasztások at, olvassa el a [Riasztási szabályok használata az Azure Monitor naplóiban című témakört.](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)

Az Azure Monitor naplózza az SQL Database díjak alapján az adatkötet a munkaterületen. Ebben az oktatóanyagban létrehozott egy ingyenes munkaterületet, amely napi 500 MB-ra korlátozódik. A korlát elérése után az adatok már nem kerülnek a munkaterületre.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Telepítse és konfigurálja az Azure Monitor naplóit.
> * Az Azure Monitor naplóival figyelheti a készleteket és az adatbázisokat.

Próbálja ki a [Bérlő elemzési oktatóanyagát.](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek a kezdeti Wingtip Jegyek SaaS-adatbázis-bérlőnkénti alkalmazás-telepítésre épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Az Azure Monitor naplói](../azure-monitor/insights/azure-sql.md)
