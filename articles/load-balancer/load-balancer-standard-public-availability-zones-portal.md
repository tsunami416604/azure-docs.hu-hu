---
title: Terheléselosztó virtuális gépek rendelkezésre állási zónák - Azure-portálon keresztül betöltése |} Microsoft Docs
description: Hozzon létre egy szabványos terheléselosztó zónaredundáns előtér terheléselosztásához virtuális gépek rendelkezésre állási zónák Azure-portálon keresztül
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Terheléselosztás virtuális gépek rendelkezésre állási zónák az Azure portál használatával szabványos terheléselosztót között

Ez a cikk lépéseit a zóna redundáns időtúllépést eléréséhez a Load Balancer szabványos nyilvános létrehozása zóna-redundancia nélkül függőség több DNS-rekordok elérése. Egy szabványos terheléselosztó egyetlen előtérbeli IP-cím automatikusan zónaredundáns. Zóna redundáns időtúllépést alkalmaz a terheléselosztó egyetlen IP-címmel most érhető el minden virtuális gép virtuális hálózatban, amely az összes rendelkezésre állási zóna között régión belül. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére. A zóna-redundancia egy vagy több rendelkezésre állási zónák sikertelen lehet, és az elérési útja survives mindaddig egy zónát a megfelelő terület marad. 

Rendelkezésre állási zónák használatával szabványos terheléselosztással kapcsolatos további információkért lásd: [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) címen.

## <a name="create-a-public-standard-load-balancer"></a>Hozzon létre egy szabványos nyilvános terheléselosztó

A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Ha a terheléselosztó létrehozása során létrehoz egy új nyilvános IP-cím, Standard Termékváltozat verzióként automatikusan konfigurálja, és azt is automatikusan zónaredundáns.

1. A képernyő bal felső részén kattintson az **Erőforrás létrehozása** > **Hálózatkezelés** > **Terheléselosztó** elemre.
2. A **Terheléselosztó létrehozása** lapon adja meg az alábbi értékeket a terheléselosztó számára:
    - A terheléselosztó neve: *myLoadBalancer*.
    - A terheléselosztó típusa: **Nyilvános**.
     - *myPublicIP* – az Ön által létrehozott új nyilvános IP-címéhez. Ehhez kattintson **egy nyilvános IP-cím kiválasztása**, és kattintson a **hozzon létre új**. Név típus *myPublicIP*, termékváltozata Standard alapértelmezés szerint, és válassza ki **zónaredundáns** a **rendelkezésre állási zóna**.
    - *myResourceGroupLBAZ* – az Ön által létrehozott új erőforráscsoport nevét.
    - A hely: **westeurope**.
3. A terheléselosztó létrehozásához kattintson a **Létrehozás** elemre.
   
    ![Load Balancer létrehozása](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban létre virtuális hálózatot, virtuális gépek létrehozása a különböző zónákhoz (1, 2 zónát, és zónát 3)-terület hozzáadása a háttérkészlet, a terheléselosztóhoz, és telepítse az IIS segítségével a zónaredundáns terhelés bala tesztelése a virtuális gépeken ncer. Ezért ha meghibásodik egy zónát, a virtuális gép ugyanabban a zónában állapotmintáihoz sikertelen lesz, és forgalom továbbra is fennáll, amelyet a többi zónákat a virtuális gépek.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. Kattintson a képernyő bal felső **hozzon létre egy erőforrást** > **hálózati** > **virtuális hálózati** , és ezekkel az értékekkel adja meg a virtuális hálózat:
    - A virtuális hálózat neve: *myVnet*.
    - *myResourceGroupLBAZ* – a meglévő erőforráscsoport nevét
    - Az alhálózat neve: *myBackendSubnet*.
2. A virtuális hálózat létrehozásához kattintson a **Létrehozás** gombra.

    ![Virtuális hálózat létrehozása](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. A képernyő felső bal oldalán kattintson **hozzon létre egy erőforrást**, a keresőmezőbe írja be a *hálózati biztonsági csoport*, és a hálózati biztonsági csoport oldalon kattintson **létrehozása**.
2. A létrehozás hálózati biztonsági csoport lapon adja meg ezeket az értékeket:
    - *myNetworkSecurityGroup* – a hálózati biztonsági csoport nevét.
    - *myResourceGroupLBAZ* – a meglévő erőforráscsoport nevét.
   
![Virtuális hálózat létrehozása](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>NSG-szabályok létrehozása

Ebben a szakaszban hoz létre NSG-szabályok a HTTP és az Azure portál használatával RDP bejövő kapcsolatok lehetővé tételéhez.

1. Az Azure portálon kattintson **összes erőforrás** a bal oldali menüből, és ezután keresési, majd kattintson a **myNetworkSecurityGroup** található, amely a **myResourceGroupLBAZ** erőforráscsoport.
2. A **Beállítások** területen kattintson a **Bejövő biztonsági szabályok** lehetőségre, majd a **Hozzáadás** gombra.
3. Adja meg az alábbi értékeket a *myHTTPRule* nevű bejövő biztonsági szabály számára a 80-as portot használó, bejövő HTTP-kapcsolatok engedélyezése érdekében:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *80*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *100*
    - Név: *myHTTPRule*
    - Leírás: *HTTP engedélyezése*
4. Kattintson az **OK** gombra.
 
 ![Virtuális hálózat létrehozása](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Ismételje meg a 2–4. lépéseket egy másik, *myRDPRule* nevű szabály létrehozásához, amely engedélyezi a 3389-es portot használó, bejövő RDP-kapcsolatokat. Ehhez adja meg az alábbi értékeket:
    - **Forrás**: *Szolgáltatáscímke*.
    - **Forrás szolgáltatáscímkéje**: *Internet*
    - **Célporttartomány**: *3389*
    - **Protokoll**: *TCP*
    - **Művelet**: *Engedélyezés*
    - **Prioritás**: *200*
    - Név: *myRDPRule*
    - Leírás: *RDP engedélyezése*


### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő felső bal oldalán kattintson **hozzon létre egy erőforrást** > **számítási** > **Windows Server 2016 Datacenter** , és írja be a a virtuális gép értékeit:
    - A virtuális gép neve: *myVM1*.        
    - A rendszergazda felhasználóneve: *azureuser*.    
    - *myResourceGroupLBAZ* – **erőforráscsoport**, jelölje be **meglévő**, majd válassza ki *myResourceGroupLBAZ*.
2. Kattintson az **OK** gombra.
3. A virtuális gép méreténél válassza a **DS1_V2** lehetőséget, majd kattintson a **Kiválasztás** gombra.
4. Adja meg az alábbi értékeket a virtuális gép beállításainál:
    - *1. zóna* – a zóna, a virtuális gép helyétől.
    -  Győződjön meg róla, hogy a következő virtuális hálózat van kiválasztva: *myVNet*.
    - Győződjön meg róla, hogy a következő alhálózat van kiválasztva: *myBackendSubnet*.
    - *myNetworkSecurityGroup* – a hálózati biztonsági csoport (tűzfal) nevét.
5. A rendszerindítási diagnosztika letiltásához kattintson a **Letiltva** elemre.
6. Kattintson az **OK** gombra, majd az összefoglaló lapon ellenőrizze a beállításokat, és kattintson a **Létrehozás** gombra.
  
 ![Virtuális gép létrehozása](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Hozzon létre egy második virtuális gép neve, *vm2 virtuális gépnek* zóna 2 és a zóna 3, és a harmadik virtuális gép *myVnet* és a virtuális hálózata *myBackendSubnet* a alhálózatként és * *myNetworkSecurityGroup* , a hálózati biztonsági csoport használatával lépések 1-6.

### <a name="install-iis-on-vms"></a>Az IIS telepítése a virtuális gépeken

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd az erőforrások listájában kattintson **myVM1** található, amely a *myResourceGroupLBAZ* erőforráscsoportot.
2. Az **Áttekintés** oldalon a **Csatlakozás** gombra kattintva hozzon létre RDP-kapcsolatot a virtuális géppel.
3. Jelentkezzen be a virtuális gépbe az *azureuser* felhasználónévvel.
4. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
5. Kattintson a Kiszolgálókezelő gyors üzembe helyezés lap **szerepkörök és szolgáltatások hozzáadása**.

   ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. A **Szerepkörök és szolgáltatások hozzáadása varázslóban** használja az alábbi értékeket:
    - A **Telepítés típusának kiválasztása** lapon kattintson a **Szerepköralapú vagy szolgáltatásalapú telepítés** elemre.
    - Az a **jelölje be a célkiszolgáló** kattintson **myVM1**.
    - Az a **válassza kiszolgálói szerepkör** kattintson **webkiszolgáló (IIS)**.
    - Kövesse az utasításokat követve fejezze be a varázslót.
2. Zárja be az RDP-munkamenetet a virtuális gép - *myVM1*.
3. Ismételje meg az 1-7 telepíteni az IIS virtuális gépeken *myVM2* és *myVM3*.

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve terheléselosztási és NAT-szabályokat fog megadni.


### <a name="create-a-backend-address-pool"></a>Hátércímkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. Hozza létre a háttércímkészletet *myBackendPool* néven, amely magában foglalja a *VM1* és *VM2* virtuális gépeket.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson a **Háttérkészletek** lehetőségre, majd a **Hozzáadás** gombra.
3. A **Háttérkészlet hozzáadása** oldalon tegye a következőket:
    - Adja a következő nevet a háttérkészletnek: *myBackEndPool.
    - A **virtuális hálózati**, kattintson a legördülő menü **myVNet**
    - A **virtuális gép**, a legördülő menüben kattintson, **myVM1**.
    - A **IP-cím**, kattintson a legördülő menü myVM1 IP-címét.
4. Kattintson a **vegyen fel új háttér-erőforrást** egyes virtuális gépek hozzáadása (*myVM2* és *myVM3*) a terheléselosztó háttérkészletéből hozzáadása.
5. Kattintson a **Hozzáadás** parancsra.

    ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Ellenőrizze, hogy a terhelés terheléselosztó háttér címkészletet beállítása jeleníti meg a három virtuális gépeinek - **myVM1**, **myVM2** és **myVM3**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy *myHealthProbe* nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson az **Állapotminták** elemre, majd a **Hozzáadás** gombra.
3. Használja az alábbi értékeket az állapotminta létrehozásához:
    - Az állapotminta neve: *myHealthProbe*.
    - Protokoll típusa: **HTTP**.
    - Portszám: *80*.
    - A mintavételi kísérletek közötti **Időköz** másodpercben megadva: *15*.
    - A **Nem kifogástalan állapot küszöbértéke**, vagyis hány egymást követő meghibásodásnak kell történnie, hogy a rendszer sérültnek minősítsen egy virtuális gépet: *2*.
4. Kattintson az **OK** gombra.

   ![Mintavétel hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre egy *myLoadBalancerRuleWeb* nevű terheléselosztási szabályt, amely felügyeli a *FrontendLoadBalancer* előtér 80-as portját, és terhelés szempontjából elosztott forgalmat továbbít a szintén a 80-as portot használó, *myBackEndPool* nevű háttércímkészlethez. 

1. Kattintson az **Összes erőforrás** elemre a bal oldali menüben, majd kattintson a **myLoadBalancer** lehetőségre az erőforráslistában.
2. A **Beállítások** területen kattintson a **Terheléselosztási szabályok** elemre, majd a **Hozzáadás** gombra.
3. Konfigurálja a terheléselosztási szabályt az alábbi értékekkel:
    - A terheléselosztási szabály neve: *myHTTPRule*.
    - Protokoll típusa: **TCP**.
    - Portszám: *80*.
    - Háttérport: *80*.
    - A háttérkészlet neve: *myBackendPool*.
    - Az állapotminta neve: *myHealthProbe*.
4. Kattintson az **OK** gombra.
    
    ![Terheléselosztási szabály hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése
1. Keresse meg a Load Balancer nyilvános IP-címét az **Áttekintés** képernyőn. Kattintson a **Minden erőforrás**, majd a **myPublicIP** elemre.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

      ![IIS-webkiszolgáló](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez válassza ki a terheléselosztót tartalmazó erőforráscsoportot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
