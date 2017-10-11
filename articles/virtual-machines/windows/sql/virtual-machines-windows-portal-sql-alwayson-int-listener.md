---
title: "Hozzon létre egy SQL Server rendelkezésre állási csoport figyelőjének az Azure virtuális gépeken |} Microsoft Docs"
description: "Részletes útmutatást ad a figyelő egy Always On rendelkezésre állási csoport létrehozása az SQL Server Azure virtuális gépeken"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/01/2017
ms.author: mikeray
ms.openlocfilehash: 09fed7e785708d4afe64905de973becc188181d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>A terheléselosztó egy Always On rendelkezésre állási csoport konfigurálása az Azure-ban
Ez a cikk azt ismerteti, hogyan egy terheléselosztót egy SQL Server Always On rendelkezésre állási csoport létrehozása az Azure Resource Manager rendszert futtató Azure virtuális gépekben. Rendelkezésre állási csoport szükséges egy adott terheléselosztóhoz, ha az SQL Server-példányok áll az Azure virtuális gépeken. A terheléselosztó a rendelkezésre állási csoport figyelője az IP-cím tárolja. Rendelkezésre állási csoport több régióba is, ha minden egyes régió egy terhelés-kiegyenlítő van szüksége.

Ez a feladat befejezéséhez szükség lehet a Resource Manager rendszert futtató Azure virtuális gépeken telepített SQL Server rendelkezésre állási csoport. Mindkét SQL Server virtuális gépek ugyanabban a rendelkezésre állási csoportba kell tartoznia. Használhatja a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) automatikus létrehozásához a rendelkezésre állási csoportot az erőforrás-kezelőben. Ez a sablon automatikusan létrehoz egy belső terheléselosztót. 

Ha kívánja, akkor [manuálisan konfigurálnia a rendelkezésre állási csoport](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Ez a cikk megköveteli, hogy a rendelkezésre állási csoportok már be van állítva.  

Kapcsolódó témakörök az alábbiak:

* [Always On rendelkezésre állási csoportok konfigurálása az Azure virtuális gép (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Egy VNet – VNet-kapcsolat beállítása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Érdekében ez a cikk keresztül, hozzon létre, és a terheléselosztó konfigurálása az Azure portálon. A folyamat befejezése után, akkor a fürt használja az IP-címet a terheléselosztóról a rendelkezésre állási csoport figyelőjének konfigurálása.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Hozza létre és konfigurálja a terheléselosztó az Azure-portálon
Ez a tevékenység része tegye a következőket:

1. Az Azure portálon a terheléselosztó létrehozása, és konfigurálja az IP-cím.
2. A háttér-készlet beállítása.
3. A mintavétel létrehozása. 
4. A terheléselosztási szabályok beállítása.

> [!NOTE]
> Ha az SQL Server-példányok több erőforráscsoportok és régiókban, hajtsa végre az kétszer, egyszer minden erőforráscsoportban.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1. lépés: A terheléselosztó létrehozása, és az IP-cím konfigurálása
Először hozza létre a terheléselosztó hasonló adataival. 

1. Nyissa meg az SQL Server virtuális gépeket tartalmazó erőforráscsoportot az Azure-portálon. 

2. Kattintson az erőforráscsoportot, **Hozzáadás**.

3. Keresse meg **terheléselosztó** majd, a keresési eredmények **terheléselosztó**, által közzétett **Microsoft**.

4. Az a **terheléselosztó** panelen kattintson a **létrehozása**.

5. Az a **létrehozás terheléselosztó** párbeszédpanel az alábbiak szerint adja meg a terheléselosztó:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A load balancer képviselő szöveges nevét. Például **sqlLB**. |
   | **Típus** |**Belső**: a legtöbb megvalósítások belső terheléselosztót, lehetővé teszi a rendelkezésre állási csoporthoz való csatlakozáshoz a virtuális hálózaton belül alkalmazások használja.  </br> **Külső**: lehetővé teszi olyan alkalmazások nyilvános internetkapcsolaton keresztül a rendelkezésre állási csoporthoz való csatlakozáshoz. |
   | **Virtuális hálózat** |Válassza ki a virtuális hálózat, amely az SQL Server intances szerepelnek. |
   | **Alhálózat** |Válassza ki az alhálózatot, amely az SQL Server-példányok szerepelnek. |
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Magánhálózati IP-cím** |Adja meg az alhálózat elérhető IP-címeit. Az IP-címet használja, a figyelő a fürt létrehozásakor. Egy PowerShell-parancsfájlt, a cikk későbbi részében használni ezt a címet a `$ILBIP` változó. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező jelenhet meg. Válassza ki az előfizetést, amelyet hozzá szeretne rendelni ehhez az erőforráshoz. Akkor általában a rendelkezésre állási csoporthoz tartozó összes erőforrás tárolóként ugyanazt az előfizetést. |
   | **Erőforráscsoport** |Válassza ki az erőforráscsoportot, amelyek az SQL Server-példányokat. |
   | **Hely** |Válassza ki az Azure-beli hely, amely az SQL Server-példányok szerepelnek. |

6. Kattintson a **Create** (Létrehozás) gombra. 

A load balancer az Azure létrehoz. A load balancer egy adott hálózati, alhálózati, erőforráscsoportot és helyet tartozik. Azure a feladat befejezése után ellenőrizze a terheléselosztási beállításokat az Azure-ban. 

### <a name="step-2-configure-the-back-end-pool"></a>2. lépés: A háttér-készlet konfigurálása
Azure meghívja a háttér-címkészlet *háttérkészlet*. Ebben az esetben a háttér-készlet a rendelkezésre állási csoport két SQL Server-példánya a címeket. 

1. Az erőforráscsoport kattintson a terheléselosztóhoz, amely létrehozta. 

2. A **beállítások**, kattintson a **háttérkészletek**.

3. A **háttérkészletek**, kattintson a **Hozzáadás** egy háttér-címkészlet létrehozása. 

4. A **háttérkészlet hozzáadása**a **neve**, adjon meg egy nevet a háttér-készlet.

5. A **virtuális gépek**, kattintson a **adja hozzá a virtuális gépek**. 

6. A **válassza ki a virtuális gépek**, kattintson a **rendelkezésre állási csoport kiválasztása**, és adja meg a rendelkezésre állási csoport, hogy az SQL Server virtuális gépek tartozik.

7. A rendelkezésre állási csoport kiválasztása után kattintson **válassza ki a virtuális gépek**, jelölje ki a két virtuális gépeket üzemeltető SQL Server-példányok a rendelkezésre állási csoport, és kattintson a **válasszon**. 

8. Kattintson a **OK** bezárásához a paneleket az **válassza ki a virtuális gépek**, és **háttérkészlet hozzáadása**. 

Azure frissíti a háttér-címkészlet beállításait. A rendelkezésre állási csoport már két SQL Server-példányokat készletét.

### <a name="step-3-create-a-probe"></a>3. lépés: A mintavétel létrehozása
A mintavétel határozza meg, hogyan Azure ellenőrzi, amelyek az SQL Server-példányok éppen birtokolja a rendelkezésre állási csoport figyelőjét. Azure-vizsgálat a szolgáltatás az IP-cím a mintavétel létrehozásakor meghatározó port alapján.

1. A terheléselosztóhoz **beállítások** panelen kattintson a **állapot-mintavételi csomagjai**. 

2. Az a **állapot-mintavételi csomagjai** panelen kattintson a **Hozzáadás**.

3. A mintavétel konfigurálása a **Hozzáadás mintavételi** panelen. A mintavétel konfigurálása a következő értékeket használja:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A mintavétel képviselő szöveges nevét. Például **SQLAlwaysOnEndPointProbe**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |A rendelkezésre álló portot is használhat. Például *59999*. |
   | **Időköz** |*5* |
   | **Sérült küszöbérték** |*2* |

4.  Kattintson az **OK** gombra. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port meg nyitva a tűzfalon, mind az SQL Server-példányok. Mindkét esetben szükséges egy bejövő forgalomra vonatkozó szabály, amelyekkel a TCP-portot. További információkért lásd: [hozzáadása vagy szerkesztése tűzfalszabály](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure hoz létre a mintavételi, és azt teszteléséhez melyik SQL Server-példány van a rendelkezésre állási csoport figyelőjének használja.

### <a name="step-4-set-the-load-balancing-rules"></a>4. lépés: A terheléselosztási szabályok beállítása
A terheléselosztási szabályok konfigurálása, hogy a terheléselosztó hogyan irányítja a forgalmat az SQL Server-példányokat. A terheléselosztóhoz engedélyezi a közvetlen kiszolgálói válasz, mert a két SQL Server-példányok csak az egyik a rendelkezésre állási csoport figyelőjének erőforrás tulajdonosa egyszerre.

1. A terheléselosztóhoz **beállítások** panelen kattintson a **terheléselosztási szabályok**. 

2. Az a **terheléselosztási szabályok** panelen kattintson a **Hozzáadás**.

3. Az a **Hozzáadás terheléselosztási szabályok** panelen a terheléselosztási szabály konfigurálása. A következő beállításokkal: 

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A terheléselosztási szabályok képviselő szöveges nevét. Például **SQLAlwaysOnEndPointListener**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |*1433* |
   | **Háttér-Port** |*1433*. Rendszer figyelmen kívül hagyja ezt az értéket, mert ez a szabály **fix IP-Címek (közvetlen kiszolgálói válasz)**. |
   | **Hálózatfigyelő** |A mintavétel létrehozott nevét használni a terheléselosztóhoz. |
   | **Munkamenet megőrzését** |**Egyik sem** |
   | **Üresjárati időkorlátja (perc)** |*4* |
   | **Lebegőpontos IP (közvetlen kiszolgálói válasz)** |**Engedélyezve** |

   > [!NOTE]
   > Lehetséges, hogy a megadott beállítások panel le kell görgetnie.
   > 

4. Kattintson az **OK** gombra. 
5. Azure terheléselosztási szabályt konfigurálja. A terheléselosztó van konfigurálva irányíthatja a forgalmat a rendelkezésre állási csoport figyelőjének az SQL Server-példány számára. 

Ezen a ponton az erőforráscsoport rendelkezik olyan terheléselosztóhoz, amely mindkét SQL Server-gépek csatlakozik. A terheléselosztó is tartalmazza az SQL Server Always On rendelkezésre állási csoport figyelőjét, IP-címet, hogy vagy a gép válaszolhassanak a rendelkezésre állási csoportok kérelmekre.

> [!NOTE]
> Ha az SQL Server-példány két külön régióban, ismételje meg a más régióban. Minden egyes régió egy terheléselosztót igényel. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>A load balancer IP-címet a fürt konfigurálása
A következő lépés, hogy adja meg a figyelő a fürtön, és a figyelő online állapotba. Tegye a következőket: 

1. A rendelkezésre állási csoport figyelőjének létrehozásához a feladatátvevő fürtön. 

2. A figyelő online állapotba.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. lépés: A rendelkezésre állási csoport figyelőjének létrehozása a feladatátvevő fürt
Ebben a lépésben kézzel létrehozhat a rendelkezésre állási csoport figyelőjének a Feladatátvevőfürt-kezelő és az SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>A figyelő konfigurációjának ellenőrzése

Ha a fürt erőforrásait, és a függőségek megfelelően vannak konfigurálva, a figyelő megtekintheti az SQL Server Management Studio kell lennie. A figyelő port megadásához tegye a következőket:

1. Indítsa el az SQL Server Management Studio eszközt, és csatlakozzon az elsődleges másodpéldány.

2. Nyissa meg a **AlwaysOn magas rendelkezésre állás** > **rendelkezésre állási csoportok** > **rendelkezésre állási csoport figyelői**.  
    Meg kell jelennie a figyelő nevét, amelyet a Feladatátvevőfürt-kezelőt hozott létre. 

3. Kattintson a jobb gombbal a figyelő nevét, és kattintson **tulajdonságok**.

4. Az a **Port** mezőben adja meg a portszámot az elérhetőségi csoport figyelője az a korábban használt $EndpointPort használatával (az alapértelmezett 1433-as volt az), és kattintson a **OK**.

Most már rendelkezik egy rendelkezésre állási csoport erőforrás-kezelő módban futó Azure virtuális gépeken. 

## <a name="test-the-connection-to-the-listener"></a>Tesztelje a kapcsolatot a figyelő
Tesztelje a kapcsolatot a következő módon:

1. Az SQL Server-példányt, amely ugyanabban a virtuális hálózatban, de nem tulajdonosa a replika RDP. A kiszolgáló lehet a más SQL Server-példány a fürtben.

2. Használjon **sqlcmd** segédprogram létrehozott kapcsolat ellenőrzéséhez. Például az alábbi parancsfájlt hoz létre egy **sqlcmd** kapcsolatot az elsődleges másodpéldány, a figyelő a Windows-hitelesítés használatával:
   
        sqlcmd -S <listenerName> -E

Az SQLCMD kapcsolat automatikusan csatlakozik, amelyen az elsődleges replika SQL Server-példány. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Egy IP-cím, egy további rendelkezésre állási csoport létrehozása

Egyes rendelkezésre állási csoport egy külön figyelő használja. Minden egyes figyelő saját IP-címmel rendelkezik. Az azonos terheléselosztóhoz segítségével az IP-cím tárolásához további figyelők. Miután létrehozott egy rendelkezésre állási csoporthoz, az IP-cím hozzáadása a terheléselosztóhoz, és adja meg a figyelő.

IP-cím hozzáadása a terheléselosztó és az Azure portál, tegye a következőket:

1. Az Azure portálon nyissa meg a terheléselosztó tartalmazó erőforráscsoportot, és kattintson a terheléselosztó. 

2. A **beállítások**, kattintson a **előtér-IP-címkészlet**, és kattintson a **Hozzáadás**. 

3. A **előtérbeli IP-cím hozzáadása**, rendelje hozzá az előtér nevét. 

4. Ellenőrizze, hogy a **virtuális hálózati** és a **alhálózati** ugyanazok, mint az SQL Server-példányokat.

5. Az IP-címének beállítása a figyelőhöz. 
   
   >[!TIP]
   >Az IP-cím beállítása statikus, és adjon meg egy címet, amely jelenleg nincs használatban az alhálózaton. Másik lehetőségként az IP-címének beállítása a dinamikus, és mentse az új előtér-IP-címkészlet. Ha így tesz, az Azure-portálon automatikusan hozzárendeli az elérhető IP-címet a készletbe. Ezután nyissa meg újra az előtér-IP-címkészletet, és módosítsa a hozzárendelési statikus. 

6. Mentse az IP-címet a figyelőhöz. 

7. Adja hozzá a állapotmintáihoz a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Name (Név)** |A mintavétel azonosító nevet.
   |**Protocol (Protokoll)** |TCP
   |**Port** |Egy nem használt TCP-port, amelyen az összes virtuális gép rendelkezésre kell állnia. Semmilyen más célra nem használható. Két figyelői nem használhatja ugyanazt a mintavételi portot. 
   |**Időköz** |A mintavételi kísérletek közötti időtartam. Használja az alapértelmezett (5).
   |**Sérült küszöbérték** |Egymást követő küszöbértékeket, amelyek a virtuális gép nem kell száma nem megfelelő állapotúnak számít.

8. Kattintson a **OK** a mintavétel mentése. 

9. Terheléselosztási szabály létrehozása. Kattintson a **terheléselosztási szabályok**, és kattintson a **Hozzáadás**.

10. Adja meg az új terheléselosztási szabályt a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Name (Név)** |A terheléselosztási szabályt azonosító nevet. 
   |**Előtérbeli IP-cím** |Válassza ki a létrehozott IP-cím. 
   |**Protocol (Protokoll)** |TCP
   |**Port** |Az SQL Server-példányok által használt portot használja. Egy alapértelmezett példány 1433-as portot használja, kivéve, ha módosította az. 
   |**Háttér-port** |Ugyanazt az értéket, mint a **Port**.
   |**Háttérkészlet** |A virtuális gépek az SQL Server-példányokat tartalmazó készlet. 
   |**Állapotmintáihoz** |Válassza ki a létrehozott mintavétel.
   |**Munkamenet megőrzését** |None
   |**Üresjárati időkorlátja (perc)** |Alapértelmezett (4)
   |**Lebegőpontos IP (közvetlen kiszolgálói válasz)** | Engedélyezve

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Az új IP-címet használja a rendelkezésre állási csoport konfigurálása

A fürt konfigurálásának befejezéséhez, ismételje meg a lépéseket, amelyek az első rendelkezésre állási csoport elküldésekor követni. Ez azt jelenti, hogy konfigurálja a [az új IP-cím fürt](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Miután hozzáadta az IP-címet a figyelőhöz, további rendelkezésre állási csoport konfigurálása a következő módon: 

1. Győződjön meg arról, hogy mindkét SQL Server virtuális gépen nyissa meg a mintavételi portot az új IP-címhez. 

2. [A kezelő hozzáadása az ügyfél-hozzáférési pont](#addcap).

3. [Az IP-erőforrás a rendelkezésre állási csoport konfigurálása](#congroup).

   >[!IMPORTANT]
   >Az IP-cím létrehozásakor használja a terheléselosztó hozzáadott IP-címét.  

4. [Az SQL Server rendelkezésre állási csoport erőforrása függővé az ügyfél-hozzáférési pontokon](#dependencyGroup).

5. [Ellenőrizze az ügyfél hozzáférési pont erőforrás az IP-címtől függő](#listname).
 
6. [A fürt paraméterek beállítása a PowerShell](#setparam).

Miután konfigurálta a rendelkezésre állási csoportot az új IP-címet használja, a figyelő a kapcsolat beállítása. 

## <a name="next-steps"></a>Következő lépések

- [Különböző régiókban Azure virtuális gépeken futó SQL Server Always On rendelkezésre állási csoport konfigurálása](virtual-machines-windows-portal-sql-availability-group-dr.md)
