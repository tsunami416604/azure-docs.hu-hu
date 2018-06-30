---
title: A Naplóelemzési Azure PaaS erőforrás gyűjtéséhez |} Microsoft Docs
description: 'Útmutató: Azure PaaS erőforrás metrikáit adatgyűjtést megőrzési és elemzése a Log Analyticshez PowerShell használatával.'
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
ms.date: 11/13/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b44a6627ab12c8a4ad21e7beded7c5fd2c2e1d39
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128462"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>A Naplóelemzési konfigurálása Azure PaaS erőforrás metrikáit gyűjteménye

Az Azure Platform (PaaS) szolgáltatás erőforrások, például az Azure SQL és webhelyekhez (webalkalmazások), mint metrikák teljesítményadatok natív módon Naplóelemzési el tudná küldeni. Ez a parancsfájl lehetővé teszi a felhasználók metrikák már telepített egy adott erőforráscsoportban vagy egy teljes előfizetésből PaaS erőforrások naplózás engedélyezéséhez. 

Jelenleg nincs mód kattintva engedélyezheti őket az Azure portálon keresztül erőforrások PaaS naplózása. Ezért kell használnia egy PowerShell-parancsfájlt. A natív metrikák naplózási képesség Naplóelemzési figyelését, valamint lehetővé teszik Azure-erőforrások léptékű figyelését. 

## <a name="prerequisites"></a>Előfeltételek
Ellenőrizze, hogy rendelkezik-e a következő Azure Resource Manager-modulok telepítve a számítógépen, a folytatás előtt:

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Azt javasoljuk, hogy az Azure Resource Manager modulok-példányok kompatibilitás érdekében a Powershellből Azure Resource Manager parancsok futtatásakor azonos verziójúak.
>
A számítógépen az Azure Resource Manager modulok legújabb verziójának telepítéséhez tekintse át [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Az Azure Diagnostics engedélyezése  
Azure Diagnostics konfigurálása PaaS erőforrások úgy érhető el, a parancsfájl végrehajtása **Enable-AzureRMDiagnostics.ps1**, az elérhető a [PowerShell-galériában](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  A parancsfájl a következő szituációkat ismerteti:
  
* Az előfizetés egy vagy több erőforráscsoportok kapcsolódó erőforrás megadása  
* Adja meg az előfizetést az adott erőforráscsoport kapcsolódó erőforrás  
* Egy erőforrást egy másik munkaterület továbbítania újrakonfigurálása

Csak erőforrásokat, amelyek támogatják az Azure diagnostics gyűjtését metrikák, és közvetlenül a Naplóelemzési küldje támogatottak.  Részletes listáját, [gyűjtése Azure szolgáltatás naplók és a metrikák Naplóelemzési használható](log-analytics-azure-storage.md) 

A következő lépésekkel töltse le és futtassa a parancsfájlt.

1.  Írja be a Windows kezdőképernyőről **PowerShell** , és kattintson a jobb gombbal PowerShell a keresési eredmények.  Válassza a menü **Futtatás rendszergazdaként**.   
2. Mentse a **Enable-AzureRMDiagnostics.ps1** parancsfájl fájlt helyileg futtassa a következő parancsot, és tárolja a parancsfájl elérési útját biztosítása.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Futtassa a `Connect-AzureRmAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.   
4. Futtassa a következő parancsfájlt `.\Enable-AzureRmDiagnostics.ps1` szeretné az adatgyűjtést egy adott erőforráshoz, az előfizetés vagy az paraméterrel paraméterek nélkül `-ResourceGroup <myResourceGroup>` erőforrás megadásához egy adott erőforráscsoportban.   
5. Válassza ki a megfelelő előfizetést a listából, ha egynél több, a megfelelő érték megadásával.<br><br> ![Válassza ki a parancsfájl által visszaadott előfizetés](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Ellenkező esetben az egyetlen rendelkezésre előfizetés automatikusan kijelöli.
6. A parancsfájl a következő előfizetéshez regisztrált Naplóelemzési munkaterület listáját adja vissza.  Jelölje ki a megfelelő a listából.<br><br> ![Válassza ki a parancsfájl által visszaadott munkaterület](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Válassza ki az Azure-erőforrás, amelyet szeretné az adatgyűjtést. Például 5 írja be, ha engedélyezi az Azure SQL-adatbázisok adatgyűjtés.<br><br> ![Parancsfájl által visszaadott válassza erőforrástípus](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Erőforrásokat, amelyek támogatják az Azure Diagnostics és az közvetlenül egy Naplóelemzési küldése gyűjtését metrikák jelölhet ki.  A parancsfájl egy értékét fogja megjeleníteni **igaz** alatt a **metrikák** oszlop az előfizetés vagy a megadott erőforráscsoport határait erőforrások listáját.    
8. A kijelölés megerősítését kéri.  Adja meg **Y** összes metrikák naplózásának engedélyezése a kijelölt definiált, a hatókör erőforrásokat, amelyek a fenti példában az előfizetés az összes SQL-adatbázisoknál.  

A parancsfájl minden erőforrás kijelölt feltételeknek megfelelő futtatni, és a metrikák gyűjtését engedélyezése. Miután befejeződött, egy üzenetet konfigurálása nem fejeződött be, láthatja.  

A művelet befejezését követően hamarosan hozzákezdhet a Log Analyticshez tárházat az Azure PaaS erőforrás adatai.  A típusú rekord `AzureMetrics` jön létre és támogatja ezeket a rekordokat elemzése a [Azure SQL elemzés](log-analytics-azure-sql.md) és [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md) megoldásokat.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Egy erőforrás szeretnék adatokat küldeni a másik munkaterület frissítése
Ha egy erőforrást, amely már küld adatokat a Naplóelemzési munkaterületet, és később úgy dönt, való hivatkozáshoz egy másik munkaterület azt úgy átkonfigurálni, futtathatja a parancsprogram a `-Update` paraméter.  

**Példa:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

A rendszer bekéri a parancsfájlt, hogy a kezdeti konfiguráció futtatásakor fogadja a hívást, ugyanazokat az információkat.  

## <a name="next-steps"></a>További lépések

* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére. 

* Használjon [egyéni mezők](log-analytics-custom-fields.md)(sikerült elemezni a eseményrekordok egyes mezőkbe.

* Felülvizsgálati [használható egyéni irányítópult létrehozása a Naplóelemzési](log-analytics-dashboards.md) keres megtudhatja, hogyan jelenítheti meg a napló a szervezete számára ésszerű módszerrel.
