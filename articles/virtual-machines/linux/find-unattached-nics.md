---
title: A nem csatolt Azure hálózati adapterek keresése és törlése | Microsoft Docs
description: Virtuális gépekhez nem csatlakoztatott Azure hálózati adapterek keresése és törlése az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 1665b5fa8d2bfd63982bcffd1d5251214ff3586b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083329"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Nem csatlakoztatott hálózati adapterek (NIC-EK) keresése és törlése Azure-beli virtuális gépekhez
Ha töröl egy virtuális gépet (VM) az Azure-ban, a hálózati adapterek (NIC) alapértelmezés szerint nem törlődnek. Ha több virtuális gépet hoz létre és töröl, a nem használt hálózati adapterek továbbra is a belső IP-címbérleteket használják. Más virtuálisgép-hálózati adapterek létrehozásakor előfordulhat, hogy nem tudnak IP-címbérletet szerezni az alhálózat címterület számára. Ez a cikk bemutatja, hogyan lehet megkeresni és törölni a nem csatolt hálózati adaptereket.

## <a name="find-and-delete-unattached-nics"></a>Nem csatolt hálózati adapterek keresése és törlése

A hálózati adapter *virtualMachine* tulajdonsága tárolja annak a virtuális GÉPNEK az azonosítóját és erőforrás-csoportját, amelyhez a hálózati adapter csatlakozik. Az alábbi szkript hurkokat hajt végre az előfizetésben található összes hálózati adapteren, és ellenőrzi, hogy a *virtualMachine* tulajdonság null értékű-e. Ha ez a tulajdonság null értékű, a hálózati adapter nincs virtuális géphez csatlakoztatva.

Az összes nem csatolt hálózati adapter megtekintéséhez erősen ajánlott a parancsfájlt a *deleteUnattachedNics* változóval *0-ra*futtatni. Ha törölni szeretné az összes nem csatolt hálózati adaptert a lista kimenetének áttekintése után, futtassa a szkriptet a *deleteUnattachedNics* *1*értékre.

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

Az Azure-beli virtuális hálózatok létrehozásával és kezelésével kapcsolatos további információkért lásd: virtuálisgép- [hálózatok létrehozása és kezelése](tutorial-virtual-network.md).
