---
title: Rendelkezésre állási csoportfigyelők & terheléselosztó konfigurálása (Azure Portal)
description: Lépésenkénti útmutató az Azure virtuális gépekSQL Server hez való mindig bekapcsolt rendelkezésre állási csoport figyelőjének létrehozásához
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096325"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Terheléselosztó konfigurálása egy rendelkezésre állási csoporthoz az Azure SQL Server virtuális gépein
Ez a cikk bemutatja, hogyan hozhat létre egy terheléselosztót egy SQL Server Always On rendelkezésre állási csoporthoz az Azure Resource Managerrel futó virtuális azure-beli virtuális gépeken. Egy rendelkezésre állási csoport terheléselosztót igényel, ha az SQL Server-példányok Az Azure virtuális gépeken vannak. A terheléselosztó tárolja a rendelkezésre állási csoport figyelőjének IP-címét. Ha egy rendelkezésre állási csoport több régióra terjed ki, minden régiónak szüksége van egy terheléselosztóra.

A feladat végrehajtásához egy SQL Server rendelkezésre állási csoport üzembe helyezéséhez az Azure virtuális gépeken futó Resource Manager. Mindkét SQL Server virtuális gépnek ugyanahhoz a rendelkezésre állási csoporthoz kell tartoznia. A [Microsoft-sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) segítségével automatikusan létrehozhatja az erőforrás-kezelőben az elérhetőségi csoportot. Ez a sablon automatikusan létrehoz egy belső terheléselosztót. 

Ha szeretné, [manuálisan is konfigurálhat egy elérhetőségi csoportot.](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

Ez a cikk megköveteli, hogy a rendelkezésre állási csoportok már konfigurálva vannak.  

Kapcsolódó témák a következők:

* [A Mindig rendelkezésre állási csoportok konfigurálása az Azure virtuális gépben (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Virtuális hálózatok közötti kapcsolat létrehozása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ebben a cikkben végigsétálva hozzon létre és konfiguráljon egy terheléselosztót az Azure Portalon. A folyamat befejezése után konfigurálja a fürtet, hogy a terheléselosztó IP-címét használja a rendelkezésre állási csoport figyelőjéhez.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása és konfigurálása az Azure Portalon
A feladat nak ebben a részében tegye a következőket:

1. Az Azure Portalon hozza létre a terheléselosztót, és konfigurálja az IP-címet.
2. Konfigurálja a háttérkészletet.
3. Hozza létre a szondát. 
4. Állítsa be a terheléselosztási szabályokat.

> [!NOTE]
> Ha az SQL Server-példányok több erőforráscsoportban és régióban találhatók, minden lépést kétszer hajtson végre, minden erőforráscsoportban egyszer.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1. lépés: A terheléselosztó létrehozása és az IP-cím konfigurálása
Először hozza létre a terheléselosztót. 

1. Az Azure Portalon nyissa meg az SQL Server virtuális gépeket tartalmazó erőforráscsoportot. 

2. Az erőforráscsoportban kattintson a **Hozzáadás gombra.**

3. Keresse meg a **terheléselosztót,** majd a keresési eredmények között válassza a Microsoft **által**közzétett **Terheléselosztó**lehetőséget.

4. A **Terheléselosztó** panelen kattintson a **Létrehozás gombra.**

5. A **Terheléselosztó létrehozása** párbeszédpanelen a következőképpen konfigurálja a terheléselosztót:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztót jelölő szöveges név. Például **sqlLB**. |
   | **Típus** |**Belső**: A legtöbb implementáció kontakaregy belső terheléselosztót, amely lehetővé teszi, hogy az ugyanazon a virtuális hálózaton belüli alkalmazások csatlakozzanak a rendelkezésre állási csoporthoz.  </br> **Külső**: Lehetővé teszi, hogy az alkalmazások nyilvános internetkapcsolaton keresztül csatlakozzanak a rendelkezésre állási csoporthoz. |
   | **Virtuális hálózat** |Válassza ki azt a virtuális hálózatot, amelyben az SQL Server-példányok találhatók. |
   | **Alhálózat** |Válassza ki azt az alhálózatot, amelyben az SQL Server-példányok találhatók. |
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Privát IP-cím** |Adjon meg egy elérhető IP-címet az alhálózatból. Ezt az IP-címet akkor használja, ha figyelőt hoz létre a fürtön. Egy PowerShell-parancsfájlban a jelen cikk későbbi `$ILBIP` részében használja ezt a címet a változóhoz. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező megjelenhet. Válassza ki az erőforráshoz társítani kívánt előfizetést. Általában ugyanaz az előfizetés, mint a rendelkezésre állási csoport összes erőforrása. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelyben az SQL Server-példányok találhatók. |
   | **Helyen** |Válassza ki azt az Azure-helyet, amelyben az SQL Server-példányok találhatók. |

6. Kattintson **a Létrehozás gombra.** 

Az Azure létrehozza a terheléselosztót. A terheléselosztó egy adott hálózathoz, alhálózathoz, erőforráscsoporthoz és helyhez tartozik. Miután az Azure befejezte a feladatot, ellenőrizze a terheléselosztó beállításait az Azure-ban. 

### <a name="step-2-configure-the-back-end-pool"></a>2. lépés: A háttérkészlet konfigurálása
Az Azure meghívja a háttércímkészlet *háttérkészletét.* Ebben az esetben a háttérkészlet a rendelkezésre állási csoport két SQL Server-példányának a címe. 

1. Az erőforráscsoportban kattintson a létrehozott terheléselosztóra. 

2. A **Beállítások párbeszédpanelen**kattintson **a Háttérkészletek**elemre.

3. **Háttérkészletein**kattintson a **Hozzáadás** gombra háttércímkészlet létrehozásához. 

4. A **Háttérkészlet hozzáadása** **csoport Név csoportjában**adja meg a háttérkészlet nevét.

5. A **Virtuális gépek csoportban**kattintson **a Virtuális gép hozzáadása**elemre. 

6. A **Virtuális gépek kiválasztása**csoportban kattintson a **Rendelkezésre állási csoport kiválasztása**elemre, majd adja meg azt a rendelkezésre állási készletet, amelyhez az SQL Server virtuális gépek tartoznak.

7. Miután kiválasztotta a rendelkezésre állási csoportot, kattintson **a Virtuális gépek kiválasztása**elemre, válassza ki az SQL Server-példányokat a rendelkezésre állási csoportban tároló két virtuális gépet, majd kattintson a Kijelölés **gombra.** 

8. Kattintson az **OK** gombra a **Választható virtuális gépek**és **háttérkészlet hozzáadása**lapkák hoz. 

Az Azure frissíti a háttércímkészlet beállításait. Most a rendelkezésre állási csoport rendelkezik egy készlet két SQL Server-példányok.

### <a name="step-3-create-a-probe"></a>3. lépés: Szonda létrehozása
A mintavétel határozza meg, hogy az Azure hogyan ellenőrzi, hogy jelenleg melyik SQL Server-példány oka a rendelkezésre állási csoport figyelője. Az Azure a szolgáltatás a mintavétel létrehozásakor megadott port IP-címe alapján vizsgálja a szolgáltatást.

1. A terheléselosztó **beállítások** paneljén kattintson az **Állapotminta parancsra.** 

2. Az **Állapotpróbák** panelen kattintson a **Hozzáadás**gombra.

3. Konfigurálja a szondát az **Add probe** panelen. A mintavétel konfigurálásához használja a következő értékeket:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A mintavételt jelölő szöveges név. Például **SQLAlwaysOnEndPointProbe**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |Bármelyik elérhető portot használhatja. Például *59999*. |
   | **Intervallum** |*5* |
   | **Nem kifogástalan állapot küszöbértéke** |*2* |

4.  Kattintson az **OK** gombra. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port mindkét SQL Server-példány tűzfalán meg van nyitva. Mindkét példányhoz bejövő szabály szükséges a használt TCP-porthoz. További információt a [Tűzfalszabály hozzáadása és szerkesztése](https://technet.microsoft.com/library/cc753558.aspx)című témakörben talál. 
> 
> 

Az Azure létrehozza a mintavételt, és ezt követően azt használja, hogy tesztelje, melyik SQL Server-példány rendelkezik a figyelő a rendelkezésre állási csoport.

### <a name="step-4-set-the-load-balancing-rules"></a>4. lépés: A terheléselosztási szabályok beállítása
A terheléselosztási szabályok azt konfigurálják, hogy a terheléselosztó hogyan irányítja a forgalmat az SQL Server-példányokhoz. Ennél a terheléselosztónál engedélyezi a közvetlen kiszolgáló-visszatérést, mert egyszerre csak a két SQL Server-példány egyike rendelkezik a rendelkezésre állási csoport figyelőerőforrásával.

1. A terheléselosztó **beállítások** paneljén kattintson a **Terheléselosztási szabályok**gombra. 

2. A **Terheléselosztási szabályok** panelen kattintson a **Hozzáadás**gombra.

3. A **Terheléselosztási szabályok hozzáadása** panelen konfigurálja a terheléselosztási szabályt. Használja a következő beállításokat: 

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztási szabályokat jelölő szöveges név. Például **SQLAlwaysOnEndPointListener**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |*1433* |
   | **Háttérport** |*1433*. Ez az érték figyelmen kívül hagyja, mert ez a szabály **lebegő IP (közvetlen kiszolgáló visszatérés)**. |
   | **Mintavétel** |Használja a terheléselosztóhoz létrehozott mintavétel nevét. |
   | **Munkamenet-állandóság** |**Nincs** |
   | **Tétlen időelés (perc)** |*4* |
   | **Lebegő IP-cím (közvetlen kiszolgálói visszatérés)** |**Engedélyezve** |

   > [!NOTE]
   > Előfordulhat, hogy az összes beállítás megtekintéséhez lefelé kell görgetnie a panelt.
   > 

4. Kattintson az **OK** gombra. 
5. Az Azure konfigurálja a terheléselosztási szabályt. Most a terheléselosztó úgy van konfigurálva, hogy a forgalmat az SQL Server-példány, amely a figyelő a rendelkezésre állási csoport. 

Ezen a ponton az erőforráscsoport rendelkezik egy terheléselosztóval, amely mindkét SQL Server-géphez csatlakozik. A terheléselosztó is tartalmaz egy IP-címet az SQL Server Mindig a rendelkezésre állási csoport figyelője, hogy bármelyik gép válaszolhat a rendelkezésre állási csoportok kéréseire.

> [!NOTE]
> Ha az SQL Server-példányok két különböző régióban találhatók, ismételje meg a lépéseket a másik régióban. Minden régióterhelés-elosztót igényel. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>A fürt konfigurálása a terheléselosztó IP-címének használatára
A következő lépés a figyelő konfigurálása a fürtön, és hozza a figyelő online állapotba. Tegye a következőket: 

1. Hozza létre a rendelkezésre állási csoport figyelője a feladatátvevő fürtön. 

2. Hozza online a hallgatót.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. lépés: A feladatátvevő fürt önkiszolgáló csoportfigyelőjének létrehozása
Ebben a lépésben manuálisan hozza létre a rendelkezésre állási csoport figyelőjét a Feladatátvevő fürtkezelőben és az SQL Server Management Studio-ban.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>A figyelő konfigurációjának ellenőrzése

Ha a fürt erőforrásai és függőségei megfelelően vannak konfigurálva, meg kell tudnia tekinteni a figyelőt az SQL Server Management Studio-ban. A figyelőport beállításához tegye a következőket:

1. Indítsa el az SQL Server Management Studio alkalmazást, majd csatlakozzon az elsődleges kópiához.

2. Nyissa meg az **AlwaysOn magas rendelkezésre állási** > **rendelkezésre állási csoportok** > **rendelkezésre állási csoportfigyelőit.**  
    Most látnia kell a feladatátvevői fürtkezelőben létrehozott figyelőnevet. 

3. Kattintson a jobb gombbal a figyelő nevére, majd kattintson **a Tulajdonságok parancsra.**

4. A **Port** mezőben adja meg a rendelkezésre állási csoportfigyelő portszámát a korábban használt $EndpointPort (az 1433 volt az alapértelmezett), majd kattintson az **OK**gombra.

Most már rendelkezik egy rendelkezésre állási csoport az Azure-ban futó virtuális gépek Erőforrás-kezelő módban. 

## <a name="test-the-connection-to-the-listener"></a>A figyelővel való kapcsolat tesztelése
Tesztelje a kapcsolatot az alábbi módon:

1. RDP egy SQL Server-példány, amely ugyanabban a virtuális hálózatban, de nem a replika. Ez a kiszolgáló lehet a fürt másik SQL Server-példánya.

2. A kapcsolat teszteléséhez használja az **sqlcmd** segédprogramot. A következő parancsfájl például **sqlcmd** kapcsolatot hoz létre az elsődleges kópiával a figyelőn keresztül a Windows-hitelesítéssel:
   
        sqlcmd -S <listenerName> -E

Az SQLCMD-kapcsolat automatikusan csatlakozik az elsődleges replikát tároló SQL Server-példányhoz. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>IP-cím létrehozása egy további rendelkezésre állási csoporthoz

Minden rendelkezésre állási csoport egy külön figyelőt használ. Minden figyelő saját IP-címmel rendelkezik. Használja ugyanazt a terheléselosztót az IP-cím további figyelők számára való lenyomva tartására. Miután létrehozott egy rendelkezésre állási csoportot, adja hozzá az IP-címet a terheléselosztóhoz, majd konfigurálja a figyelőt.

HA IP-címet szeretne hozzáadni egy terheléselosztóhoz az Azure Portalon, tegye a következőket:

1. Az Azure Portalon nyissa meg a terheléselosztót tartalmazó erőforráscsoportot, majd kattintson a terheléselosztóra. 

2. A **BEÁLLÍTÁSOK**csoportban kattintson **az Előtér-IP-készlet**elemre, majd a **Hozzáadás**gombra. 

3. Az **Előtér-IP-cím hozzáadása**csoportban rendeljen nevet az előtérhez. 

4. Ellenőrizze, hogy a **virtuális hálózat** és az **alhálózat** megegyezik-e az SQL Server-példányokkal.

5. Állítsa be a figyelő IP-címét. 
   
   >[!TIP]
   >Az IP-címet statikusra állíthatja, és olyan címet írhat be, amely jelenleg nincs az alhálózatban. Azt is megteheti, hogy az IP-címet dinamikusra állítja, és menti az új előtér-IP-készletet. Ha így tesz, az Azure Portal automatikusan hozzárendel egy elérhető IP-címet a készlethez. Ezután nyissa meg újra az előtér-IP-készletet, és módosítsa a hozzárendelést statikusra. 

6. Mentse a figyelő IP-címét. 

7. Adjon hozzá egy állapotminta a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Név** |Egy név, ami azonosítja a szondát.
   |**Protocol (Protokoll)** |TCP
   |**Port** |Nem használt TCP-port, amelynek minden virtuális gépen elérhetőnek kell lennie. Nem használható más célra. Nincs két figyelőhasználhatja ugyanazt a mintavételi portot. 
   |**Intervallum** |A mintavételi kísérletek közötti idő. Használja az alapértelmezett (5).
   |**Nem kifogástalan állapot küszöbértéke** |Az egymást követő küszöbértékek száma, amelyek nem sikerül, mielőtt egy virtuális gép nem megfelelő állapotúnak minősül.

8. A mintavétel mentéséhez kattintson az **OK** gombra. 

9. Hozzon létre egy terheléselosztási szabályt. Kattintson **a Terheléselosztási szabályok**, majd **a Hozzáadás**gombra.

10. Konfigurálja az új terheléselosztási szabályt a következő beállításokkal:

    |Beállítás |Érték
    |:-----|:----
    |**Név** |A terheléselosztási szabály azonosítására szolgáló név. 
    |**Előtérbeli IP-cím** |Válassza ki a létrehozott IP-címet. 
    |**Protocol (Protokoll)** |TCP
    |**Port** |Használja az SQL Server-példányok által használt portot. Az alapértelmezett példány az 1433-as portot használja, hacsak nem módosítja. 
    |**Háttérablak** |Használja ugyanazt az értéket, mint **a Port**.
    |**Háttérkészlet** |Az SQL Server-példányokkal rendelkező virtuális gépeket tartalmazó készlet. 
    |**Állapotadat-mintavétel** |Válassza ki a létrehozott mintavételt.
    |**Munkamenet-állandóság** |None
    |**Tétlen időelés (perc)** |Alapértelmezett (4)
    |**Lebegő IP-cím (közvetlen kiszolgálói visszatérés)** | Engedélyezve

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>A rendelkezésre állási csoport beállítása az új IP-cím használatára

A fürt konfigurálásának befejezéséhez ismételje meg az első rendelkezésre állási csoport beállításakor végrehajtott lépéseket. Ez azt, hogy konfigurálja a [fürtöt az új IP-cím használatára.](#configure-the-cluster-to-use-the-load-balancer-ip-address) 

Miután hozzáadott egy IP-címet a figyelőhöz, konfigurálja a további rendelkezésre állási csoportot az alábbi módon: 

1. Ellenőrizze, hogy az új IP-cím mintavételi portja meg van-e nyitva mindkét SQL Server virtuális gépen. 

2. [A Fürtkezelőben adja hozzá az ügyfél hozzáférési pontját.](#addcap)

3. [Konfigurálja az IP-erőforrást a rendelkezésre állási csoporthoz.](#congroup)

   >[!IMPORTANT]
   >Az IP-cím létrehozásakor használja a terheléselosztóhoz hozzáadott IP-címet.  

4. [Az SQL Server rendelkezésre állási csoportjának erőforrásának függővé tételét az ügyfél hozzáférési pontjától.](#dependencyGroup)

5. [Az ügyfél-hozzáférési pont erőforrásának függővé az IP-címtől függővé kell tenni.](#listname)
 
6. [Állítsa be a fürt paramétereit a PowerShellben.](#setparam)

Miután konfigurálta a rendelkezésre állási csoportot az új IP-cím használatára, konfigurálja a kapcsolatot a figyelővel. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Terheléselosztási szabály hozzáadása az elosztott rendelkezésre állási csoporthoz

Ha egy rendelkezésre állási csoport részt vesz egy elosztott rendelkezésre állási csoportban, a terheléselosztónak egy további szabályra van szüksége. Ez a szabály tárolja az elosztott rendelkezésre állási csoport figyelője által használt portot.

>[!IMPORTANT]
>Ez a lépés csak akkor érvényes, ha az elérhetőségi csoport egy [elosztott rendelkezésre állási csoportban](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)vesz részt. 

1. Minden olyan kiszolgálón, amely részt vesz az elosztott rendelkezésre állási csoportban, hozzon létre egy bejövő szabályt az elosztott rendelkezésre állási csoport figyelőjének TCP-portján. Sok példában a dokumentáció az 5022-es. 

1. Az Azure Portalon kattintson a terheléselosztóra, majd a **Terheléselosztási szabályok**elemre, majd a **+Hozzáadás**gombra. 

1. Hozza létre a terheléselosztási szabályt a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Név** |Az elosztott rendelkezésre állási csoport terheléselosztási szabályának azonosítására szolgáló név. 
   |**Előtérbeli IP-cím** |Használja ugyanazt az előtér-IP-címet, mint a rendelkezésre állási csoport.
   |**Protocol (Protokoll)** |TCP
   |**Port** |5022 - Az [elosztott rendelkezésre állási csoport végpontfigyelőportja.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)</br> Bármilyen elérhető port lehet.  
   |**Háttérablak** | 5022 - Használja ugyanazt az értéket, mint **a Port**.
   |**Háttérkészlet** |Az SQL Server-példányokkal rendelkező virtuális gépeket tartalmazó készlet. 
   |**Állapotadat-mintavétel** |Válassza ki a létrehozott mintavételt.
   |**Munkamenet-állandóság** |None
   |**Tétlen időelés (perc)** |Alapértelmezett (4)
   |**Lebegő IP-cím (közvetlen kiszolgálói visszatérés)** | Engedélyezve

Ismételje meg ezeket a lépéseket a terheléselosztó számára az elosztott rendelkezésre állási csoportokban részt vevő többi rendelkezésre állási csoporton.

Ha korlátozza a hozzáférést egy Azure Network Security Group, győződjön meg arról, hogy az engedélyezési szabályok tartalmazzák a háttér-SQL Server virtuális gép IP-címek, és a terheléselosztó lebegő IP-címek az AG figyelő és a fürt alapvető IP-cím, ha alkalmazható.

## <a name="next-steps"></a>További lépések

- [SQL Server Always On availability csoport konfigurálása különböző régiókban lévő Azure virtuális gépeken](virtual-machines-windows-portal-sql-availability-group-dr.md)
