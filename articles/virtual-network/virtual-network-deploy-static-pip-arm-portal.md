---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása – Azure Portal | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre statikus nyilvános IP-címmel rendelkező virtuális gépet a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 9e06e4079a5118e0aa9dedb1fca719f0b28e5716
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448624"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása a Azure Portal használatával

Létrehozhat egy statikus nyilvános IP-címmel rendelkező virtuális gépet is. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Statikus nyilvános IP-címet rendeljen hozzá, nem pedig dinamikus címet, hogy a cím ne legyen módosítva. További információ a [statikus nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ha egy meglévő virtuális géphez hozzárendelt nyilvános IP-címet szeretne módosítani dinamikusról statikusra, vagy magánhálózati IP-címekkel szeretne dolgozni, tekintse meg az [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md)című témakört. A nyilvános IP-címek [névleges díjszabással](https://azure.microsoft.com/pricing/details/ip-addresses)rendelkeznek, és az előfizetések által használható nyilvános IP-címek száma [korlátozva](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **számítás**lehetőséget, majd válassza a **Windows Server 2016 virtuális gép**lehetőséget, vagy válasszon másik operációs rendszert.
3. Adja meg vagy válassza ki a következő adatokat, fogadja el a többi beállítás alapértelmezett értékeit, majd kattintson az **OK gombra**:

    |Beállítás|Érték|
    |---|---|
    |Név|myVM|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Hely| Válassza ki az **USA keleti** régióját|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások**területen válassza a **nyilvános IP-cím**elemet.
6. Adja meg a *myPublicIpAddress*, válassza a **statikus**lehetőséget, majd kattintson az **OK gombra**, ahogy az a következő képen látható:

   ![Statikus kijelölése](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Ha a nyilvános IP-címnek standard SKU-nak kell lennie, válassza a **standard** elemet az **SKU**alatt. További információ a [nyilvános IP-cím SKU-](virtual-network-ip-addresses-overview-arm.md#sku)ról. Ha a virtuális gépet egy nyilvános Azure Load Balancer háttér-készletéhez adja hozzá, akkor a virtuális gép nyilvános IP-címének SKU-jának meg kell egyeznie a terheléselosztó nyilvános IP-címének SKU-jának. Részletekért lásd: [Azure Load Balancer](../load-balancer/skus.md).

6. Válasszon ki egy portot, vagy ne válasszon portot a **nyilvános bejövő portok kiválasztása**területen. A portál 3389 van kiválasztva, hogy engedélyezze a távoli hozzáférést a Windows Server rendszerű virtuális géphez az internetről. Az 3389-as port az internetről való megnyitása nem ajánlott éles számítási feladatokhoz.

   ![Válasszon portot](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Fogadja el a fennmaradó alapértelmezett beállításokat, majd kattintson **az OK gombra**.
8. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget. A virtuális gép üzembe helyezése néhány percet vesz igénybe.
9. A virtuális gép üzembe helyezését követően írja be a *myPublicIpAddress* kifejezést a portál felső részén található keresőmezőbe. Ha a **myPublicIpAddress** megjelenik a keresési eredmények között, válassza ki.
10. Megtekintheti a hozzárendelt nyilvános IP-címet, valamint azt, hogy a cím hozzá van-e rendelve a **myVM** virtuális géphez, ahogy az a következő képen látható:

    ![A képernyőképen a nyilvános I P-címek ablaktáblája látható, az I P-címe és a neve. ](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Az Azure hozzárendelt egy nyilvános IP-címet a virtuális gépet a ben létrehozó régióban használt címekről. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

11. A **konfiguráció** beállítás megadásával ellenőrizheti, hogy a hozzárendelés **statikus**-e.

    ![A képernyőképen a nyilvános I P-címek ablaktáblája látható a kiválasztott konfigurációs elemmel.](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerén belül. Az operációs rendszer nem ismeri az Azure nyilvános IP-címeit. Bár a magánhálózati IP-címek beállításait az operációs rendszerhez is hozzáadhatja, azt javasoljuk, hogy csak akkor hajtsa végre ezt, ha szükséges, és nem, amíg az olvasó nem [ad hozzá privát IP-címet az operációs rendszerhez](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- További információ az összes [nyilvános IP-cím beállításairól](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és a [statikus magánhálózati IP-](virtual-network-network-interface-addresses.md#add-ip-addresses) címek egy Azure-beli virtuális géphez való hozzárendeléséről
- További információ a [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról