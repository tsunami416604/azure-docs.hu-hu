---
title: Bejövő forgalom szűrése az Azure Firewall DNAT-tal az Azure Portalon
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewall DNAT-ot az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/28/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e37d5b050c5ca957b59c1e0a60c88171c1fc4a23
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582241"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Oktatóanyag: Bejövő forgalom szűrése az Azure Firewall DNAT-tal az Azure Portalon

Az Azure Firewall DNAT (Destination Network Address Translation, célhálózati címfordítás) funkciójának konfigurálásával lefordíthatja és szűrheti az alhálózatokra bejövő forgalmat. DNAT konfigurálásakor a NAT-szabály gyűjtemény céljának beállítása **Dnat**. A NAT-szabálygyűjtemény minden szabálya használható arra, hogy lefordítsa a tűzfal nyilvános IP-címét és portját egy magánhálózati IP-címre és portra. A DNAT-szabályok implicit módon hozzáadnak egy kapcsolódó hálózati szabályt a lefordított adatforgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * DNAT-szabály konfigurálása
> * A tűzfal tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ebben az oktatóanyagban két társított virtuális hálózatot hozunk létre:

- **VN-Hub** (központi virtuális hálózat) – ezen a virtuális hálózaton található a tűzfal.
- **VN-Spoke** (küllő virtuális hálózat) – ezen a virtuális hálózaton található a számítási feladat kiszolgálója.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.
2. Az Azure Portal kezdőlapján kattintson az **Erőforráscsoportok** elemre, majd a **Hozzáadás** elemre.
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **RG-DNAT-Test**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Create** (Létrehozás) gombra.

## <a name="set-up-the-network-environment"></a>A hálózati környezet beállítása

Először hozza létre a virtuális hálózatokat, és társítsa őket.

### <a name="create-the-hub-vnet"></a>A központi virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Hálózat** területen kattintson a **Virtuális hálózatok** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Név** mezőbe írja be a következőt: **VN-Hub**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**.

     Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
     > [!NOTE]
     > A AzureFirewallSubnet alhálózat minimális mérete/26-os.
10. A **Címtartomány** mezőbe írja be a következőt: **10.0.1.0/24**.
11. Használja a többi alapértelmezett beállítást, és kattintson a **Létrehozás** elemre.

### <a name="create-a-spoke-vnet"></a>Küllő virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Hálózat** területen kattintson a **Virtuális hálózatok** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Név** mezőbe írja be a következőt: **VN-Spoke**.
5. A **Címtér** mezőbe írja be a következőt: **192.168.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **SN-Workload**.

    Ezen az alhálózaton lesz a kiszolgáló.
10. A **Címtartomány** mezőbe írja be a következőt: **192.168.1.0/24**.
11. Használja a többi alapértelmezett beállítást, és kattintson a **Létrehozás** elemre.

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

Most társítsa a két virtuális hálózatot.

#### <a name="hub-to-spoke"></a>A központot a küllővel

1. Kattintson a **VN-Hub** virtuális hálózatra.
2. A **Beállítások** területen kattintson a **Társviszonyok** lehetőségre.
3. Kattintson a **Hozzáadás** parancsra.
4. Adja meg a **Peer-HubSpoke** nevet.
5. A virtuális hálózatnál válassza a **VN-Spoke** lehetőséget.
6. Kattintson az **OK** gombra.

#### <a name="spoke-to-hub"></a>A küllőt a központtal

1. Kattintson a **VN-Spoke** virtuális hálózatra.
2. A **Beállítások** területen kattintson a **Társviszonyok** lehetőségre.
3. Kattintson a **Hozzáadás** parancsra.
4. Adja meg a **Peer-SpokeHub** nevet.
5. A virtuális hálózatnál válassza a **VN-Hub** lehetőséget.
6. Kattintson a **Továbbított forgalom engedélyezése** elemre.
7. Kattintson az **OK** gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a számítási feladat futtatásához, és helyezze el az **SN-Workload** alhálózaton.

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Compute** területen kattintson a **Virtuális gépek** elemre.
3. Kattintson a **Hozzáadás** elemre, majd a **Windows Server**, a **Windows Server 2016 Datacenter**, végül pedig a **Létrehozás** elemre.

**Alapvető beállítások**

1. A **Név** mezőbe írja be a következőt: **Srv-Workload**.
5. Adjon meg egy felhasználónevet és jelszót.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Kattintson az **OK** gombra.

**Méret**

1. Válasszon egy Windows Servert futtató teszt virtuális géphez megfelelő méretet. Például **B2ms** (8 GB RAM, 16 GB tárterület).
2. Kattintson a **Kiválasztás** gombra.

**Beállítások**

1. A **Hálózat** területen a **Virtuális hálózat** mezőnél válassza a **VN-Spoke** lehetőséget.
2. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
3. Kattintson a **Nyilvános IP-cím**, majd a **Nincs** lehetőségre.
4. A **Nyilvános bejövő portok kiválasztása** mezőben válassza a **Nincsenek nyilvános bejövő portok** lehetőséget. 
2. Hagyja meg az egyéb alapértelmezett beállításokat, és kattintson az **OK** gombra.

**összegzés**

Tekintse át az összefoglalást, majd kattintson a **Létrehozás** gombra. Ez eltarthat pár percig.

Az üzembe helyezés befejeztével jegyezze fel a virtuális gép magánhálózati IP-címét. Ezt később a tűzfal konfigurálása során használjuk majd. Kattintson a virtuális gép nevére, majd a **Beállítások** területen kattintson a **Hálózat** lehetőségre a magánhálózati IP-cím lekéréséhez.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

1. A portál kezdőlapján kattintson az **Erőforrás létrehozása** gombra.
2. Kattintson a **Hálózat**, majd a **Kiemelt**, végül az **Összes megtekintése** elemre.
3. Kattintson a **Tűzfal**, majd a **Létrehozás** elemre. 
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Name (Név)     |FW-DNAT-test|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**Meglévő használata**: RG-DNAT-Test |
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: VN-Hub|
   |Nyilvános IP-cím     |**Új létrehozása**. A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Kattintson az **Áttekintés + létrehozás** elemre.
6. Tekintse át az összefoglalást, majd kattintson a **Létrehozás** elemre a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Ha az üzembe helyezés elkészült, nyissa meg az **RG-DNAT-Test** erőforráscsoportot, majd kattintson az **FW-DNAT-test** tűzfalra.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Az **SN-Workload** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. Az Azure Portal kezdőlapján kattintson a **Minden szolgáltatás** elemre.
2. A **Hálózat** területen kattintson az **Útválasztási táblázatok** elemre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Név** mezőbe írja be a következőt: **RT-FWroute**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Create** (Létrehozás) gombra.
9. Kattintson a **Frissítés** elemre, majd az **RT-FWroute** útválasztási táblázatra.
10. Kattintson az **Alhálózatok**, majd a **Társítás** elemre.
11. Kattintson a **Virtuális hálózat** elemre, majd válassza a **VN-Spoke** elemet.
12. Az **Alhálózat** mezőben válassza az **SN-Workload** elemet.
13. Kattintson az **OK** gombra.
14. Kattintson az **Útvonalak**, majd a **Hozzáadás** elemre.
15. Az **Útvonal neve** mezőbe írja be következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Kattintson az **OK** gombra.

## <a name="configure-a-nat-rule"></a>Egy NAT-szabály konfigurálása

1. Nyissa meg az **RG-DNAT-Test** erőforráscsoportot, majd kattintson az **FW-DNAT-test** tűzfalra. 
2. Az **FW-DNAT-test** oldal **Beállítások** területén kattintson a **Szabályok** elemre. 
3. Kattintson a **hozzáadása NAT-szabályok gyűjteményéhez**. 
4. A **Név** mezőbe írja be a következőt: **RC-DNAT-01**. 
5. A **Prioritás** mezőbe írja be a következőt: **200**. 
6. A **Szabályok** területen a **Név** mezőbe írja be a következőt: **RL-01**.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: *. 
9. A **Célcímek** mezőbe írja be a tűzfal nyilvános IP-címét. 
10. A **Célportok** mezőbe írja be a következőt: **3389**. 
11. A **Lefordított cím** mezőbe írja be az Srv-Workload virtuális gép magánhálózati IP-címét. 
12. A **Lefordított port** mezőben adja meg a **3389** értéket. 
13. Kattintson a **Hozzáadás** parancsra. 

## <a name="test-the-firewall"></a>A tűzfal tesztelése

1. Csatlakoztasson egy távoli asztalt a tűzfal nyilvános IP-címére. Ekkor az **Srv-Workload** virtuális gépre kell csatlakoznia.
2. Zárja be a távoli asztalt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje az **RG-DNAT-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * DNAT-szabály konfigurálása
> * A tűzfal tesztelése

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
