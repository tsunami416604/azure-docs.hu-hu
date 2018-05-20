---
title: Több IP-konfigurációk az Azure-ban a terheléselosztási |} Microsoft Docs
description: Terheléselosztás elsődleges és másodlagos IP-konfiguráció között.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 2fbf7bd736a59cbcd6231fa8325a084f54927467
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Terheléselosztás több IP-konfigurációk az Azure portál használatával

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)


Ebben a cikkben az oktatóanyagban módosítjuk mutatjuk be Azure Load Balancer használata a másodlagos hálózati illesztő tartományvezérlőre (NIC) több IP-címmel. Az alábbi ábrán a mi esetünkben látható:

![Terheléselosztói forgatókönyv](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

A mi esetünkben használunk a következő konfigurációt:

- Két virtuális gépek (VM) Windows rendszert futtató.
- Minden virtuális gép rendelkezik egy elsődleges és egy másodlagos hálózati adaptert.
- Minden másodlagos hálózati adapter két IP-konfigurációval rendelkezik.
- Egyes virtuális gépek gazdagépek két webhely: contoso.com és fabrikam.com.
- Minden webhelyre van kötve egy IP-konfiguráció másodlagos hálózati adapteren
- Az Azure Load Balancer segítségével teszi közzé a két előtér-IP-cím, egy minden webhelyre vonatkozóan. Az előtér-címek minden webhelyre vonatkozóan a megfelelő IP-konfiguráció felé irányuló forgalom terjesztésére szolgálnak.
- Ugyanazt a portszámot az előtér-IP-címek és a háttér-címkészlet IP-címeket szolgál.

## <a name="prerequisites"></a>Előfeltételek

A forgatókönyv példa feltételezi, hogy rendelkezik-e nevű erőforráscsoport **contosofabrikam** , amely konfigurálva van az alábbiak szerint:

- Az erőforráscsoport tartalmazza a virtuális hálózati nevű **myVNet**.
- A **myVNet** hálózat tartalmaz nevű két virtuális gép **VM1** és **vm2 virtuális gépnek**.
- VM1 és vm2 virtuális gépnek vannak ugyanabban a rendelkezésre állási csoportban elnevezett **myAvailset**. 
- VM1 és vm2 virtuális gépnek is egy elsődleges hálózati adapter nevű **VM1NIC1** és **VM2NIC1**, illetve. 
- VM1 és vm2 virtuális gépnek is egy másodlagos hálózati adapter nevű **VM1NIC2** és **VM2NIC2**, illetve.

Több hálózati adapterrel rendelkező virtuális gépek létrehozásával kapcsolatos további információkért lásd: [több hálózati adapterrel rendelkező virtuális gép létrehozása a PowerShell használatával](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Hajtsa végre a hálózati terheléselosztást a több IP-konfigurációk

Kövesse az alábbi lépéseket a cikkben ismertetett forgatókönyvben eléréséhez.

### <a name="step-1-configure-the-secondary-nics"></a>1. lépés: A másodlagos hálózati adapterek konfigurálása

Az egyes virtuális gépek a virtuális hálózaton vegye fel a másodlagos hálózati adapter IP-konfigurációja:  

1. Keresse meg az Azure-portálon: http://portal.azure.com. Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső sarokban válassza ki a **erőforráscsoport** ikonra. Válassza ki az erőforráscsoportot, ahol a virtuális gépek találhatók (például **contosofabrikam**). A **erőforráscsoportok** ablaktáblán megjelennek azok az erőforrások és a hálózati adaptert a virtuális gépekhez.

3. A másodlagos hálózati adapter az egyes virtuális gépek adja hozzá az IP-konfiguráció:

    1. Válassza ki a másodlagos hálózati adaptert szeretne konfigurálni.
    
    2. Válassza ki **IP-konfigurációk**. A következő ablaktábla felső részén, jelölje ki **Hozzáadás**.

    3. A **hozzáadása IP-konfigurációk**, a második IP-konfiguráció hozzáadása a hálózati adapterre: 

        1. Adja meg a másodlagos IP-konfiguráció nevét. (Például VM1 és vm2 virtuális gépnek, az IP-konfiguráció neve **VM1NIC2-ipconfig2** és **VM2NIC2-ipconfig2**, illetve.)

        2. Az a **magánhálózati IP-cím**, **foglalási** beállításnál válassza **statikus**.

        3. Kattintson az **OK** gombra.

A második IP-konfiguráció után a másodlagos hálózati adapter befejeződött, megjelenik a a **IP-konfigurációk** beállításait a megadott hálózati adaptert.

### <a name="step-2-create-the-load-balancer"></a>2. lépés: A terheléselosztó létrehozása

Hozza létre a terheléselosztó-konfigurációhoz:

1. Keresse meg az Azure-portálon: http://portal.azure.com. Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső sarokban válassza **hozzon létre egy erőforrást** > **hálózati** > **terheléselosztó**. Válassza ki, **létrehozása**.

3. A **létrehozás terheléselosztó**, írja be a terheléselosztó nevét. Ebben a forgatókönyvben a név használunk **mylb**.

4. A **nyilvános IP-cím**, hozzon létre egy új nyilvános IP-cím nevű **PublicIP1**.

5. A **erőforráscsoport**, válassza ki a meglévő erőforráscsoportot a virtuális géphez (például **contosofabrikam**). Válassza ki a terheléselosztó való telepítéséhez, és válassza ki a helyet **OK**.

A load balancer megkezdené központi telepítését. Központi telepítés sikeres befejezéséhez néhány percig is tarthat. Központi telepítés befejezése után, a terheléselosztó az erőforráscsoportban erőforrásként jelenik meg.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3. lépés: Az előtér-IP-címkészlet konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja az előtér-IP-címkészlet a terheléselosztó:

1. Válassza a portál **további szolgáltatások**. A Szűrő mezőbe írja be **nyilvános IP-cím** majd **nyilvános IP-címek**. A következő ablaktábla felső részén, jelölje ki **Hozzáadás**.

2. Két nyilvános IP-címek konfigurálása (**PublicIP1** és **PublicIP2**) mindkét webhelyekhez (contoso.com és fabrikam.com):

    1. Adjon meg egy nevet az előtér-IP-címe.

    2. A **erőforráscsoport**, válassza ki a meglévő erőforráscsoportot a virtuális géphez (például **contosofabrikam**).

    3. A **hely**, jelölje ki a virtuális gépek ugyanazon a helyen.

    4. Kattintson az **OK** gombra.

    A nyilvános IP-címek létrehozása után jelennek meg a a **nyilvános IP-cím** címek.

3. <a name="step3-3"></a>Válassza a portál **további szolgáltatások**. A Szűrő mezőbe írja be **terheléselosztó** majd **terheléselosztó**. 

4. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e hozzá az előtér-IP-készlet.

5. A **beállítások**, jelölje be **előtérbeli IP-konfiguráció**. A következő ablaktábla felső részén, jelölje ki **Hozzáadás**.

6. Adjon meg egy nevet az előtérbeli IP-cím (például **contosofe** vagy **fabrikamfe**).

7. <a name="step3-7"></a>Válassza ki **IP-cím**. A **válassza a nyilvános IP-cím**, válassza ki az előtér-IP-címét (**PublicIP1** vagy **PublicIP2**).

8. A második előtér-IP-cím létrehozása megismétlésével <a href="#step3-3">3. lépés</a> keresztül <a href="#step3-7">7. lépés</a> ebben a szakaszban.

Az előtér-készlet konfigurálása után az IP-címek láthatók a terheléselosztó **előtérbeli IP-konfiguráció** beállításait. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4. lépés: A háttér-készlet konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja a terheléselosztó a háttér-címkészlet:
        
1. Válassza a portál **további szolgáltatások**. A Szűrő mezőbe írja be **terheléselosztó** majd **terheléselosztó**.

2. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e hozzáadni a háttér-készletet.

3. A **beállítások**, jelölje be **Háttérkészletek**. Írja be a háttér-készlet nevét (például **contosopool** vagy **fabrikampool**). A következő ablaktábla felső részén, jelölje ki **Hozzáadás**. 

4. A **társított**, jelölje be **rendelkezésre állási csoport**.

5. A **rendelkezésre állási csoport**, jelölje be **myAvailset**.

6. A cél hálózati IP-konfigurációk mindkét virtuális gépek hozzáadása: 

    ![Háttér-címkészleteket terheléselosztóhoz](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. A **cél virtuális gép**, válassza ki a virtuális Gépet, a háttér-készlethez hozzáadni kívánt (például **VM1** vagy **vm2 virtuális gépnek**).

    2. A **hálózati IP-konfiguráció**, válassza ki a virtuális gép az előző lépésben kiválasztott másodlagos hálózati adapter IP-konfigurációja (például **VM1NIC2-ipconfig2** vagy **VM2NIC2-ipconfig2** ).

7. Kattintson az **OK** gombra.

A háttér-készlet konfigurálása után a címek jelennek meg a terheléselosztó a **háttérkészlet** beállításait.

### <a name="step-5-configure-the-health-probe"></a>5. lépés: A állapotmintáihoz konfigurálása

Adja meg a terheléselosztóhoz egy állapotmintáihoz:

1. Válassza a portál **további szolgáltatások**. A Szűrő mezőbe írja be **terheléselosztó** majd **terheléselosztó**.

2. Válassza ki a terheléselosztó (**mylb**), hogy szeretné-e a állapotmintáihoz való hozzáadása.

3. A **beállítások**, jelölje be **állapotmintáihoz**. A következő ablaktábla felső részén, jelölje ki **Hozzáadás**. 

4. Adjon meg egy nevet a állapotmintáihoz (például **HTTP**). Kattintson az **OK** gombra.

### <a name="step-6-configure-load-balancing-rules"></a>6. lépés: A terheléselosztási szabályok konfigurálása

Minden webhelyre (contoso.com és fabrikam.com) a terheléselosztási szabályok konfigurálása:
    
1. <a name="step6-1"></a>A **beállítások**, jelölje be ** terheléselosztási szabályok **. A következő ablaktábla felső részén, jelölje ki **Hozzáadás**. 

2. A **neve**, írja be a terheléselosztási szabály nevét (például **HTTPc** contoso.com, vagy **HTTPf** a, fabrikam.com).

3. A **előtér-IP-cím**, válassza ki a korábban létrehozott előtér-IP-cím (például **contosofe** vagy **fabrikamfe**).

4. A **Port** és **háttérportot**, tartsa meg az alapértelmezett értéket **80**.

5. A **fix IP-Címek (közvetlen kiszolgálói válasz)**, jelölje be **engedélyezve**.

6. <a name="step6-6"></a>Válassza ki **OK**.

7. A második terheléselosztási szabály létrehozása megismétlésével <a href="#step6-1">1. lépés</a> keresztül <a href="#step6-6">6. lépés</a> ebben a szakaszban.

A szabályok konfigurálása után jelennek meg a terheléselosztó a **terheléselosztási szabályok** beállításait.

### <a name="step-7-configure-dns-records"></a>7. lépés: A DNS-rekordok konfigurálása

Utolsó lépésként állítsa be a DNS-erőforrásrekordok a terheléselosztóhoz tartozó előtér-IP-címekkel mutasson. You can host your domains in Azure DNS. Az Azure DNS-sel terheléselosztással kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- További tudnivalók az Azure a terheléselosztási egyesítése [terheléselosztás szolgáltatások használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja naplók különböző típusú kezelésére és hibaelhárítására terheléselosztó [analytics keresse meg a Azure terheléselosztó](../load-balancer/load-balancer-monitor-log.md).
