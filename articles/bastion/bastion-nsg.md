---
title: Virtuális gépek és NSG használata az Azure Bastion-ben
description: A hálózati biztonsági csoportokat az Azure Bastion használatával is használhatja. Az ehhez a konfigurációhoz szükséges alhálózatok ismertetése.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: charwen
ms.openlocfilehash: a69aa8d8a6dc324d6fe28219316c36ac2ec816a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987701"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>A NSG-hozzáférés és az Azure Bastion használata

Az Azure Bastion használatakor hálózati biztonsági csoportokat (NSG) is használhat. További információ: [biztonsági csoportok](../virtual-network/security-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Ebben a diagramban:

* A megerősített gazdagép üzembe helyezése a virtuális hálózaton történik.
* A felhasználó bármely HTML5 böngésző használatával csatlakozik a Azure Portalhoz.
* A felhasználó az Azure-beli virtuális gépre RDP/SSH-ra navigál.
* Integrációs csatlakozás – egyetlen kattintással RDP/SSH-munkamenet a böngészőben
* Nem szükséges nyilvános IP-cím az Azure-beli virtuális gépen.

## <a name="network-security-groups"></a><a name="nsg"></a>Network security groups (Hálózati biztonsági csoportok)

Ez a szakasz a felhasználó és az Azure-alapú megerősített hálózat közötti hálózati forgalmat, valamint a virtuális hálózatban megcélzott virtuális gépeket mutatja be:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Az Azure Bastion üzembe helyezése kifejezetten a ***AzureBastionSubnet***.

* **Bejövő forgalom:**

   * **Bejövő forgalom a nyilvános internetről:** Az Azure Bastion létrehoz egy nyilvános IP-címet, amelyhez a 443-es port szükséges a nyilvános IP-címen a bejövő forgalom számára. A AzureBastionSubnet nem szükséges megnyitni a 3389/22-es portot.
   * Forgalom átadása **Az Azure Bastion Control Plan síkja:** A vezérlési sík kapcsolata esetében engedélyezze a 443-es portot a **GatewayManager** szolgáltatás címkéjén. Ez lehetővé teszi, hogy az átjáró-kezelő képes legyen kommunikálni az Azure Bastion-vel.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="NSG":::

* **Kimenő forgalom:**

   * **Kimenő forgalom a cél virtuális gépek felé:** Az Azure Bastion a cél virtuális gépeket magánhálózati IP-címekre fogja elérni. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokhoz a 3389-es és 22-es porton.
   * **Kimenő forgalom az Azure más nyilvános végpontjai felé:** Az Azure Bastion-nek képesnek kell lennie az Azure-on belüli különböző nyilvános végpontokhoz való kapcsolódásra (például diagnosztikai naplók és mérési naplók tárolására). Ezért az Azure Bastion-nek 443-re kell **AzureCloud** a szolgáltatási címkéhez.


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="NSG":::

### <a name="target-vm-subnet"></a>Cél virtuálisgép-alhálózat
Ez az az alhálózat, amely az RDP/SSH-t tartalmazó cél virtuális gépet tartalmazza.

   * **Bejövő forgalom az Azure Bastion-ből:** Az Azure Bastion privát IP-címen éri el a cél virtuális gépet. Az RDP-/SSH-portokat (3389/22-as portokat) meg kell nyitni a cél virtuális gépen a privát IP-címeken. Az ajánlott eljárás az, ha az Azure megerősített alhálózati IP-címtartományt ebben a szabályban adja hozzá, hogy csak a Bastion tudja megnyitni ezeket a portokat a célként megadott virtuálisgép-alhálózatban lévő virtuális gépeken.


## <a name="next-steps"></a>Következő lépések

További információ az Azure Bastion-ről: [Gyakori kérdések](bastion-faq.md).
