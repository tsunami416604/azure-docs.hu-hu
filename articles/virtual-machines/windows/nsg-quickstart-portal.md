---
title: Portok megnyitása virtuális géphez a Azure Portal használatával
description: Megtudhatja, hogyan nyithat meg egy portot/hozzon létre egy végpontot a Windows rendszerű virtuális géphez az Azure Portal Resource Manager-alapú üzemi modelljének használatával
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
ms.openlocfilehash: fe9b0f590dbb728a54ac66fcaa49bf3d00417e41
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033111"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Portok megnyitása virtuális géphez a Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Keresse meg és válassza ki a virtuális géphez tartozó erőforráscsoportot, válassza a **Hozzáadás**lehetőséget, majd keresse meg és válassza ki a **hálózati biztonsági csoportot**.

2. Kattintson a **Létrehozás** gombra.

    Megnyílik a **hálózati biztonsági csoport létrehozása** ablak.

    ![Hálózati biztonsági csoport létrehozása](./media/nsg-quickstart-portal/create-nsg.png)

2. Adja meg a hálózati biztonsági csoport nevét. 

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot, majd válasszon ki egy helyet.

4. Válassza a **Létrehozás** lehetőséget a hálózati biztonsági csoport létrehozásához.

## <a name="create-an-inbound-security-rule"></a>Bejövő biztonsági szabály létrehozása

1. Válassza ki az új hálózati biztonsági csoportot. 

2. Válassza a **bejövő biztonsági szabályok**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    ![Bejövő szabály hozzáadása](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Válassza az **Advanced** (Speciális) lehetőséget. 

4. Válasszon egy közös **szolgáltatást** a legördülő menüből, például **http**. A **Custom (egyéni** ) lehetőséget is választhatja, ha a használni kívánt portot szeretné megadni. 

5. Szükség esetén módosíthatja a **prioritást** vagy a **nevet**. A prioritás befolyásolja a szabályok alkalmazásának sorrendjét: minél kisebb a numerikus érték, annál korábbi a szabály alkalmazása.

6. A szabály létrehozásához válassza a **Hozzáadás** lehetőséget.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Hálózati biztonsági csoport hozzárendelése alhálózattal

Az utolsó lépés a hálózati biztonsági csoport társítása egy alhálózattal vagy egy adott hálózati adapterrel. Ebben a példában a hálózati biztonsági csoportot egy alhálózattal társítjuk. 

1. Válassza az **alhálózatok**, majd a **hozzárendelés**lehetőséget.

    ![Hálózati biztonsági csoport hozzárendelése alhálózattal](./media/nsg-quickstart-portal/associate-subnet.png)

2. Válassza ki a virtuális hálózatot, majd válassza ki a megfelelő alhálózatot.

    ![Hálózati biztonsági csoport társítása virtuális hálózattal](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Az alhálózathoz csatlakoztatott virtuális gépek mostantól elérhetők az 80-es porton.

## <a name="additional-information"></a>További információ

[A cikkben ismertetett lépéseket a Azure PowerShell használatával is végrehajthatja](nsg-quickstart-powershell.md).

A cikkben ismertetett parancsok segítségével gyorsan lekérheti a virtuális gépre irányuló forgalmat. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. További információ: [hálózati forgalom szűrése hálózati biztonsági csoporttal](../../virtual-network/tutorial-filter-network-traffic.md).

A magasan elérhető webalkalmazások esetében érdemes lehet egy Azure Load Balancer mögött elhelyezni a virtuális gépeket. A terheléselosztó elosztja a forgalmat a virtuális gépekre egy olyan hálózati biztonsági csoporttal, amely forgalmi szűrést biztosít. További információkért lásd: [Windows rendszerű virtuális gépek terheléselosztása az Azure-ban egy magasan elérhető alkalmazás létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, létrehozott egy bejövő szabályt, amely engedélyezi a HTTP-forgalmat az 80-as porton, majd hozzárendeli a szabályt egy alhálózathoz. 

A részletesebb környezetek létrehozásáról a következő cikkekben talál információt:
- [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
- [Biztonsági csoportok](../../virtual-network/security-overview.md)