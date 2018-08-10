---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gép statikus nyilvános IP-címet az Azure portal használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: jdial
ms.openlocfilehash: 9b6db45e38267c70adef3f5a341b8b918b9e78fb
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714427"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Hozzon létre egy virtuális gépet egy statikus nyilvános IP-címet az Azure portal használatával

Létrehozhat egy virtuális gépet egy statikus nyilvános IP-címmel. Nyilvános IP-cím lehetővé teszi, hogy egy virtuális géphez az internetről érkező kommunikációt. Rendeljen hozzá egy statikus nyilvános IP-címet, nem pedig a dinamikus címet, annak érdekében, hogy a cím soha nem módosul. Tudjon meg többet [statikus nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#allocation-method). Módosítsa a statikus, dinamikus egy meglévő virtuális géphez társított nyilvános IP-címet, vagy magánhálózati IP-címek használata esetén lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md). Nyilvános IP-címekre egy [névleges díj](https://azure.microsoft.com/pricing/details/ip-addresses), és van egy [korlát](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) előfizetésenként használható nyilvános IP-címek száma.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza ki **számítási**, majd válassza ki **Windows Server 2016 virtuális gép**, vagy Ön egy másik operációs rendszeren.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **beállítások**válassza **nyilvános IP-cím**.
6. Adja meg *myPublicIpAddress*válassza **statikus**, majd válassza ki **OK**, ahogy az alábbi képen is látható:

   ![Válassza ki a statikus](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Ha a nyilvános IP-címet kell lennie, a standard Termékváltozat, válassza ki a **Standard** alatt **Termékváltozat**. Tudjon meg többet [nyilvános IP-cím termékváltozatok](virtual-network-ip-addresses-overview-arm.md#sku). Ha a rendszer felveszi a virtuális gép egy nyilvános Azure Load Balancer háttérkészlethez, az a virtuális gép nyilvános IP-cím Termékváltozatának meg kell egyeznie a terheléselosztó nyilvános IP-cím-Termékváltozat. További információkért lásd: [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Válassza ki a portot, vagy nincs portokat a **nyilvános bejövő portok kiválasztása**. Portál 3389-es van jelölve, a távoli hozzáférés engedélyezése a Windows Server virtuális géphez az internetről. Éles számítási feladatok esetében nem ajánlott a 3389-es port megnyitása az internetről.

   ![Válassza ki azt a portot](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Fogadja el a többi alapértelmezett beállítást, és válassza ki **OK**.
8. Az a **összefoglalás** lapon jelölje be **létrehozás**. A virtuális gép üzembe helyezése néhány percet vesz igénybe.
9. A virtuális gép üzembe helyezése után adja meg a *myPublicIpAddress* , a portál tetején található keresőmezőbe. Amikor **myPublicIpAddress** megjelenik a keresési eredmények között, válassza ki.
10. Megtekintheti a nyilvános IP-címet, amely hozzá van rendelve, és hogy a cím hozzá van rendelve a **myVM** virtuális gépet, az alábbi ábrán látható módon:

    ![Nyilvános IP-cím megtekintése](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure hozzárendelt nyilvános IP-címet a virtuális gépet hozott létre a régióban használt címek. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

11. Válassza ki **konfigurációs** meggyőződni arról, hogy a hozzárendelés **statikus**.

    ![Nyilvános IP-cím megtekintése](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
Ne módosítsa az IP-címbeállítások, a virtuális gép operációs rendszerén belül. Az operációs rendszer nem észleli az Azure nyilvános IP-címek. Bár a magánhálózati IP-cím beállításait az operációs rendszer is hozzáadhat, javasoljuk, hogy nem így, ha szükséges, és a csak olvasási után nem [magánhálózati IP-cím hozzáadása operációs rendszer](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) az Azure-ban
- További információ az összes [nyilvános IP-cím beállításai](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Tudjon meg többet [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és hozzárendelése egy [statikus magánhálózati IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) Azure virtuális gépeken
- További információ a létrehozásával [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek