---
title: Log Analytics-munkaterület létrehozása az Azure CLI használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre Egy Log Analytics-munkaterületet, amely lehetővé teszi a felügyeleti megoldásokat és az adatgyűjtést a felhőből és a helyszíni környezetből az Azure CLI segítségével.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0e91bc9c994a48b335c3ccb7373a9f4f5dc6d1e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605089"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Log Analytics-munkaterület létrehozása az Azure CLI 2.0-s rendszerrel

Az Azure CLI 2.0 az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure CLI 2.0-t egy Log Analytics-munkaterület üzembe helyezéséhez az Azure Monitorban. A Log Analytics-munkaterület az Azure Monitor naplóadatainak egyedi környezete. Minden munkaterület saját adattárházzal és konfigurációval rendelkezik, és az adatforrások és megoldások úgy vannak konfigurálva, hogy az adataikat egy adott munkaterületen tárolják. Ha a következő forrásokból kíván adatokat gyűjteni, naplóelemzési munkaterületre van szüksége:

* Azure-erőforrások az előfizetésben  
* A System Center Operations Manager által figyelt helyszíni számítógépek  
* Eszközgyűjtemények a Configuration Managertől  
* Diagnosztikai vagy naplóadatok az Azure Storage-ból  

Más források, például az Azure virtuális gépek és a Windows vagy Linux virtuális gépek a környezetben, tekintse meg az alábbi témaköröket:

* [Adatok gyűjtése az Azure virtuális gépeiről](../learn/quick-collect-azurevm.md)
* [Adatok gyűjtése hibrid Linux számítógépről](../learn/quick-collect-linux-computer.md)
* [Adatok gyűjtése hibrid Windows rendszerű számítógépről](quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre [egy ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Munkaterület létrehozása
Hozzon létre egy munkaterületet [az az csoport központi telepítésével.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) A következő példa létrehoz egy munkaterületet az *eastus* helyen egy Erőforrás-kezelő sablon használatával a helyi számítógépről. A JSON-sablon úgy van beállítva, hogy csak a munkaterület nevét kérje meg, és megadja az alapértelmezett értéket a többi paraméterhez, amelyek valószínűleg szabványos konfigurációként lennének a környezetben. Vagy tárolhatja a sablont egy Azure storage-fiókban a szervezetmegosztott hozzáféréshez. A sablonok használatával kapcsolatos további információkért [lásd: Erőforrások telepítése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

A támogatott régiókról a [Log Analytics régióban található,](https://azure.microsoft.com/regions/services/) és a **Termék keresése** mezőben keres.

A következő paraméterek alapértelmezett értéket állítanak meg:

* hely - alapértelmezés szerint usa keleti része
* sku - alapértelmezés szerint a 2018 áprilisában kiadott új GB-onkénti díjszabási szint

>[!WARNING]
>Ha egy Log Analytics-munkaterületet hoz létre vagy konfigurál egy olyan előfizetésben, amely az új 2018 áprilisi díjszabási modellt választotta, az egyetlen érvényes Log Analytics-díjszabási szint a **PerGB2018.**
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

2. A sablont az igényeinek megfelelően szerkesztheti. Tekintse át a [Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) sablonhivatkozást, és ismerje meg, hogy mely tulajdonságok és értékek támogatottak.
3. Mentse a fájlt **deploylaworkspacetemplate.json fájlként** egy helyi mappába.   
4. Készen áll a sablon üzembe helyezésére. Használja a sablont tartalmazó mappából származó alábbi parancsokat. Amikor a rendszer munkaterületi nevet kér, adjon meg egy globálisan egyedi nevet az összes Azure-előfizetésben.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

![Példa eredmény, ha a telepítés befejeződött](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>További lépések
Most, hogy rendelkezik egy rendelkezésre álló munkaterülettel, konfigurálhatja a figyelési telemetriai adatok gyűjteményét, naplókereséseket futtathat az adatok elemzéséhez, és hozzáadhat egy felügyeleti megoldást további adatok és elemzési elemzések biztosításához.  

* Ha engedélyezni szeretné az Azure-erőforrásokból származó adatgyűjtést az Azure Diagnostics vagy az Azure Storage használatával, olvassa [el az Azure-szolgáltatásnaplók és -metrikák gyűjtése a Log Analytics szolgáltatásban való használatra című témakört.](../platform/collect-azure-metrics-logs.md)  
* Adja hozzá [a System Center Operations Manageradatforrást](../platform/om-agents.md) az Operations Manager felügyeleti csoportját jelentéstevő ügynököktől, és tárolhatja azokat a Log Analytics-munkaterületen.  
* Csatlakoztassa a [Configuration Manager](../platform/collect-sccm.md) t a hierarchia gyűjteményeinek részét tartalmazó számítógépek importálásához.  
* Tekintse át az elérhető [figyelési megoldásokat,](../insights/solutions.md) és azt, hogy miként adhat hozzá vagy távolíthat el egy megoldást a munkaterületről.
