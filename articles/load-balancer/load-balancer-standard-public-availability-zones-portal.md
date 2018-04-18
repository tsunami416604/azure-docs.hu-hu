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
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Terheléselosztás virtuális gépek rendelkezésre állási zónák az Azure portál használatával szabványos terheléselosztót között

Ez a cikk lépéseit a zóna redundáns időtúllépést eléréséhez a Load Balancer szabványos nyilvános létrehozása zóna-redundancia nélkül függőség több DNS-rekordok elérése. Egy szabványos terheléselosztó egyetlen előtérbeli IP-cím automatikusan zónaredundáns. Zóna redundáns időtúllépést alkalmaz a terheléselosztó egyetlen IP-címmel most érhető el minden virtuális gép virtuális hálózatban, amely az összes rendelkezésre állási zóna között régión belül. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére. A zóna-redundancia egy vagy több rendelkezésre állási zónák sikertelen lehet, és az elérési útja survives mindaddig egy zónát a megfelelő terület marad. 

Rendelkezésre állási zónák használatával szabványos terheléselosztással kapcsolatos további információkért lásd: [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) címen.

## <a name="create-a-public-standard-load-balancer"></a>Hozzon létre egy szabványos nyilvános terheléselosztó

Standard terheléselosztó csak egy szabványos nyilvános IP-cím támogatja. Ha a terheléselosztó létrehozása során létrehoz egy új nyilvános IP-cím, Standard Termékváltozat verzióként automatikusan konfigurálja, és azt is automatikusan zónaredundáns.

1. A képernyő felső bal oldalán kattintson **hozzon létre egy erőforrást** > **hálózati** > **terheléselosztó**.
2. Az a **hozzon létre egy terhelés-kiegyenlítő** lapon adja meg ezeket az értékeket a terheléselosztó:
    - *myLoadBalancer* – a terheléselosztó nevét.
    - **Nyilvános** – a terheléselosztó típusától.
     - *myPublicIP* – az Ön által létrehozott új nyilvános IP-címéhez. Ehhez kattintson **egy nyilvános IP-cím kiválasztása**, és kattintson a **hozzon létre új**. Név típus *myPublicIP*, termékváltozata Standard alapértelmezés szerint, és válassza ki **zónaredundáns** a **rendelkezésre állási zóna**.
    - *myResourceGroupLBAZ* – az Ön által létrehozott új erőforráscsoport nevét.
    - **westeurope** - helyét.
3. Kattintson a **létrehozása** a terheléselosztó létrehozásához.
   
    ![Load Balancer létrehozása](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Háttér-kiszolgálókat hoz létre

Ebben a szakaszban létre virtuális hálózatot, virtuális gépek létrehozása a különböző zónákhoz (1, 2 zónát, és zónát 3)-terület hozzáadása a háttérkészlet, a terheléselosztóhoz, és telepítse az IIS segítségével a zónaredundáns terhelés bala tesztelése a virtuális gépeken ncer. Ezért ha meghibásodik egy zónát, a virtuális gép ugyanabban a zónában állapotmintáihoz sikertelen lesz, és forgalom továbbra is fennáll, amelyet a többi zónákat a virtuális gépek.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
1. Kattintson a képernyő bal felső **hozzon létre egy erőforrást** > **hálózati** > **virtuális hálózati** , és ezekkel az értékekkel adja meg a virtuális hálózat:
    - *myVnet* – a virtuális hálózat nevét.
    - *myResourceGroupLBAZ* – a meglévő erőforráscsoport nevét
    - *myBackendSubnet* – az alhálózati név.
2. Kattintson a **létrehozása** a virtuális hálózat létrehozásához.

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
2. A **beállítások**, kattintson a **bejövő biztonsági szabályok**, és kattintson a **Hozzáadás**.
3. Adja meg ezeket az értékeket a bejövő biztonsági szabály nevű *myHTTPRule* egy 80-as portot a bejövő HTTP-kapcsolatoknál engedélyezéséhez:
    - *Címke szolgáltatás* – **forrás**.
    - *Internet* – **szolgáltatás címke forrás**
    - *80* – **cél porttartományok**
    - *TCP* – **protokoll**
    - *Engedélyezése* – **művelet**
    - *100* a **prioritása**
    - *myHTTPRule* neve
    - *HTTP engedélyezése* – leírás
4. Kattintson az **OK** gombra.
 
 ![Virtuális hálózat létrehozása](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Ismételje meg a 2 – 4 nevű egy másik szabály létrehozásához *myRDPRule* engedélyezi az bejövő RDP-kapcsolat 3389-es portot használja a következő értékekkel:
    - *Címke szolgáltatás* – **forrás**.
    - *Internet* – **szolgáltatás címke forrás**
    - *3389-es* – **cél porttartományok**
    - *TCP* – **protokoll**
    - *Engedélyezése* – **művelet**
    - *200* a **prioritása**
    - *myRDPRule* neve
    - *RDP engedélyezése* – leírás


### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. A képernyő felső bal oldalán kattintson **hozzon létre egy erőforrást** > **számítási** > **Windows Server 2016 Datacenter** , és írja be a a virtuális gép értékeit:
    - *myVM1* – a virtuális gép nevét.        
    - *azureuser* – a rendszergazdai felhasználónevet.    
    - *myResourceGroupLBAZ* – **erőforráscsoport**, jelölje be **meglévő**, majd válassza ki *myResourceGroupLBAZ*.
2. Kattintson az **OK** gombra.
3. Válassza ki **DS1_V2** a virtuális gépet, majd kattintson a méretét **válasszon**.
4. Adja meg ezeket az értékeket a virtuális gép beállításait:
    - *1. zóna* – a zóna, a virtuális gép helyétől.
    -  *myVNet* -győződjön meg arról, ki van jelölve, ahol a virtuális hálózatot.
    - *myBackendSubnet* -az van kiválasztva, mint annak a biztosítására.
    - *myNetworkSecurityGroup* – a hálózati biztonsági csoport (tűzfal) nevét.
5. Kattintson a **letiltott** letiltani a rendszerindítási diagnosztika.
6. Kattintson a **OK**, tekintse át a beállításokat az Összegzés lapon, és kattintson a **létrehozása**.
  
 ![Virtuális gép létrehozása](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Hozzon létre egy második virtuális gép neve, *vm2 virtuális gépnek* zóna 2 és a zóna 3, és a harmadik virtuális gép *myVnet* és a virtuális hálózata *myBackendSubnet* a alhálózatként és * *myNetworkSecurityGroup* , a hálózati biztonsági csoport használatával lépések 1-6.

### <a name="install-iis-on-vms"></a>Az IIS telepítése a virtuális gépeken

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd az erőforrások listájában kattintson **myVM1** található, amely a *myResourceGroupLBAZ* erőforráscsoportot.
2. A a **áttekintése** kattintson **Connect** távoli asztali eléréséhez a virtuális Gépet.
3. Jelentkezzen be a felhasználónevet használva a virtuális gép *azureuser*.
4. Keresse meg a kiszolgáló asztalának **Windows felügyeleti eszközök**>**Kiszolgálókezelő**.
5. Kattintson a Kiszolgálókezelő gyors üzembe helyezés lap **szerepkörök és szolgáltatások hozzáadása**.

   ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. Az a **hozzáadása szerepkörök és szolgáltatások varázsló**, a következő értékeket használja:
    - Az a **telepítés típusának kiválasztása** kattintson **szerepköralapú vagy szolgáltatásalapú telepítés**.
    - Az a **jelölje be a célkiszolgáló** kattintson **myVM1**.
    - Az a **válassza kiszolgálói szerepkör** kattintson **webkiszolgáló (IIS)**.
    - Kövesse az utasításokat követve fejezze be a varázslót.
2. Zárja be az RDP-munkamenetet a virtuális gép - *myVM1*.
3. Ismételje meg az 1-7 telepíteni az IIS virtuális gépeken *myVM2* és *myVM3*.

## <a name="create-load-balancer-resources"></a>Hozzon létre a load balancer erőforrások

Ebben a szakaszban egy háttér címkészletet, és egy állapotmintáihoz terheléselosztási beállításokat konfigurálja, és adja meg a terheléselosztó és a NAT-szabályok.


### <a name="create-a-backend-address-pool"></a>Háttér-címkészlet létrehozása

Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét. A háttér-címkészlet létrehozása *myBackendPool* felvenni *VM1* és *vm2 virtuális gépnek*.

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myLoadBalancer** erőforrások listából.
2. A **beállítások**, kattintson a **háttérkészletek**, kattintson a **Hozzáadás**.
3. Az a **háttérkészlet hozzáadásával** lapján tegye a következőket:
    - Név mezőbe írja be * myBackEndPool a háttérkészlet néven.
    - A **virtuális hálózati**, kattintson a legördülő menü **myVNet**
    - A **virtuális gép**, a legördülő menüben kattintson, **myVM1**.
    - A **IP-cím**, kattintson a legördülő menü myVM1 IP-címét.
4. Kattintson a **vegyen fel új háttér-erőforrást** egyes virtuális gépek hozzáadása (*myVM2* és *myVM3*) a terheléselosztó háttérkészletéből hozzáadása.
5. Kattintson a **Hozzáadás** parancsra.

    ![Hozzáadás a háttércímkészlethez – ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Ellenőrizze, hogy a terhelés terheléselosztó háttér címkészletet beállítása jeleníti meg a három virtuális gépeinek - **myVM1**, **myVM2** és **myVM3**.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Hozzon létre egy állapotmintáihoz *myHealthProbe* a virtuális gépek állapotának figyelését.

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myLoadBalancer** erőforrások listából.
2. A **beállítások**, kattintson a **állapot-mintavételi csomagjai**, majd kattintson a **hozzáadása**.
3. A állapotmintáihoz létrehozásához ezeket az értékeket használja:
    - *myHealthProbe* – a állapotmintáihoz nevét.
    - **HTTP** – a protokolltípus.
    - *80* - portszám.
    - *15* - számú **időköz** mintavételi kísérletek közötti másodpercben.
    - *2* - számú **sérült küszöbérték** vagy egymást követő sikertelen mintavételek, amely kell történnie ahhoz a virtuális gép nem megfelelő állapotúnak számít.
4. Kattintson az **OK** gombra.

   ![Mintavétel hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Hozzon létre olyan terheléselosztó szabályhoz *myLoadBalancerRuleWeb* a 80-as portot a elülső rétegben *FrontendLoadBalancer* és az elosztott terhelésű hálózati forgalom küldése a háttér címkészletet *myBackEndPool* is a 80-as portot használja. 

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myLoadBalancer** erőforrások listából.
2. A **beállítások**, kattintson a **terheléselosztási szabályok**, majd kattintson a **hozzáadása**.
3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálása:
    - *myHTTPRule* – a terheléselosztási szabály nevét.
    - **TCP** – a protokolltípus.
    - *80* - portszám.
    - *80* – a háttér-porthoz.
    - *myBackendPool* - háttérkészlet nevét.
    - *myHealthProbe* – a állapotmintáihoz nevét.
4. Kattintson az **OK** gombra.
    
    ![Terheléselosztási szabály hozzáadása](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>A load balancer tesztelése
1. A nyilvános IP-cím keresése a terheléselosztó a a **áttekintése** képernyő. Kattintson a **összes erőforrás** majd **myPublicIP**.

2. Másolja a nyilvános IP-címet, és illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett lapon jelenik meg a böngészőben.

      ![Az IIS-webkiszolgáló](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje a csoportot, és töltse be a terheléselosztó és minden kapcsolódó erőforrások. Ehhez az szükséges, válassza ki, amely tartalmazza a terheléselosztóhoz, majd kattintson az erőforráscsoport **törlése**.

## <a name="next-steps"></a>További lépések

További információ [szabványos terheléselosztó](load-balancer-standard-overview.md).
