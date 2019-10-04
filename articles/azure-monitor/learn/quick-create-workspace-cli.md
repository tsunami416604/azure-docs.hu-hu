---
title: Hozzon létre egy Log Analytics-munkaterületet az Azure CLI-vel |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Log Analytics-munkaterület felügyeleti megoldások és az adatgyűjtésről a felhőbeli és helyszíni környezeteket az Azure CLI-vel való engedélyezéséhez.
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
ms.openlocfilehash: 7ee8302a026cf7584996bca481e79190586b77b3
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838843"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Log Analytics-munkaterület létrehozásához Azure CLI 2.0 használatával

Az Azure CLI 2.0 az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Log Analytics munkaterületet a Azure Monitorban az Azure CLI 2,0 használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Az előfizetés Azure-erőforrások  
* A helyszíni System Center Operations Manager által felügyelt számítógépek  
* A System Center Configuration Manager eszközgyűjtemények  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  
 
Más forrásokból, például az Azure virtuális gépek és a Windows vagy Linux rendszerű virtuális gépek a környezetben a következő témakörökben talál:

* [Azure virtuális gépekről történő adatgyűjtést](../learn/quick-collect-azurevm.md)
* [Hibrid Linux számítógépekről történő adatgyűjtést](../learn/quick-collect-linux-computer.md)
* [Hibrid Windows-számítógépekről történő adatgyűjtést](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre [egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Hozzon létre egy munkaterületet az [az Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)paranccsal. Az alábbi példa egy munkaterületet hoz létre a *eastus* helyen a helyi számítógép Resource Manager-sablonjának használatával. A JSON-sablon csak kéri a munkaterület nevére van beállítva, és valószínűleg használni kívánt szabványos konfigurációt a környezetében, a többi paraméter alapértelmezett értéket határoz meg. Vagy tárolhatja az Azure storage-fiók, megosztott hozzáférésre. a sablon a szervezet. További információ a sablonokkal való munkát: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/resource-group-template-deploy-cli.md)

További információ a támogatott régiókkal kapcsolatban: [régiók log Analytics](https://azure.microsoft.com/regions/services/) a (Azure monitor), és keressen rá a **termék** keresése elemre. 

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
4. Készen áll a sablon üzembe helyezésére. Használja az alábbi parancsokat a sablont tartalmazó mappából. Ha a rendszer a munkaterület nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:

![Ha üzembe helyezés kész eredményének](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>További lépések
Most, hogy a munkaterület érhető el, figyelési telemetriai adatok gyűjtésének konfigurálása, naplókereséseket elemezheti az adatokat, és adjon hozzá egy felügyeleti megoldás, további adat- és elemzési elemzéseket biztosít.  

* Ahhoz, hogy az adatok gyűjtését az Azure-erőforrásokat az Azure Diagnostics vagy az Azure storage, lásd: [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../platform/collect-azure-metrics-logs.md).  
* Adjon hozzá [System Center Operations Manager alkalmazást adatforrásként](../platform/om-agents.md) adatokat gyűjteni az Operations Manager felügyeleti csoportnak jelentő ügynökök és a Log Analytics-munkaterületen tárolja.  
* Csatlakozás [Configuration Manager](../platform/collect-sccm.md) számítógépek, amelyek tagjai a hierarchiában lévő gyűjtemények importálása.  
* Tekintse át az elérhető [figyelési megoldásokat](../insights/solutions.md) , valamint azt, hogyan adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
