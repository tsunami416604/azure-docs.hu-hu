---
title: Virtuális gépek és NSG használata az Azure Bastion-ben
description: Ez a cikk bemutatja, hogyan építhet be NSG-hozzáférést az Azure Bastion használatával
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732197"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>A NSG-hozzáférés és az Azure Bastion használata

Az Azure Bastion használatakor hálózati biztonsági csoportokat (NSG) is használhat. További információ: [biztonsági csoportok](../virtual-network/security-overview.md). 

![Architektúra](./media/bastion-nsg/nsg-architecture.png)

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

* **Kimenő forgalom:**

   * **Kimenő forgalom a cél virtuális gépek felé:** Az Azure Bastion a cél virtuális gépeket magánhálózati IP-címekre fogja elérni. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokhoz a 3389-es és 22-es porton.
   * **Kimenő forgalom az Azure más nyilvános végpontjai felé:** Az Azure Bastion-nek képesnek kell lennie az Azure-on belüli különböző nyilvános végpontokhoz való kapcsolódásra (például diagnosztikai naplók és mérési naplók tárolására). Ezért az Azure Bastion-nek 443-re kell **AzureCloud** a szolgáltatási címkéhez.

### <a name="target-vm-subnet"></a>Cél virtuálisgép-alhálózat
Ez az az alhálózat, amely az RDP/SSH-t tartalmazó cél virtuális gépet tartalmazza.

   * **Bejövő forgalom az Azure Bastion-ből:** Az Azure Bastion privát IP-címen éri el a cél virtuális gépet. Az RDP-/SSH-portokat (3389/22-as portokat) meg kell nyitni a cél virtuális gépen a privát IP-címeken. Az ajánlott eljárás az, ha az Azure megerősített alhálózati IP-címtartományt ebben a szabályban adja hozzá, hogy csak a Bastion tudja megnyitni ezeket a portokat a célként megadott virtuálisgép-alhálózatban lévő virtuális gépeken.


## <a name="next-steps"></a>További lépések

További információ az Azure Bastion-ről: [Gyakori kérdések](bastion-faq.md).
