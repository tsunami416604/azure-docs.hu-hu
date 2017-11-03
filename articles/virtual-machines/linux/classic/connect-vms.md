---
title: "Linux virtuális gépek csatlakozni felhőszolgáltatásban |} Microsoft Docs"
description: "A klasszikus üzembe helyezési modellt az Azure-felhőszolgáltatásban vagy a virtuális hálózat számára létrehozott Linux virtuális gépek csatlakozni."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 2fd23055-6b34-4ef0-88a8-fc19e32fb3c9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: e222645509640b104410f87e4bcd22834c8d9ec1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Csatlakozás egy virtuális hálózat vagy a felhőalapú szolgáltatással a klasszikus üzembe helyezési modellel létrehozott Linux virtuális gépek
> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A klasszikus üzembe helyezési modellel létrehozott Linux virtuális gépek felhőszolgáltatásban kerülnek. A felhőalapú szolgáltatás úgy működik, mint egy tárolót, és egy egyedi nyilvános DNS-nevet, egy nyilvános IP-cím és az interneten keresztül a virtuális gép eléréséhez végpontok készlete. A felhőszolgáltatás lehet egy virtuális hálózatban, de ez nem követelmény. Emellett [csatlakozás Windows virtuális gépek virtuális hálózati vagy felhőalapú szolgáltatásként](../../windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Ha egy felhőalapú szolgáltatás nem része virtuális hálózatnak, hívott egy *önálló* felhőalapú szolgáltatás. A virtuális gépek önálló felhőalapú szolgáltatásként kommunikálnak más virtuális gépekkel más virtuális gépeket nyilvános DNS-nevek használatával, és a forgalom halad az interneten keresztül. Ha egy virtuális hálózatban a virtuális gépek egy felhőalapú szolgáltatás abban, hogy a felhőalapú szolgáltatás képes kommunikálni a virtuális hálózat más virtuális gépek összes forgalom küldése az interneten keresztül nélkül.

Ha a virtuális gépeket helyez az azonos önálló felhőalapú szolgáltatás, terheléselosztás és a rendelkezésre állási csoportok továbbra is használhatja. További információkért lásd: [terheléselosztási virtuális gépek](../../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [virtuális gépek rendelkezésre állásának kezelése](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Azonban nem a virtuális gépek alhálózatok rendszerezése vagy önálló felhőalapú szolgáltatásként csatlakozni a helyi hálózatra. Íme egy példa:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Következő lépések
A virtuális gép létrehozása után már jó ötlet [hozzá adatlemezt](attach-disk.md) , a szolgáltatások és a munkafolyamatok jogosult az adatok tárolási helyét.
