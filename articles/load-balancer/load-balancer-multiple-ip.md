---
title: Több IP-konfiguráció – Azure portal a terheléselosztás
titlesuffix: Azure Load Balancer
description: Terheléselosztás több elsődleges és másodlagos IP-konfigurációkban.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856120"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>A terheléselosztás több IP-konfiguráció az Azure portal használatával

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)


Ebben a cikkben sorra bemutatják, hogyan tartományvezérlőn másodlagos hálózati adapter (NIC) több IP-címekkel rendelkező Azure Load Balancer használatát. A következő ábra mutatja be az esetben:

![Terheléselosztói forgatókönyv](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

Ebben az esetben használjuk a következő konfigurációt:

- Két virtuális gép (VM), amelyek a Windows futtatják.
- Minden virtuális gép rendelkezik egy elsődleges és a egy másodlagos hálózati adapteren.
- Minden másodlagos hálózati adapter két IP-konfigurációval rendelkezik.
- Minden virtuális gép, amelyen két webhely: contoso.com és fabrikam.com.
- Minden webhely van kötve, egy IP-konfigurációt a másodlagos hálózati adapteren.
- Az Azure Load Balancer segítségével teszi közzé a két előtér-IP-cím, egy az egyes webhelyek. Az előtér-címek segítségével osztja el a forgalmat a megfelelő IP-konfigurációhoz minden webhelyre vonatkozóan.
- Ugyanazt a portszámot az előtér-IP-címek és a háttérkészlet-IP-címeket szolgál.

## <a name="prerequisites"></a>Előfeltételek

A példaforgatókönyv feltételezi, hogy nevű erőforráscsoport **contosofabrikam** megfelelően van konfigurálva a következő:

- Az erőforráscsoport tartalmazza egy nevű virtuális hálózatot **myVNet**.
- A **myVNet** hálózat tartalmaz két virtuális gépet nevű **VM1** és **VM2**.
- A VM1, VM2 és szerepelnek az azonos nevű rendelkezésre állási csoportja **myAvailset**. 
- A VM1 és a VM2 egy elsődleges hálózati Adaptert is **VM1NIC1** és **VM2NIC1**, illetve. 
- A VM1 és a VM2 is egy másodlagos hálózati Adaptert **VM1NIC2** és **VM2NIC2**, illetve.

Több hálózati adapterrel rendelkező virtuális gépek létrehozásával kapcsolatos további információkért lásd: [több hálózati adapterrel rendelkező virtuális gép létrehozása PowerShell-lel](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Végezhet terheléselosztást, a több IP-konfiguráció

Fejezze be a következő lépésekkel érhető el a következő cikkben ismertetett forgatókönyvet.

### <a name="step-1-configure-the-secondary-nics"></a>1. lépés: Konfigurálja a másodlagos hálózati adapterekhez

Az egyes virtuális Gépekhez a virtuális hálózaton adja hozzá a másodlagos hálózati adapter IP-konfiguráció:  

1. Tallózással keresse meg az Azure portal: https://portal.azure.com. Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső sarokban válassza ki a **erőforráscsoport** ikonra. Válassza ki az erőforráscsoportot, amelyben a virtuális gépek találhatók (például **contosofabrikam**). A **erőforráscsoportok** ablaktábla megjeleníti az összes az erőforrások és a hálózati adapter a virtuális gépek számára.

3. Az egyes virtuális gépek másodlagos hálózati Adapterre adja hozzá az IP-konfiguráció:

    1. Válassza ki a másodlagos hálózati Adaptert szeretne konfigurálni.
    
    2. Válassza ki **IP-konfigurációk**. Felső részén, a következő panelen válassza ki a **Hozzáadás**.

    3. A **hozzáadása IP-konfigurációk**, adjon hozzá egy második IP-konfigurációt a hálózati adapterre: 

        1. Adja meg a másodlagos IP-konfiguráció nevét. (Például a VM1 és a VM2 esetében az IP-konfiguráció neve **VM1NIC2-ipconfig2** és **VM2NIC2-ipconfig2**, illetve.)

        2. Az a **magánhálózati IP-cím**, **foglalási** beállításnál válassza **statikus**.

        3. Kattintson az **OK** gombra.

A második IP-konfiguráció után a másodlagos hálózati adapter van végzett, megjelenik a **IP-konfigurációk** beállításait az adott hálózati adapteren.

### <a name="step-2-create-the-load-balancer"></a>2. lépés: A terheléselosztó létrehozása

A terheléselosztó a konfiguráció létrehozása:

1. Tallózással keresse meg az Azure portal: https://portal.azure.com. Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső sarokban válassza **erőforrás létrehozása** > **hálózatkezelés** > **terheléselosztó**. Majd **létrehozás**.

3. A **terheléselosztó létrehozása**, írja be a terheléselosztó nevét. Ebben a forgatókönyvben a nevű használjuk **mylb**.

4. A **nyilvános IP-cím**, hozzon létre egy új nyilvános IP-cím néven **PublicIP1**.

5. A **erőforráscsoport**, válassza ki a meglévő erőforráscsoportot a virtuális gépek számára (például **contosofabrikam**). Válassza ki a helyet, a terheléselosztó üzembe helyezése, és válassza ki a **OK**.

A terheléselosztó üzembe helyezéséhez elindul. Üzembe helyezés eltarthat néhány perc alatt sikeresen befejeződik. Üzembe helyezés befejezése után a terheléselosztó az erőforráscsoportban erőforrásként jelenik meg.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3. lépés: Az előtérbeli IP-címkészlet konfigurálása

Minden olyan webhelyhez (contoso.com és fabrikam.com) adja meg a terheléselosztó előtérbeli IP-címkészlet:

1. Válassza a portál **további szolgáltatások**. Írja be a Szűrő mezőbe **nyilvános IP-cím** majd **nyilvános IP-címek**. Felső részén, a következő panelen válassza ki a **Hozzáadás**.

2. Két nyilvános IP-címek konfigurálása (**PublicIP1** és **PublicIP2**) tartozó mindkét webhely (a contoso.com és fabrikam.com):

   1. Adjon meg egy nevet az előtér-IP-cím.

   2. A **erőforráscsoport**, válassza ki a meglévő erőforráscsoportot a virtuális gépek számára (például **contosofabrikam**).

   3. A **hely**, mert a virtuális gépek ugyanazt a helyet kiválasztani.

   4. Kattintson az **OK** gombra.

      A nyilvános IP-címek létrehozása után azokat területen megjelenített a **nyilvános IP-cím** címeket.

3. <a name="step3-3"></a>Válassza a portál **további szolgáltatások**. Írja be a Szűrő mezőbe **terheléselosztó** majd **Load Balancer**. 

4. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e az előtérbeli IP-címkészlet hozzáadása.

5. A **beállítások**válassza **előtérbeli IP-konfiguráció**. Felső részén, a következő panelen válassza ki a **Hozzáadás**.

6. Adjon meg egy nevet az előtér-IP-cím (például **contosofe** vagy **fabrikamfe**).

7. <a name="step3-7"></a>Válassza ki **IP-cím**. A **válassza a nyilvános IP-cím**, válassza ki az előtér-IP-címek (**PublicIP1** vagy **PublicIP2**).

8. A második előtér-IP-cím létrehozása megismétlésével <a href="#step3-3">3. lépés</a> keresztül <a href="#step3-7">7. lépés</a> ebben a szakaszban.

Az előtérkészlet konfigurálása után az IP-címek jelennek meg a terheléselosztó alatt **előtérbeli IP-konfiguráció** beállításait. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4. lépés: A háttérkészlet konfigurálása

Minden olyan webhelyhez (contoso.com és fabrikam.com) a háttér-címkészletet konfigurálja a terheléselosztó:
        
1. Válassza a portál **további szolgáltatások**. Írja be a Szűrő mezőbe **terheléselosztó** majd **Load Balancer**.

2. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e hozzáadni a háttérkészlethez.

3. A **beállítások**válassza **Háttérkészletek**. Adja meg a háttérkészlet nevét (például **contosopool** vagy **fabrikampool**). Felső részén, a következő panelen válassza ki a **Hozzáadás**. 

4. A **társított**válassza **rendelkezésre állási csoport**.

5. A **rendelkezésre állási csoport**válassza **myAvailset**.

6. Adja hozzá a célhálózat IP-konfigurációja mindkét virtuális gép számára: 

    ![A terheléselosztó háttérkészlet konfigurálása](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. A **céloldali virtuális gép**, válassza ki a virtuális Gépet, a háttér-készlethez hozzáadni kívánt (például **VM1** vagy **VM2**).

    2. A **hálózati IP-konfiguráció**, válassza ki a virtuális gép, amely az előző lépésben kiválasztott másodlagos hálózati adapter IP-konfigurációja (például **VM1NIC2-ipconfig2** vagy **VM2NIC2-ipconfig2** ).

7. Kattintson az **OK** gombra.

A háttérkészlet-beállítások konfigurálása után a címek jelennek meg a terheléselosztó alatt **háttérkészlet** beállításait.

### <a name="step-5-configure-the-health-probe"></a>5. lépés: Az állapotadat-mintavétel konfigurálása

Adja meg a terheléselosztó egy állapotminta:

1. Válassza a portál **további szolgáltatások**. Írja be a Szűrő mezőbe **terheléselosztó** majd **Load Balancer**.

2. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e az állapotadat-mintavétel hozzáadása.

3. A **beállítások**válassza **állapotadat-mintavétel**. Felső részén, a következő panelen válassza ki a **Hozzáadás**. 

4. Adjon meg egy nevet az állapotminta (például **HTTP**). Kattintson az **OK** gombra.

### <a name="step-6-configure-load-balancing-rules"></a>6. lépés: Terheléselosztási szabályok konfigurálása

Minden olyan webhelyhez (contoso.com és fabrikam.com) a terheléselosztási szabályok konfigurálása:
    
1. <a name="step6-1"></a>A **beállítások**válassza **terheléselosztási szabályok**. Felső részén, a következő panelen válassza ki a **Hozzáadás**. 

2. A **neve**, írja be a terheléselosztási szabály nevét (például **HTTPc** contoso.com, vagy **HTTPf** a fabrikam.com).

3. A **előtérbeli IP-cím**, válassza ki a korábban létrehozott előtérbeli IP-címe (például **contosofe** vagy **fabrikamfe**).

4. A **Port** és **háttérport**, tartsa meg az alapértelmezett érték **80-as**.

5. A **fix IP-(közvetlen kiszolgálói válasz)** válassza **letiltott**.

6. <a name="step6-6"></a>Válassza ki **OK**.

7. A második terheléselosztási szabály létrehozása megismétlésével <a href="#step6-1">1. lépés</a> keresztül <a href="#step6-6">6. lépés</a> ebben a szakaszban.

A szabályok konfigurálása után, a terheléselosztó területen megjelenített **terheléselosztási szabályok** beállításait.

### <a name="step-7-configure-dns-records"></a>7. lépés: DNS-rekordok konfigurálása

Utolsó lépésként konfigurálja a DNS-erőforrásrekordok a terheléselosztó megfelelő előtér-IP-címekre mutassanak. You can host your domains in Azure DNS. Azure DNS használata a Load Balancer kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- Többféle terheléselosztási szolgáltatás az Azure-ban történő használatáról további [terheléselosztási szolgáltatás használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja különféle naplók kezelése és a load balancer hibaelhárítása [Azure Monitor-naplók az Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
