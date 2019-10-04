---
title: Frissítse az Azure-erőforrások által felügyelt alkalmazások |} A Microsoft Docs
description: Ismerteti, hogyan lehet az erőforrásokat a felügyelt működni a felügyelt alkalmazás Azure-erőforráscsoportot.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043454"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Az a felügyelt erőforrásokkal való munka erőforráscsoportot az Azure által felügyelt alkalmazás

Ez a cikk ismerteti, hogyan frissíthető egy felügyelt alkalmazás részeként üzembe helyezett erőforrásokat. Felügyelt alkalmazás közzétevője, mint rendelkezik az erőforrásokhoz való hozzáférés a felügyelt erőforráscsoporthoz. Frissíteni ezeket az erőforrásokat, meg kell keresse meg a kezelt erőforráscsoport, egy felügyelt alkalmazáshoz társított, és az erőforráscsoport az erőforrás eléréséhez.

Ez a cikk feltételezi, hogy a felügyelt alkalmazás a központilag telepített a [felügyelt webalkalmazás (IaaS) az Azure felügyeleti szolgáltatásaival](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) mintaprojektet. Hogy a felügyelt alkalmazás tartalmaz egy **standard D1 v2** virtuális gépet. Ha nem telepített, akkor ezt a felügyelt alkalmazást, hogy megismerkedjen a felügyelt erőforráscsoport frissítésére szolgáló lépéseket továbbra is használhatja a ebben a cikkben.

Az alábbi képen látható az üzembe helyezett alkalmazást.

![Felügyelt alkalmazás üzembe helyezése](./media/update-managed-resources/deployed.png)

Ez a cikk az Azure CLI használatával:

* Azonosíthatja a felügyelt alkalmazás
* A felügyelt erőforráscsoporthoz azonosítása
* Azon virtuális gépek erőforrás(ok) meghatározása a felügyelt erőforráscsoportban
* Módosítsa a virtuális gép méretét (akár egy kisebb méretet, ha nem, vagy további terhelés nagyobb)
* Szabályzat hozzárendelése a felügyelt erőforráscsoporthoz, amely meghatározza az engedélyezett helyek

## <a name="get-managed-application-and-managed-resource-group"></a>Felügyelt alkalmazás és a felügyelt erőforráscsoport lekérése

A felügyelt alkalmazások egy erőforráscsoportba tartozó használja:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

A kezelt erőforráscsoport Azonosítóját használja:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>A felügyelt erőforráscsoportban lévő virtuális gépek átméretezése

A virtuális gépek a kezelt erőforráscsoport megtekintéséhez adja meg a kezelt erőforráscsoport nevét.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

A virtuális gépek méretét frissítéséhez használja:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

A művelet befejezése után ellenőrizze az alkalmazás fut, a Standard D2 v2.

![Standard D2 v2 használatával felügyelt alkalmazás](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Házirend alkalmazása a felügyelt erőforráscsoport

A kezelt erőforráscsoport és a hozzárendelés egy szabályzat lekérése a hatókörben. A szabályzat **e56962a6-4747-49cd-b67b-bf8b01975c4c** egy beépített szabályzat engedélyezett helyek meghatározásához.

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

Az engedélyezett helyek megjelenítéséhez használja:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A szabályzat-hozzárendelés megjelenik a portálon.

![Szabályzat-hozzárendelés megtekintése](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Mintaprojektjeit, lásd: [mintaprojektekkel az Azure által felügyelt alkalmazások](sample-projects.md).
