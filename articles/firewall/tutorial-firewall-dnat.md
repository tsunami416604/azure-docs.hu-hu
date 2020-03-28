---
title: Bejövő internetes forgalom szűrése az Azure Firewall DNST használatával a portál használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewall DNAT-ot az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/02/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7220e48c6103352108bdb89e107bb862ee194040
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251486"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Oktatóanyag: Szűrje a bejövő internetes forgalmat az Azure Firewall DNST használatával az Azure Portalhasználatával

Konfigurálhatja az Azure Firewall Destination Network Address Translation (DNAT) fordítása és szűrése bejövő internetes forgalmat az alhálózatok. A DNST konfigurálásakor a NAT-szabálygyűjtési művelet **dnst**lesz beállítva. A NAT-szabálygyűjtemény minden szabálya használható arra, hogy lefordítsa a tűzfal nyilvános IP-címét és portját egy magánhálózati IP-címre és portra. A DNAT-szabályok implicit módon hozzáadnak egy kapcsolódó hálózati szabályt a lefordított adatforgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * DNAT-szabály konfigurálása
> * A tűzfal tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Ebben az oktatóanyagban két társított virtuális hálózatot hozunk létre:

- **VN-Hub** (központi virtuális hálózat) – ezen a virtuális hálózaton található a tűzfal.
- **VN-Spoke** (küllő virtuális hálózat) – ezen a virtuális hálózaton található a számítási feladat kiszolgálója.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Az Azure Portal kezdőlapján válassza az **Erőforráscsoportok**lehetőséget, majd válassza **a Hozzáadás**lehetőséget.
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **RG-DNAT-Test**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Létrehozás** gombra.

## <a name="set-up-the-network-environment"></a>A hálózati környezet beállítása

Először hozza létre a virtuális hálózatokat, és társítsa őket.

### <a name="create-the-hub-vnet"></a>A központi virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza a **Minden szolgáltatás**lehetőséget.
2. A **Hálózat csoportban**válassza a **Virtuális hálózatok**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **VN-Hub**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**.

     Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
     > [!NOTE]
     > Az AzureFirewallSubnet alhálózat mérete /26. Az alhálózat méretéről az [Azure Firewall gyIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)című témakörben talál további információt.

10. A **Címtartomány**mezőbe írja be a **10.0.1.0/26 mezőt.**
11. Használja a többi alapértelmezett beállítást, majd válassza a **Létrehozás gombot.**

### <a name="create-a-spoke-vnet"></a>Küllő virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza a **Minden szolgáltatás**lehetőséget.
2. A **Hálózat csoportban**válassza a **Virtuális hálózatok**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **VN-Spoke**.
5. A **Címtér** mezőbe írja be a következőt: **192.168.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **SN-Workload**.

    Ezen az alhálózaton lesz a kiszolgáló.
10. A **Címtartomány** mezőbe írja be a következőt: **192.168.1.0/24**.
11. Használja a többi alapértelmezett beállítást, majd válassza a **Létrehozás gombot.**

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

Most társítsa a két virtuális hálózatot.

1. Válassza ki a **VN-Hub** virtuális hálózatot.
2. A **Beállítások csoportban**válassza **a Társviszony-létesítések**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja be a **Peering nevét a VN-Hubról a VN-Küllős névre.** **Peer-HubSpoke**
5. A virtuális hálózatnál válassza a **VN-Spoke** lehetőséget.
6. Írja be **a Peer-SpokeHub** **nevet a VN-Küllős a VN-Hub név.**
7. A **VN-Küllőről a VN-Hubra továbbított forgalom engedélyezése a VN-Hub** elemre **van kiválasztva.**
8. Válassza **az OK gombot.**

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a számítási feladat futtatásához, és helyezze el az **SN-Workload** alhálózaton.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.
2. A **Népszerű**csoportban válassza a **Windows Server 2016 Datacenter**lehetőséget.

**Alapvető beállítások**

1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
1. A **virtuális gép nevéhez**írja be az **Srv-Workload (Srv-Workload ) mezőbe.**
1. A **Régió**területen válassza ki ugyanazt a helyet, amelyet korábban használt.
1. Adjon meg egy felhasználónevet és jelszót.
1. Válassza a **Tovább: Lemezek**lehetőséget.

**Lemezek**
1. Válassza a **Tovább: Hálózat lehetőséget.**

**Hálózat**

1. **Virtuális hálózat esetén**válassza a **VN-Küllős**lehetőséget.
2. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
3. **A Nyilvános IP-cím** esetén válassza a **Nincs**lehetőséget.
4. **Nyilvános bejövő portok**esetén válassza a **Nincs**lehetőséget. 
2. Hagyja meg a többi alapértelmezett beállítást, és válassza **a Tovább : Kezelés**lehetőséget.

**Felügyelet**

1. A **Rendszerindításdia**esetén válassza **a Ki**lehetőséget.
1. Válassza **a Véleményezés + Létrehozás lehetőséget.**

**Véleményezés + Létrehozás**

Tekintse át az összegzést, és válassza a **Létrehozás gombot.** Ez eltarthat pár percig.

Az üzembe helyezés befejeztével jegyezze fel a virtuális gép magánhálózati IP-címét. Ezt később a tűzfal konfigurálása során használjuk majd. Válassza ki a virtuális gép nevét, és a **Beállítások csoportban**válassza a **Hálózat lehetőséget** a privát IP-cím megkereséséhez.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

1. A portál kezdőlapján válassza az **Erőforrás létrehozása**lehetőséget.
2. Válassza **a Hálózat lehetőséget,** majd a **Kiemelt**után a Mind **et látnoki**lehetőséget.
3. Válassza **a Tűzfal**lehetőséget, majd a **Létrehozás lehetőséget.** 
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Név     |FW-DNAT-test|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**Meglévő használata**: RG-DNAT-Test |
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: VN-Hub|
   |Nyilvános IP-cím     |**Hozzon létre újat.** A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd a **Létrehozás gombra** a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. A telepítés befejezése után nyissa meg az **RG-DNAT-Test** erőforráscsoportot, és válassza ki az **FW-DNAT-teszt** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Az **SN-Workload** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. Az Azure Portal kezdőlapján válassza a **Minden szolgáltatás**lehetőséget.
2. A **Hálózat csoportban**válassza **az Útvonaltáblák lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Név** mezőbe írja be a következőt: **RT-FWroute**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Létrehozás** gombra.
9. Válassza **a Frissítés**lehetőséget, majd válassza az **RT-FWroute útvonaltáblát.**
10. Válassza **az Alhálózatok**lehetőséget, majd a **Társítás**lehetőséget.
11. Válassza **a Virtuális hálózat**lehetőséget, majd a **VN-küllős**lehetőséget.
12. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
13. Válassza **az OK gombot.**
14. Válassza **az Útvonalak**lehetőséget, majd a **Hozzáadás**lehetőséget.
15. Az **Útvonal neve** mezőbe írja be következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Válassza **az OK gombot.**

## <a name="configure-a-nat-rule"></a>NAT-szabály konfigurálása

1. Nyissa meg az **RG-DNAT-tesztet,** és válassza ki az **FW-DNAT-teszt** tűzfalat. 
2. Az **FW-DNAT-teszt** lap **Beállítások területén**válassza a **Szabályok**lehetőséget. 
3. Válassza **a NAT-szabálygyűjtemény hozzáadása lehetőséget.** 
4. A **Név** mezőbe írja be a következőt: **RC-DNAT-01**. 
5. A **Prioritás** mezőbe írja be a következőt: **200**. 
6. A **Szabályok** területen a **Név** mezőbe írja be a következőt: **RL-01**.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: *. 
9. A **Célcímek** mezőbe írja be a tűzfal nyilvános IP-címét. 
10. A **Célportok** mezőbe írja be a következőt: **3389**. 
11. A **Lefordított cím** mezőbe írja be az Srv-Workload virtuális gép magánhálózati IP-címét. 
12. A **Lefordított port** mezőben adja meg a **3389** értéket. 
13. Válassza a **Hozzáadás** lehetőséget. 

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
