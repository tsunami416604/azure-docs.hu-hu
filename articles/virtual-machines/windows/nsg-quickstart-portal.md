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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884385"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Hogyan nyithat meg portokat a virtuális gép az Azure portal használatával
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Keresse meg és válassza ki az erőforráscsoportot a virtuális gép számára, válasszon **Hozzáadás**, majd keresse meg és jelölje ki **hálózati biztonsági csoport**.

2. Kattintson a **Létrehozás** gombra.

    A **hálózati biztonsági csoport létrehozása** ablak nyílik meg.

    ![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

2. Adja meg a hálózati biztonsági csoport nevét. 

3. Válassza ki vagy hozzon létre egy erőforráscsoportot, majd válassza ki azt a helyet.

4. Válassza ki **létrehozás** a hálózati biztonsági csoport létrehozásához.

## <a name="create-an-inbound-security-rule"></a>Bejövő biztonsági szabály létrehozása

1. Válassza ki az új hálózati biztonsági csoportot. 

2. Válassza ki **bejövő biztonsági szabályok**, majd **Hozzáadás**.

    ![Bejövő szabály hozzáadása](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Válassza az **Advanced** (Speciális) lehetőséget. 

4. Válasszon egy közös **szolgáltatás** elemet a legördülő menüben, mint például **HTTP**. Lehetőség kiválasztásával **egyéni** Ha lehetővé szeretné tenni egy adott port használatára. 

5. Igény szerint módosítsa a **prioritású** vagy **neve**. A prioritás hatással van, amelyben szabályokat a rendszer sorrendben alkalmazza: minél alacsonyabb a numerikus érték, a korábban a szabály vonatkozik.

6. Válassza ki **Hozzáadás** a szabály létrehozásához.

## <a name="associate-your-network-security-group-with-a-subnet"></a>A hálózati biztonsági csoport társítása egy alhálózattal

Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ebben a példában azt fogjuk társítani a hálózati biztonsági csoport egy alhálózathoz. 

1. Válassza ki **alhálózatok**, majd **társítása**.

    ![Hálózati biztonsági csoport hozzárendelése egy alhálózathoz](./media/nsg-quickstart-portal/associate-subnet.png)

2. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.

    ![A hálózati biztonsági csoport társítását a virtuális hálózatok](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Olyan virtuális gépek a alhálózathoz csatlakozik a 80-as port már érhetők el.

## <a name="additional-information"></a>További információ

Emellett [hajtsa végre a lépéseket ebben a cikkben az Azure PowerShell-lel](nsg-quickstart-powershell.md).

Az ebben a cikkben leírt parancsokat adhat gyorsan hozzáférhet az adatforgalom a virtuális géphez. Hálózati biztonsági csoportok számos nagyszerű funkciókkal és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További információkért lásd: [egy hálózati biztonsági csoportot a hálózati forgalom szűrése](../../virtual-network/tutorial-filter-network-traffic.md).

A magas rendelkezésre állású webes alkalmazásokhoz fontolja meg a virtuális gépek Azure-terheléselosztó mögött elhelyezni. A terheléselosztó osztja el a virtuális gépekhez, forgalom, amely biztosítja a forgalom szűrése hálózati biztonsági csoport. További információkért lásd: [terheléselosztása magas rendelkezésre állású alkalmazások létrehozásához Azure-beli Windows virtuális gépek](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy hálózati biztonsági csoport és a egy bejövő szabályt, amely lehetővé teszi, hogy a 80-as port HTTP-forgalmat, és az alhálózatokhoz, hogy a szabályhoz társított létrehozott. 

Részletesebb környezetek létrehozásáról a következő cikkekben talál:
- [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
- [Biztonsági csoportok](../../virtual-network/security-overview.md)