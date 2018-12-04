---
title: Gyűjtése a Log Analytics használatával az Azure PaaS erőforrás-mérőszámok |} A Microsoft Docs
description: Útmutató a PowerShell-lel megőrzési és elemzése a Log Analytics Azure PaaS erőforrás-metrikák gyűjtésének engedélyezéséhez.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 464e497c806a0b3172cbf4f5ae802693def1b435
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832666"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Az Azure PaaS-erőforrás-metrikák gyűjtésének konfigurálása a Log Analytics használatával

A Platformszolgáltatás (PaaS) erőforrásokat, például az Azure SQL és a Websites (webalkalmazások), mint az Azure Platform kibocsátható metrikák teljesítményadatok natív módon a Log Analytics szolgáltatásba. Ez a szkript lehetővé teszi, hogy a felhasználók számára, hogy engedélyezze a mérőszámok, naplózás a már üzembe helyezte az egy adott erőforráscsoportban vagy egy teljes előfizetés PaaS-erőforrásokhoz. 

Nincs még ma, hogy engedélyezze a mérőszámok, naplózás a paas-hez erőforrásokat az Azure Portalon keresztül lehetőség. Ezért szeretné használni egy PowerShell-parancsfájlt. A natív metrikák naplózási képesség figyelés a Log Analytics, valamint lehetővé teszi nagy mennyiségű Azure-erőforrások figyeléséhez. 

## <a name="prerequisites"></a>Előfeltételek
Ellenőrizze, hogy rendelkezik-e az alábbi Azure Resource Manager-modulokat, mielőtt folytatná a számítógépre telepített:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Azt javasoljuk, hogy az Azure Resource Manager-modulokat is ugyanazt a verziót a kompatibilitás érdekében az Azure Resource Manager-parancsok a Powershellből való futtatásakor.
>
Az Azure Resource Manager modul legújabb verziójának telepítése a számítógépre: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Az Azure-diagnosztika engedélyezése  
A PaaS-erőforrásokat az Azure Diagnostics konfigurálásának kapcsolódással hajtsa végre a parancsprogramot, **engedélyezése – AzureRMDiagnostics.ps1**, amely érhető el a [PowerShell-galériából](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52).  A szkript a következő eseteket támogatja:
  
* Adjon meg egy vagy több erőforráscsoport az előfizetéshez kapcsolódó erőforrások  
* Adjon meg egy adott erőforráscsoporthoz az előfizetéshez kapcsolódó erőforrások  
* Konfigurálja újra úgy, hogy egy másik munkaterülethez továbbítsa erőforrás

Csak olyan erőforrásokat, amelyek támogatják az Azure-beli diagnosztikai metrikák gyűjtését, és küldése közvetlenül a Log Analytics használata támogatott.  Tekintse át a részletes listát [gyűjtése az Azure naplói és a Log Analytics használati metrikái](collect-azure-metrics-logs.md) 

A következő lépésekkel letöltéséhez, és hajtsa végre a parancsfájlt.

1.  A Windows kezdőképernyőn írja be a **PowerShell** PowerShell a jobb gombbal kattintson a keresési eredmények közül.  Válassza a menü **Futtatás rendszergazdaként**.   
2. Mentse a **engedélyezése – AzureRMDiagnostics.ps1** helyileg futtassa a következő parancsot, és tárolja a parancsfájl elérési útjának megadása a parancsfájlt.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Futtassa a `Connect-AzureRmAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.   
4. Futtassa a következő szkriptet `.\Enable-AzureRmDiagnostics.ps1` engedélyezni az adatgyűjtést egy adott erőforrást az előfizetésében, vagy a paraméter a paraméterek nélkül `-ResourceGroup <myResourceGroup>` erőforrás megadásához egy adott erőforráscsoportban.   
5. Válassza ki a megfelelő előfizetést a listából, ha egynél több, a helyes érték megadásával.<br><br> ![Parancsfájl által visszaadott előfizetés kiválasztása](./media/collect-azurepass-posh/script-select-subscription.png)<br> Ellenkező esetben automatikusan kijelöli az egyetlen elérhető előfizetés.
6. A szkript ezután az előfizetés regisztrálva Log Analytics-munkaterületek listáját adja vissza.  Válassza ki a megfelelő elemet a listából.<br><br> ![Válassza ki a parancsfájl által visszaadott munkaterület](./media/collect-azurepass-posh/script-select-workspace.png)<br> 
7. Válassza ki az Azure-erőforrást, amelyet szeretne a gyűjtés engedélyezéséhez. Ha például 5 írja be, engedélyezheti az adatok gyűjtése az SQL Azure adatbázis.<br><br> ![Parancsfájl által visszaadott válassza erőforrás típusa](./media/collect-azurepass-posh/script-select-resource.png)<br>
   Az Azure Diagnostics és az küldése közvetlenül a Log Analyticshez való gyűjtését metrikák támogató erőforrások csak választhat.  A szkript egy értékét fogja megjeleníteni **igaz** alatt a **metrikák** oszlop felderíti az előfizetés vagy a megadott erőforráscsoport az erőforrások listáját.    
8. A kijelölés megerősítését kéri.  Adja meg **Y** összes metrikák naplózásának engedélyezése a kiválasztott definiált, a hatókör erőforrásokat, amelyek ebben a példában az előfizetésben található összes SQL-adatbázisok.  

A parancsfájl futtatásához a kiválasztott feltételeknek megfelelő minden egyes erőforrás, és a hozzájuk metrikák gyűjtésének engedélyezéséhez. Miután elkészült, egy üzenet jelzi a konfiguráció véget ér, látni fogja.  

Hamarosan a művelet befejezését követően hozzákezdhet az Azure PaaS-erőforrás a Log Analytics-adattárban adatainak megtekintéséhez.  Típusú rekord `AzureMetrics` jön létre, és ezeket a rekordokat elemzése által támogatott a [Azure SQL Analytics](../../log-analytics/log-analytics-azure-sql.md) és [Azure Web Apps Analytics](../../log-analytics/log-analytics-azure-web-apps-analytics.md) felügyeleti megoldások.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Egy erőforrás adatokat küldeni egy másik munkaterület frissítése
Ha egy erőforrás, amely már küld adatokat a Log Analytics-munkaterülethez van, és később úgy dönt, azt egy másik munkaterület hivatkozni úgy, a parancsprogram futtatásával az `-Update` paraméter.  

**Példa:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

A rendszer bekéri válaszolni, ugyanazokat az információkat, mint a kezdeti konfigurálása a szkriptet futtatta.  

## <a name="next-steps"></a>További lépések

* Ismerje meg [naplókereséseket](../../azure-monitor/log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 

* Használat [egyéni mezők](../../log-analytics/log-analytics-custom-fields.md)(így az eseményrekordok elemezni az egyes mezőket.

* Felülvizsgálat [használható egyéni irányítópult létrehozása a Log Analytics](../../azure-monitor/platform/dashboards.md) tudni, hogyan jelenítheti meg a napló keres környezetben megfelelőnek bizonyuló a szervezet számára.
