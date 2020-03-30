---
title: Az Azure virtuális hálózati TAP áttekintése | Microsoft dokumentumok
description: További információ a virtuális hálózat TAP. A virtuális hálózat TAP-ja a virtuálisgép-hálózati forgalom mély másolatát biztosítja, amely egy csomaggyűjtőnek streamelhető.
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
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279610"
---
# <a name="virtual-network-tap"></a>Virtuális hálózat TAP-jai

Az Azure virtuális hálózati TAP (Terminal Access Point) lehetővé teszi a virtuális gép hálózati forgalmának folyamatos streamelését egy hálózati csomaggyűjtő vagy -elemző eszköz felé. A gyűjtő vagy elemző eszközt egy [hálózati virtuális berendezés](https://azure.microsoft.com/solutions/network-appliances/) partner biztosítja. A virtuális hálózati TAP-val való együttműködésre ellenőrzött partnermegoldások listáját a [partnermegoldások](#virtual-network-tap-partner-solutions)ban találja.

> [!IMPORTANT]
> Virtuális hálózati TAP jelenleg előzetes verzióban az Összes Azure-régiókban. A virtuális hálózati TAP használatához az előzetes verzióban kell <azurevnettap@microsoft.com> beiratkoznia, ha e-mailt küld az előfizetésazonosítójával. Az előfizetés regisztrációja után egy e-mailt fog kapni. Nem tudja használni a funkciót, amíg nem kap egy visszaigazoló e-mailt. Ez az előzetes verzió szolgáltatásiszint-szerződés nélkül érhető el, és nem használható éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A [részleteket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) a Microsoft Azure előzetes verziók kiegészítő használati feltételei ben találja.

## <a name="virtual-network-tap-partner-solutions"></a>Virtuális hálózati TAP partnermegoldások

### <a name="network-packet-brokers"></a>Hálózati csomagbrókerek

- [Nagy switch nagy monitoring szövet](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva prizmák](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Biztonsági elemzés, hálózati/alkalmazásteljesítmény-kezelés

- [Ébren biztonság](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch felhő](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Sötétnyomkövetés](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Kiberbiztonság](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon között](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Az alábbi képen látható, hogyan működik a virtuális hálózat TAP. Tap-konfigurációt adhat hozzá a virtuális hálózatban telepített virtuális géphez csatlakoztatott [hálózati adapterhez.](virtual-network-network-interface.md) A cél egy virtuális hálózati IP-cím ugyanabban a virtuális hálózatban, mint a figyelt hálózati adapter vagy egy [társviszonyt létesített virtuális](virtual-network-peering-overview.md) hálózat. A virtuális hálózati TAP gyűjtőmegoldás a magas rendelkezésre állás érdekében az Azure belső terheléselosztója mögött is telepíthető. Az egyes megoldások telepítési lehetőségeinek kiértékeléséről a [partnermegoldások](#virtual-network-tap-partner-solutions)ban talál lehetőséget.

![A virtuális hálózat TAP működése](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Előfeltételek

Virtuális hálózati TAP létrehozása előtt meg kell kapnia egy megerősítő e-mailt, hogy regisztrált az előzetes verzióban, és egy vagy több virtuális gépet hozott létre [az Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) telepítési modell és egy partner megoldás összesítésére a TAP-forgalom ugyanabban az azure-régióban. Ha nincs partnermegoldás a virtuális hálózatban, tekintse meg a [partnermegoldások](#virtual-network-tap-partner-solutions) üzembe helyezéséhez. Ugyanazt a virtuális hálózati TAP-erőforrást használhatja több hálózati csatolóból származó forgalom összesítésére ugyanabban vagy különböző előfizetésekben. Ha a figyelt hálózati adapterek különböző előfizetésekben vannak, az előfizetéseket ugyanahhoz az Azure Active Directory-bérlőhöz kell társozni. Emellett a figyelt hálózati adapterek és a célvégpont a TAP-forgalom összesítéséhez lehet társviszony-létesített virtuális hálózatok ugyanabban a régióban. Ha ezt a telepítési modellt használja, győződjön meg arról, hogy a [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md) engedélyezve van a virtuális hálózati TAP konfigurálása előtt.

## <a name="permissions"></a>Engedélyek

A TAP-konfiguráció hálózati adaptereken történő alkalmazásához használt fiókokat hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely a következő táblából rendeli hozzá a szükséges műveleteket:

| Műveletek | Név |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Virtuális hálózati TAP-erőforrás létrehozásához, frissítéséhez, olvasásához és törléséhez szükséges |
| Microsoft.Network/networkInterfaces/read | A tap-t konfiguráló hálózati csatoló erőforrás ának olvasásához szükséges |
| Microsoft.Network/tapConfigurations/* | A TAP-konfiguráció létrehozásához, frissítéséhez, olvasásához és törléséhez szükséges egy hálózati csatolón |

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre virtuális hálózatot A TAP](tutorial-tap-virtual-network-cli.md).
