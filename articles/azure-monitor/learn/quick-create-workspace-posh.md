---
title: Hozzon létre egy Log Analytics-munkaterületet az Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Log Analytics-munkaterület ahhoz, hogy felügyeleti megoldások és az adatgyűjtésről a felhőbeli és helyszíni környezetből az Azure PowerShell használatával.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 908e2c7f5ad8350880b1a85a68d8b3a5486041d5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099654"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Log Analytics-munkaterület létrehozása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modul üzembe helyezése az Azure-ban, amely egy saját adattárházzal, adatforrások és megoldások egyedi környezet Log Analytics-munkaterületet.  Ebben a cikkben leírt lépések szükségesek, ha azt tervezi, a következő forrásokból származó adatok gyűjtése a:

* Az előfizetés Azure-erőforrások  
* A helyszíni System Center Operations Manager által felügyelt számítógépek  
* A System Center Configuration Manager eszközgyűjtemények  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  
 
Más forrásokból, például az Azure virtuális gépek és a Windows vagy Linux rendszerű virtuális gépek a környezetben a következő témakörökben talál:

* [Azure virtuális gépekről történő adatgyűjtést](../../azure-monitor/learn/quick-collect-azurevm.md)
* [Hibrid Linux számítógépekről történő adatgyűjtést](../../azure-monitor/learn/quick-collect-linux-computer.md)
* [Hibrid Windows-számítógépekről történő adatgyűjtést](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre [egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Hozzon létre egy munkaterület a [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). A következő példában létrehozunk egy nevű munkaterület *TestWorkspace* erőforráscsoportban *labor* a a *eastus* hely helyi Resource Manager-sablon használatával gép. A JSON-sablon csak kéri a munkaterület nevére van beállítva, és valószínűleg használni kívánt szabványos konfigurációt a környezetében, a többi paraméter alapértelmezett értéket határoz meg. 

A következő paraméterekkel állítsa be az alapértelmezett érték:

* hely – USA keleti RÉGIÓJA, az alapértelmezett érték
* Termékváltozat - alapértelmezés szerint a 2018 áprilisi díjszabási modell megjelent új GB-onkénti tarifacsomag kiválasztása

>[!WARNING]
>Ha létrehozása vagy konfigurálása a Log Analytics-munkaterület egy előfizetésben, amely rendelkezik az új 2018 áprilisi díjszabási modell jelentkezett, van-e a csak érvényes tarifacsomagban a Log Analytics **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Hozzon létre, és a sablon üzembe helyezése

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Szerkessze a sablont az igényeknek.  Felülvizsgálat [Microsoft.OperationalInsights/workspaces sablon](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referencia megtudhatja, milyen tulajdonságok és értékek támogatottak. 
3. Mentse a fájlt **deploylaworkspacetemplate.json** egy helyi mappába.   
4. Készen áll a sablon üzembe helyezésére. Használja az alábbi parancsokat a sablont tartalmazó könyvtárban:

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:

![Ha üzembe helyezés kész eredményének](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>További lépések
Most, hogy a munkaterület érhető el, figyelési telemetriai adatok gyűjtésének konfigurálása, naplókereséseket elemezheti az adatokat, és adjon hozzá egy felügyeleti megoldás, további adat- és elemzési elemzéseket biztosít.  

* Ahhoz, hogy az adatok gyűjtését az Azure-erőforrásokat az Azure Diagnostics vagy az Azure storage, lásd: [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../../azure-monitor/platform/collect-azure-metrics-logs.md).  
* Adjon hozzá [System Center Operations Manager alkalmazást adatforrásként](../../azure-monitor/platform/om-agents.md) adatokat gyűjteni az Operations Manager felügyeleti csoportnak jelentő ügynökök és a Log Analytics-munkaterületen tárolja.  
* Csatlakozás [Configuration Manager](../../azure-monitor/platform/collect-sccm.md) számítógépek, amelyek tagjai a hierarchiában lévő gyűjtemények importálása.  
* Tekintse át a [felügyeleti megoldások](../../azure-monitor/insights/solutions.md) érhető el, és hogyan lehet hozzáadni vagy megoldás eltávolítása a munkaterületről.
