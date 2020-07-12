---
title: Azure Service Fabric hálózatkezelés – ajánlott eljárások
description: Ajánlott eljárások és kialakítási szempontok az Azure Service Fabric használatával történő hálózati kapcsolatok kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261144"
---
# <a name="networking"></a>Hálózat

Az Azure Service Fabric-fürtök létrehozásakor és kezelésekor hálózati kapcsolatot biztosít a csomópontjai és alkalmazásai számára. A hálózati erőforrások közé tartozik az IP-címtartományok, a virtuális hálózatok, a terheléselosztó és a hálózati biztonsági csoportok. Ebben a cikkben az ezen erőforrásokkal kapcsolatos ajánlott eljárásokat ismerheti meg.

Tekintse át az Azure [Service Fabric hálózatkezelési mintáit](./service-fabric-patterns-networking.md) , amelyből megtudhatja, hogyan hozhat létre olyan fürtöket, amelyek a következő szolgáltatásokat használják: meglévő virtuális hálózat vagy alhálózat, statikus nyilvános IP-cím, csak belső terheléselosztó, belső és külső terheléselosztó.

## <a name="infrastructure-networking"></a>Infrastruktúra-hálózatkezelés
Maximalizálja a virtuális gép teljesítményét a gyorsított hálózatkezeléssel azáltal, hogy deklarálja a enableAcceleratedNetworking tulajdonságot a Resource Manager-sablonban, az alábbi kódrészlet egy virtuálisgép-méretezési csoport Networkinterfaceconfigurations szakaszához, amely lehetővé teszi a gyorsított hálózatkezelést:

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
A Service Fabric-fürt Linux rendszeren is kiépíthető [gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-cli.md), a [Windows pedig gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-powershell.md).

A gyorsított hálózatkezelést az Azure Virtual Machine Series SKU-k támogatják: a D/DSv2, a D/DSv3, az E/ESv3, az F/FS, a FSv2 és az MS/MMS. A gyorsított hálózatkezelést sikeresen teszteltük a Standard_DS8_v3 1/23/2019 SKU használatával Service Fabric Windows-fürtön, illetve a 01/29/2019-es Standard_DS12_v2 a Service Fabric Linux-fürtön való használatával.

Ha egy meglévő Service Fabric fürtön szeretné engedélyezni a gyorsított hálózatkezelést, először [egy virtuálisgép-méretezési csoport hozzáadásával kell kialakítania egy Service Fabric-fürtöt](./virtual-machine-scale-set-scale-node-type-scale-out.md)a következők elvégzéséhez:
1. NodeType kiépítése a gyorsított hálózatkezelés engedélyezésével
2. A szolgáltatások és az állapotuk áttelepítése a kiépített NodeType a gyorsított hálózatkezelés engedélyezésével

Az infrastruktúra horizontális felskálázása szükséges a gyorsított hálózatkezelés engedélyezéséhez egy meglévő fürtön, mert a gyorsított hálózatkezelés engedélyezése a leállást okozhatja, mivel a rendelkezésre állási csoportba tartozó összes virtuális gépet le kell állítani, és fel kell [szabadítani a gyorsított hálózatkezelés bármely meglévő hálózati adapterre való engedélyezése előtt](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Fürt hálózatkezelése

* Service Fabric fürtöket meglévő virtuális hálózatba lehet telepíteni a [Service Fabric hálózati mintákban](./service-fabric-patterns-networking.md)leírt lépések végrehajtásával.

* A hálózati biztonsági csoportok (NSG-EK) olyan csomópont-típusokhoz ajánlottak, amelyek korlátozzák a bejövő és a kimenő forgalmat a fürtön. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a NSG. Példa: ![ Service FABRIC NSG-szabályok][NSGSetup]

* A Service Fabric rendszerszolgáltatásokat tartalmazó elsődleges csomópont típusát nem kell a külső terheléselosztó használatával kitenni, és [belső](./service-fabric-patterns-networking.md#internal-only-load-balancer) terheléselosztó is elérhetővé teheti.

* Használjon [statikus nyilvános IP-címet](./service-fabric-patterns-networking.md#static-public-ip-address-1) a fürthöz.

## <a name="application-networking"></a>Alkalmazás hálózatkezelése

* A Windows-tárolók számítási feladatainak futtatásához használja a [nyílt hálózati módot](./service-fabric-networking-modes.md#set-up-open-networking-mode) a szolgáltatások közötti kommunikáció megkönnyítéséhez.

* Használjon fordított proxyt, például [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) vagy a [Service Fabric fordított proxyt](./service-fabric-reverseproxy.md) a gyakori alkalmazás-portok (például 80 vagy 443) elérhetővé tétele érdekében.

* Azon gapped gépeken üzemeltetett Windows-tárolók esetében, amelyek nem tudnak lekérni alaprétegeket az Azure Cloud Storage-ból, felülbírálják a külső réteg viselkedését a [--Allow-nem terjeszthető összetevők](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) jelző használatával a Docker-démonban.

## <a name="next-steps"></a>Következő lépések

* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
