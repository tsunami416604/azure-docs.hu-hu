---
title: Nyissa meg a portokat a virtuális gép az Azure portal használatával |} A Microsoft Docs
description: Ismerje meg, hogyan nyisson meg egy portot / egy végpontot a resource manager üzemi modell segítségével az Azure Portalon, a Windows virtuális gép létrehozása
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 2820dcabf042d7463f9776b42f277a0457caf3b6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929015"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Hogyan nyithat meg portokat a virtuális gép az Azure portal használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
Emellett [ezeket a lépéseket az Azure PowerShell-lel](nsg-quickstart-powershell.md).

Először hozzon létre a hálózati biztonsági csoport. Válasszon ki egy erőforráscsoportot a portálon, válassza a **Hozzáadás**, majd keresse meg és jelölje ki **hálózati biztonsági csoport**:

![A hálózati biztonsági csoport hozzáadása](./media/nsg-quickstart-portal/add-nsg.png)

Adja meg a hálózati biztonsági csoport nevét, válassza ki vagy hozzon létre egy erőforráscsoportot, és válassza ki azt a helyet. Válassza ki **létrehozás** befejezése:

![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

Válassza ki az új hálózati biztonsági csoportot. Válassza a "Bejövő biztonsági szabályok", majd válassza ki a **Hozzáadás** gombra kattintva hozzon létre egy szabályt:

![Bejövő szabály felvétele](./media/nsg-quickstart-portal/add-inbound-rule.png)

Hozzon létre egy szabályt, amely lehetővé teszi a forgalmat:

- Válassza ki a **alapszintű** gombra. Alapértelmezés szerint a **speciális** ablaka néhány további konfigurációs lehetőségek például egy adott forrás IP-blokk- vagy tartomány meghatározásához.
- Válasszon egy közös **szolgáltatás** elemet a legördülő menüben, mint például *HTTP*. Lehetőség kiválasztásával *egyéni* biztosít egy adott port használatára. 
- Ha kívánja, módosítsa a prioritás vagy nevét. A prioritás hatással van, amelyben szabályokat a rendszer sorrendben alkalmazza – alsó numerikus érték, a korábban a szabály vonatkozik.
- Amikor elkészült, válassza ki a **OK** a szabály létrehozása:

![Egy bejövő szabály létrehozása](./media/nsg-quickstart-portal/create-inbound-rule.png)

Az utolsó lépés, hogy a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapter. Hozzunk társítson egy alhálózatot a hálózati biztonsági csoport. Válassza ki **alhálózatok**, majd válassza a **társítása**:

![Hálózati biztonsági csoport hozzárendelése egy alhálózathoz](./media/nsg-quickstart-portal/associate-subnet.png)

Válassza ki a virtuális hálózatot, és válassza ki a megfelelő alhálózatot:

![A hálózati biztonsági csoport társítását a virtuális hálózatok](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Ezzel létrehozott egy hálózati biztonsági csoport és a egy bejövő szabályt, amely engedélyezi a forgalmat a 80-as porton, és az alhálózatokhoz társított létrehozott. Olyan virtuális gépek a alhálózathoz csatlakozik a 80-as porton érhető el.

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik elsajátíthatja a használatát az adatforgalom a virtuális géphez. Hálózati biztonsági csoportok számos nagyszerű funkciókkal és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További információ [létrehozása egy hálózati biztonsági csoportok és ACL-szabályok itt](../../virtual-network/tutorial-filter-network-traffic.md).

A magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure Load Balancer mögött. A terheléselosztó elosztja a forgalmat, amely biztosítja a forgalom szűrése hálózati biztonsági csoport rendelkező virtuális gépek között. További információkért lásd: [betöltés Linux rendszerű virtuális gépek terheléselosztása az Azure-ban magas rendelkezésre állású alkalmazások létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű HTTP-forgalmat engedélyező szabállyal. Részletesebb környezetek létrehozásáról a következő cikkekben talál:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport?](../../virtual-network/security-overview.md)