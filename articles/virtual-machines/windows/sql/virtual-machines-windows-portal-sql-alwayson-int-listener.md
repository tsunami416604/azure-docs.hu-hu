---
title: Azure virtual machines szolgáltatásban hozzon létre egy SQL Server rendelkezésre állási csoport figyelőjének |} A Microsoft Docs
description: Részletes útmutató egy Always On rendelkezésre állási csoport egy figyelő létrehozásához az SQL Server Azure-beli virtuális gépeken
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: e87b58ecd72291365f9eba70c807e3018c02ae07
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382739"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Egy Always On rendelkezésre állási csoport terheléselosztó konfigurálása az Azure-ban
Ez a cikk bemutatja egy SQL Server Always On rendelkezésre állási csoport terheléselosztó létrehozása az Azure virtual machines szolgáltatásban, amely futtatja az Azure Resource Manager. Rendelkezésre állási csoport terheléselosztó van szükség, ha az SQL Server-példányok Azure-beli virtuális gépeken. A terheléselosztó IP-címét a rendelkezésre állási csoport figyelőjének tárolja. Ha egy rendelkezésre állási csoportban több régióban is kiterjed, minden egyes régió egy terheléselosztó van szüksége.

Ez a feladat elvégzéséhez szüksége lesz egy SQL Server rendelkezésre állási csoport és a Resource Manager rendszert futtató Azure virtuális gépeken telepített. Az SQL Server virtuális gépeket is ugyanabban a rendelkezésre állási csoporthoz kell tartoznia. Használhatja a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) a Resource Managerben a rendelkezésre állási csoport automatikus létrehozásához. Ez a sablon automatikusan létrehoz egy belső terheléselosztót. 

Ha szeretné, akkor [egy rendelkezésre állási csoport manuális konfigurálása](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Ez a cikk megköveteli, hogy a rendelkezésre állási csoportok már konfigurálva vannak.  

Kapcsolódó témakörök a következők:

* [Always On rendelkezésre állási csoportok konfigurálása az Azure virtuális gép (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [A virtuális hálózatok közötti kapcsolat konfigurálása Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ez a cikk ajánljuk figyelmébe, szerint hozzon létre, és a load balancer konfigurálása az Azure Portalon. A folyamat befejezése után a fürt használja az IP-címet a terheléselosztó a rendelkezésre állási csoport figyelőjének konfigurálnia.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Hozzon létre, és a load balancer konfigurálása az Azure Portalon
Ez a feladat részén tegye a következőket:

1. Az Azure Portalon hozza létre a terheléselosztót, és konfigurálja az IP-címet.
2. Konfigurálja a háttér-címkészletet.
3. A projekt létrehozásához. 
4. A terheléselosztási szabályok megadása

> [!NOTE]
> Ha az SQL Server-példányokat több erőforráscsoportok és régióban találhatók, hajtsa végre az egyes lépések kétszer, egyszer az egyes erőforráscsoportokban.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1. lépés: A terheléselosztó létrehozása és az IP-cím konfigurálása
Először hozza létre a terheléselosztót. 

1. Az Azure Portalon nyissa meg az erőforráscsoport, amely tartalmazza az SQL Server-virtuálisgépek. 

2. Kattintson az erőforráscsoport **Hozzáadás**.

3. Keresse meg **terheléselosztó** , és ezt követően a keresési eredmények között, válassza **Load Balancer**, amely által közzétett **Microsoft**.

4. Az a **terheléselosztó** panelen kattintson a **létrehozás**.

5. Az a **terheléselosztó létrehozása** párbeszédpanelen adja meg a terheléselosztó a következő:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A load balancer jelölő szöveges nevét. Ha például **sqlLB**. |
   | **Típus** |**Belső**: a legtöbb megvalósításokban belső terheléselosztó, amely lehetővé teszi az alkalmazások az adott virtuális hálózaton belül kapcsolódni a rendelkezésre állási csoporthoz.  </br> **Külső**: lehetővé teszi az alkalmazások szeretne csatlakozni a nyilvános internetkapcsolaton keresztül a rendelkezésre állási csoportot. |
   | **Virtuális hálózat** |Válassza ki a virtuális hálózat, amely az SQL Server vendégfelhasználói szerepelnek. |
   | **Alhálózat** |Válassza ki az alhálózatot, amelyet az SQL Server-példányok szerepelnek. |
   | **IP-cím hozzárendelése** |**Static** |
   | **Magánhálózati IP-cím** |Adjon meg egy elérhető IP-címet az alhálózatról. Az IP-címet használja, a figyelő a fürt létrehozásakor. Egy PowerShell-parancsprogram, ez a cikk későbbi részében használni ezt a címet a `$ILBIP` változó. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező jelenhet meg. Válassza ki az ehhez az erőforráshoz társítani kívánt előfizetést. Fontos általában ugyanazt az előfizetést a rendelkezésre állási csoport összes erőforrását. |
   | **Erőforráscsoport** |Válassza ki az erőforráscsoportot, amely az SQL Server-példányok szerepelnek. |
   | **Hely** |Válassza ki az Azure-beli hely, amely az SQL Server-példányok szerepelnek. |

6. Kattintson a **Create** (Létrehozás) gombra. 

Az Azure létrehozza a terheléselosztóhoz. A terheléselosztó egy adott hálózati, alhálózat, erőforráscsoport és hely tartozik. Az Azure a feladat befejezése után ellenőrizze a terheléselosztó beállításai az Azure-ban. 

### <a name="step-2-configure-the-back-end-pool"></a>2. lépés: Konfigurálja a háttérkészlet
Az Azure meghívja a háttér-címkészletet *háttérkészlet*. Ebben az esetben a háttérkészlet a rendelkezésre állási csoport két SQL Server-példánya a címeket. 

1. Az erőforráscsoportban kattintson a létrehozott terheléselosztó. 

2. A **beállítások**, kattintson a **háttérkészletek**.

3. A **háttérkészletek**, kattintson a **Hozzáadás** egy háttér-címkészlet létrehozása. 

4. A **háttérkészlet hozzáadása**alatt **neve**, írja be a háttérkészlet nevét.

5. A **virtuális gépek**, kattintson a **adjon hozzá egy virtuális gépet**. 

6. A **válassza ki a virtuális gépek**, kattintson a **egy rendelkezésre állási csoportot válasszon**, és adja meg a rendelkezésre állási csoportot, hogy az SQL Server-virtuálisgépek tartozik.

7. Miután kiválasztotta a rendelkezésre állási csoportban, kattintson a **válassza ki a virtuális gépeket**, válassza ki a két virtuális gépek, amelyeken az SQL Server-példányok a rendelkezésre állási csoport, és kattintson **kiválasztása**. 

8. Kattintson a **OK** gombra kattintva zárja be a paneleket az **válassza ki a virtuális gépek**, és **háttérkészlet hozzáadása**. 

Az Azure frissíti a háttér-címkészletet beállításait. A rendelkezésre állási csoport már két SQL Server-példány készletét.

### <a name="step-3-create-a-probe"></a>3. lépés: Mintavétel létrehozása
A mintavétel határozza meg, hogyan Azure ellenőrzi az SQL Server-példányokat, amely éppen birtokolja a rendelkezésre állási csoport figyelője. Az Azure-mintavételek a szolgáltatás Ön által létrehozott a mintavételi port az IP-címe alapján.

1. A terheléselosztó **beállítások** panelen kattintson a **állapotadat-mintavételek**. 

2. Az a **állapotadat-mintavételek** panelen kattintson a **Hozzáadás**.

3. A mintavétel konfigurálása a **mintavétel hozzáadása** panelen. A mintavétel konfigurálásához kövesse az alábbi értékeket:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A mintavétel jelölő szöveges nevét. Ha például **SQLAlwaysOnEndPointProbe**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |Minden elérhető port is használhatja. Ha például *59999*. |
   | **Intervallum** |*5* |
   | **Nem kifogástalan állapot küszöbértéke** |*2* |

4.  Kattintson az **OK** gombra. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port nyitva a tűzfalon az SQL Server-példány is. Példány is igényli egy bejövő szabályt a TCP-portot, amelyet használhat. További információkért lásd: [hozzáadása vagy szerkesztése tűzfalszabály](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Az Azure hoz létre a mintavétel, és azt teszteléséhez melyik SQL Server-példány rendelkezik a figyelőt a rendelkezésre állási csoport használja.

### <a name="step-4-set-the-load-balancing-rules"></a>4. lépés: A terheléselosztási szabályok beállítása
A terheléselosztási szabályok konfigurálása, hogy a terheléselosztó hogyan irányítja a forgalmat az SQL Server-példányokat. Ehhez a terheléselosztóhoz engedélyezi a közvetlen kiszolgálói válasz, mert a két SQL Server-példányok csak az egyik a rendelkezésre állási csoport figyelőjének erőforrás tulajdonosa egyszerre.

1. A terheléselosztó **beállítások** panelen kattintson a **terheléselosztási szabályok**. 

2. Az a **terheléselosztási szabályok** panelen kattintson a **Hozzáadás**.

3. Az a **terheléselosztási szabályok hozzáadása** panelen konfigurálja a terheléselosztási szabály. Használja a következő beállításokat: 

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A terheléselosztási szabályok jelölő szöveges nevét. Ha például **SQLAlwaysOnEndPointListener**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |*1433* |
   | **Háttérport** |*1433*. Rendszer figyelmen kívül hagyja ezt az értéket, mert ez a szabály használ **fix IP-(közvetlen kiszolgálói válasz)**. |
   | **Mintavétel** |A mintavétel létrehozott nevét használja ehhez a terheléselosztóhoz. |
   | **Munkamenet megőrzését** |**Egyik sem** |
   | **Üresjárat időkorlátja (perc)** |*4* |
   | **Nem fix IP (közvetlen kiszolgálói válasz)** |**Engedélyezve van** |

   > [!NOTE]
   > Előfordulhat, hogy le kell görgetnie a panel az összes megadott beállítások megjelenítéséhez.
   > 

4. Kattintson az **OK** gombra. 
5. Azure úgy konfigurálja a terheléselosztási szabály. Most már a terheléselosztó forgalmat útvonal a figyelőt a rendelkezésre állási csoport az SQL Server-példány van konfigurálva. 

Ezen a ponton az az erőforráscsoport olyan terheléselosztóhoz, amely csatlakozik az SQL Server-gépeket is rendelkezik. A load balancer is tartalmaz az SQL Server Always On rendelkezésre állási csoport figyelőjét, IP-címet, úgy, hogy mindkét gép válaszolhat a rendelkezésre állási csoportokra vonatkozó kéréseket.

> [!NOTE]
> Ha az SQL Server-példányok két külön régióban, ismételje meg a más régióban található. Minden egyes régió egy terheléselosztót igényel. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurálja a fürt használni a terheléselosztó IP-címe
A következő lépés, hogy konfigurálja a figyelőt a fürtön, és a figyelő online állapotba. Tegye a következőket: 

1. A rendelkezésre állási csoport figyelőjének létrehozásához a feladatátvevő fürtön. 

2. A figyelő online állapotba.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. lépés: A rendelkezésre állási csoport figyelőjének létrehozásához a feladatátvevő fürt
Ebben a lépésben, manuálisan hozzon létre a rendelkezésre állási csoport figyelőjének a Feladatátvevőfürt-kezelő és az SQL Server Management Studióval.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>A figyelő a konfiguráció ellenőrzése

Ha a fürt erőforrásait és a függőségek megfelelően vannak konfigurálva, megtekintheti a figyelő az SQL Server Management Studio kell lennie. A figyelőport beállításához tegye a következőket:

1. Indítsa el az SQL Server Management Studiót, és kapcsolódjon az elsődleges replika.

2. Lépjen a **AlwaysOn magas rendelkezésre állás** > **rendelkezésre állási csoportok** > **rendelkezésre állási csoport figyelője**.  
    Meg kell jelennie a figyelő nevét, amely a Feladatátvevőfürt-kezelő létrehozta. 

3. Kattintson a jobb gombbal a figyelő nevét, és kattintson **tulajdonságok**.

4. Az a **Port** mezőt, a korábban használt $EndpointPort használatával adja meg a port számát a rendelkezésre állási csoport figyelőjét (az alapértelmezett 1433-as volt az), és kattintson a **OK**.

Most már a Resource Manager módban futó Azure virtuális gépek rendelkezésre állási csoport. 

## <a name="test-the-connection-to-the-listener"></a>A figyelő a kapcsolat tesztelése
A kapcsolat tesztelése az alábbiak szerint:

1. RDP-vel, hogy az azonos virtuális hálózatba, de nem tulajdonosa a replika SQL Server-példányt. A kiszolgáló lehet a többi SQL Server-példány a fürtben.

2. Használat **sqlcmd** segédprogram a kapcsolat teszteléséhez. Például hozza létre a következő parancsfájl egy **sqlcmd** kapcsolatot az elsődleges replika, a figyelő a Windows-hitelesítés használatával:
   
        sqlcmd -S <listenerName> -E

Az SQLCMD-kapcsolatot automatikusan csatlakozik az SQL Server-példány, amelyen az elsődleges replika. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Hozzon létre egy IP-cím további rendelkezésre állási csoporthoz

Egyes rendelkezésre állási csoport egy külön figyelőt használ. Minden egyes figyelő a saját IP-címmel rendelkezik. Használja az azonos terheléselosztóhoz, amely tárolja az IP-cím további figyelők esetében. Miután létrehozott egy rendelkezésre állási csoport, az IP-cím hozzáadása a terheléselosztóhoz, és konfigurálja a figyelőt.

Az IP-címet ad hozzá egy terheléselosztó és az Azure portal, tegye a következőket:

1. Az Azure Portalon nyissa meg az erőforráscsoport, amely tartalmazza a terheléselosztóhoz, majd kattintson a terheléselosztó. 

2. A **beállítások**, kattintson a **előtérbeli IP-készlet**, és kattintson a **Hozzáadás**. 

3. A **adja hozzá az előtérbeli IP-cím**, rendelje hozzá az előtér nevét. 

4. Ellenőrizze, hogy a **virtuális hálózati** és a **alhálózati** ugyanazok, mint az SQL Server-példányokat.

5. Állítsa be az IP-címet a figyelőhöz. 
   
   >[!TIP]
   >A statikus IP-cím beállítva, és írjon be egy címet, jelenleg nem használt alhálózaton. Másik lehetőségként dinamikus IP-cím beállítva, és mentse az új előtérbeli IP-címkészlet. Ha így tesz, az Azure portal automatikusan hozzárendel egy elérhető IP-címet a készlet. Ezután nyissa meg újra az előtér-IP-készlet és a hozzárendelés Módosítsa statikusra. 

6. Mentse az IP-címet a figyelőhöz. 

7. Állapotadat-mintavétel hozzáadása a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Name (Név)** |A mintavétel azonosító nevet.
   |**Protocol (Protokoll)** |TCP
   |**Port** |Egy nem használt TCP-portot, amely az összes virtuális gép rendelkezésre kell állnia. Semmilyen más célra nem használható. Két figyelői nem használhatja ugyanazt a mintavételi portot. 
   |**Intervallum** |A mintavételi kísérletek közötti idő mennyisége. Használja az alapértelmezett (5).
   |**Nem kifogástalan állapot küszöbértéke** |Hány egymást követő küszöbértékeket, amelyek nem kell a virtuális gép nem megfelelő állapotúnak számít.

8. Kattintson a **OK** a mintavétel mentése. 

9. Hozzon létre egy terheléselosztási szabályt. Kattintson a **terheléselosztási szabályok**, és kattintson a **Hozzáadás**.

10. Adja meg az új terheléselosztási szabályt a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Name (Név)** |A terheléselosztási szabályt azonosító nevet. 
   |**Előtérbeli IP-cím** |Válassza ki a létrehozott IP-cím. 
   |**Protocol (Protokoll)** |TCP
   |**Port** |Használhatja az SQL Server-példányok által használt portot. Egy alapértelmezett példány, 1433-as portot használja, változtatta meg. 
   |**Háttérport** |Ugyanazt az értéket, **Port**.
   |**Háttérkészlet** |A készlet, amely tartalmazza a virtuális gépek és az SQL Server-példányokat. 
   |**Állapotadat-mintavétel** |Válassza ki a létrehozott mintavételt.
   |**Munkamenet megőrzését** |None
   |**Üresjárat időkorlátja (perc)** |Alapértelmezett (4)
   |**Nem fix IP (közvetlen kiszolgálói válasz)** | Engedélyezve

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Az új IP-címet használja a rendelkezésre állási csoport konfigurálása

A fürt konfigurálásának befejezéséhez ismételje meg a végrehajtott lépések rövidek, amikor végzett az első rendelkezésre állási csoportot. Azt jelenti, konfigurálja a [az új IP-címet használó fürt](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Miután hozzáadott egy IP-címet a figyelőhöz, konfigurálja a további rendelkezésre állási csoportot az alábbiak szerint: 

1. Győződjön meg arról, hogy az SQL Server virtuális gépeket is nyitva-e a mintavételi portot az új IP-cím. 

2. [A kezelőben adja hozzá az ügyfél hozzáférési pontjának](#addcap).

3. [Az IP-erőforrást a rendelkezésre állási csoport konfigurálása](#congroup).

   >[!IMPORTANT]
   >Az IP-cím létrehozásakor a terheléselosztóhoz hozzáadott IP-címet használja.  

4. [Győződjön meg arról, az SQL Server rendelkezésre állási csoport erőforrása függ, az ügyfél hozzáférési pontjának](#dependencyGroup).

5. [Ellenőrizze az ügyfél hozzáférési pont erőforrás az IP-cím függő](#listname).
 
6. [A fürt paraméterek beállítása a PowerShell](#setparam).

Miután konfigurálta a rendelkezésre állási csoportot az új IP-címet használja, a figyelő a kapcsolat konfigurálása. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Terheléselosztási szabály elosztott rendelkezésre állási csoport

Ha egy elosztott rendelkezésre állási csoport rendelkezésre állási csoport tagja, a load balancer további szabály van szüksége. Ez a szabály a portot használják az elosztott rendelkezésre állási csoport figyelőjének tárolja.

>[!IMPORTANT]
>Ez a lépés csak akkor érvényes, ha részt vesz a rendelkezésre állási csoport egy [elosztott rendelkezésre állási csoport](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Az elosztott rendelkezésre állási csoportban szereplő terméknek minden egyes kiszolgálón hozzon létre egy bejövő szabályt az az elosztott rendelkezésre állási csoport figyelőjének TCP-port. Példákat dokumentációt 5022 használja. 

1. Az Azure Portalon kattintson a terheléselosztón, és kattintson a **terheléselosztási szabályok**, és kattintson a **+ Hozzáadás**. 

1. A terheléselosztási szabály a következő beállításokkal hozza létre:

   |Beállítás |Érték
   |:-----|:----
   |**Name (Név)** |A terheléselosztási szabályt az elosztott rendelkezésre állási csoporthoz azonosító nevet. 
   |**Előtérbeli IP-cím** |Az azonos előtérbeli IP-cím a rendelkezésre állási csoportot használja.
   |**Protocol (Protokoll)** |TCP
   |**Port** |5022 - portnak a [elosztott rendelkezésre állási csoport felhővégpont-figyelő](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Minden elérhető port lehet.  
   |**Háttérport** | 5022 - használat, azonos értéket **Port**.
   |**Háttérkészlet** |A készlet, amely tartalmazza a virtuális gépek és az SQL Server-példányokat. 
   |**Állapotadat-mintavétel** |Válassza ki a létrehozott mintavételt.
   |**Munkamenet megőrzését** |None
   |**Üresjárat időkorlátja (perc)** |Alapértelmezett (4)
   |**Nem fix IP (közvetlen kiszolgálói válasz)** | Engedélyezve

Ismételje meg ezeket a lépéseket a terheléselosztó a többi rendelkezésre állási csoportok, amelyek az elosztott rendelkezésre állási csoportok részt.

Ha Ön az Azure hálózati biztonsági csoporttal való hozzáférés korlátozása győződjön meg arról, hogy az engedélyezési szabályok közé tartoznak a háttér SQL Server virtuális gép IP-címek és a terheléselosztó nem fix IP-címek a rendelkezésre állási csoport figyelőjének és a fürt alapvető IP-címét, ha van ilyen.

## <a name="next-steps"></a>További lépések

- [Különböző régiókban lévő Azure virtuális gépeken futó SQL Server Always On rendelkezésre állási csoport konfigurálása](virtual-machines-windows-portal-sql-availability-group-dr.md)
