---
title: Terheléselosztás több IP-konfiguráción – Azure Portal
titleSuffix: Azure Load Balancer
description: Ebben a cikkben az Azure Portal on az Azure Portal használatával az elsődleges és másodlagos IP-konfigurációk terheléselosztása.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077007"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Terheléselosztás több IP-konfiguráción az Azure Portal használatával

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)
> * [parancssori felület](load-balancer-multiple-ip-cli.md)


Ebben a cikkben bemutatjuk, hogyan használhatja az Azure Load Balancer több IP-címekkel egy másodlagos hálózati adapter vezérlő (NIC). Az alábbi ábra a forgatókönyvünket mutatja be:

![Terheléselosztói forgatókönyv](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

A mi forgatókönyvünkben a következő konfigurációt használjuk:

- Két windowsos virtuális gép (VM).
- Minden virtuális gép rendelkezik egy elsődleges és egy másodlagos hálózati adapterrel.
- Minden másodlagos hálózati adapter két IP-konfigurációval rendelkezik.
- Minden virtuális gép két webhelyet üzemeltet: contoso.com és fabrikam.com.
- Minden webhely a másodlagos hálózati adapter IP-konfigurációjához van kötve.
- Az Azure Load Balancer két előtér-IP-cím, egy-egy webhely. Az előtér-címek a forgalom elosztására szolgálnak a megfelelő IP-konfigurációkhoz az egyes webhelyeken.
- Ugyanaz tanportszám az előtér-IP-címekhez és a háttérkészlet IP-címeihez is használatos.

## <a name="prerequisites"></a>Előfeltételek

A példapélda feltételezi, hogy rendelkezik egy **contosofabrikam** nevű erőforráscsoporttal, amely a következőképpen van konfigurálva:

- Az erőforráscsoport tartalmaz egy **myVNet**nevű virtuális hálózatot.
- A **myVNet-hálózat** két **VM1** és **VM2**nevű virtuális gépet tartalmaz.
- A VM1 és a VM2 ugyanabban a **myAvailset**nevű rendelkezésre állási halmazban található. 
- VM1 és VM2 mindegyikrendelkezik egy elsődleges hálózati adapter nevű **VM1NIC1** és **VM2NIC1,** illetve. 
- VM1 és VM2 mindegyik rendelkezik egy másodlagos hálózati adapter nevű **VM1NIC2** és **VM2NIC2**, illetve.

A több hálózati adapterrel rendelkező virtuális gépek létrehozásáról a [Virtuálisgép létrehozása több hálózati adapterrel a PowerShell használatával című témakörben talál](../virtual-machines/windows/multiple-nics.md)további információt.

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Terheléselosztás végrehajtása több IP-konfiguráción

Hajtsa végre az alábbi lépéseket a cikkben ismertetett forgatókönyv eléréséhez.

### <a name="step-1-configure-the-secondary-nics"></a>1. lépés: A másodlagos hálózati adapterek konfigurálása

A virtuális hálózat minden egyes virtuális gépéhez adja hozzá a másodlagos hálózati adapter IP-konfigurációját:  

1. Tallózással keresse https://portal.azure.commeg az Azure-portált: . . Jelentkezzen be Azure-fiókjával.

2. A képernyő bal felső részén válassza az **Erőforráscsoport** ikont. Ezután válassza ki azt az erőforráscsoportot, amelyben a virtuális gépek találhatók (például **contosofabrikam).** Az **Erőforráscsoportok** ablaktábla a virtuális gépek összes erőforrását és hálózati adapterét megjeleníti.

3. Az egyes virtuális gépek másodlagos hálózati adaptere esetén adja hozzá az IP-konfigurációt:

    1. Válassza ki a konfigurálni kívánt másodlagos hálózati adaptert.
    
    2. Válassza az **IP-konfigurációk lehetőséget.** A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.**

    3. Az **IP-konfigurációk hozzáadása csoportban**adjon hozzá egy második IP-konfigurációt a hálózati adapterhez: 

        1. Adja meg a másodlagos IP-konfiguráció nevét. (VM1 és VM2 esetén például nevezze el a **VM1NIC2-ipconfig2** és a **VM2NIC2-ipconfig2**IP-konfigurációt.)

        2. A **Privát IP-cím**, **A Foglalás** beállításhoz válassza a **Statikus**lehetőséget.

        3. Válassza **az OK gombot.**

Miután a másodlagos hálózati adapter második IP-konfigurációja befejeződött, az az adott hálózati adapter **IP-konfigurációs** beállításai alatt jelenik meg.

### <a name="step-2-create-the-load-balancer"></a>2. lépés: A terheléselosztó létrehozása

Hozza létre a terheléselosztót a konfigurációhoz:

1. Tallózással keresse https://portal.azure.commeg az Azure-portált: . . Jelentkezzen be Azure-fiókjával.

2. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > **terheléselosztó**lehetőséget. Ezután válassza a **Létrehozás gombot.**

3. A **Terheléselosztó létrehozása**csoportban adja meg a terheléselosztó nevét. Ebben a forgatókönyvben a **mylb**nevet használjuk.

4. A **Nyilvános IP-cím csoportban**hozzon létre egy új nyilvános IP-címet, a **PublicIP1**címet.

5. Az **Erőforráscsoport**csoport csoportban válassza ki a virtuális gépek meglévő erőforráscsoportját (például **contosofabrikam).** Válassza ki azt a helyet, ahol a terheléselosztót telepíteni szeretné, majd kattintson az **OK gombra.**

A terheléselosztó megkezdi az üzembe helyezést. A telepítés sikeres befejezése néhány percet is igénybe vehet. A központi telepítés befejezése után a terheléselosztó erőforrásként jelenik meg az erőforráscsoportban.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3. lépés: Az előtér-IP-készlet konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja az előtér-IP-készletet a terheléselosztón:

1. A portálon válassza a **További szolgáltatások**lehetőséget. A szűrőmezőbe írja be a **Nyilvános IP-címet,** majd válassza a **Nyilvános IP-címek**lehetőséget. A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.**

2. Két nyilvános IP-cím **(PublicIP1** és **PublicIP2)** konfigurálása mindkét webhelyhez (contoso.com és fabrikam.com):

   1. Írja be az előtér-IP-cím nevét.

   2. Az **Erőforráscsoport**csoport esetében válassza ki a virtuális gépek meglévő erőforráscsoportját (például **contosofabrikam).**

   3. A **Hely**területen válassza ki ugyanazt a helyet, mint a virtuális gépek.

   4. Válassza **az OK gombot.**

      A nyilvános IP-címek létrehozása után azok a **nyilvános IP-címek** alatt jelennek meg.

3. <a name="step3-3"></a>A portálon válassza a **További szolgáltatások**lehetőséget. A szűrőmezőbe írja be a **terheléselosztót,** és válassza a **Terheléselosztó**lehetőséget . 

4. Válassza ki azt a terheléselosztót **(mylb),** amelyhez hozzá szeretné adni az előtér-IP-készletet.

5. A **Beállítások csoportban**válassza **az Előtér IP-konfigurációja**lehetőséget. A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.**

6. Írja be az előtér-IP-cím (például **contosofe** vagy **fabrikamfe**) nevét.

7. <a name="step3-7"></a>Válassza az **IP-címet.** A **Nyilvános IP-cím kiválasztása**csoportban válassza ki az előtérhez **(PublicIP1** vagy **PublicIP2)** vonatkozó IP-címeket.

8. A második előtér-IP-cím létrehozása a szakasz <a href="#step3-7">3–7.</a> <a href="#step3-3">step 3</a>

Az előtérkészlet konfigurálása után az IP-címek a terheléselosztó **előtér IP-konfigurációs** beállításai alatt jelennek meg. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4. lépés: A háttérkészlet konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja a terheléselosztó háttércímkészletét:
        
1. A portálon válassza a **További szolgáltatások**lehetőséget. A szűrőmezőbe írja be a **terheléselosztót,** és válassza a **Terheléselosztó**lehetőséget .

2. Válassza ki azt a terheléselosztót **(mylb),** amelyhez hozzá szeretné adni a háttérkészletet.

3. A **Beállítások csoportban**válassza **a Háttérkészletek lehetőséget.** Írja be a háttérkészlet (például **contosopool** vagy **fabrikampool)** nevét. A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.** 

4. A **Társítotthoz beállításban**válassza az **Elérhetőségi készlet lehetőséget.**

5. A **Rendelkezésre állás beállításhoz**válassza a **myAvailset lehetőséget.**

6. Adja hozzá a célhálózati IP-konfigurációkat mindkét virtuális géphez: 

    ![Háttérkészletek konfigurálása terheléselosztóhoz](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. A **cél virtuális gép,** válassza ki a virtuális gépet, amelyet hozzá szeretne adni a háttér-készlet (például **VM1** vagy **VM2).**

    2. Hálózati **IP-konfiguráció**esetén válassza ki az előző lépésben kiválasztott virtuális gép másodlagos hálózati adapterének IP-konfigurációját (például **VM1NIC2-ipconfig2** vagy **VM2NIC2-ipconfig2).**

7. Válassza **az OK gombot.**

A háttérkészlet konfigurálása után a címek a terheléselosztó **háttérkészlet-beállításai** között jelennek meg.

### <a name="step-5-configure-the-health-probe"></a>5. lépés: Az állapotminta konfigurálása

Állítson be egy állapotmintát a terheléselosztóhoz:

1. A portálon válassza a **További szolgáltatások**lehetőséget. A szűrőmezőbe írja be a **terheléselosztót,** és válassza a **Terheléselosztó**lehetőséget .

2. Válassza ki azt a terheléselosztót **(mylb),** amelyhez hozzá szeretné adni az állapotmintát.

3. A **Beállítások csoportban**válassza az **Állapot mintavétel lehetőséget.** A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.** 

4. Írja be az állapotminta nevét (például **HTTP**). Válassza **az OK gombot.**

### <a name="step-6-configure-load-balancing-rules"></a>6. lépés: Terheléselosztási szabályok konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja a terheléselosztási szabályokat:
    
1. <a name="step6-1"></a>A **Beállítások csoportban**válassza a **Terheléselosztási szabályok lehetőséget.** A következő ablaktáblán, a felső ablak tetején válassza a **Hozzáadás lehetőséget.** 

2. A **Név mezőbe**írja be a terheléselosztási szabály nevét (például **HTTPc** contoso.com vagy **HTTPf** fabrikam.com).

3. **Előtér-IP-cím**esetén válassza ki a korábban létrehozott előtér-IP-címet (például **contosofe** vagy **fabrikamfe).**

4. A **Port** és **a Backend port**esetében tartsa meg az alapértelmezett **80**értéket.

5. **Lebegő IP (közvetlen kiszolgálóvisszatérés)** esetén válassza **a Letiltva**lehetőséget.

6. <a name="step6-6"></a>Válassza **az OK gombot.**

7. Hozza létre a második terheléselosztó szabályt <a href="#step6-6">step 6</a> az <a href="#step6-1">1.</a>

A szabályok konfigurálása után azok a **terheléselosztási terheléselosztási szabályok** beállításai között jelennek meg.

### <a name="step-7-configure-dns-records"></a>7. lépés: DNS-rekordok konfigurálása

Utolsó lépésként állítsa be úgy a DNS-erőforrásrekordokat, hogy azok a terheléselosztó megfelelő előtér-IP-címeire mutassanak. A tartományok at az Azure DNS-ben üzemeltetheti. Az Azure DNS és a terheléselosztó használatával kapcsolatos további tudnivalókért olvassa el [az Azure DNS használata más Azure-szolgáltatásokkal című témakört.](../dns/dns-for-azure-services.md)

## <a name="next-steps"></a>További lépések
- További információ a terheléselosztási szolgáltatások Azure-beli kombinálásáról [az Azure terheléselosztási szolgáltatásainak használata során.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Megtudhatja, hogy miként használhat különböző típusú naplókat a terheléselosztó kezeléséhez és hibaelhárításához az [Azure Load Balancer naplóiban.](../load-balancer/load-balancer-monitor-log.md)
