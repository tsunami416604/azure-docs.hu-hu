---
title: Nem csatlakoztatott Azure-hálózati adapterek keresése és törlése
description: Az Azure CLI-vel nem virtuális gépekhez nem csatolt Azure hálózati adapterek megkeresése és törlése
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945135"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Nem csatlakoztatott hálózati csatolókártyák (NIC-k) megkeresése és törlése az Azure virtuális gépeihez
Ha töröl egy virtuális gépet (VM) az Azure-ban, a hálózati csatolókártyák (NIC-k) alapértelmezés szerint nem törlődnek. Ha több virtuális gépet hoz létre és töröl, a nem használt hálózati adapterek továbbra is a belső IP-címbérleteket használják. Más virtuálisgép-hálózati adapterek létrehozásakor előfordulhat, hogy nem tudnak IP-címbérletet szerezni az alhálózat címterében. Ez a cikk bemutatja, hogyan keresheti meg és törölheti a nem csatlakoztatott hálózati adaptereket.

## <a name="find-and-delete-unattached-nics"></a>Nem csatolt hálózati adapterek keresése és törlése

A hálózati adapter *virtualMachine* tulajdonsága tárolja annak a virtuális gépnek az azonosítóját és erőforráscsoportját, amelyhez a hálózati adapter kapcsolódik. A következő parancsfájl hurkok között az összes hálózati adapterek egy előfizetésben, és ellenőrzi, ha a *VirtualMachine* tulajdonság null. Ha ez a tulajdonság null értékű, a hálózati adapter nem kapcsolódik a virtuális géphez.

Az összes független hálózati adapter megtekintéséhez javasoljuk, hogy először futtassa a parancsfájlt a *deleteUnattachedNics* változóval *0-ra*. Ha a listakimenet áttekintése után törölni szeretné az összes nem csatlakoztatott hálózati adaptert, futtassa a parancsfájlt *unattachedNics* értékkel *1-re*.

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

A virtuális hálózatok Azure-beli létrehozásáról és kezeléséről a [Virtuálisgép-hálózatok létrehozása és kezelése](tutorial-virtual-network.md)című témakörben talál további információt.
