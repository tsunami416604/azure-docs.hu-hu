---
title: Virtuális gépek és NSG-k együttműködése az Azure-bástyában
description: Ez a cikk bemutatja, hogyan építheti be az NSG-hozzáférést az Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087267"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Az NSG-hozzáférés és az Azure-bástya együttműködése

Az Azure Bastion használata során hálózati biztonsági csoportokat (NSG-ket) használhat. További információt a [Biztonsági csoportok című témakörben talál.](../virtual-network/security-overview.md) 

![Architektúra](./media/bastion-nsg/nsg-architecture.png)

Ezen az ábrán:

* A megerősített állomás telepítve van a virtuális hálózatra.
* A felhasználó bármely HTML5-böngészővel csatlakozik az Azure Portalhoz.
* A felhasználó az RdP/SSH-ba való navigálás az Azure virtuális gépre.
* Integráció csatlakoztatása – egykattintásos RDP/SSH munkamenet a böngészőben
* Nincs szükség nyilvános IP-cím az Azure virtuális gép.

## <a name="network-security-groups"></a><a name="nsg"></a>Network security groups (Hálózati biztonsági csoportok)

Ez a szakasz a felhasználó és az Azure-bástya közötti hálózati forgalmat mutatja be, valamint a virtuális hálózatban lévő virtuális gépek célszolgáltatását:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Az Azure Bastion kifejezetten az AzureBastionSubnet üzembe helyezése.

* **Be- és forgalom:**

   * **Be- és forgalom nyilvános internetről:** Az Azure Bastion létrehoz egy nyilvános IP-címet, amely a 443-as portot a nyilvános IP-címre engedélyezve van a forgalom forgalomhoz. A 3389/22-es portot NEM kell megnyitni az AzureBastionSubnet-en.
   * **Forgalom be- és betiltása az Azure-bastion vezérlősíkjáról:** A vezérlősík-kapcsolathoz engedélyezze a 443-as portot a **GatewayManager** szolgáltatáscímkéből. Ez lehetővé teszi, hogy a vezérlősík, azaz a Gateway Manager képes legyen beszélni az Azure Bastion.

* **Kimenő forgalom:**

   * **Kimenő forgalom a cél virtuális gépek:** Az Azure Bastion privát IP-címén keresztül éri el a célvirtuális gépeket. Az NSG-knek engedélyeznie kell a 3389-es és 22-es port más cél virtuálisgép-alhálózatokra irányuló kimenő forgalmat.
   * **Kimenő forgalom az Azure más nyilvános végpontjaira:** Az Azure Bastion képesnek kell lennie arra, hogy csatlakozzon a különböző nyilvános végpontok az Azure-on belül (például diagnosztikai naplók tárolására és a mérési naplók). Emiatt az Azure Bastion kell kimenő 443 **AzureCloud** szolgáltatáscímkét.

* **Cél virtuális gép alhálózata:** Ez az az alhálózat, amely tartalmazza azt a célvirtuális gépet, amelyhez RDP/SSH kíván lenni.

   * **Forgalom be- és betiltása az Azure-bástyáról:** Az Azure Bastion privát IP-címén keresztül éri el a célvirtuális gép. RDP/SSH portok (portok 3389/22 volt) meg kell nyitni a cél virtuális gép oldalán magán IP-cím felett. Ajánlott eljárásként hozzáadhatja az Azure Bastion alhálózati IP-címtartományt ebben a szabályban, hogy csak a Bastion tudja megnyitni ezeket a portokat a cél virtuálisgép-alhálózatban lévő célvirtuális gépeken.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>NSG-k alkalmazása az AzureBastionSubnet-re

Ha nsg-t hoz létre és alkalmaz az ***AzureBastionSubnet-re,*** győződjön meg arról, hogy az nsg-ben hozzáadta a következő szabályokat. Ha nem adja hozzá ezeket a szabályokat, az NSG létrehozása/frissítése sikertelen lesz:

* **Vezérlősík-kapcsolat:** Bejövő a 443-as on GatewayManager
* **Diagnosztikai naplózás és egyéb:** Kimenő 443-as az AzureCloudba. A szolgáltatáscímkén belüli regionális címkék még nem támogatottak.
* **Cél virtuális gép:** Kimenő 3389 és 22 a VirtualNetwork

Ebben a [rövid útmutató sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)elérhető egy NSG-szabály példa.

## <a name="next-steps"></a>További lépések

Az Azure-bástyáról további információt a [gyakori kérdések című témakörben talál.](bastion-faq.md)
