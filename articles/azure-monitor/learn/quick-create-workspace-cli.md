---
title: Log Analytics munkaterület létrehozása az Azure CLI használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Log Analytics munkaterületet a Felhőbeli és a helyszíni környezetekben az Azure CLI-vel való felügyeleti megoldások és adatgyűjtés engedélyezéséhez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: b696d57919383e87f8e5e647b774fc9e4dbdf16b
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513473"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Log Analytics munkaterület létrehozása az Azure CLI 2,0-vel

Az Azure CLI 2.0 az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Log Analytics munkaterületet a Azure Monitorban az Azure CLI 2,0 használatával. A Log Analytics munkaterület egy egyedi környezet, amely Azure Monitor naplózza az adatnaplót. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat. Ha a következő forrásokból szeretne adatgyűjtést végrehajtani, Log Analytics munkaterületre van szüksége:

* Azure-erőforrások az előfizetésében  
* System Center Operations Manager által figyelt helyszíni számítógépek  
* Configuration Managerból származó eszközök gyűjteményei  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  

Más forrásokhoz, például az Azure-beli virtuális gépekhez és a környezetben található Windows-vagy Linux-alapú virtuális gépekhez a következő témakörökben talál további információt:

* [Adatok gyűjtése az Azure Virtual Machines szolgáltatásból](../learn/quick-collect-azurevm.md)
* [Adatok gyűjtése hibrid Linux rendszerű számítógépről](../learn/quick-collect-linux-computer.md)
* [Adatok gyűjtése hibrid Windows-számítógépről](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre [egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Munkaterületek létrehozása
Hozzon létre egy munkaterületet az [az Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)paranccsal. Az alábbi példa egy munkaterületet hoz létre a *eastus* helyen a helyi számítógép Resource Manager-sablonjának használatával. A JSON-sablon úgy van konfigurálva, hogy csak a munkaterület nevére Kérdezzen, és a többi olyan paraméter alapértelmezett értékét adja meg, amely valószínűleg a környezetben megszokott konfigurációként lenne felhasználva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

További információ a támogatott régiókkal kapcsolatban: [régiók log Analytics](https://azure.microsoft.com/regions/services/) a (Azure monitor), és keressen rá a **termék** keresése elemre.

A következő paraméterek alapértelmezett értéket állítanak be:

* Location (alapértelmezett) – az USA keleti régiója
* SKU – alapértelmezett érték az új GB-os díjszabási szinten, amely az áprilisi 2018 díjszabási modellben jelent meg

>[!WARNING]
>Ha Log Analytics munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új, április 2018 díjszabási modellbe van lefoglalva, az egyetlen érvényes Log Analytics díjszabási csomag **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Sablon létrehozása és üzembe helyezése

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

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Tekintse át a [Microsoft. OperationalInsights/munkaterületek sablonjának](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) hivatkozását, hogy megtudja, milyen tulajdonságokat és értékeket támogat a rendszer.
3. Mentse ezt a fájlt **deploylaworkspacetemplate. JSON** néven egy helyi mappába.   
4. Készen áll a sablon üzembe helyezésére. Használja az alábbi parancsokat a sablont tartalmazó mappából. Ha a rendszer a munkaterület nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy elérhetővé tett egy munkaterületet, beállíthatja a figyelési telemetria gyűjteményét, futtathatja a naplók kereséseit az adatelemzéshez, és hozzáadhat egy felügyeleti megoldást további információk és elemzési elemzések megadásához.  

* Az Azure-erőforrások Azure Diagnostics vagy az Azure Storage szolgáltatással történő gyűjtésének engedélyezéséhez lásd: az [Azure-szolgáltatások naplófájljainak és metrikáinak gyűjtése a log Analytics való használatra](../platform/collect-azure-metrics-logs.md).  
* Adja hozzá [System Center Operations Manager adatforrásként](../platform/om-agents.md) az Operations Manager felügyeleti csoportot jelentő ügynököktől származó adatok gyűjtéséhez, és tárolja azt a log Analytics munkaterületen.  
* [Configuration Manager](../platform/collect-sccm.md) összekapcsolásával importálhatja azokat a számítógépeket, amelyek a hierarchiában gyűjtemények tagjai.  
* Tekintse át az elérhető [figyelési megoldásokat](../insights/solutions.md) , valamint azt, hogyan adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
