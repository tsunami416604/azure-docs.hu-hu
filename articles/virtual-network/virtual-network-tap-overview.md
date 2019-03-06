---
title: Az Azure virtual network KOPPINTSON – áttekintés |} A Microsoft Docs
description: Ismerje meg a virtuális hálózati TAP. KOPPINTSON a virtuális hálózat nyújt, amely továbbítható egy csomag gyűjtő virtuális gép hálózati forgalom mély másolatát.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kaanan
ms.openlocfilehash: 5532174b6fc72f51e7ba7a946e601e7d97c7808e
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408445"
---
# <a name="virtual-network-tap"></a>Virtuális hálózat TAP-jai

Az Azure virtuális hálózati TAP (Terminálszolgáltatások hozzáférési pont) lehetővé teszi folyamatosan stream a virtuális gép hálózati forgalmat egy hálózati csomag adatgyűjtőnek vagy az analytics eszközt. A gyűjtő vagy analytics eszközt által biztosított egy [hálózati virtuális berendezésen](https://azure.microsoft.com/solutions/network-appliances/) partner. Dolgozunk a virtuális hálózati TAP érvényesített partneri megoldások listáját lásd: [partneri megoldások](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Virtuális hálózati TAP jelenleg fejlesztői előzetes verzióban a WestCentralUS Azure-régióban. Virtuális hálózati TAP használatához regisztrálnia kell az előzetes verzióban érhető el az e-mail <azurevnettap@microsoft.com> az előfizetés-azonosítóval. Az előfizetés regisztrációja után egy e-mailt fog kapni. Ön nem használhatja a funkció, amíg nem kap egy megerősítő e-mailt. A developer Preview verzióját egy szolgáltatásiszint-szerződés nélkül, és nem használható éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. Tekintse meg a [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) részleteiről.

## <a name="virtual-network-tap-partner-solutions"></a>Virtuális hálózati TAP partnermegoldások

### <a name="network-packet-brokers"></a>Hálózati csomag közvetítők

- [Big Data típusú kapcsoló big Data típusú a Fabric figyelése](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Biztonsági elemzés, hálózati és alkalmazásteljesítmény-felügyelet

- [Biztonsági ébren](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Kiberbiztonsági](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Nubeva Prizmákkal](https://www.nubeva.com/azurevtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

A következő kép bemutatja hogyan virtuális hálózati TAP működik. Egy KOPPINTÁSSAL konfigurációt adhat meg egy [hálózati adapter](virtual-network-network-interface.md) , amely a virtuális hálózaton üzembe helyezett virtuális géphez van csatolva. A célja az azonos virtuális hálózatba, a figyelt hálózati adaptert egy virtuális hálózati IP-címet vagy egy [virtuális Társhálózat](virtual-network-peering-overview.md) hálózati. A gyűjtő megoldást a virtuális hálózati TAP is üzembe helyezhetők mögött egy [Azure belső terheléselosztó](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) magas rendelkezésre állás érdekében. Kiértékelése egyéni megoldás üzembe helyezési lehetőségeit, tekintse meg a [partneri megoldások](#virtual-network-tap-partner-solutions).

![Hogyan virtuális hálózati TAP működik](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt létrehozna egy virtuális hálózati TAP, kell érkezett, hogy regisztrált az előzetes verzióban érhető el, és a egy vagy több virtuális gép használatával létrehozott megerősítést kérő e [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) üzembe helyezési modell és a egy partner a megoldás a KOPPINTSON forgalom WestCentralUS régió összesítéséhez szükséges tartományt. Ha a virtuális hálózat nem rendelkezik egy partneri megoldás, [partneri megoldások](#virtual-network-tap-partner-solutions) üzembe helyez egyet. Az azonos virtuális hálózatban a forgalom összesítéséhez erőforrás KOPPINTSON az azonos vagy eltérő előfizetésekben lévő több hálózati adapterrel is használhatja. Ha a figyelt hálózati adapterek különböző előfizetésekhez tartoznak, az előfizetések ugyanahhoz az Azure Active Directory-bérlőhöz társított kell lennie. Ezenkívül a figyelt hálózati adapterek és a cél-végpont, KOPPINTSON a forgalom összesítéséhez szükséges lehet az ugyanazon régióban lévő virtuális társhálózaton belüli. Az üzemi modell használatakor győződjön meg arról, hogy a [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md) engedélyezve van a virtuális hálózati TAP konfigurálása előtt.

## <a name="permissions"></a>Engedélyek

A alkalmazni, KOPPINTSON a konfigurációt a hálózati adaptereken használja fiókokat hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendelt a szükséges lépéseket a következő táblázatból:

| Műveletek | Name (Név) |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Kell létrehoznia, frissíteni, olvassa el és egy virtuális hálózati TAP-erőforrás törlése |
| Microsoft.Network/networkInterfaces/read | A hálózati adapter erőforrás, amelyen a KOPPINTSON konfigurálandó olvasásához szükséges |
| Microsoft.Network/tapConfigurations/* | Szükséges létrehozása, frissítése, olvassa el és a hálózati adapteren KOPPINTSON konfigurációjának törlése |

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy virtuális hálózati TAP](tutorial-tap-virtual-network-cli.md).
