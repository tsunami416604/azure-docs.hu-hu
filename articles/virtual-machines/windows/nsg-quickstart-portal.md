---
title: "Nyissa meg a portokat a virtuális gép az Azure portál használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan nyisson meg egy portot / hozzon létre egy végpontot, a Windows virtuális gépre, a resource manager üzembe helyezési modellben az Azure portál használatával"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Egy virtuális géphez portok megnyitása és az Azure portál
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Gyors parancsok
Emellett [elvégzi ezeket a lépéseket az Azure PowerShell](nsg-quickstart-powershell.md).

Először hozzon létre a hálózati biztonsági csoport. Válasszon egy erőforráscsoportot a portálon, válassza a **Hozzáadás**, majd keresse meg és jelölje ki **hálózati biztonsági csoport**:

![Hálózati biztonsági csoport hozzáadása](./media/nsg-quickstart-portal/add-nsg.png)

Adja meg a hálózati biztonsági csoport nevét, válassza ki vagy hozzon létre egy erőforráscsoportot, és jelöljön ki egy helyet. Válassza ki **létrehozása** befejezése:

![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

Válassza ki az új hálózati biztonsági csoporthoz. Válassza a "Bejövő biztonsági szabályok", majd válassza ki a **Hozzáadás** gombra kattintva hozzon létre egy szabályt:

![Egy bejövő forgalomra vonatkozó szabály hozzáadása](./media/nsg-quickstart-portal/add-inbound-rule.png)

Egy szabály létrehozására, amely lehetővé teszi, hogy a forgalmat:

- Válassza ki a **alapvető** gombra. Alapértelmezés szerint a **speciális** ablaka néhány további konfigurációs beállítások, például egy adott forrás IP-blokk vagy port tartomány meghatározásához.
- Válasszon egy közös **szolgáltatás** a legördülő menüből, például a *HTTP*. Igény szerint kiválaszthatja *egyéni* arra, hogy egy adott port használatára. 
- Ha szükséges, módosítsa a prioritás vagy neve. A prioritás hatással van, amelyben a szabályok érvényesek - minél alacsonyabb a numerikus érték, a korábban a szabály vonatkozik.
- Amikor elkészült, válassza ki a **OK** a szabály létrehozásához:

![Egy bejövő forgalomra vonatkozó szabály létrehozása](./media/nsg-quickstart-portal/create-inbound-rule.png)

A végső lépés a hálózati biztonsági csoporthoz társítandó alhálózat vagy egy adott hálózati illesztőn. A hálózati biztonsági csoport most társítandó alhálózat. Válassza ki **alhálózatok**, majd válassza a **társítása**:

![Hálózati biztonsági csoport társítandó alhálózat](./media/nsg-quickstart-portal/associate-subnet.png)

Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózati:

![Egy hálózati biztonsági csoportot társít a virtuális hálózat](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Hálózati biztonsági csoport, egy bejövő szabályt, amely lehetővé teszi a forgalom 80-as porton, és azt egy alhálózathoz társított létre most hozott létre. A virtuális gépek csatlakozhat az adott alhálózat 80-as porton érhetők el.

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik, amelyekből megismerheti a forgalom halad a virtuális Gépet. Hálózati biztonsági csoportok számos különleges szolgáltatásait és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További tudnivalók [itt szabályok létrehozása a hálózati biztonsági csoport és a hozzáférés-vezérlési lista](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure terheléselosztó mögött. A load balancer osztja el a forgalmat a virtuális gépekhez, a hálózati biztonsági csoport, amely biztosítja a forgalomszűrést végez. További információkért lásd: [betöltése Linux virtuális gépek magas rendelkezésre állású alkalmazás létrehozása az Azure-ban egyenleg](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. További részletes környezetek létrehozásáról a következő cikkekben találhat:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/virtual-networks-nsg.md)