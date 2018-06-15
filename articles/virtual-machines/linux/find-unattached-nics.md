---
title: Keresse meg és törölje a nem csatolt Azure hálózati adapterek |} Microsoft Docs
description: Kereséséhez, és törli, amelyek nem kapcsolódnak az Azure CLI 2.0 rendelkező virtuális gépek Azure hálózati adapterrel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: iainfou
ms.openlocfilehash: c730866fe73305a37b37038699a7f729085a16aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31427144"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Hogyan keresse meg és törölje a leválasztott hálózati illesztőt kártyák (NIC) Azure virtuális gépekhez
Ha töröl egy virtuális gép (VM) az Azure-ban, a hálózati adapterek (NIC) alapértelmezés szerint nem törlődnek. Hoz létre, és több virtuális gép törlése, ha a nem használt hálózati adaptert a belső IP-címbérletek használatával továbbra is. Más virtuális gép hálózati adaptert létrehozni, mert nem sikerült beolvasnia a címterületen belülre az alhálózat egy IP-címbérletet lehetnek. Ez a cikk bemutatja, hogyan keresse meg és törölje a nem csatlakoztatott hálózati adapterrel.

## <a name="find-and-delete-unattached-nics"></a>Keresse meg és törölje a nem csatlakoztatott hálózati adapterrel

A *virtualMachine* a hálózati adapter a tulajdonság tárolja azt a azonosító és az erőforrás csoport a virtuális gép hálózati adapter csatlakozik. A következő parancsfájl egy előfizetésben található hálózati adapterek végighalad, és ellenőrzi, hogy a *virtualMachine* tulajdonsága null értékű. Ez a tulajdonság értéke null, ha a hálózati adapter nem egy virtuális Géphez van csatolva.

A leválasztott hálózati adapterek megtekintéséhez azt rendelkezik erősen ajánlott az első alkalommal történő futtatásakor a parancsprogram a *deleteUnattachedNics* változó *0*. A leválasztott hálózati adapterek törléséhez a lista kimeneti áttekintése után futtassa a parancsfájlt *deleteUnattachedNics* való *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>További lépések

A létrehozása és kezelése az Azure virtuális hálózatairól további információkért lásd: [létrehozása és kezelése a Virtuálisgép-hálózatok](tutorial-virtual-network.md).
