---
title: Az Azure Service Fabric hálózati gyakorlati tanácsai
description: Gyakorlati tanácsok és tervezési szempontok a hálózati kapcsolatok Azure Service Fabric használatával történő kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551794"
---
# <a name="networking"></a>Hálózat

Az Azure Service Fabric-fürtök létrehozása és kezelése során hálózati kapcsolatot biztosít a csomópontok és alkalmazások számára. A hálózati erőforrások közé tartoznak az IP-címtartományok, a virtuális hálózatok, a terheléselosztók és a hálózati biztonsági csoportok. Ebben a cikkben megismerheti az ezen erőforrásokkal kapcsolatos gyakorlati tanácsokat.

Tekintse át az Azure [Service Fabric hálózati minták megtudhatja,](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) hogyan hozhat létre fürtöket, amelyek a következő funkciókat: Meglévő virtuális hálózat vagy alhálózat, statikus nyilvános IP-cím, csak belső terheléselosztó, vagy belső és külső terheléselosztó.

## <a name="infrastructure-networking"></a>Infrastruktúra-hálózat
Maximalizálja a virtuális gép teljesítményét az Accelerated Networking segítségével, az EnableAcceleratedNetworking tulajdonság nak az Erőforrás-kezelő sablonban történő deklarálásával a következő kódrészlet egy virtuálisgép-méretezési készlet hálózati felületkonfigurációja, amely lehetővé teszi a gyorsított hálózatkezelést:

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
A Service Fabric-fürt [linuxos, gyorsított hálózati](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)és [Windows-os, gyorsított hálózati rendszerrel rendelkező rendszeren](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)is kiépíthető.

Az Accelerated Networking az Azure Virtual Machine Series SKUs-ok esetében támogatott: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 és Ms/MMs. Az Accelerated Networking tesztelése sikeresen megtörtént a Standard_DS8_v3 termékváltozat használatával 2019.1/23/2019-en egy Service Fabric Windows-fürthöz, és Standard_DS12_v2 a 2019.01/29/2019-es service fabric Linux-fürthöz.

Ha engedélyezni szeretné a gyorsított hálózatkezelést egy meglévő Service Fabric-fürtön, először egy [szolgáltatásháló-fürt et kell kibővítenie egy virtuálisgép-méretezési készlet hozzáadásával,](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)hogy a következőket hajtsa végre:
1. NodeType kiépítése gyorsított hálózattal engedélyezve
2. A szolgáltatások és állapotuk áttelepítése a kiosztott NodeType típusra a gyorsított hálózat engedélyezve

Az infrastruktúra bővítésére van szükség az Accelerated Networking engedélyezéséhez egy meglévő fürtön, mert a gyorsított hálózatkezelés engedélyezése állásidőt okozna, mivel a rendelkezésre állási halmazban lévő összes virtuális gépet le kell [állítani, és fel kell osztani, mielőtt engedélyezné a gyorsított hálózatkezelést bármely meglévő hálózati adapteren.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)

## <a name="cluster-networking"></a>Fürthálózat

* A Service Fabric-fürtök a [Service Fabric hálózati mintázatában](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)ismertetett lépések végrehajtásával telepíthetők egy meglévő virtuális hálózatba.

* Hálózati biztonsági csoportok (NSG-k) használata olyan csomóponttípusokhoz ajánlott, amelyek korlátozzák a fürtjük be- és kimenő forgalmát. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva az NSG-ben. Például: ![Service Fabric NSG-szabályok][NSGSetup]

* Az elsődleges csomópont típusát, amely a Service Fabric rendszerszolgáltatásait tartalmazza, nem kell elérhetővé tenni a külső terheléselosztón keresztül, és egy [belső terheléselosztó](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer) is elérhetővé teheti.

* Használjon [statikus nyilvános IP-címet](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) a fürthöz.

## <a name="application-networking"></a>Alkalmazáshálózat

* A Windows tárolók munkaterhelésének futtatásához használjon [nyílt hálózati módot](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) a szolgáltatások közötti kommunikáció megkönnyítéséhez.

* Használjon fordított proxyt, például [a Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) vagy a [Service Fabric fordított proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) t a közös alkalmazásportok, például a 80 vagy 443.

* Az olyan légréses gépeken üzemeltetett Windows-tárolók esetében, amelyek nem tudják lekérni az alaprétegeket az Azure felhőalapú tárhelyéről, felülbírálja az idegen réteg viselkedését a [Docker démon --allow-nondistributable-artifacts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) jelzőjével.

## <a name="next-steps"></a>További lépések

* Fürt létrehozása virtuális gépeken vagy Windows Server rendszert futtató számítógépeken: [Service Fabric-fürt létrehozása Windows Server rendszerhez](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxot futtató számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
