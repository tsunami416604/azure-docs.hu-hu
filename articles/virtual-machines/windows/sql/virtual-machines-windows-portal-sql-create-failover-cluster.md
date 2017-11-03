---
title: "SQL Server FCI - Azure virtuális gépek |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan SQL Server feladatátvevő fürt példány létrehozásához Azure virtuális gépeken."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2017
ms.author: mikeray
ms.openlocfilehash: ec35b4a02c04d5b6d0bbf9049927529258c3825b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Azure virtuális gépeken futó SQL Server-példány feladatátvevő fürt konfigurálása

Ez a cikk azt ismerteti, hogy egy SQL Server feladatátvevő fürtbeli példány (FCI) létrehozása a Resource Manager modellt az Azure virtuális gépeken. Ez a megoldás használ [közvetlen tárolóhelyek a Windows Server 2016 Datacenter edition \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) szoftveres virtuális TÁROLÓHÁLÓZATTAL, amely a tároló (adatlemezek) szinkronizálja a csomópontok között (Azure virtuális gépeken), egy Windows-fürt. S2D 's new in Windows Server 2016.

Az alábbi ábra mutatja a teljes megoldás az Azure virtuális gépeken:

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

A fenti ábrán látható:

- Két Azure virtuális gépeken a Windows feladatátvevő fürtben. Ha egy virtuális gép feladatátvevő fürtben is nevezzük egy *fürtcsomópont*, vagy *csomópontok*.
- Minden virtuális gép két vagy több adatlemezek van.
- S2D szinkronizálja az adatokat az adatok lemezre, és megadja a szinkronizált adattároló mint egy tárolókészletet.
- A tárolókészlet megadja a feladatátvevő fürtre a fürt megosztott kötetei (CSV).
- Az SQL Server FCI fürtszerepkör az adatmeghajtók a fürt megosztott kötetei szolgáltatás használ.
- Egy Azure terheléselosztó a IP-cím ahhoz, hogy az SQL Server FCI-hez.
- Az Azure rendelkezésre állási csoport összes erőforrást tartalmazza.

   >[!NOTE]
   >Az ábrán vannak az összes Azure-erőforrások ugyanabban az erőforráscsoportban vannak.

S2D kapcsolatos részletekért lásd: [közvetlen tárolóhelyek a Windows Server 2016 Datacenter edition \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D kétféle típusú architektúrák - átszervezett és többszörösen összevont támogatja. Ez a dokumentum architektúrájáról többszörösen összevont. A többszörösen összevont infrastruktúra a tárolási helyezi a fürtözött alkalmazást futtató kiszolgálón. Ebben az architektúrában a tároló nem mindegyik SQL Server FCI csomópontján.

### <a name="example-azure-template"></a>Példa Azure-sablon alapján

A teljes megoldás az Azure-ban hozhat létre a sablonból. Példa egy sablon érhető el a Githubon [Azure gyors üzembe helyezési sablonokat](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ebben a példában nem tervezett, vagy bármely adott munkaterhelés tesztelve. A sablon egy SQL Server FCI a tartományhoz csatlakoztatott S2D tároló létrehozásához is futtathatja. Értékelje ki a sablont, és módosítsa a célokra.

## <a name="before-you-begin"></a>Előkészületek

Van néhány dolgot tudnia kell, és elolvashatja, amely kell helyen, mielőtt továbblép.

### <a name="what-to-know"></a>Tudnivalók a
Javasoljuk, hogy az alábbi technológiák működési ismerkedjen:

- [Windows-fürt technológiák](http://technet.microsoft.com/library/hh831579.aspx)
-  [SQL Server feladatátvevő fürt példányok](http://msdn.microsoft.com/library/ms189134.aspx).

Emellett rendelkeznie kell a következő technológiákat általános ismertetése:

- [A Windows Server 2016 közvetlen tárolóhelyek használatával Hyper átszervezett megoldás](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure erőforráscsoport-sablonok](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Mi szükséges

Ebben a cikkben lévő utasítások követése előtt már rendelkeznie kell:

- A Microsoft Azure-előfizetés.
- Azure virtuális gépeken futó Windows-tartományhoz.
- Objektumok létrehozása az Azure virtuális gép engedéllyel rendelkező fiók.
- Egy Azure-beli virtuális hálózat alhálózatnak megfelelő IP-címterének és a következő összetevőnél:
   - Mindkét virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Minden egyes FCI IP-címet.
- A DNS a Azure hálózaton, a tartományvezérlők mutató konfigurálva.

Az előfeltételek teljesülnek folytassa a a feladatátvevő fürt. Az első lépés a virtuális gépek létrehozásához.

## <a name="step-1-create-virtual-machines"></a>1. lépés: Virtuális gépek létrehozása

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com) az előfizetéshez.

1. [Egy Azure rendelkezésre állási csoport létrehozása](../tutorial-availability-sets.md).

   A rendelkezésre állási csoportok virtuális gépek beállítása tartalék tartományokban, és a tartományok frissítése. A rendelkezésre állási csoport gondoskodik arról, hogy az alkalmazás egyetlen ponton felmerülő hibákat, például a hálózati kapcsoló vagy kiszolgálók állvány teljesítménye nincs hatással.

   Ha létrehozta az erőforráscsoport nem a virtuális gépek, elvégezhető az Azure rendelkezésre állási csoport létrehozásakor. A rendelkezésre állási csoport létrehozása használata az Azure-portálon, tegye a következőket:

   - Az Azure portálon kattintson  **+**  Azure piactérről elemre. Keresse meg **rendelkezésre állási csoport**.
   - Kattintson a **rendelkezésre állási csoport**.
   - Kattintson a **Create** (Létrehozás) gombra.
   - Az a **rendelkezésre állási csoport létrehozása** panelen állítsa be a következő értékeket:
      - **Név**: a rendelkezésre állási csoport nevét.
      - **Előfizetés**: az Azure-előfizetést.
      - **Erőforráscsoport**: Ha egy meglévő csoportot szeretne, kattintson a **meglévő** és a legördülő listából válassza ki a csoportot. Máskülönben válassza **hozzon létre új** , és írja be a csoport nevét.
      - **Hely**: állítsa be a helyet, ha azt tervezi, a virtuális gépek létrehozásához.
      - **Tartományok fault**: használja az alapértelmezett (3).
      - **Tartományok frissítése**: használja az alapértelmezett (5).
   - Kattintson a **létrehozása** a rendelkezésre állási létrehozása.

1. A virtuális gépek a rendelkezésre állási csoport létrehozása.

   Az Azure rendelkezésre állási készlet két SQL Server virtuális gép kiépítéséhez. Útmutatásért lásd: [egy SQL Server rendszerű virtuális gép az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md).

   Mindkét virtuális gép helye:

   - Az azonos Azure erőforráscsoport, amely a rendelkezésre állási csoportban van.
   - Ugyanazon a hálózaton a tartományvezérlővel.
   - Az alhálózat IP-címterület elegendő a virtuális gépek és az összes példányoktól végül használhat ezen a fürtön.
   - Az Azure rendelkezésre állási készlet.   

      >[!IMPORTANT]
      >Nem állíthatók be vagy rendelkezésre állási csoport egy virtuális gép létrehozása után módosítsa.

   Kép kiválasztása a Azure piactérről. Használhatja a Piactéri lemezkép, amely tartalmazza a Windows Server és SQL Server, vagy csak a Windows Server. További információkért lásd: [áttekintése az SQL Server Azure virtuális gépeken](../../virtual-machines-windows-sql-server-iaas-overview.md)

   Az Azure katalógusában hivatalos SQL Server-rendszerképeit közé tartozik a telepített SQL Server-példány, valamint az SQL Server telepítési szoftver és a szükséges kulcs.

   Válassza ki a megfelelő lemezképet, hogyan szeretné kifizetni a SQL Server licence alapján:

   - **Használati licencelési kell fizetnie**: ezek a lemezképek perc költsége tartalmazza az SQL Server licencelési:
      - **A Windows Server Datacenter 2016 SQL Server 2016 Enterprise**
      - **Az SQL Server 2016 Standard a Windows Server Datacenter 2016**
      - **SQL Server 2016 fejlesztői a Windows Server Datacenter 2016**

   - **Bring your-saját-licencet (BYOL)**

      - **{BYOL} A Windows Server Datacenter 2016 SQL Server 2016 Enterprise**
      - **{BYOL} Az SQL Server 2016 Standard a Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server-példány. A feladatátvevő fürt és a S2D konfigurálását követően az SQL Server FCI létrehozásához szüksége lesz az előre telepített SQL Server-adathordozót.

   Másik lehetőségként használata Azure piactéren elérhető rendszerkép csak az operációs rendszer. Válasszon egy **Windows Server 2016 Datacenter** rendszerképet, telepítse az SQL Server FCI konfigurálása a feladatátvevő fürt és a S2D után. A kép nem tartalmaz az SQL Server telepítési adathordozóról. A telepítési adathordozó helyezze olyan helyre, ahol futtathatja az SQL Server telepítése, az egyes kiszolgálók.

1. Miután Azure hoz létre a virtuális gépek, minden virtuális gép RDP-csatlakozni.

   Amikor először csatlakozik egy virtuális gép RDP-, a számítógép megkérdezi kell felderíthetők a hálózaton lévő számítógép engedélyezéséhez. Kattintson a **Yes** (Igen) gombra.

1. Ha egy SQL Server-alapú virtuális gépek lemezképet használ, távolítsa el az SQL Server-példányt.

   - A **programok és szolgáltatások**, kattintson a jobb gombbal **Microsoft SQL Server 2016 (64 bites)** kattintson **Eltávolítás/módosítás**.
   - Kattintson a **eltávolítása**.
   - Válassza ki az alapértelmezett példányt.
   - Távolítsa el az összes szolgáltatás **adatbázismotor-szolgáltatások**. Ne távolítsa el az **közös szolgáltatások**. Tekintse meg az alábbi képen látható:

      ![Szolgáltatások eltávolítása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kattintson a **következő**, és kattintson a **eltávolítása**.

1. <a name="ports"></a>Nyissa meg a tűzfal portjait.

   Minden egyes virtuális gépen nyissa meg a következő portokat a Windows tűzfalon.

   | Cél | TCP-Port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port az SQL Server alapértelmezett példánya esetében. Ha lemezkép a gyűjteményből, ezt a portot automatikusan megnyílik.
   | Állapotmintáihoz | 59999 | Bármely nyitott TCP-portot. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotmintáihoz](#probe) és a fürt ezen a porton.  

1. Tároló hozzáadása a virtuális gép. Részletes információkért lásd: [tároló](../premium-storage.md).

   Mindkét virtuális gépek legalább két lemez szükséges.

   Csatlakoztassa a lemezeket nyers - nem NTFS formátumú lemezek.
      >[!NOTE]
      >Ha NTFS fájlrendszerű lemezek csatlakoztat, nem szabad jogosultsági ellenőrzéssel csak engedélyezheti a S2D.  

   Legalább két prémium szintű Storage (SSD lemezek) összes virtuális Géphez csatolása. Ajánlott legalább P30 (1 TB) lemezek.

   Set-állomás gyorsítótárazását a **írásvédett**.

   A tárolási kapacitás, éles környezetben használhat a a munkaterheléstől függ. A cikkben leírt értékei demonstrációs és tesztelésére.

1. [A virtuális gépek felvétele a már meglévő tartomány](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Miután a virtuális gépek létrehozása és konfigurálva, konfigurálhat a feladatátvevő fürt.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>2. lépés: A Windows feladatátvevő fürt konfigurálása S2D

A következő lépés a feladatátvevő fürt konfigurálása S2D. Ebben a lépésben a következő részlépések fogja végrehajtani:

1. Adja hozzá a Windows feladatátvételi fürtszolgáltatás
1. A fürt ellenőrzése
1. A feladatátvevő fürt létrehozása
1. A felhő tanúsító létrehozása
1. Tároló hozzáadása

### <a name="add-windows-failover-clustering-feature"></a>Adja hozzá a Windows feladatátvételi fürtszolgáltatás

1. Első lépésként csatlakozni az első virtuális gép RDP-, amely tagja a helyi rendszergazdák, és engedélyekkel rendelkezik objektumok létrehozásához az Active Directory tartományi fiók használatával. Ez a fiók használata a további konfigurálást.

1. [Vegye fel a Feladatátvételi fürtszolgáltatást minden egyes virtuális géphez](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Feladatátvételi fürtszolgáltatás telepítése a felhasználói felületen, tegye a következőket mindkét virtuális gépeken.
   - A **Kiszolgálókezelő**, kattintson a **kezelése**, és kattintson a **szerepkörök és szolgáltatások hozzáadása**.
   - A **hozzáadása szerepkörök és szolgáltatások varázsló**, kattintson a **következő** amíg elér **szolgáltatások kiválasztása**.
   - A **szolgáltatások kiválasztása**, kattintson a **feladatátvételi fürtszolgáltatás**. Az összes szükséges szolgáltatásokat és a felügyeleti eszközök közé tartozik. Kattintson a **szolgáltatások hozzáadása**.
   - Kattintson a **következő** majd **Befejezés** a szolgáltatás telepítéséhez.

   A PowerShell használatával a Feladatátvételi fürtszolgáltatás telepítéséhez futtassa a következő parancsfájlt egy rendszergazda PowerShell-munkamenetben a virtuális gépek egyik.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Referenciaként a következő lépéseket kövesse a 3. lépés [közvetlen tárolóhelyek használata a Windows Server 2016 Hyper átszervezett megoldás](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ez az útmutató utasításokat a hivatkozik [fürt ellenőrzése](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

A felhasználói felületén vagy a PowerShell segítségével a fürt ellenőrzése.

Ellenőrizze a fürt a felhasználói felületen, a következő lépések egyikét a virtuális gépek.

1. A **Kiszolgálókezelő**, kattintson a **eszközök**, majd kattintson a **Feladatátvevőfürt-kezelő**.
1. A **Feladatátvevőfürt-kezelő**, kattintson a **művelet**, majd kattintson a **konfiguráció ellenőrzése...** .
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók kiválasztása vagy a fürt**, adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások**, válassza a **csak a kijelölt tesztek futtatása**. Kattintson a **Tovább** gombra.
1. A **kijelölés tesztelése**, következők kivételével az összes teszt **tárolási**. Tekintse meg az alábbi képen látható:

   ![Tesztek ellenőrzése](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kattintson a **Tovább** gombra.
1. A **megerősítő**, kattintson a **következő**.

A **konfiguráció ellenőrzése varázsló** az ellenőrző tesztet.

A PowerShell segítségével a fürt ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazda PowerShell-munkamenetben a virtuális gépek egyik.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

A feladatátvevő fürt létrehozása után a fürt ellenőrzéséhez.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ez az útmutató hivatkozik [a feladatátvevő fürt létrehozása](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

A feladatátvevő fürt létrehozása, az alábbiak szükségesek:
- A virtuális gépek, amelyek a fürt csomópontjai képezik nevei.
- A feladatátvevő fürt nevét
- A feladatátvevő fürt IP-címet. A fürtcsomópontokkal azonos Azure virtuális hálózat és alhálózat nem használatos olyan IP-cím használható.

A következő PowerShell egy feladatátvevő fürtöt hoz létre. Frissítse a parancsfájlt a csomópontok (a virtuális gép neve) és egy szabad IP-cím az Azure virtuális hálózat nevét:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>A felhő tanú létrehozása

Felhő tanúsító is egy új fürt kvórum tanúsítójának tárolódnak az Azure Storage-Blobba. Ezzel eltávolítja a különálló virtuális gépek üzemeltetéséhez a tanúsító fájlmegosztás szükséges.

1. [A feladatátvevő fürt tanúsító felhő létrehozása](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. A blob-tároló létrehozása.

1. A tárelérési kulcsok és a tároló URL-cím mentése.

1. Konfigurálja a feladatátvevő fürt fürt kvórum tanúsító lemezét. Megtekintéséhez [konfigurálja a kvórum tanúsítóját a felhasználói felületen]. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) a felhasználói felületen.

### <a name="add-storage"></a>Tároló hozzáadása

A lemez S2D kell üres, partíciók vagy egyéb adatok nélkül. Tiszta lemezek hajtsa végre a [a jelen útmutató lépéseit](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Engedélyezési tároló közvetlen tárolóhelyek \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell lehetővé teszi, hogy a tárolóhelyek – közvetlen.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   A **Feladatátvevőfürt-kezelő**, most már megtekintheti a tárolókészlethez.

1. [Hozzon létre egy kötetet](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Az S2D funkcióit egyike, hogy automatikusan létrehoz egy tárolókészletet engedélyezése. Most már készen áll a kötet létrehozásához. A PowerShell-parancsmag segítségével `New-Volume` automatizálja a kötet létrehozási folyamata, beleértve a formázás, felvétele a fürtbe, és a fürt megosztott kötetei (CSV) létrehozása. Az alábbi példa létrehoz egy 800 gigabájt (GB) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Ez a parancs után egy 800 GB-os kötet fürterőforrásként csatlakoztatva. A kötet jelenleg `C:\ClusterStorage\Volume1\`.

   Az alábbi ábrán látható a S2D a fürt megosztott kötetei:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3. lépés: Feladatátvevő fürtön belüli feladatátvétel tesztelése

A Feladatátvevőfürt-kezelőben, győződjön meg arról, hogy a tárolási erőforrások áthelyezheti a többi fürtcsomóponton. Ha a feladatátvevő fürt kapcsolódás **Feladatátvevőfürt-kezelő** és a tárterület áthelyezését egyik csomópontról a másikra, készen áll az FCI konfigurálása.

## <a name="step-4-create-sql-server-fci"></a>4. lépés: Az SQL Server FCI létrehozása

Miután beállította a feladatátvevő fürt és a fürt-összetevők, beleértve a tárolási, az SQL Server FCI hozhat létre.

1. Az első virtuális gép RDP-csatlakozni.

1. A **Feladatátvevőfürt-kezelő**, győződjön meg arról, hogy minden fürt alapvető erőforrásai az első virtuális gépen. Szükség esetén a virtuális gép összes erőforrások áthelyezése.

1. Keresse meg a telepítési adathordozón. A virtuális gépet az Azure piactéren elérhető rendszerkép valamelyikét használja, ha az adathordozó itt található: `C:\SQLServer_<version number>_Full`. Kattintson a **telepítő**.

1. Az a **SQL Server telepítési központjának**, kattintson a **telepítési**.

1. Kattintson a **új SQL Server feladatátvevő fürt telepítése**. Kövesse a varázsló utasításait követve telepítse az SQL Server FCI.

   Az FCI adatkönyvtárak kell fürtözött tárhelyen. A S2D már nem egy megosztott lemez, de a csatlakoztatási pont egy kötet minden kiszolgálón. S2D szinkronizálja a kötet mindkét csomópont között. A kötet a fürt jeleníti meg egy megosztott fürtkötethez. A fürt megosztott kötetei szolgáltatás csatlakoztatási pontot használ az adatok könyvtárakat.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. A varázsló befejezése után a telepítő telepíti egy SQL Server FCI az első csomóponton.

1. A telepítő sikeres telepítése után az FCI az első csomóponton a második csomópont RDP-csatlakozni.

1. Nyissa meg a **SQL Server telepítési központjának**. Kattintson a **telepítési**.

1. Kattintson a **csomópont hozzáadása az SQL Server rendszerű feladatátvevő fürtök**. Kövesse az SQL server telepítése, és ez a kiszolgáló hozzáadása az FCI a varázsló utasításait.

   >[!NOTE]
   >Ha SQL Server Azure piactér gyűjtemény kép használt, SQL Server-eszközök szerepeltek a lemezképpel. Ha nem használja ezt a lemezképet, az SQL Server-eszközök külön kell telepítenie. Lásd: [töltse le az SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>5. lépés: Az Azure terheléselosztó létrehozása

Az Azure virtuális gépeken fürtök használatával a terheléselosztó IP-címet, amely egyszerre csak egy fürtcsomóponton kell tartsa. Ebben a megoldásban a terheléselosztó IP-címét tartalmazza az SQL Server FCI-hez.

[Hozza létre és konfigurálja az Azure terheléselosztó](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása az Azure portálon

A terheléselosztó létrehozása:

1. Az Azure portálon lépjen az erőforráscsoport, a virtuális gépekkel.

1. Kattintson a **+ Hozzáadás**. Keresés a piactérre **terheléselosztó**. Kattintson a **terheléselosztó**.

1. Kattintson a **Create** (Létrehozás) gombra.

1. Adja meg a terheléselosztóhoz:

   - **Név**: A nevet, amely azonosítja a terheléselosztó hasonló adataival.
   - **Típus**: lehet, hogy a terheléselosztó nyilvános vagy titkos. A privát terheléselosztó az azonos virtuális hálózaton belül érhetők el. Az Azure alkalmazások használhatják a saját terheléselosztó. Ha az alkalmazásnak az SQL Server elérésére közvetlenül az interneten keresztül, használjon egy nyilvános terheléselosztó.
   - **Virtuális hálózati**: és a virtuális gépek ugyanahhoz a hálózathoz.
   - **Alhálózati**: a virtuális gépek azonos alhálózaton.
   - **Magánhálózati IP-cím**: az SQL Server FCI fürt hálózati erőforráshoz hozzárendelt azonos IP-cím.
   - **előfizetés**: az Azure-előfizetést.
   - **Erőforráscsoport**: ugyanabban az erőforráscsoportban használják a virtuális gépeket.
   - **Hely**: Azure ugyanott használják a virtuális gépeket.
   Tekintse meg az alábbi képen látható:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurálja a terheléselosztó háttérkészletéből

1. Térjen vissza az Azure-erőforráscsoportot a virtuális gépekkel, és keresse meg az új terheléselosztó. Előfordulhat, hogy az erőforráscsoport a nézet frissítéséhez. Kattintson a terheléselosztó hasonló adataival.

1. A load balancer paneljén kattintson **háttérkészletek**.

1. Kattintson a **+ Hozzáadás** a háttérkészlet hozzáadásával.

1. Adjon meg egy nevet a háttérkészlet.

1. Kattintson a **adja hozzá a virtuális gépek**.

1. Az a **válassza ki a virtuális gépek** panelen kattintson a **rendelkezésre állási csoport kiválasztása**.

1. Válassza ki a rendelkezésre állási csoport, hogy az SQL Server virtuális gépek helyezte-e.

1. Az a **válassza ki a virtuális gépek** panelen kattintson a **válassza ki a virtuális gépek**.

   Az Azure-portálon az alábbi képen hasonlóan kell kinéznie:

   ![CreateLoadBalancerBackEnd](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. Kattintson a **válasszon** a a **válassza ki a virtuális gépek** panelen.

1. Kattintson a **OK** kétszer.

### <a name="configure-a-load-balancer-health-probe"></a>Terheléselosztói állapotfigyelő mintavétel konfigurálása

1. A load balancer paneljén kattintson **állapot-mintavételi csomagjai**.

1. Kattintson a **+ Hozzáadás**.

1. Az a **Hozzáadás állapotmintáihoz** panelen <a name="probe"> </a>beállításához a mintavételi paraméterek:

   - **Név**: a állapotmintáihoz nevét.
   - **Protokoll**: TCP.
   - **Port**: az elérhető TCP-port beállítása. Ez a port egy megnyitott tűzfal portra van szüksége. Használja a [ugyanazt a portot](#ports) állítja be a állapotmintáihoz a tűzfalon.
   - **Időköz**: 5 másodperc.
   - **Sérült küszöbérték**: 2 egymást követő hibák.

1. Kattintson az OK gombra.

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A load balancer paneljén kattintson **terheléselosztási szabályok**.

1. Kattintson a **+ Hozzáadás**.

1. A terheléselosztási szabályok paraméterek beállítása:

   - **Név**: a terheléselosztási szabályok nevét.
   - **Előtérbeli IP-cím**: az IP-címet használja az SQL Server FCI fürt hálózati erőforráshoz.
   - **Port**: az SQL Server FCI TCP-port beállítása. Az alapértelmezett példány portja az 1433-as.
   - **A háttérportot**: ezt az értéket használja ugyanazt a portot, mint a **Port** értéke, ha engedélyezi a **fix IP-Címek (közvetlen kiszolgálói válasz)**.
   - **Háttérkészlet**: a korábban megadott értékektől háttér-készlet nevét használja.
   - **Állapotmintáihoz**: használja a korábban megadott értékektől állapotmintáihoz.
   - **Munkamenet megőrzését**: nincs.
   - **Üresjárat időkorlátja (perc)**: 4.
   - **Lebegőpontos IP (közvetlen kiszolgálói válasz)**: engedélyezve

1. Kattintson az **OK** gombra.

## <a name="step-6-configure-cluster-for-probe"></a>6. lépés: A fürt mintavétel konfigurálása

Állítsa a fürt mintavételi portot paramétert a PowerShellben.

Hogy a fürt mintavételi portot paraméter, frissítse a változók a következő parancsfájlban a környezet értékeivel. Távolítsa el a csúcsos zárójelek `<>` a parancsfájlból. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A fenti parancsfájlban a környezet értékeinek beállítását. Az alábbi lista ismerteti a értékeket:

   - `<Cluster Network Name>`: A hálózati Windows Server feladatátvevő fürt nevét. A **Feladatátvevőfürt-kezelő** > **hálózatok**, kattintson a jobb gombbal a hálózaton, és kattintson a **tulajdonságok**. A megfelelő érték a **neve** a a **általános** fülre. 

   - `<SQL Server FCI IP Address Resource Name>`: Az SQL Server FCI IP-cím erőforrás neve. A **Feladatátvevőfürt-kezelő** > **szerepkörök**, az SQL Server FCI szerepkör alatt a **kiszolgálónév**, kattintson a jobb gombbal az IP-cím erőforrás, és kattintson a **Tulajdonságok**. A megfelelő érték a **neve** a a **általános** fülre. 

   - `<ILBIP>`: A ILB IP-címet. Ez a cím konfigurálva van az Azure-portálon ILB előtér-címként. Ez egyben az SQL Server FCI IP-címet. Megtalálhatja az **Feladatátvevőfürt-kezelő** ugyanazon az oldalon tulajdonságok hol található a `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: A rendszerállapot terheléselosztói mintavétel a megadott mintavételi portot van. Minden nem használt TCP-port használata esetén érvényes. 

>[!IMPORTANT]
>Az alhálózati maszkot a fürt paraméternek kell lennie a TCP IP szórási címre: `255.255.255.255`.

Miután beállította a fürt mintavétel minden a fürt paramétereket a PowerShell. Futtassa a következő parancsfájlt:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>: 7. lépés az FCI-feladatátvétel

A fürt működését érvényesítéséhez FCI feladatátvételi tesztje. Hajtsa végre az alábbi lépéseket:

1. Csatlakozás az SQL Server FCI fürtcsomópontok RDP-egyike.

1. Nyissa meg **Feladatátvevőfürt-kezelő**. Kattintson a **szerepkörök**. Figyelje meg, melyik csomóponton az SQL Server FCI szerepkör.

1. Kattintson a jobb gombbal az SQL Server FCI szerepkör.

1. Kattintson a **áthelyezése** kattintson **lehető legalkalmasabb csomópontra**.

**Feladatátvevőfürt-kezelő** jeleníti meg a szerepkört és erőforrást kapcsolat nélküli módba. Az erőforrások majd helyezze át, és a többi csomóponton online állapotba.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat tesztelése, jelentkezzen be egy másik virtuális gép ugyanazon a virtuális hálózaton. Nyissa meg **SQL Server Management Studio** , és csatlakozzon az SQL Server FCI nevét.

>[!NOTE]
>Ha szükséges, akkor [töltse le az SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Korlátozások
Az Azure virtuális gépeken Microsoft elosztott tranzakciók koordinátora (DTC) nem támogatott a példányoktól, mert az RPC-portot a terheléselosztó által nem támogatott.

## <a name="see-also"></a>Lásd még:

[A telepítő S2D a távoli asztal (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-összevont megoldás a tárolóhelyek közvetlen](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Közvetlen tárolóhelyek áttekintése](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[S2D SQL Server támogatása](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
