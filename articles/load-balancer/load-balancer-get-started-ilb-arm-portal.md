---
title: "Bevezetés a belső terheléselosztó Resource Managerben, az Azure Portalon történő létrehozásába | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó a Resource Managerben az Azure Portalon"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ac14fb9-8d14-4892-bfe6-8bc74c48ae2c
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6961255e4b1a269b3ac9bafb5f1b3ced7a2a8943

---

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Belső terheléselosztó létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](load-balancer-get-started-ilb-classic-ps.md) helyett javasol.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Bevezetés belső terheléselosztó Azure Portalon történő létrehozásába

Az alábbi lépések segítségével hozzon létre egy belső terheléselosztót az Azure Portalon.

1. Egy böngészőből keresse fel az [Azure Portalt](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. A képernyő bal felső részében kattintson az **Új** > **Hálózat** > **Terheléselosztó** elemre.
3. A **Terheléselosztó létrehozása** panelen adja meg a terheléselosztó **nevét**.
4. A **Séma** alatt kattintson a **Belső** elemre.
5. Kattintson a **Virtuális hálózat** elemre, majd válassza ki azt a virtuális hálózatot, amelyben a terheléselosztót szeretné létrehozni.

   > [!NOTE]
   > Ha nem jelenik meg a használni kívánt virtuális hálózat, ellenőrizze a terheléselosztóhoz használt **helyet**, és szükség szerint módosítsa.

6. Kattintson az **Alhálózat** elemre, majd válassza ki azt az alhálózatot, amelyben a terheléselosztót létre szeretné hozni.
7. Az **IP-cím hozzárendelése** panelen kattintson a **Dinamikus** vagy a **Statikus** elemre attól függően, hogy a terheléselosztó IP-címét rögzítettre (statikus) szeretné-e beállítani vagy sem.

   > [!NOTE]
   > Ha statikus IP-cím használatát választja, akkor meg kell adnia egy címet a terheléselosztó számára.

8. Az **Erőforráscsoport** alatt adja meg a terheléselosztó új erőforráscsoportjának a nevét, vagy kattintson a **Meglévő kiválasztása** elemre, és válasszon ki egy meglévő erőforráscsoportot.
9. Kattintson a **Létrehozás** gombra.

## <a name="configure-load-balancing-rules"></a>Terheléselosztási szabályok konfigurálása

A terheléselosztó létrehozása után lépjen a terheléselosztó erőforrásához, és konfigurálja.
A terheléselosztási szabály konfigurálása előtt konfigurálnia kell egy háttér-címkészletet és egy mintavételt.

### <a name="step-1-configure-a-back-end-pool"></a>1. lépés: Háttérkészlet konfigurálása

1. Az Azure Portalon kattintson a **Tallózás** > **Terheléselosztók** elemre, majd kattintson a fent létrehozott terheléselosztóra.
2. A **Beállítások** panelen kattintson a **Háttérkészletek** elemre.
3. A **Háttércímkészletek** panelen kattintson a **Hozzáadás** gombra.
4. A **Háttérkészlet hozzáadása** panelen adja meg a háttérkészlet **nevét**, majd kattintson az **OK** gombra.

### <a name="step-2-configure-a-probe"></a>2. lépés: Mintavétel konfigurálása

1. Az Azure Portalon kattintson a **Tallózás** > **Terheléselosztók** elemre, majd kattintson a fent létrehozott terheléselosztóra.
2. A **Beállítások** panelen kattintson a **Mintavételek** elemre.
3. A **Mintavételek** panelen kattintson a **Hozzáadás** gombra.
4. A **Mintavétel hozzáadása** panelen adja meg a mintavétel **nevét**.
5. A **Protokoll** alatt válassza ki a **HTTP** (webhelyekhez) vagy a **TCP** (más TCP-alapú alkalmazásokhoz) elemet.
6. A **Port** alatt adja meg a mintavétel elérésekor használni kívánt portot.
7. Az **Elérési út** alatt (csak HTTP-mintavételek esetén) adja meg a mintavételként használni kívánt elérési utat.
8. Az **Időköz** alatt adja meg, hogy milyen gyakran kell mintát venni az alkalmazásból.
9. A **Nem kifogásolatlan állapot küszöbértéke** alatt adja meg, hogy hány sikertelen kísérlet után kell a háttér virtuális gépet nem megfelelő állapotúként megjelölni.
10. Mintavétel létrehozásához kattintson az **OK** gombra.

### <a name="step-3-configure-load-balancing-rules"></a>3. lépés: Terheléselosztási szabályok konfigurálása

1. Az Azure Portalon kattintson a **Tallózás** > **Terheléselosztók** elemre, majd kattintson a fent létrehozott terheléselosztóra.
2. A **Beállítások** panelen kattintson a **Terheléselosztási szabályok** elemre.
3. A **Terheléselosztási szabályok** panelen kattintson a **Hozzáadás** gombra.
4. A **Terheléselosztási szabály hozzáadása** panelen adja meg a szabály **nevét**.
5. A **Protokoll** alatt válassza ki a **HTTP** (webhelyekhez) vagy a **TCP** (más TCP-alapú alkalmazásokhoz) elemet.
6. A **Port** alatt adja meg a port azon ügyfeleit, amelyekhez a terheléselosztóban csatlakozni kell.
7. A **Háttérport** alatt adja meg a háttérkészletben használni kívánt portot (általában a terheléselosztó portja és a háttérport azonos).
8. A **Háttérkészlet** alatt válassza ki a fent létrehozott háttérkészletet.
9. A **Munkamenet megőrzése** alatt válassza ki, hogyan szeretné megőrizni a munkameneteket.
10. Az **Üresjárat időkorlátja (perc)** alatt adja meg az üresjárati időkorlátot.
11. A **Nem fix IP-cím (közvetlen kiszolgálói válasz)** alatt kattintson a **Letiltva** vagy az **Engedélyezve** elemre.
12. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO5-->


