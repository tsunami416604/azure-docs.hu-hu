---
title: Egyéni vezérlő hozzáférés szerepkör - Avere vFXT az Azure-hoz
description: Egy egyéni hozzáférési szerepkör a Avere vFXT fürt vezérlő létrehozása
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 7ed20ccac879ec8eba1c3fbd91e38d05b08a12d2
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303112"
---
# <a name="customized-controller-access-role"></a>Testre szabott hozzáférés szerepkört

Az Azure-fürtben vezérlő Avere vFXT használja egy felügyelt identitás- és szerepköralapú hozzáférés-vezérlés (RBAC), hogy a fürt létrehozása és kezelése. 

Alapértelmezés szerint a fürt vezérlő van hozzárendelve a [beépített tulajdonosi szerepkör](../role-based-access-control/built-in-roles.md#owner). Is a tartományvezérlő hozzáférés hatókörét az erőforráscsoport - elemek kívül a fürt erőforráscsoportja, nem módosítható.

Ez a cikk bemutatja, hogyan hozhat létre a saját hozzáférés szerepkört a fürt vezérlő az alapértelmezett beállítás használata helyett. 

## <a name="edit-the-role-prototype"></a>A szerepkör prototípus szerkesztése

Indítsa el a rendelkezésre álló prototípus szerepkör <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt>.

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

Adja hozzá a Avere vFXT az Azure-beli előfizetés-azonosítója az AssignableScopes utasításban. Testre szabhatja a név, és adja hozzá, vagy definíciókat az alter igény szerint. 

Legyen körültekintő, ha a jogosultságok korlátozása. Fürt létrehozása meghiúsulhat, ha a vezérlő nem rendelkezik megfelelő hozzáféréssel. 

A mi jogosultságok, a fürt vezérlő ismertetése súgó létre kell hoznia egy fürthöz, [hozzon létre egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt). 

Mentse az egyéni szerepkör-definíció egy .JSON kiterjesztésű fájlt. 

## <a name="define-the-role"></a>A szerepkör definiálása 

Kövesse az alábbi lépéseket az egyéni szerepkör-definíció hozzáadása az előfizetéséhez. 

1. Nyissa meg az Azure Cloud Shellt az Azure Portalon, vagy tallózással keresse meg [ https://shell.azure.com ](https://shell.azure.com).

1. Az Azure CLI-parancs használatával váltson vFXT előfizetéséhez:

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. A szerepkör létrehozásához:

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   A fájlnév és elérési út helyén ```/avere-contributor-custom.json``` ebben a példában. 

A szerepkör-definíció parancs kimenetét mentse - a szerepkör-azonosító, amely kell megadnia a fürt létrehozási sablont tartalmaz. 

## <a name="find-the-role-id"></a>Keresse meg a szerepkör-azonosító

A Avere vFXT központi telepítési sablont kell a szerepkör globálisan egyedi azonosítóját (GUID) egy egyéni szerepkör hozzárendelése a vezérlő. 

A szerepkör GUID ezen a képernyőn egy 32 karakterből álló karakterlánc: 8e3af657-a8ff-443c-a75c-2fe8c4bcb635

Keresse ki a szerepkör GUID, használja ezt a parancsot a szerepkör nevű a ```--name``` paraméter.

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
Adja meg a következő karakterláncot a **Avere fürt létrehozása a szerepkör-azonosító** mezőben, az Azure-hoz a Avere vFXT telepítésekor.

## <a name="next-steps"></a>További lépések

Olvassa el a Avere vFXT telepítése az Azure- [vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md)
