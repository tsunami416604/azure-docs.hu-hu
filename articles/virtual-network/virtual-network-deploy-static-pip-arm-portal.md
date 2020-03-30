---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása - Azure portal | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egy statikus nyilvános IP-címet használó virtuális gép az Azure Portalhasználatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043542"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása az Azure Portal használatával

Létrehozhat egy statikus nyilvános IP-címmel rendelkező virtuális gépet. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Rendeljen statikus nyilvános IP-címet dinamikus cím helyett, hogy a cím soha ne változik. További információ a [statikus nyilvános IP-címekről.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Ha egy meglévő virtuális géphez rendelt nyilvános IP-címet dinamikusról statikusra szeretne módosítani, vagy magánhálózati IP-címekkel szeretne dolgozni, olvassa el az [IP-címek hozzáadása, módosítása vagy eltávolítása című témakört.](virtual-network-network-interface-addresses.md) A nyilvános IP-címek [névleges díja](https://azure.microsoft.com/pricing/details/ip-addresses)van, és az előfizetésenként használható nyilvános IP-címek száma [korlátozva](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza **a Számítási**lehetőséget, majd válassza a Windows Server **2016 virtuális gép**lehetőséget, vagy egy másik, Ön által választott operációs rendszert.
3. Írja be vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd kattintson az **OK gombra:**

    |Beállítás|Érték|
    |---|---|
    |Név|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Hely| **Usa keleti része**|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások csoportban**válassza a **Nyilvános IP-cím**lehetőséget.
6. Írja be *a myPublicIpAddress parancsot,* válassza a **Statikus**lehetőséget, majd az **OK**gombot az alábbi képen látható módon:

   ![Statikus kijelölés](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Ha a nyilvános IP-címnek szabványos termékváltozatnak kell lennie, válassza a **Szabványos** lehetőséget a **Termékváltozat**csoportban. További információ a [nyilvános IP-cím-skus-okról.](virtual-network-ip-addresses-overview-arm.md#sku) Ha a virtuális gép hozzá lesz adva egy nyilvános Azure Load Balancer háttérkészletéhez, a virtuális gép nyilvános IP-címének termékváltozatának meg kell egyeznie a terheléselosztó nyilvános IP-címének termékváltozatával. További részletek az [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Jelöljön ki egy portot, vagy ne jelöljön ki portokat **a Nyilvános bejövő portok kiválasztása csoportban.** A 3389-es portál a Windows Server virtuális gép internetről történő távoli elérésének engedélyezéséhez van kiválasztva. A 3389-es port megnyitása az internetről nem ajánlott éles számítási feladatokhoz.

   ![Port kijelölése](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Fogadja el a fennmaradó alapértelmezett beállításokat, és válassza **az OK gombot.**
8. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget. A virtuális gép üzembe helyezése néhány percet vesz igénybe.
9. A virtuális gép üzembe helyezése után írja be a *myPublicIpAddress kifejezést* a portál tetején lévő keresőmezőbe. Amikor **a myPublicIpAddress** megjelenik a keresési eredmények között, jelölje ki azt.
10. Megtekintheti a nyilvános IP-címet, amely hozzá van rendelve, és hogy a cím van rendelve a **myVM** virtuális gép, ahogy az alábbi képen látható:

    ![Nyilvános IP-cím megtekintése](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Az Azure nyilvános IP-címet rendelt a virtuális gépet létrehozó régióban használt címekről. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

11. Válassza a **Konfiguráció** lehetőséget annak megerősítéséhez, hogy a hozzárendelés **statikus.**

    ![Nyilvános IP-cím megtekintése](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerén belül. Az operációs rendszer nem ismeri az Azure nyilvános IP-címeit. Bár hozzáadhat privát IP-címbeállításokat az operációs rendszerhez, azt javasoljuk, hogy csak akkor tegye meg, ha szükséges, és csak [a Privát IP-cím hozzáadása az operációs rendszerhez](virtual-network-network-interface-addresses.md#private)című elolvasása után.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információ az Azure [nyilvános IP-címeiről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- További információ a [nyilvános IP-cím összes beállításáról](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és statikus [privát IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) hozzárendeléséről egy Azure virtuális géphez
- További információ a [Linux és](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról