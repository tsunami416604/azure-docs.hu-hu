---
title: Azure Service Fabric hálózatkezelés – ajánlott eljárások | Microsoft Docs
description: Ajánlott eljárások Service Fabric hálózatkezelés kezeléséhez.
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
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "66237329"
---
# <a name="networking"></a>Hálózat

Az Azure Service Fabric-fürtök létrehozásakor és kezelésekor hálózati kapcsolatot biztosít a csomópontjai és alkalmazásai számára. A hálózati erőforrások közé tartozik az IP-címtartományok, a virtuális hálózatok, a terheléselosztó és a hálózati biztonsági csoportok. Ebben a cikkben az ezen erőforrásokkal kapcsolatos ajánlott eljárásokat ismerheti meg.

Tekintse át az Azure [Service Fabric hálózatkezelési mintáit](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) , és Ismerje meg, hogyan hozhat létre olyan fürtöket, amelyek a következő szolgáltatásokat használják: Meglévő virtuális hálózat vagy alhálózat, statikus nyilvános IP-cím, csak belső terheléselosztó, belső és külső terheléselosztó.

## <a name="infrastructure-networking"></a>Infrastruktúra-hálózatkezelés
Maximalizálja a virtuális gép teljesítményét a gyorsított hálózatkezeléssel azáltal, hogy deklarálja a enableAcceleratedNetworking tulajdonságot a Resource Manager-sablonban, az alábbi kódrészlet egy virtuálisgép-méretezési csoport Networkinterfaceconfigurations szakaszához, amely Gyorsított hálózatkezelés engedélyezése:

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
A Service Fabric-fürt [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)rendszeren is kiépíthető gyorsított hálózatkezeléssel, a [Windows pedig gyorsított hálózatkezeléssel](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

A gyorsított hálózatkezelés támogatott az Azure-beli virtuális gépek Series SKU-ban: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 és MS/MMS. A gyorsított hálózatkezelést sikeresen teszteltük a Standard_DS8_v3 SKU 1/23/2019-on való használatával egy Service Fabric Windows-fürthöz, és a 01/29/2019-es Standard_DS12_v2-t használja egy Service Fabric Linux-fürthöz.

Ha egy meglévő Service Fabric fürtön szeretné engedélyezni a gyorsított hálózatkezelést, először [egy virtuálisgép-méretezési csoport hozzáadásával kell kialakítania egy Service Fabric](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)-fürtöt a következők elvégzéséhez:
1. NodeType kiépítése a gyorsított hálózatkezelés engedélyezésével
2. A szolgáltatások és az állapotuk áttelepítése a kiépített NodeType a gyorsított hálózatkezelés engedélyezésével

Az infrastruktúra horizontális felskálázása szükséges a gyorsított hálózatkezelés engedélyezéséhez egy meglévő fürtön, mert a gyorsított hálózatkezelés engedélyezése az állásidőt okozhatja, mivel a rendelkezésre állási csoportokban lévő összes virtuális gép leállítása és felszabadítása szükséges. [ a gyorsított hálózatkezelés engedélyezése bármely meglévő hálózati adapteren](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Fürt hálózatkezelése

* Service Fabric fürtöket meglévő virtuális hálózatba lehet telepíteni a [Service Fabric hálózati mintákban](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)leírt lépések végrehajtásával.

* A hálózati biztonsági csoportok (NSG-EK) olyan csomópont-típusokhoz ajánlottak, amelyek korlátozzák a bejövő és a kimenő forgalmat a fürtön. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a NSG. Példa: ![Service Fabric NSG-szabályok][NSGSetup]

* A Service Fabric rendszerszolgáltatásokat tartalmazó elsődleges csomópont típusát nem kell a külső terheléselosztó használatával kitenni, és [belső](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer) terheléselosztó is elérhetővé teheti.

* Használjon [statikus nyilvános IP-címet](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) a fürthöz.

## <a name="application-networking"></a>Alkalmazás hálózatkezelése

* A Windows-tárolók számítási feladatainak futtatásához használja a [nyílt hálózati módot](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) a szolgáltatások közötti kommunikáció megkönnyítéséhez.

* Használjon fordított proxyt, például [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) vagy a [Service Fabric fordított proxyt](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) a gyakori alkalmazás-portok (például 80 vagy 443) elérhetővé tétele érdekében.

* Azon gapped gépeken üzemeltetett Windows-tárolók esetében, amelyek nem tudnak lekérni alaprétegeket az Azure Cloud Storage-ból, felülbírálják a külső réteg viselkedését a [--Allow-nem terjeszthető összetevők](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) jelző használatával a Docker-démonban.

## <a name="next-steps"></a>További lépések

* Hozzon létre egy fürtöt a virtuális gépeken vagy a Windows Servert futtató számítógépeken: [Service Fabric fürt létrehozása a Windows Serverhez](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
