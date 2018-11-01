---
title: Avere vFXT nem tulajdonosi megoldás – Azure
description: Megkerülő megoldás, hogy a felhasználók Avere vFXT telepítése az Azure előfizetés tulajdonosának engedélye nélkül
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634104"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Nem tulajdonosai üzembe helyezéséhez Avere vFXT engedélyezése

Ezek az utasítások olyan megoldás, amely lehetővé teszi a felhasználói előfizetés nélkül hozhat létre egy Azure rendszer Avere vFXT tulajdonosi jogosultságokkal.

(A rendszer vFXT hajtsa végre a létrehozás tulajdonosi jogosultságokkal rendelkező felhasználói Avere telepítése az ajánlott módszer a lépéseket, a [a Avere vFXT létrehozásának előkészítéséhez](avere-vfxt-prereqs.md).)  

A megoldáshoz szükséges létrehozása egy további hozzáférési szerepkör, amely lehetővé teszi a felhasználóknak, a fürt telepítéséhez szükséges engedélyekkel. A szerepkör-előfizetés tulajdonosa hozza létre, és egy olyan tulajdonost kell rendelni, megfelelő felhasználók. 

Előfizetés tulajdonosa is kell [fogadja el a használati feltételeket](avere-vfxt-prereqs.md) a Avere vFXT Piactéri lemezképhez. 

> [!IMPORTANT] 
> Ezen lépések mindegyike a a fürthöz használt előfizetés tulajdonosa jogosultsággal rendelkező felhasználó által kell tenni.

1. Ezek a sorok másolja és mentse őket egy fájlban (például `averecreatecluster.json`). A szereplő előfizetés-azonosító használata a `AssignableScopes` utasítást.

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

1. A szerepkör létrehozása a következő parancs futtatásával:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Példa:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Ez a szerepkör hozzárendelése a felhasználót, hogy létrehozza a fürtöt:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Ez az eljárás után minden ehhez a szerepkörhöz hozzárendelt felhasználó rendelkezik az előfizetés a következő engedélyekkel: 

* Hozzon létre, és a hálózati infrastruktúra konfigurálása
* A fürt vezérlő létrehozása
* A fürt létrehozása a fürt vezérlőről fürt létrehozási parancsfájlok futtatása
