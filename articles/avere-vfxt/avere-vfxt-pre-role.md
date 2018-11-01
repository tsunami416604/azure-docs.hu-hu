---
title: Nincs vezérlő – Azure Avere vFXT Avere szerepkör létrehozása
description: A metódus szükséges RBAC szerepkör nélkül egy fürt tartományvezérlő virtuális gép létrehozása
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634196"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>A Avere vFXT fürt modul hozzáférés szerepkör nélkül a tartományvezérlő létrehozása

Ez a dokumentum a fürt csomópont hozzáférés szerepkör létrehozása a parancssorból, a fürt tartományvezérlő virtuális gép létrehozása előtt metódust mutatja be. 

Szeretne létrehozni, azt a fürt vezérlőről, olvassa el a [hozza létre a fürt csomópont hozzáférés szerepkört](avere-vfxt-deploy.md#create-the-cluster-node-access-role). A vezérlő rendszerkép tartalmazza a szerepkör prototípus-fájlt. A fájl frissítése meg előfizetési azonosítóját, és adja meg a szerepkör helyileg a virtuális gép vezérlő használatával.

## <a name="create-an-azure-rbac-role"></a>Az Azure RBAC-szerepkör létrehozása

A Avere vFXT rendszer használja [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) történő hitelesítéséhez szükséges feladatok végrehajtásához a vFXT fürtcsomópontokon.  

Normál vFXT fürt művelet részeként egyéni vFXT elvégzésére van szükség, olvassa el az Azure erőforrás-tulajdonságok, tárhely kezelése és más csomópontok hálózatiadapter-beállítások felügyelete. 

Ez a szerepkör a vFXT csomópontok csak a fürt tartományvezérlő virtuális gép esetében nem szolgál.  

Ha szeretne létrehozni a szerepkört, előtt a vezérlő, kövesse az alábbi lépéseket: 

1. Nyissa meg az Azure Cloud Shellt az Azure Portalon, vagy tallózással keresse meg [ https://shell.azure.com ](https://shell.azure.com).

1. Az Azure CLI-parancs használatával váltson vFXT előfizetéséhez:

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. A parancsok használatához töltse le a szerepkör-definíció a marketplace-lemezképből, és szerkesztheti azt. 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. Szerkessze a fájlt meg előfizetési azonosítóját, és törölje a sor fölé. Mentse a fájlt az ``avere-cluster.json``.

   ![Szövegszerkesztőben az előfizetés-azonosító és a "távolítsa el ezt a sort" törlésre kiválasztott megjelenítő konzol](media/avere-vfxt-edit-role.png)

5. A szerepkör létrehozásához:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

A fürt létrehozásakor adja meg annak a szerepkörnek a nevét (ebben az esetben `avere-cluster`). A fürt létrehozási parancsfájl hozzárendeli a szerepkört az újonnan létrehozott fürt csomópontjaihoz. 

## <a name="sample-cluster-node-runtime-role-definition"></a>Minta fürt csomópont modul szerepkör-definíció

> [!IMPORTANT] 
> Ez a minta-definíció a GA előtti verzióról a termék vettük. Ha a jelenlegi termék terjesztési kap verziószáma eltérő, azt a verziót használja.

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```