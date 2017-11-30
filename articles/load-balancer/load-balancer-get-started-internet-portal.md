---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása – Azure Portal | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a Resource Managerben az Azure Portallal"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 29c74c2cc58e7cab1364b2fc2c74951440dea0ab
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="creating-an-internet-facing-load-balancer-using-the-azure-portal"></a>Internetkapcsolattal rendelkező terheléselosztó létrehozása az Azure Portallal

> [!div class="op_single_selector"]
> * [Portál](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti.

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Tartalmazza a terheléselosztó létrehozása érdekében elvégzendő feladatok sorozatát, és részletesen elmagyarázza, mit kell végrehajtani a cél megvalósítása érdekében.

## <a name="what-is-required-to-create-an-internet-facing-load-balancer"></a>Mi szükséges egy internetkapcsolattal rendelkező terheléselosztó létrehozásához?

A terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehozni és konfigurálni.

* Előtér-IP-konfiguráció – a nyilvános IP-címeket tartalmazza a bejövő hálózati forgalomhoz.
* Háttércímkészlet – hálózati adaptereket (NIC) tartalmaz, amelyek segítségével a virtuális gépek fogadhatják a terheléselosztóról érkező hálózati forgalmat.
* Terheléselosztási szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá háttércímkészlet portjaihoz.
* Bejövő NAT-szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben.
* Mintavételezők – állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben.

További információkat szerezhet a terheléselosztónak az Azure Resource Managerben használt összetevőiről [Az Azure Resource Manager által nyújtott támogatás a terheléselosztó számára](load-balancer-arm.md) című részben.

## <a name="set-up-a-load-balancer-in-azure-portal"></a>Terheléselosztó beállítása az Azure Portalon

> [!IMPORTANT]
> A példa feltételezi, hogy Ön rendelkezik egy **myVNet** nevű virtuális hálózattal. A művelet elvégzéséhez lásd a [Virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) című részt. Feltételezi továbbá, hogy a **myVNet** hálózat tartalmaz egy **LB-Subnet-BE** nevű alhálózatot és két virtuális gépet (**web1** és **web2**) a **myVNet** ugyanazon **myAvailSet** nevű rendelkezésre állási csoportján belül. A virtuális gépek létrehozásához tekintse meg [ezt a hivatkozást](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Egy böngészőből keresse fel az Azure Portalt: [http://portal.azure.com](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. A képernyő bal felső részén válassza ki az **Új** > **Hálózat** > **Terheléselosztó** elemet.
3. A **Terheléselosztó létrehozása** panelen írja be a terheléselosztó nevét. Most **myLoadBalancer** a neve.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. A **Nyilvános IP-cím** alatt hozzon létre egy új nyilvános IP-címet **myPublicIP** néven.
6. Az Erőforráscsoport alatt válassza ki a **myRG** elemet. Ezután válassza ki a megfelelő **helyet**, majd kattintson az **OK** gombra. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![Terheléselosztó erőforráscsoportjának frissítése](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-a-back-end-address-pool"></a>Háttércímkészlet létrehozása

1. A terheléselosztó sikeres üzembe helyezését követően válassza ki azt az erőforrások közül. A Beállítások alatt válassza ki a Háttérkészletek elemet. Írja be a háttérkészlet nevét. Ezután kattintson a megjelenő panel tetején található **Hozzáadás** gombra.
2. A **Háttérkészlet hozzáadása** panelen kattintson a **Virtuális gép hozzáadása** lehetőségre.  A **Rendelkezésre állási készlet** alatt válassza ki a **Rendelkezésre állási készlet kiválasztása** lehetőséget, majd válassza ki a **myAvailSet** elemet. Ezután a panel Virtuális gépek szakasza alatt válassza ki a **Virtuális gépek kiválasztása** lehetőséget, majd kattintson a **web1** és a **web2** elemre, a terheléselosztás céljából létrehozott két virtuális gépre. Győződjön meg róla, hogy mindkét gép bal oldalán kék pipa található, az alábbi képnek megfelelően. Ezután kattintson ugyanazon a panelen a **Kiválasztás** elemre, majd a **Virtuális gépek kiválasztása** panelen az OK gombra, s végül a **Háttérkészlet hozzáadása** panelen az **OK** gombra.

    ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Ellenőrizze, hogy az értesítések legördülő listája tartalmaz-e a terheléselosztó háttérkészletének mentésére vonatkozó frissítést is a két virtuális gép (**web1** és **web2**) frissítésén kívül.

## <a name="create-a-probe-lb-rule-and-nat-rules"></a>Mintavétel, LB-szabály és NAT-szabályok létrehozása

1. Hozzon létre egy állapotmintát.

    A terheléselosztó Beállítások menüjéből válassza ki a Mintavételek elemet. Ezután kattintson a panel tetején található **Hozzáadás** gombra.

    Két módszer van a mintavétel konfigurálására: HTTP vagy TCP. Ez a példa a HTTP módszert mutatja be, de a TCP is hasonló módon konfigurálható.
    Frissítse a szükséges információkat. Amint már említettük, a **myLoadBalancer** elosztja a 80-as port forgalmának terhelését. A kiválasztott elérési út a HealthProbe.aspx, az időköz 15 másodperc, a nem kifogástalan állapot küszöbértéke pedig 2. Ha végzett, a projekt létrehozásához kattintson az **OK** gombra.

    Mozgassa a mutatót az „i” ikon fölé, ha többet szeretne megtudni az egyes konfigurációkról, és arról, hogyan lehet azokat módosítani az igényei szerint.

    ![Mintavétel hozzáadása](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

2. Hozzon létre egy terheléselosztó-szabályt.

    Kattintson a terheléselosztó Beállítások szakaszában található Terheléselosztási szabályok betöltése lehetőségre. Az új panelen kattintson a **Hozzáadás** gombra. Adjon nevet a szabálynak. Itt a neve HTTP. Válassza ki az előtérportot és a háttérportot. Ebben a példában mindkettőnek 80-as port van kiválasztva. Válassza ki az **LB-háttér** elemet háttérkészletként, illetve a korábban létrehozott **HealthProbe** elemet mintavételként. A többi konfigurációt a saját igényeinek megfelelően állíthatja be. Ezután kattintson az OK gombra a terheléselosztási szabály mentéséhez.

    ![Terheléselosztási szabály hozzáadása](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

3. Bejövő NAT-szabályok létrehozása

    Kattintson a terheléselosztó Beállítások szakaszában található Bejövő NAT-szabályok lehetőségre. Az új panelen kattintson a **Hozzáadás** gombra. Ezután adjon nevet a bejövő NAT-szabálynak. Itt **inboundNATrule1** a neve. A célnak a korábban létrehozott nyilvános IP-címnek kell lennie. A Szolgáltatás alatt Válassza ki az Egyéni elemet, és válassza ki a használni kívánt protokollt. Itt a TCP van kiválasztva. Adja meg a portot: 3441, illetve a célportot, ebben az esetben: 3389. ezután kattintson az OK gombra a szabály mentéséhez.

    Az első szabály létrehozása után ismételje meg ezt a lépést a második bejövő NAT-szabályra is, amelynek neve inboundNATrule2, a 3442-es porttól a 3389-es célportig.

    ![Bejövő NAT-szabály hozzáadása](./media/load-balancer-get-started-internet-portal/6-load-balancer-inbound-nat-rules.png)

## <a name="remove-a-load-balancer"></a>Terheléselosztó eltávolítása

Terheléselosztó törléséhez válassza ki az eltávolítani kívánt terheléselosztót. A *Terheléselosztó* panelen kattintson a panel tetején található **Törlés** gombra. Ezután válassza ki az **Igen** lehetőséget, ha a rendszer megkérdezi.

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-cli.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
