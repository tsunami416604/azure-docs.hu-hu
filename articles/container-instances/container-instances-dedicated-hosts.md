---
title: Telepítés dedikált gazdagépen
description: Dedikált gazdagép használata az Azure Container Instances számítási feladataivalódi gazdagép-elkülönítésének eléréséhez
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025033"
---
# <a name="deploy-on-dedicated-hosts"></a>Üzembe helyezés dedikált gazdagépeken

A "dedikált" egy Azure Container Instances (ACI) termékváltozat, amely egy elkülönített és dedikált számítási környezetet biztosít a biztonságosan futó tárolók számára. A dedikált termékváltozat-eredmények használata minden egyes tárolócsoportban, amely egy dedikált fizikai kiszolgálóegy Azure-adatközpontban, biztosítva a teljes számítási feladatok elkülönítését, hogy megfeleljen a szervezet biztonsági és megfelelőségi követelményeinek. 

A dedikált termékváltozat megfelelő a tároló számítási feladatok, amelyek megkövetelik a számítási feladatok elkülönítését a fizikai kiszolgáló szempontjából.

## <a name="prerequisites"></a>Előfeltételek

* A dedikált termékváltozat használatára szolgáló előfizetések alapértelmezett korlátja 0. Ha szeretné használni ezt a termékváltozatot az éles tároló központi telepítések, hozzon létre egy [Azure-támogatási kérelmet][azure-support] a korlát növeléséhez.

## <a name="use-the-dedicated-sku"></a>A dedikált termékváltozat használata

> [!IMPORTANT]
> A dedikált termékváltozat használata csak a legújabb API-verzióban (2019-12-01) érhető el, amely jelenleg be van vezetve. Adja meg ezt az API-verziót a központi telepítési sablonban.
>

A 2019-12-01 API-verzióval `sku` kezdődően van egy tulajdonság a telepítési sablon tárolócsoport-tulajdonságok szakasza alatt, amely az ACI központi telepítéséhez szükséges. Jelenleg használhatja ezt a tulajdonságot egy Azure Resource Manager üzembe helyezési sablon részeként az ACI.Currently, you can use this property as part of an Azure Resource Manager deployment template for ACI. További információ az ACI-erőforrások sablonnal történő központi telepítéséről az [oktatóanyagban: Többtárolós csoport telepítése Erőforrás-kezelő sablon használatával.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

A `sku` tulajdonság az alábbi értékek egyikével rendelkezhet:
* `Standard`- a standard ACI telepítési választás, amely még mindig garantálja a hipervizor szintű biztonságot 
* `Dedicated`- számítási feladatok szintjének elkülönítésére szolgál dedikált fizikai gazdagépekkel a tárolócsoporthoz

## <a name="modify-your-json-deployment-template"></a>A JSON-telepítési sablon módosítása

A központi telepítési sablonban módosítsa vagy adja hozzá a következő tulajdonságokat:
* A `resources`területen `apiVersion` `2019-12-01`a beállítás a.
* A tárolócsoport tulajdonságai alatt `sku` adjon `Dedicated`hozzá egy értékű tulajdonságot.

Íme egy példa egy tárolócsoport központi telepítési sablonerőforrás-szakaszának, amely a dedikált termékváltozatot használja:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

A következőkben egy teljes sablon, amely egyetlen tárolópéldányt futtató mintatárolócsoportot telepít:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>A tárolócsoport üzembe helyezése

Ha létrehozta és szerkesztette a központi telepítési sablonfájlt az asztalon, feltöltheti azt a Cloud Shell könyvtárába a fájl húzásával. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az [az csoport telepítési létrehozási][az-group-deployment-create] parancsával.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A sikeres üzembe helyezés egy dedikált gazdagépen történik.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
