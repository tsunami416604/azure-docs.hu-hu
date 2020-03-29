---
title: Az Avere vFXT nem tulajdonosi megoldás - Azure
description: 'Megoldás: Az előfizetés tulajdonosi engedélyével nem rendelkező felhasználók számára az Avere vFXT azure-hoz való üzembe helyezését'
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153275"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Az Avere vFXT nem tulajdonosi üzembe helyezésének engedélyezése

Ezek az utasítások egy olyan megoldás, amely lehetővé teszi, hogy az előfizetés tulajdonosi jogosultságokkal nem rendelkező felhasználó Hozzon létre egy Avere vFXT azure-rendszerhez.

(Az Avere vFXT rendszer telepítésének ajánlott módja, ha a tulajdonosi jogosultságokkal rendelkező felhasználó elvégezheti a létrehozási lépéseket, amint azt [az Avere vFXT létrehozásának előkészítése](avere-vfxt-prereqs.md)című részben ismertetik.)  

A megoldás egy további hozzáférési szerepkör létrehozását jelenti, amely elegendő engedélyt ad a felhasználóknak a fürt telepítéséhez. A szerepkört egy előfizetés tulajdonosának kell létrehoznia, és a tulajdonosnak hozzá kell rendelnie a megfelelő felhasználókhoz.

Az előfizetés tulajdonosának el kell [fogadnia az](avere-vfxt-prereqs.md) Avere vFXT piactéri lemezkép használati feltételeit is.

> [!IMPORTANT]
> Ezeket a lépéseket a fürthöz használt előfizetéstulajdonosi jogosultságokkal rendelkező felhasználónak kell megtennie.

1. Másolja ezeket a sorokat, és `averecreatecluster.json`mentse őket egy fájlba (például ). Használja az előfizetés-azonosítóját a `AssignableScopes` kimutatásban.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. A szerepkör létrehozásához futtassa ezt a parancsot:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Példa:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Rendelje hozzá ezt a szerepkört a fürtöt létrehozó felhasználóhoz:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

A folyamat befejezése után a szerepkör a következő engedélyeket adja az előfizetéshez hozzárendelt felhasználóknak:

* A hálózati infrastruktúra létrehozása és konfigurálása
* A fürtvezérlő létrehozása
* Fürtlétrehozási parancsfájlok futtatása a fürtvezérlőből a fürt létrehozásához
