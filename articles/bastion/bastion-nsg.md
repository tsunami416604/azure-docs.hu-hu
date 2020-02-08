---
title: Virtuális gépek és NSG használata az Azure Bastion-ben
description: Ez a cikk bemutatja, hogyan építhet be NSG-hozzáférést az Azure Bastion használatával
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087267"
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

## <a name="nsg"></a>Hálózati biztonsági csoportok

Ez a szakasz a felhasználó és az Azure-alapú megerősített hálózat közötti hálózati forgalmat, valamint a virtuális hálózatban megcélzott virtuális gépeket mutatja be:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Az Azure Bastion üzembe helyezése kifejezetten a AzureBastionSubnet történik.

* **Bejövő forgalom:**

   * **Bejövő forgalom a nyilvános internetről:** Az Azure Bastion létrehoz egy nyilvános IP-címet, amelyhez a 443-es port szükséges a nyilvános IP-címen a bejövő forgalom számára. A AzureBastionSubnet nem szükséges megnyitni a 3389/22-es portot.
   * Forgalom átadása **Az Azure Bastion Control Plan síkja:** A vezérlési sík kapcsolata esetében engedélyezze a 443-es portot a **GatewayManager** szolgáltatás címkéjén. Ez lehetővé teszi, hogy az átjáró-kezelő képes legyen kommunikálni az Azure Bastion-vel.

* **Kimenő forgalom:**

   * **Kimenő forgalom a cél virtuális gépek felé:** Az Azure Bastion a cél virtuális gépeket magánhálózati IP-címekre fogja elérni. A NSG engedélyeznie kell a kimenő forgalmat más cél virtuálisgép-alhálózatokhoz a 3389-es és 22-es porton.
   * **Kimenő forgalom az Azure más nyilvános végpontjai felé:** Az Azure Bastion-nek képesnek kell lennie az Azure-on belüli különböző nyilvános végpontokhoz való kapcsolódásra (például diagnosztikai naplók és mérési naplók tárolására). Ezért az Azure Bastion-nek 443-re kell **AzureCloud** a szolgáltatási címkéhez.

* **Cél virtuálisgép-alhálózat:** Ez az az alhálózat, amely az RDP/SSH-t tartalmazó cél virtuális gépet tartalmazza.

   * **Bejövő forgalom az Azure Bastion-ből:** Az Azure Bastion privát IP-címen éri el a cél virtuális gépet. Az RDP-/SSH-portokat (3389/22-as portokat) meg kell nyitni a cél virtuális gépen a privát IP-címeken. Az ajánlott eljárás az, ha az Azure megerősített alhálózati IP-címtartományt ebben a szabályban adja hozzá, hogy csak a Bastion tudja megnyitni ezeket a portokat a célként megadott virtuálisgép-alhálózatban lévő virtuális gépeken.

## <a name="apply"></a>NSG alkalmazása a AzureBastionSubnet

Ha NSG hoz létre és alkalmaz ***AzureBastionSubnet***, győződjön meg arról, hogy az alábbi szabályokat adta hozzá a NSG. Ha nem adja hozzá ezeket a szabályokat, a NSG létrehozása/frissítése sikertelen lesz:

* **Vezérlési sík kapcsolata:** 443-es bejövő GatewayManager
* **Diagnosztikai naplózás és egyebek:** 443-ra kimenő AzureCloud. A szolgáltatási címkén belüli regionális címkék még nem támogatottak.
* **Cél virtuális gép:** Kimenő 3389 és 22 közötti VirtualNetwork

Ebben a rövid útmutatóban egy NSG-szabály is [elérhető.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)

## <a name="next-steps"></a>Következő lépések

További információ az Azure Bastion-ről: [Gyakori kérdések](bastion-faq.md).
