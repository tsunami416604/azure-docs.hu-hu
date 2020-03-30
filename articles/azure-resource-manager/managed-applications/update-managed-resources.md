---
title: Kezelt erőforrások frissítése
description: Bemutatja, hogyan dolgozhat egy Azure által felügyelt alkalmazás felügyelt erőforráscsoportjában lévő erőforrásokon.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651214"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Erőforrások kal való munka az Azure által felügyelt alkalmazás felügyelt erőforráscsoportjában

Ez a cikk a felügyelt alkalmazás részeként üzembe helyezett erőforrások frissítését ismerteti. Egy felügyelt alkalmazás közzétevőjeként hozzáférhet a felügyelt erőforráscsoport erőforrásaihoz. Az erőforrások frissítéséhez meg kell találnia a felügyelt alkalmazáshoz társított felügyelt erőforráscsoportot, és el kell érnie az erőforráscsoportot az adott erőforráscsoportban.

Ez a cikk feltételezi, hogy telepítette a felügyelt alkalmazást a [felügyelt webalkalmazásban (IaaS)](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) az Azure felügyeleti szolgáltatások mintaprojektjével. A felügyelt alkalmazás tartalmaz egy **Standard_D1_v2** virtuális gépet. Ha még nem telepítette a felügyelt alkalmazást, továbbra is használhatja ezt a cikket a felügyelt erőforráscsoport frissítésének lépéseimegismeréséhez.

Az alábbi képen látható az üzembe helyezett felügyelt alkalmazás.

![Telepített felügyelt alkalmazás](./media/update-managed-resources/deployed.png)

Ebben a cikkben az Azure CLI használatával:

* A felügyelt alkalmazás azonosítása
* A kezelt erőforráscsoport azonosítása
* A felügyelt erőforráscsoportban lévő virtuálisgép-erőforrás(ok) azonosítása
* Módosítsa a virtuális gép méretét (ha nem használja, kisebb méretre, vagy nagyobbra a nagyobb terhelés támogatásához)
* Házirend hozzárendelése a felügyelt erőforráscsoporthoz, amely megadja az engedélyezett helyeket

## <a name="get-managed-application-and-managed-resource-group"></a>Felügyelt alkalmazás- és felügyelt erőforráscsoport beszerezni

A felügyelt alkalmazások erőforráscsoportban való lekéreléséhez használja a következőket:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

A felügyelt erőforráscsoport azonosítójának lekért:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Virtuális gépek átméretezése a felügyelt erőforráscsoportban

A felügyelt erőforráscsoportban lévő virtuális gépek megtekintéséhez adja meg a felügyelt erőforráscsoport nevét.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

A virtuális gépek méretének frissítéséhez használja a következőket:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

A művelet befejezése után ellenőrizze, hogy az alkalmazás a Standard D2 v2-n fut-e.

![Felügyelt alkalmazás standard D2 v2 használatával](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Házirend alkalmazása felügyelt erőforráscsoportra

A felügyelt erőforráscsoport és a házirend hozzárendelése az adott hatókörben. Az **e56962a6-4747-49cd-b67b-bf8b01975c4c** házirend az engedélyezett helyek meghatározására szolgáló beépített házirend.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Az engedélyezett helyek megtekintéséhez használja a következőket:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A házirend-hozzárendelés megjelenik a portálon.

![Házirend-hozzárendelés megtekintése](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Mintaprojektekről [lásd: Mintaprojektek az Azure által felügyelt alkalmazásokhoz.](sample-projects.md)
