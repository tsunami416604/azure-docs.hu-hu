---
title: Keresse meg és törölje a nem csatolt Azure hálózati adapterek |} A Microsoft Docs
description: Keresse meg, és nem csatlakoztatott virtuális gépeket az Azure CLI-vel az Azure-hálózati adapter törlése
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 9d8345aacb603a6411fdc693ac9f808778d27333
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954330"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Keresse meg és nem csatolt hálózati adapter törlése kártyák (NIC) az Azure virtuális gépek
Ha töröl egy virtuális gépet (VM) az Azure-ban, a hálózati adapterek (NIC) nem törlődnek alapértelmezés szerint. Hozzon létre, és több virtuális gép törlése, ha a használaton kívüli hálózati adapterek továbbra is használja a belső IP-címbérleteket. Ahogy a többi virtuális gép hálózati adaptereket hoz létre, nem olvasható be egy IP-címbérletet, az az alhálózat címtartománya a lehet. Ez a cikk bemutatja, hogyan keresse meg és nem csatolt hálózati adapter törlése.

## <a name="find-and-delete-unattached-nics"></a>Nem csatolt hálózati adapterek keresése és törlése

A *virtualMachine* egy hálózati adapter a tulajdonság tárolja az Azonosítót és erőforrás csoport a virtuális gép a hálózati adapter csatlakozik. A következő parancsfájl végighalad egy adott előfizetés összes hálózati adapterének, és ellenőrzi, hogy a *virtualMachine* tulajdonság null értékű. Ez a tulajdonság értéke null, ha a hálózati adapter nem egy virtuális Géphez van csatlakoztatva.

Szeretné megtekinteni a nem csatolt hálózati adapterek, azt rendelkezik erősen ajánlott az első alkalommal történő futtatásakor a parancsfájlt a *deleteUnattachedNics* változó *0*. A nem csatolt hálózati adapter kimeneti listában áttekintése után törléséhez futtassa a parancsfájlt *deleteUnattachedNics* való *1*.

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

Hozhat létre és kezelhet az Azure-beli virtuális hálózatokkal kapcsolatos további információkért lásd: [létrehozása és kezelése a Virtuálisgép-hálózatok](tutorial-virtual-network.md).
