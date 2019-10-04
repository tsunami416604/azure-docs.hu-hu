---
title: Virtuális gépek és NSG használata az Azure Bastion-ben | Microsoft Docs
description: Ez a cikk bemutatja, hogyan építhet be NSG-hozzáférést az Azure Bastion használatával
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694946"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>A NSG-hozzáférés és az Azure Bastion (előzetes verzió) használata

Az Azure Bastion használatakor hálózati biztonsági csoportokat (NSG) is használhat. További információ: [biztonsági csoportok](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektúra](./media/bastion-nsg/nsg_architecture.png)

Ebben a diagramban:

* A megerősített gazdagép üzembe helyezése a virtuális hálózaton történik.
* A felhasználó bármely HTML5 böngésző használatával csatlakozik a Azure Portalhoz.
* A felhasználó kiválasztja azt a virtuális gépet, amelyhez csatlakozni szeretne.
* Egyetlen kattintással megnyílik az RDP/SSH-munkamenet a böngészőben.
* Nem szükséges nyilvános IP-cím az Azure-beli virtuális gépen.

## <a name="nsg"></a>Hálózati biztonsági csoportok

* **AzureBastionSubnet:** Az Azure Bastion üzembe helyezése az adott AzureBastionSubnet történik.  
    * **Bejövő forgalom a nyilvános internetről:** Az Azure Bastion létrehoz egy nyilvános IP-címet, amelyhez a 443-es port szükséges a nyilvános IP-címen a bejövő forgalom számára. A AzureBastionSubnet nem szükséges megnyitni a 3389/22-es portot.
    * **Kimenő forgalom a cél virtuális gépek felé:** Az Azure Bastion a cél virtuális gépeket magánhálózati IP-címekre fogja elérni. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokra.
* **Cél virtuálisgép-alhálózat:** Ez az az alhálózat, amely az RDP/SSH-t tartalmazó cél virtuális gépet tartalmazza.
    * **Bejövő forgalom az Azure Bastion-ből:** Az Azure Bastion privát IP-címen éri el a cél virtuális gépet. Az RDP-/SSH-portokat (a 3389-es és a 22-es portot) a cél virtuális gépen kell megnyitni a privát IP-címeken.

## <a name="apply"></a>NSG alkalmazása a AzureBastionSubnet

Ha a NSG alkalmazza a **AzureBastionSubnet**, engedélyezze a következő két szolgáltatási címkét az Azure Control Plane és az infrastruktúra számára:

* **GatewayManager (csak Resource Manager esetén)** : Ez a címke az Azure Gateway Manager szolgáltatáshoz tartozó címek előtagjait jelöli. Ha az értékhez GatewayManager ad meg, a forgalom a GatewayManager számára engedélyezett vagy megtagadható.  Ha NSG hoz létre a AzureBastionSubnet, engedélyezze a GatewayManager címkét a bejövő forgalom számára.

* **AzureCloud (csak Resource Manager esetén)** : Ez a címke az Azure IP-címtartományt jelöli, beleértve az összes adatközpont nyilvános IP-címét. Ha az értékhez AzureCloud ad meg, a forgalom engedélyezett vagy megtagadható az Azure nyilvános IP-címei számára. Ha csak egy adott régió AzureCloud szeretné engedélyezni a hozzáférést, megadhatja a régiót. Ha például az USA keleti régiójában csak az Azure AzureCloud szeretné engedélyezni a hozzáférést, megadhatja a AzureCloud. EastUS szolgáltatást. Ha NSG hoz létre a AzureBastionSubnet, engedélyezze a AzureCloud címkét a kimenő forgalom számára. Ha a 443-es portot az internetre nyitja meg, akkor nem kell engedélyeznie a AzureCloud címkét a bejövő forgalom számára.

## <a name="next-steps"></a>További lépések

További információ az Azure Bastion-ről: [Gyakori kérdések](bastion-faq.md)
