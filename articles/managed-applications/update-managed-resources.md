---
title: "Erőforrásokat az Azure-ban kezelt alkalmazások |} Microsoft Docs"
description: "Ismerteti, hogyan működnek a felügyelt erőforrások a kezelt alkalmazás – az Azure erőforráscsoport."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 59dce2fe7d91cc80f991e5ff298be7757ae19ef4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>A kezelt erőforrások használata az Azure-erőforráscsoport felügyelt alkalmazás

Ez a cikk ismerteti a kezelt alkalmazás részeként üzembe helyezett erőforrásokat. Kezelt alkalmazás közzétevőként az erőforrásokhoz való hozzáférés a felügyelt erőforráscsoportban van. Ezeket az erőforrásokat frissítéséhez szükség keresse meg a felügyelt erőforráscsoportot, egy felügyelt alkalmazáshoz társított, és az erőforráscsoport erőforrás elérésére.

Ez a cikk feltételezi, hogy a felügyelt alkalmazást, a telepített a [felügyelt webalkalmazásról (IaaS) és az Azure szolgáltatások](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) mintaprojektet. Arra, hogy a kezelt alkalmazás tartalmazza a **standard D1 v2** virtuális gépet. Ha nem telepített, hogy a felügyelt alkalmazási, továbbra is használhatja ez a cikk a Ismerkedjen meg a lépéseket egy felügyelt erőforráscsoport frissítéséhez.

A következő kép bemutatja a telepített felügyelt alkalmazás.

![Kezelt alkalmazás telepítve](./media/update-managed-resources/deployed.png)

Ebben a cikkben az Azure CLI-t használja:

* A kezelt alkalmazás azonosítása
* A felügyelt erőforrások csoportnak
* A virtuális gép (oka) t a felügyelt erőforráscsoportban azonosítása
* Módosítsa a Virtuálisgép-méretet (vagy egy kisebb méretet, ha a fel nem használt vagy mérete nagyobb terhelést támogatásához)
* A felügyelt erőforráscsoport, amely meghatározza az engedélyezett helyek házirend hozzárendelése

## <a name="get-managed-application-and-managed-resource-group"></a>A felügyelt alkalmazási és a felügyelt erőforráscsoport

A kezelt alkalmazások erőforráscsoportban használatához:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

A felügyelt erőforráscsoport azonosítója, amelyet:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Felügyelt erőforráscsoportban méretezze át a virtuális gépek

A virtuális gépek a felügyelt erőforráscsoport megtekintéséhez adja meg a felügyelt erőforráscsoport nevét.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

A virtuális gépek méretét frissítéséhez használja:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

A művelet befejezése után ellenőrizze az alkalmazás fut a Standard D2 v2.

![Standard D2 v2 használatával kezelt alkalmazás](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Felügyelt erőforráscsoport házirend alkalmazása

Beolvasni a felügyelt erőforráscsoport és a hozzárendelési házirend hatókörnek. A házirend **e56962a6-4747-49cd-b67b-bf8b01975c4c** megadása az engedélyezett helyek beépített házirend.

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

A házirend-hozzárendelés megjelenik a portálon.

![Házirend-hozzárendelés megtekintése](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [felügyelt használatát áttekintő cikkben](overview.md).
* Mintaprojektjeit, lásd: [a kezelt alkalmazások az Azure-hoz](sample-projects.md).
* Felügyelt alkalmazások közzétételéhez az Azure piactéren kapcsolatos információkért lásd: [felügyelt alkalmazások a piactéren](publish-marketplace-app.md).