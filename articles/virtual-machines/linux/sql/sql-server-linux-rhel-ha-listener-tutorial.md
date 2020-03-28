---
title: Rendelkezésre állási csoportfigyelő konfigurálása az SQL Server hez RHEL virtuális gépeken az Azure -Linux virtuális gépeken | Microsoft dokumentumok
description: 'Tudnivalók: rendelkezésre állási csoportfigyelő beállítása az SQL Serverben RHEL virtuális gépeken az Azure-ban'
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096591"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Oktatóanyag: Az SQL Server rendelkezésre állási csoportfigyelőjének konfigurálása RHEL virtuális gépeken az Azure-ban

> [!NOTE]
> A bemutatott bemutató **nyilvános előzetes verzióban érhető el.** 
>
> Ebben az oktatóanyagban az SQL Server 2017 és az RHEL 7.6 használatát használjuk, de az SQL Server 2019 rhel 7 vagy RHEL 8 rendszerben is használható a HA konfigurálásához. A rendelkezésre állási csoport erőforrásainak konfigurálására szolgáló parancsok megváltoztak az RHEL 8-ban, és érdemes megnézni a [rendelkezésre állási csoport erőforrásának létrehozása](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) és az RHEL 8 erőforrások című cikket a helyes parancsokról.

Ez az oktatóanyag az Azure-beli RHEL virtuális gépeken lévő SQL-kiszolgálók rendelkezésre állási csoportfigyelőjének létrehozásához szükséges lépéseket ismerteti. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> - Terheléselosztó létrehozása az Azure Portalon
> - A terheléselosztó háttérkészletének konfigurálása
> - Szonda létrehozása a terheléselosztóhoz
> - A terheléselosztási szabályok beállítása
> - A terheléselosztó erőforrás létrehozása a fürtben
> - A rendelkezésre állási csoport figyelőjének létrehozása
> - A figyelőhöz való csatlakozás tesztelése
> - Feladatátvétel tesztelése

## <a name="prerequisite"></a>Előfeltétel

Befejezett [ **oktatóanyag: Az SQL Server rendelkezésre állási csoportjainak konfigurálása RHEL virtuális gépeken az Azure-ban**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása az Azure Portalon

A következő utasítások végigviszik [az](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) 1–4. [Load balancer - Azure portal](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

1. Az Azure Portalon nyissa meg az SQL Server virtuális gépeket tartalmazó erőforráscsoportot. 

2. Az erőforráscsoportban kattintson a **Hozzáadás gombra.**

3. Keresse meg a **terheléselosztót,** majd a keresési eredmények között válassza a Microsoft **által**közzétett **Terheléselosztó**lehetőséget.

4. A **Terheléselosztó** panelen kattintson a **Létrehozás gombra.**

5. A **Terheléselosztó létrehozása** párbeszédpanelen a következőképpen konfigurálja a terheléselosztót:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztót jelölő szöveges név. Például **sqlLB**. |
   | **Típus** |**Belső** |
   | **Virtuális hálózat** |A létrehozott alapértelmezett virtuális hálózat neve **VM1VNET**lesz. |
   | **Alhálózat** |Válassza ki azt az alhálózatot, amelyben az SQL Server-példányok találhatók. Az alapértelmezett érték **vm1Subnet**.|
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Privát IP-cím** |Használja `virtualip` a fürtben létrehozott IP-címet. |
   | **Előfizetés** |Használja az erőforráscsoporthoz használt előfizetést. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelyben az SQL Server-példányok találhatók. |
   | **Helyen** |Válassza ki azt az Azure-helyet, amelyben az SQL Server-példányok találhatók. |

### <a name="configure-the-back-end-pool"></a>A háttérkészlet konfigurálása
Az Azure meghívja a háttércímkészlet *háttérkészletét.* Ebben az esetben a háttérkészlet a rendelkezésre állási csoport három SQL Server-példányának a címe. 

1. Az erőforráscsoportban kattintson a létrehozott terheléselosztóra. 

2. A **Beállítások párbeszédpanelen**kattintson **a Háttérkészletek**elemre.

3. **Háttérkészletein**kattintson a **Hozzáadás** gombra háttércímkészlet létrehozásához. 

4. A **Háttérkészlet hozzáadása** **csoport Név csoportjában**adja meg a háttérkészlet nevét.

5. A **Társított hoz csoportban**válassza a **Virtuális gép**lehetőséget. 

6. Jelölje ki a környezetben lévő virtuális gépeket, és társítsa a megfelelő IP-címet az egyes kijelöléshez.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Háttérkészlet hozzáadása":::

7. Kattintson a **Hozzáadás** gombra. 

### <a name="create-a-probe"></a>Mintavétel létrehozása

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

5. Jelentkezzen be az összes virtuális gépre, és nyissa meg a mintavételi portot a következő parancsokkal:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Az Azure létrehozza a mintavételt, és ezt követően azt használja, hogy tesztelje, melyik SQL Server-példány rendelkezik a figyelő a rendelkezésre állási csoport.

### <a name="set-the-load-balancing-rules"></a>A terheléselosztási szabályok beállítása

A terheléselosztási szabályok azt konfigurálják, hogy a terheléselosztó hogyan irányítja a forgalmat az SQL Server-példányokhoz. Ennél a terheléselosztónál engedélyezi a közvetlen kiszolgáló-visszatérést, mert egyszerre csak a három SQL Server-példány egyike rendelkezik a rendelkezésre állási csoport figyelőerőforrásával.

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

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Terheléselosztási szabály hozzáadása":::

4. Kattintson az **OK** gombra. 
5. Az Azure konfigurálja a terheléselosztási szabályt. Most a terheléselosztó úgy van konfigurálva, hogy a forgalmat az SQL Server-példány, amely a figyelő a rendelkezésre állási csoport. 

Ezen a ponton az erőforráscsoport rendelkezik egy terheléselosztóval, amely az összes SQL Server-géphez csatlakozik. A terheléselosztó is tartalmaz egy IP-címet az SQL Server Mindig a rendelkezésre állási csoport figyelője, hogy bármely gép válaszolhat a rendelkezésre állási csoportok kéréseire.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>A terheléselosztó erőforrás létrehozása a fürtben

1. Jelentkezzen be az elsődleges virtuális gépbe. Létre kell hoznunk az erőforrást az Azure terheléselosztó mintavételi port engedélyezéséhez (az 59999-et a példánkban használjuk). Futtassa az alábbi parancsot:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Hozzon létre egy `virtualip` `azure_load_balancer` csoportot, amely tartalmazza a és az erőforrás:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Megkötések hozzáadása

1. A helymegosztási megkötést úgy kell konfigurálni, hogy az Azure load balancer IP-cím és az AG erőforrás ugyanazon a csomóponton fut. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Hozzon létre egy rendelési megkötést annak érdekében, hogy az AG-erőforrás az Azure terheléselosztó IP-címe előtt működjön. Míg a helymegosztási megkötés rendelési megkötést jelent, ez azt kényszeríti.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. A megkötések ellenőrzéséhez futtassa a következő parancsot:

    ```bash
    sudo pcs constraint list --full
    ```

    A következő kimenetnek kell megjelennie:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelőjének létrehozása

1. Az elsődleges csomóponton futtassa a következő parancsot az SQLCMD vagy az SSMS fájlban:

    - Cserélje le az alábbi `virtualip` IP-címet az IP-címre.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Jelentkezzen be az egyes virtuálisgép-csomópontokba. A következő paranccsal nyissa meg a hosts fájlt, és állítsa be az `ag1-listener` állomásnév-feloldást az egyes gépeken.

    ```
    sudo vi /etc/hosts
    ```

    A **vi** szerkesztőben `i` írja be a szöveg beszúrásához, és `ag1-listener`egy üres sorba adja hozzá a . Ezután `ag1-listener` adja hozzá az IP melletti szóköz után.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    A **vi** szerkesztőből való kilépéshez először nyomja meg `:wq` az **Esc** billentyűt, majd írja be a parancsot a fájl megírásához, majd lépjen ki. Ezt minden csomóponton.

## <a name="test-the-listener-and-a-failover"></a>A figyelő és a feladatátvétel tesztelése

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Az SQL Server szolgáltatásba való bejelentkezés tesztelése a rendelkezésre állási csoport figyelőjével

1. Az SQLCMD segítségével jelentkezzen be az SQL Server elsődleges csomópontjára a rendelkezésre állási csoport figyelőjének nevével:

    - Használjon korábban létrehozott bejelentkezést, és cserélje le `<YourPassword>` a megfelelő jelszóval. Az alábbi példa `sa` az SQL Server kiszolgálóval létrehozott bejelentkezést használja.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Ellenőrizze annak a kiszolgálónak a nevét, amelyhez kapcsolódik. Futtassa a következő parancsot az SQLCMD-ben:

    ```sql
    SELECT @@SERVERNAME
    ```

    A kimenetnek meg kell jelennie az aktuális elsődleges csomóponton. Ennek akkor `VM1` kell lennie, ha még soha nem tesztelt feladatátvételt.

    Lépjen ki az SQL-munkamenetből a `exit` parancs beírásával.

### <a name="test-a-failover"></a>Feladatátvétel tesztelése

1. Futtassa a következő parancsot az `<VM2>` elsődleges kópia vagy egy másik kópia manuális feladatátvételéhez. Cserélje `<VM2>` le a kiszolgálónév értékére.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Ha ellenőrzi a korlátozásokat, látni fogja, hogy egy másik megkötés tlett hozzá a manuális feladatátvétel miatt:

    ```bash
    sudo pcs constraint list --full
    ```

    Látni fogja, hogy egy azonosítóval `cli-prefer-ag_cluster-master` ellátott megkötés tlett hozzá.

1. Távolítsa el a kényszert `cli-prefer-ag_cluster-master` az azonosítóval a következő paranccsal:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Ellenőrizze a fürt erőforrásait a paranccsal, `sudo pcs resource`és `<VM2>`látnia kell, hogy az elsődleges példány most van.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Az SQLCMD segítségével jelentkezzen be az elsődleges replikába a figyelő nevével:

    - Használjon korábban létrehozott bejelentkezést, és cserélje le `<YourPassword>` a megfelelő jelszóval. Az alábbi példa `sa` az SQL Server kiszolgálóval létrehozott bejelentkezést használja.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Ellenőrizze azt a kiszolgálót, amelyhez csatlakozik. Futtassa a következő parancsot az SQLCMD-ben:

    ```sql
    SELECT @@SERVERNAME
    ```

    Látnia kell, hogy most csatlakozik a virtuális gép, amely nem sikerült át.

## <a name="next-steps"></a>További lépések

Az Azure-beli terheléselosztókról a következő témakörben talál további információt:

> [!div class="nextstepaction"]
> [Terheléselosztó konfigurálása egy rendelkezésre állási csoporthoz az Azure SQL Server virtuális gépein](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
