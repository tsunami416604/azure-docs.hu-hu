---
title: Portok megnyitása virtuális géphez az Azure Portal használatával
description: Megtudhatja, hogy miként nyithat meg egy portot/ hozhat létre végpontot a Windows virtuális géphez az Azure Portal erőforrás-kezelőtelepítési modelljével
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 130d3315b5a9a6f175bd3d67ed33a034ab5f8dda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371411"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Portok megnyitása virtuális gépre az Azure Portalsegítségével
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Keresse meg és jelölje ki a virtuális gép erőforráscsoportját, válassza a **Hozzáadás**lehetőséget, majd keresse meg és válassza a **Hálózati biztonsági csoport lehetőséget.**

2. Kattintson a **Létrehozás** gombra.

    Megnyílik **a Hálózati biztonsági csoport létrehozása** ablak.

    ![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

2. Adja meg a hálózati biztonsági csoport nevét. 

3. Jelöljön ki vagy hozzon létre egy erőforráscsoportot, majd jelöljön ki egy helyet.

4. A hálózati biztonsági csoport létrehozásához válassza a **Létrehozás** gombot.

## <a name="create-an-inbound-security-rule"></a>Bejövő biztonsági szabály létrehozása

1. Válassza ki az új hálózati biztonsági csoportot. 

2. Válassza **a Bejövő biztonsági szabályok**lehetőséget, majd a **Hozzáadás lehetőséget.**

    ![Bejövő szabály hozzáadása](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Válassza az **Advanced** (Speciális) lehetőséget. 

4. Válasszon egy közös **szolgáltatást** a legördülő menüből, például **http**. Az Egyéni lehetőséget akkor is **kiválaszthatja,** ha egy adott portot szeretne megadni. 

5. Szükség esetén módosítsa a **Prioritás** vagy a **Név .** A prioritás a szabályok alkalmazásának sorrendjét befolyásolja: minél alacsonyabb a numerikus érték, annál előbbre alkalmazza a szabályt.

6. A szabály létrehozásához válassza a **Hozzáadás** lehetőséget.

## <a name="associate-your-network-security-group-with-a-subnet"></a>A hálózati biztonsági csoport társítása alhálózattal

Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ebben a példában a hálózati biztonsági csoportot egy alhálózathoz társítjuk. 

1. Válassza **az Alhálózatok**lehetőséget, majd a **Társítás**lehetőséget.

    ![Hálózati biztonsági csoport társítása alhálózattal](./media/nsg-quickstart-portal/associate-subnet.png)

2. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.

    ![Hálózati biztonsági csoport társítása virtuális hálózattal](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Az alhálózathoz csatlakozó virtuális gépek most már elérhetők a 80-as porton.

## <a name="additional-information"></a>További információ

A [cikkben ismertetett lépéseket az Azure PowerShell használatával](nsg-quickstart-powershell.md)is végrehajthatja.

Az ebben a cikkben leírt parancsok lehetővé teszik, hogy gyorsan a virtuális gépre áramló forgalmat. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. További információt a [Hálózati forgalom szűrése hálózati biztonsági csoporttal](../../virtual-network/tutorial-filter-network-traffic.md)című témakörben talál.

A magas rendelkezésre állású webes alkalmazások esetében fontolja meg a virtuális gépek egy Azure-terheléselosztó mögé helyezését. A terheléselosztó forgalmat oszt ki a virtuális gépek, egy hálózati biztonsági csoport, amely a forgalom szűrése. További információ: [Terheléselosztás Windows virtuális gépek az Azure-ban, hogy hozzon létre egy magas rendelkezésre állású alkalmazás.](tutorial-load-balancer.md)

## <a name="next-steps"></a>További lépések
Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, létrehozott egy bejövő szabályt, amely engedélyezi a HTTP-forgalmat a 80-as porton, majd a szabályt egy alhálózathoz társítja. 

A részletesebb környezetek létrehozásáról az alábbi cikkekben talál információt:
- [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
- [Biztonsági csoportok](../../virtual-network/security-overview.md)