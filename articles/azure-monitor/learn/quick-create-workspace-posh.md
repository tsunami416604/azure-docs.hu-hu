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
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: 6f27aeb65cb9077011e662c165ca26202546db26
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905732"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Log Analytics-munkaterület létrehozása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modul üzembe helyezése az Azure monitorban Log Analytics-munkaterületet. Log Analytics-munkaterület olyan Azure Monitor naplóadatok egyedi környezet. Az egyes munkaterületeken saját adattár és a konfigurációs van, és tárolja az adatokat egy adott munkaterület az adatforrások és megoldások vannak konfigurálva. Ha azt tervezi, a következő forrásokból származó adatok gyűjtése a Log Analytics-munkaterület szükséges:

* Az előfizetés Azure-erőforrások  
* A helyszíni System Center Operations Manager által felügyelt számítógépek  
* A System Center Configuration Manager eszközgyűjtemények  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  
 
Más forrásokból, például az Azure virtuális gépek és a Windows vagy Linux rendszerű virtuális gépek a környezetben a következő témakörökben talál:

* [Azure virtuális gépekről történő adatgyűjtést](../learn/quick-collect-azurevm.md)
* [Hibrid Linux számítógépekről történő adatgyűjtést](../learn/quick-collect-linux-computer.md)
* [Hibrid Windows-számítógépekről történő adatgyűjtést](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre [egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz az Azure PowerShell-lel Az modul. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Hozzon létre egy munkaterületet a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). A következő példában létrehozunk egy nevű munkaterület *TestWorkspace* erőforráscsoportban *labor* a a *eastus* hely helyi Resource Manager-sablon használatával gép. A JSON-sablon csak kéri a munkaterület nevére van beállítva, és valószínűleg használni kívánt szabványos konfigurációt a környezetében, a többi paraméter alapértelmezett értéket határoz meg. 

További információ a támogatott régiók: [Log Analytics az elérhető régiók](https://azure.microsoft.com/regions/services/) , és keresse meg az Azure Monitor az **keresse meg a termék** mező. 

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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
            "apiVersion": "2015-11-01-preview",
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

2. Szerkessze a sablont az igényeknek. Felülvizsgálat [Microsoft.OperationalInsights/workspaces sablon](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referencia megtudhatja, milyen tulajdonságok és értékek támogatottak. 
3. Mentse a fájlt **deploylaworkspacetemplate.json** egy helyi mappába.   
4. Készen áll a sablon üzembe helyezésére. Használja az alábbi parancsokat a sablont tartalmazó könyvtárban:

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:

![Ha üzembe helyezés kész eredményének](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>További lépések
Most, hogy a munkaterület érhető el, figyelési telemetriai adatok gyűjtésének konfigurálása, naplókereséseket elemezheti az adatokat, és adjon hozzá egy felügyeleti megoldás, további adat- és elemzési elemzéseket biztosít.  

* Ahhoz, hogy az adatok gyűjtését az Azure-erőforrásokat az Azure Diagnostics vagy az Azure storage, lásd: [gyűjtése az Azure szolgáltatás naplókat és mérőszámokat használható az Azure monitorban](../platform/collect-azure-metrics-logs.md).  
* Adjon hozzá [System Center Operations Manager alkalmazást adatforrásként](../platform/om-agents.md) adatokat gyűjteni az Operations Manager felügyeleti csoportnak jelentő ügynökök és a Log Analytics-munkaterületen tárolja.  
* Csatlakozás [Configuration Manager](../platform/collect-sccm.md) számítógépek, amelyek tagjai a hierarchiában lévő gyűjtemények importálása.  
* Tekintse át a [figyelési megoldások](../insights/solutions.md) érhető el, és hogyan lehet hozzáadni vagy megoldás eltávolítása a munkaterületről.
