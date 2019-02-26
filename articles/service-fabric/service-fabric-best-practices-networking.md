---
title: Hálózatkezelés az ajánlott eljárások az Azure Service Fabric |} A Microsoft Docs
description: A Service Fabric hálózatkezelés kezelésének ajánlott eljárásai.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 86ad6fce34f323d94f7b9c318ba81f547360d4df
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804958"
---
# <a name="networking"></a>Hálózat

Létrehozása és kezelése az Azure Service Fabric-fürtök, a csomópontok és az alkalmazások számára meg van adva hálózati kapcsolat. A hálózati erőforrások közé tartoznak az IP-címtartományok, virtuális hálózatok, terheléselosztók és hálózati biztonsági csoportok. Ebben a cikkben megismerheti az ajánlott eljárások az ezekhez az erőforrásokhoz.

Az Azure [Service Fabric-hálózatkezelés minták](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) megtudhatja, hogyan hozhat létre fürtöket, amelyek a következő funkciókat használja: Meglévő virtuális hálózat vagy alhálózat, statikus nyilvános IP-címet, csak belső terheléselosztót, vagy a belső és külső terheléselosztó.

## <a name="infrastructure-networking"></a>Hálózati infrastruktúra
Gyorsított hálózatkezelésű virtuális gépén teljesítmény maximalizálása, deklarálásával enableAcceleratedNetworking tulajdonságot a Resource Manager-sablonban szereplő, az alábbi kódrészlet egy virtuális gép méretezési beállítása NetworkInterfaceConfigurations, lehetővé teszi a gyorsított Hálózatkezelés:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric-fürt kiépítése a [Linuxos gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), és [gyorsított hálózatkezelésű Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Gyorsított hálózatkezelés az Azure virtuálisgép-sorozat termékváltozatok támogatják: D/DSv2, a DSv3/D, E/ESv3, F/FS, FSv2 és Ms és Mms. Gyorsított hálózatkezelés tesztelés sikeresen az Standard_DS8_v3 Termékváltozat az 1/23/2019-beli Service Fabric Windows-fürt, és a 2019/01/29 Standard_DS12_v2 használatával a Service Fabric Linux-fürt.

Gyorsított hálózatkezelés engedélyezéséhez egy meglévő Service Fabric-fürtre, először szüksége [Service Fabric-fürt kétirányú méretezése hozzáadásával egy virtuálisgép-méretezési csoportban](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), a következőket hajthatja végre:
1. A gyorsított hálózatkezelés engedélyezett egy NodeType kiépítése
2. A szolgáltatások és az állapotuk át a kiépített NodeType gyorsított hálózatkezelésű engedélyezve

Infrastruktúra kiterjesztése szükség a gyorsított hálózatkezelés engedélyezéséhez egy meglévő fürtön, mert engedélyezése a gyorsított hálózatkezelés helyen okozna állásidő, mivel egy rendelkezésre állási csoportban található összes virtuális gép lehet [állítsa le és minden olyan meglévő hálózati adapter a gyorsított hálózatkezelés engedélyezése előtt szabadítsa fel](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Fürthálózat

* Service Fabric-fürtök ismertetett lépéseket követve telepíthető egy meglévő virtuális hálózatban [hálózatkezelés minták a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Hálózati biztonsági csoportok (NSG), amelyek korlátozzák a bejövő és kimenő forgalmat a fürt csomóponttípusok használata javasolt. Győződjön meg arról, hogy a szükséges portokat az NSG-ben. Példa: ![Service Fabric NSG-szabályok][NSGSetup]

* Az elsődleges csomóponttípushoz, amely tartalmazza a Service Fabric-rendszerszolgáltatások nem kell a külső terheléselosztó keresztül, és által elérhetővé tett egy [belső load balancer](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Használja a [statikus nyilvános IP-cím](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) a fürt számára.

## <a name="application-networking"></a>Alkalmazás-hálózatkezelés

* Windows-tárolókhoz kapcsolódó számítási feladatok futtatásához használja [hálózati mód megnyitásához](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) szolgáltatások közötti kommunikáció megkönnyítése.

* Fordított proxyt használ, például [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) vagy a [Service Fabric fordított proxyja](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) , például a 80-as vagy 443-as közös alkalmazás-portokat tesz elérhetővé.

## <a name="next-steps"></a>További lépések

* Fürt létrehozása a virtuális gépek vagy a Windows Server rendszert futtató számítógépeken: [A Service Fabric-fürt létrehozása a Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása a virtuális gépek vagy a Linux operációs rendszert futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
