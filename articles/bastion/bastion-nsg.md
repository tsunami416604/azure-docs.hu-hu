---
title: A virtuális gépek és NSG-k az Azure megerősített |} A Microsoft Docs
description: Ez a cikk ismerteti, hogy miként építheti be az NSG-hozzáférés az Azure megerősített
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191602"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Az NSG-hozzáférés és az Azure megerősített (előzetes verzió)

Az Azure megerősített használatakor a hálózati biztonsági csoportok (NSG-k) is használhatja. További információkért lásd: [biztonsági csoportok](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektúra](./media/bastion-nsg/nsg_architecture.png)

Az alábbi ábrán látható:

* A virtuális hálózatban van üzembe helyezve a bástyagazdagép.
* A felhasználó csatlakozik az Azure Portalon HTML5 böngészőkben használatával.
* A felhasználó kijelöli a virtuális gép csatlakozni.
* Egyetlen kattintással az RDP/SSH-munkamenetet a böngészőben nyílik meg.
* Az Azure virtuális gépen nincs nyilvános IP-cím szükséges.

## <a name="nsg"></a>Hálózati biztonsági csoportok

* **AzureBastionSubnet:** Az Azure megerősített az adott AzureBastionSubnet van üzembe helyezve.  
    * **A nyilvános internetről bejövő forgalmat:** Az Azure megerősített létrehoz egy nyilvános IP-cím, amelyet engedélyezve van a nyilvános IP-cím a bejövő forgalom a 443-as porton. Port: 3389/22 nem szükségesek a AzureBastionSubnet megnyitni.
    * **Cél virtuális gépek felé irányuló kimenő adatforgalmat:** Az Azure megerősített fogják tudni elérni a cél virtuális gépek magánhálózati IP. Az NSG-k más céloldali Virtuálisgép-alhálózatok felé irányuló kimenő adatforgalmat engedélyezni kell.
* **Céloldali Virtuálisgép-alhálózat:** Ez az az alhálózatot, amelyet a cél virtuális gépen, amelyet szeretne az RDP/ssh-KAPCSOLATOT tartalmaz.
    * **Azure megerősített érkező bejövő forgalmat:** Az Azure megerősített el fog érni a cél virtuális gép magánhálózati IP. RDP/SSH-port (3389-es port és a 22-es, illetve) kell a cél virtuális gép oldalán kell megnyitni a magánhálózati IP.

## <a name="apply"></a>NSG-ket alkalmaz az AzureBastionSubnet

Ha NSG-ket alkalmaz az **AzureBastionSubnet**, Azure vezérlősík és az infrastruktúra a következő két szolgáltatáscímkék engedélyezése:

* **(Csak Resource Manager) GatewayManager**: Ez a címke az Azure-átjáró Manager szolgáltatás címelőtagjait. Ha GatewayManager értéket ad meg, engedélyezett vagy tiltott forgalmat GatewayManager való.

* **(Csak Resource Manager) AzureCloud**: Ez a címke azt jelzi, hogy az IP-címtér az Azure-ban minden adatközpont nyilvános IP-címek többek között. Az érték az AzureCloud ad meg, ha engedélyezett vagy tiltott forgalmat az Azure nyilvános IP-címeket. Csak szeretné egy adott régióban AzureCloud való hozzáférés engedélyezése, ha a régió is megadhat. Ha például csak az USA keleti régiójában Azure AzureCloud hozzáférést szeretne, megadhatja AzureCloud.EastUS szolgáltatáscímkét.

## <a name="next-steps"></a>További lépések

Azure megerősített kapcsolatos további információkért lásd: a [– gyakori kérdések](bastion-faq.md)