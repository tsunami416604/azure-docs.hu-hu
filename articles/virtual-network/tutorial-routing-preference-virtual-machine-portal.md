---
title: Útválasztási beállítások konfigurálása egy virtuális gép számára – Azure Portal
description: Megtudhatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet az útválasztási beállítások megválasztásával a Azure Portal használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: 35e4b90e9bdd52a20bf892f0784b191384f271ac
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533706"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Útválasztási beállítások konfigurálása virtuális géphez a Azure Portal használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek útválasztási beállításait. A virtuális gépről érkező internetes forgalom az INTERNETSZOLGÁLTATÓ hálózatán keresztül lesz átirányítva, ha az **Internet** lehetőséget választja útválasztási preferenciaként. Az alapértelmezett útválasztás a Microsoft globális hálózatán keresztül történik.

Ez a cikk bemutatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet, amely a Azure Portal használatával irányítja át a forgalmat a nyilvános interneten keresztül.

> [!IMPORTANT]
> Az útválasztási preferencia jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://preview.portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2016 virtuális gép** lehetőséget, vagy válasszon másik operációs rendszert.
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
5. A **hálózat** lapon kattintson az **új létrehozása** **nyilvános IP-címhez** elemre.
6. Adja meg a *myPublicIpAddress*, válassza az SKU **standard** lehetőséget, majd válassza az Útválasztás preferencia **Internet** lehetőséget, majd az **OK gombot**, ahogy az alábbi képen látható:

   ![Statikus kijelölése](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Válasszon ki egy portot, vagy ne válasszon portot a **nyilvános bejövő portok kiválasztása** területen. A portál 3389 van kiválasztva, hogy engedélyezze a távoli hozzáférést a Windows Server rendszerű virtuális géphez az internetről. Az 3389-as port az internetről való megnyitása nem ajánlott éles számítási feladatokhoz.

   ![Válasszon portot](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Fogadja el a fennmaradó alapértelmezett beállításokat, majd kattintson **az OK gombra**.
8. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget. A virtuális gép üzembe helyezése néhány percet vesz igénybe.
9. A virtuális gép üzembe helyezését követően írja be a *myPublicIpAddress* kifejezést a portál felső részén található keresőmezőbe. Ha a **myPublicIpAddress** megjelenik a keresési eredmények között, válassza ki.
10. Megtekintheti a hozzárendelt nyilvános IP-címet, valamint azt, hogy a cím hozzá van-e rendelve a **myVM** virtuális géphez, ahogy az a következő képen látható:

    ![Képernyőfelvétel: a hálózati adapter mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Válassza a **hálózatkezelés** lehetőséget, majd kattintson a NIC **mynic** elemre, majd válassza ki a nyilvános IP-címet annak megerősítéséhez, hogy az útválasztási beállításokat a rendszer **internetként** rendeli hozzá.

    ![A képernyőképen egy nyilvános I P-címnek az I P-címe és az útválasztási preferencia látható.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések
- További információ a [nyilvános IP-címekről az útválasztási beállításokkal](routing-preference-overview.md).
- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).
