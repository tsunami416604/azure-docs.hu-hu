---
title: SQL Server-os – Azure Virtual Machines | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozható létre SQL Server feladatátvevő fürt példánya az Azure Virtual Machinesban.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: b30ccbcba0b2126d1fe1abce9ae67a55ce25f601
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170253"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>SQL Server feladatátvevő fürt példányának konfigurálása az Azure-ban Virtual Machines

Ez a cikk azt ismerteti, hogyan hozható létre SQL Server feladatátvevő fürt példánya (verzió) az Azure Virtual Machines szolgáltatásban Resource Manager-modellben. Ez a megoldás a [Windows Server 2016 Datacenter edition Közvetlen tárolóhelyek \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) szoftvert használja, amely a Windows-fürt csomópontjai (Azure-beli virtuális gépek) közötti tárolót (adatlemezeket) szinkronizálja. A S2D a Windows Server 2016 újdonsága.

Az alábbi ábrán az Azure Virtual Machines teljes megoldása látható:

![Rendelkezésreállási csoport](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Az előző ábrán az alábbiak láthatók:

- Két Azure-beli virtuális gép egy Windows feladatátvevő fürtben. Ha egy virtuális gép feladatátvevő fürtben van, akkor azt is nevezik *fürtcsomópont*vagy *csomópontoknak*.
- Mindegyik virtuális gép két vagy több adatlemezzel rendelkezik.
- A S2D szinkronizálja az adatokat az adatlemezen, és a szinkronizált tárolót tárolóként jeleníti meg.
- A Storage-készlet egy fürt megosztott kötetét (CSV) jeleníti meg a feladatátvevő fürtben.
- Az SQL Server-os szerepköralapú fürt szerepkör a CSV-t használja az adatmeghajtókhoz.
- Egy Azure Load Balancer, amely a SQL Server-alapú IP-címet fogja tárolni.
- Az Azure-beli rendelkezésre állási csoport minden erőforrást tárol.

   >[!NOTE]
   >A diagramon lévő összes Azure-erőforrás ugyanabban az erőforráscsoporthoz van.

A S2D kapcsolatos részletekért lásd: [Windows Server 2016 Datacenter edition Közvetlen tárolóhelyek \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

A S2D két típusú architektúrát támogat – a konvergens és a Hyper-konvergált. A jelen dokumentumban található architektúra Hyper-konvergált. A Hyper-konvergens infrastruktúra a tárolót a fürtözött alkalmazást futtató kiszolgálókon helyezi el. Ebben az architektúrában a tárterület minden SQL Server-os csomóponton található.

## <a name="licensing-and-pricing"></a>Licencelés és díjszabás

Az Azure Virtual Machines-on a pay (TB) vagy a saját licencek (BYOL) virtuálisgép-rendszerképeinek használatával SQL Server licenceket használhat. A kiválasztott rendszerkép típusa befolyásolja, hogy milyen díjat számítunk fel.

Az Azure-beli SQL Server TB licencelési szolgáltatásának feladatátvételi fürtszolgáltatása a (z) összes, a (z) és a passzív csomópontokat tartalmazó összes csomópont esetében felszámítja az Virtual Machines. További információ: [SQL Server Enterprise Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

A frissítési garanciával rendelkező Nagyvállalati Szerződésokkal rendelkező ügyfelek jogosultak az egyes aktív csomópontok egy ingyenes passzív és 1. szintű csomópontjának használatára. Ahhoz, hogy használhassa ezt az előnyt az Azure-ban, használja a BYOL virtuálisgép-rendszerképeket, majd ugyanazt a licencet használja a (z) és a (z)-es verzió aktív és passzív További információ: [nagyvállalati szerződés](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Az Azure Virtual Machines SQL Server TB és BYOL-licencelésének összehasonlításához tekintse meg az SQL-alapú [virtuális gépek első lépéseivel](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)foglalkozó témakört.

A licencelési SQL Serverával kapcsolatos teljes információkért tekintse meg a [díjszabást](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Példa Azure-sablonra

A teljes megoldás az Azure-ban egy sablonból is létrehozható. Egy sablon például elérhető a GitHub [Azure gyorsindító-sablonokban](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ez a példa nincs megtervezve vagy tesztelve az adott számítási feladatokhoz. A sablon futtatásával létrehozhatja a tartományhoz csatlakoztatott S2D-tárolóval rendelkező SQL Server-t. Kiértékelheti a sablont, és módosíthatja azt a célra.

## <a name="before-you-begin"></a>Előzetes teendők

Van néhány dolog, amit tudnia kell, és a folytatás előtt néhány dolog szükséges.

### <a name="what-to-know"></a>Tudnivalók
A következő technológiákat kell megismernie:

- [Windows-fürtök technológiái](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányai](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Az egyik fontos különbség az, hogy egy Azure IaaS VM vendég feladatátvevő fürtön egyetlen NIC-kiszolgálót (fürtcsomópont) és egyetlen alhálózatot ajánlunk. Az Azure-hálózatok fizikai redundanciával rendelkeznek, így nincs szükség további hálózati adapterre és alhálózatra az Azure IaaS virtuális gépek vendégfürtjén. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein. 

Emellett általános ismeretekkel kell rendelkeznie az alábbi technológiákról:

- [Hyper-konvergens megoldás Közvetlen tárolóhelyek használatával a Windows Server 2016-ben](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg az Azure Virtual Machines szolgáltatásban SQL Server feladatátvevő fürt példányai csak a [SQL Server IaaS-ügynök bővítményének](virtual-machines-windows-sql-server-agent-extension.md) [egyszerűsített](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) felügyeleti módjával támogatottak. Távolítsa el a teljes bővítményt a feladatátvevő fürtben részt vevő virtuális gépekről, majd regisztrálja őket az SQL VM erőforrás-szolgáltató `lightweight` módban. A teljes bővítmény olyan funkciókat támogat, mint például az automatikus biztonsági mentés, a javítások és a speciális portálok kezelése. Ezek a funkciók nem fognak működni az SQL virtuális gépeken, miután az ügynököt egyszerűsített felügyeleti módban újratelepítette.

### <a name="what-to-have"></a>Mi a teendő

A cikkben szereplő utasítások követése előtt a következőkkel kell rendelkeznie:

- Microsoft Azure előfizetés.
- Egy Windows-tartomány az Azure Virtual Machines szolgáltatásban.
- Egy olyan fiók, amely engedéllyel rendelkezik objektumok létrehozásához az Azure-beli virtuális gépen.
- Egy Azure-beli virtuális hálózat és alhálózat, amely elegendő IP-címmel rendelkezik a következő összetevőkhöz:
   - Mindkét virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Egy IP-cím minden egyes egyes adattömbhöz.
- DNS konfigurálva az Azure-hálózaton, amely a tartományvezérlőkre mutat.

Ezeknek az előfeltételeknek a végrehajtásával folytathatja a feladatátvételi fürt felépítésekor. Első lépésként hozza létre a virtuális gépeket.

## <a name="step-1-create-virtual-machines"></a>1\. lépés: virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az előfizetésével.

1. [Hozzon létre egy Azure-beli rendelkezésre állási készletet](../tutorial-availability-sets.md).

   A rendelkezésre állási csoport a tartalék tartományok és a frissítési tartományok között csoportosítja a virtuális gépeket. A rendelkezésre állási csoport gondoskodik arról, hogy az alkalmazást egyetlen meghibásodási pont sem befolyásolja, például a hálózati kapcsoló vagy a kiszolgálók rack egysége.

   Ha nem hozta létre az erőforráscsoportot a virtuális gépek számára, akkor azt egy Azure-beli rendelkezésre állási csoport létrehozásakor végezze el. Ha a Azure Portal használatával hozza létre a rendelkezésre állási készletet, hajtsa végre a következő lépéseket:

   - Az Azure Portalban kattintson a **+** elemre az Azure Marketplace megnyitásához. Keressen rá a **rendelkezésre állási csoportra**.
   - Kattintson a **rendelkezésre állási csoport**elemre.
   - Kattintson a  **Create** (Létrehozás) gombra.
   - A **rendelkezésre állási csoport létrehozása** panelen állítsa be a következő értékeket:
      - **Name (név**): a rendelkezésre állási csoport neve.
      - **Előfizetés**: az Azure-előfizetése.
      - **Erőforráscsoport**: Ha meglévő csoportot szeretne használni, kattintson a **meglévő használata** elemre, és válassza ki a csoportot a legördülő listából. Ellenkező esetben válassza az **új létrehozása** elemet, és adja meg a csoport nevét.
      - **Hely**: állítsa be azt a helyet, ahol létre szeretné hozni a virtuális gépeket.
      - Tartalék **tartományok**: használja az alapértelmezett (3) értéket.
      - **Frissítési tartományok**: használja az alapértelmezett (5) értéket.
   - A rendelkezésre állási csoport létrehozásához kattintson a **Létrehozás** gombra.

1. Hozza létre a virtuális gépeket a rendelkezésre állási csoportból.

   Hozzon létre két SQL Server virtuális gépet az Azure rendelkezésre állási készletében. Útmutatásért lásd: [SQL Server virtuális gép kiépítése a Azure Portalban](virtual-machines-windows-portal-sql-server-provision.md).

   Mindkét virtuális gép elhelyezése:

   - Ugyanabban az Azure-erőforráscsoportban, amelyben a rendelkezésre állási csoport található.
   - A tartományvezérlővel megegyező hálózaton.
   - Olyan alhálózaton, amely a virtuális gépekhez elegendő IP-címmel rendelkezik, és a fürtön esetlegesen használt összes FCIs.
   - Az Azure rendelkezésre állási készletében.   

      >[!IMPORTANT]
      >A virtuális gép létrehozása után a rendelkezésre állási csoport nem állítható be vagy nem módosítható.

   Válasszon egy rendszerképet az Azure piactéren. Használhat olyan Piactéri rendszerképet, amely tartalmazza a Windows Servert és a SQL Servert, vagy csak a Windows Servert. Részletekért lásd: a [SQL Server áttekintése az Azure](virtual-machines-windows-sql-server-iaas-overview.md) -ban Virtual machines

   Az Azure katalógusában található hivatalos SQL Server rendszerképek közé tartozik egy telepített SQL Server példány, valamint a SQL Server telepítési szoftver és a szükséges kulcs.

   Válassza ki a megfelelő képet aszerint, hogy hogyan szeretné fizetni a SQL Server licencért:

   - **Fizetés/használati licencelés**: a lemezképek másodpercenkénti díja tartalmazza a SQL Server licencelést:
      - **SQL Server 2016 Enterprise Windows Server Datacenter 2016**
      - **SQL Server 2016 standard Windows Server Datacenter 2016 rendszeren**
      - **SQL Server 2016 Developer a Windows Server Datacenter 2016**

   - **Saját licenc használata (BYOL)**

      - **BYOL SQL Server 2016 Enterprise Windows Server Datacenter 2016**
      - **BYOL SQL Server 2016 standard Windows Server Datacenter 2016 rendszeren**

   >[!IMPORTANT]
   >A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server példányt. A feladatátvevő fürt és a S2D konfigurálása után az előre telepített SQL Server adathordozó létrehozásához létre kell hoznia a SQL Server.

   Azt is megteheti, hogy csak az operációs rendszert használja az Azure Marketplace-lemezképek használatára. Válasszon egy **Windows Server 2016 Datacenter** -rendszerképet, és telepítse a SQL Server a következőt a feladatátvevő fürt és a S2D konfigurálása után. Ez a rendszerkép nem tartalmaz SQL Server telepítési adathordozót. Helyezze a telepítési adathordozót egy olyan helyre, ahol futtathatja az SQL Server telepítést az egyes kiszolgálókon.

1. Miután az Azure létrehozta a virtuális gépeket, csatlakozzon az összes RDP-vel rendelkező virtuális géphez.

   Amikor először csatlakozik egy RDP-vel rendelkező virtuális géphez, a számítógép megkérdezi, hogy szeretné-e, hogy a számítógép felderíthető legyen a hálózaton. Kattintson a **Yes** (Igen) gombra.

1. Ha a SQL Server-alapú virtuálisgép-rendszerképek egyikét használja, távolítsa el a SQL Server példányt.

   - A **programok és szolgáltatások**területen kattintson a jobb gombbal a **Microsoft SQL Server 2016 (64 bites)** elemre, majd kattintson az **Eltávolítás/módosítás**parancsra.
   - Kattintson az **Eltávolítás**gombra.
   - Válassza ki az alapértelmezett példányt.
   - Távolítsa el az összes funkcióját az **adatbázismotor-szolgáltatások**területen. Ne távolítsa el a **megosztott szolgáltatásokat**. Tekintse meg a következő képet:

      ![Szolgáltatások eltávolítása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kattintson a **tovább**, majd az **Eltávolítás**gombra.

1. <a name="ports"></a>Nyissa meg a tűzfal portjait.

   Minden virtuális gépen nyissa meg a következő portokat a Windows tűzfalon.

   | Rendeltetés | TCP-port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port a SQL Server alapértelmezett példányaihoz. Ha a katalógusból rendszerképet használt, a rendszer automatikusan megnyitja a portot.
   | Állapotadat-mintavétel | 59999 | Bármilyen nyitott TCP-port. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotának](#probe) mintavételét és a fürtöt, hogy ezt a portot használja.  

1. Adja hozzá a tárolót a virtuális géphez. Részletes információkért lásd: [tár hozzáadása](../disks-types.md).

   Mindkét virtuális gépnek legalább két adatlemezre van szüksége.

   Nyers lemezek csatolása – nem NTFS fájlrendszerű lemezek.
      >[!NOTE]
      >Ha NTFS fájlrendszerű lemezeket csatlakoztat, csak a lemezre vonatkozó jogosultság-ellenőrzés nélküli S2D engedélyezheti.  

   Csatoljon legalább két prémium SSD-t az egyes virtuális gépekhez. Legalább P30 (1 TB) lemezt ajánlunk.

   Állítsa be a gazdagép gyorsítótárazását **írásvédett**értékre.

   Az éles környezetekben használt tárolókapacitás a munkaterheléstől függ. A cikkben ismertetett értékek a demonstrációra és tesztelésre szolgálnak.

1. [Adja hozzá a virtuális gépeket a már meglévő tartományhoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

A virtuális gépek létrehozása és konfigurálása után beállíthatja a feladatátvevő fürtöt.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>2\. lépés: a Windows feladatátvevő fürt konfigurálása a S2D

A következő lépés a feladatátvevő fürt konfigurálása a S2D. Ebben a lépésben a következő allépéseket fogja végrehajtani:

1. Windows feladatátvételi fürtszolgáltatás hozzáadása
1. A fürt ellenőrzése
1. A feladatátvevő fürt létrehozása
1. A tanúsító felhő létrehozása
1. Tárhely hozzáadása

### <a name="add-windows-failover-clustering-feature"></a>Windows feladatátvételi fürtszolgáltatás hozzáadása

1. A kezdéshez csatlakozzon az első virtuális géphez RDP használatával olyan tartományi fiókkal, amely a helyi Rendszergazdák csoport tagja, és jogosult objektumok létrehozására a Active Directoryban. Ezt a fiókot használja a többi konfigurációhoz.

1. [Adja hozzá a feladatátvételi fürtszolgáltatást az egyes virtuális gépekhez](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Ha a felhasználói felületen szeretné telepíteni a feladatátvételi fürtszolgáltatást, hajtsa végre a következő lépéseket mindkét virtuális gépen.
   - A **Kiszolgálókezelőben**kattintson a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása**elemre.
   - A **szerepkörök és szolgáltatások hozzáadása varázslóban**kattintson a **tovább** gombra, amíg **ki nem választja a funkciókat**.
   - A **szolgáltatások kiválasztása**területen kattintson a **feladatátvételi fürtszolgáltatás**elemre. Adja meg az összes szükséges funkciót és a felügyeleti eszközöket. Kattintson a **szolgáltatások hozzáadása**elemre.
   - Kattintson a **tovább** gombra, majd a funkciók telepítéséhez kattintson a **Befejezés** gombra.

   A feladatátvételi fürtszolgáltatás PowerShell-lel történő telepítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

A következő lépések követik a Hyper-konvergált megoldás 3. lépésében található utasításokat a [Windows Server 2016 közvetlen tárolóhelyek használatával](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ez az útmutató a [fürt ellenőrzése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)szakaszban található utasításokra vonatkozik.

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell-lel.

A fürt felhasználói felülettel való ellenőrzéséhez hajtsa végre az alábbi lépéseket az egyik virtuális gépen.

1. A **Kiszolgálókezelőben**kattintson az **eszközök**, majd a **Feladatátvevőfürt-kezelő**elemre.
1. A **Feladatátvevőfürt-kezelő**kattintson a **művelet**, majd a **Konfiguráció ellenőrzése..** . elemre.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése lapon**írja be mindkét virtuális gép nevét.
1. A **tesztelési beállítások**lapon válassza a **csak a kiválasztott tesztek futtatása**lehetőséget. Kattintson a **Tovább** gombra.
1. A **tesztelés kiválasztásakor**adja meg az összes tesztet a **tárterület**kivételével. Tekintse meg a következő képet:

   ![Tesztek ellenőrzése](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kattintson a **Tovább** gombra.
1. **Megerősítéskor**kattintson a **tovább**gombra.

A **Konfiguráció ellenőrzése varázsló** futtatja az ellenőrző teszteket.

A fürt a PowerShell-lel való ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

A fürt érvényesítése után hozza létre a feladatátvevő fürtöt.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ez [az útmutató a feladatátvevő fürt létrehozására](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster)utal.

A feladatátvevő fürt létrehozásához a következők szükségesek:
- A fürtcsomópontok részévé váló virtuális gépek nevei.
- A feladatátvevő fürt neve
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem szerepel ugyanazon az Azure-beli virtuális hálózaton és az alhálózaton, mint a fürtcsomópontok.

#### <a name="windows-server-2008-2016"></a>Windows Server 2008-2016

A következő PowerShell egy feladatátvevő fürtöt hoz létre a **Windows Server 2008-2016**-hez. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure VNET:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

A következő PowerShell egy feladatátvevő fürtöt hoz létre a Windows Server 2019-hez.  További információkért tekintse át a következőt [: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure VNET:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása

A Felhőbeli tanúsító a fürtözött kvórum tanúsító új típusa, amelyet egy Azure Storage Blob tárol. Ez megszünteti a tanúsító megosztást üzemeltető különálló virtuális gép igényét.

1. [Hozzon létre egy Felhőbeli tanúsító a feladatátvevő fürthöz](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Hozzon létre egy BLOB-tárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címét.

1. Konfigurálja a feladatátvevő fürtöt tanúsító kvórumot. Lásd: [a kvórum tanúsító beállítása a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) a kezelőfelületen.

### <a name="add-storage"></a>Tárhely hozzáadása

A S2D tartozó lemezeknek üresnek kell lenniük, és partíciók vagy egyéb adathalmazok nélkül kell lenniük. A lemezek tisztításához kövesse az [útmutató lépéseit](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. A [közvetlen tárolóhelyek engedélyezése \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell lehetővé teszi a közvetlen tárolóhelyek használatát.  

   ```powershell
   Enable-ClusterS2D
   ```

   **Feladatátvevőfürt-kezelő**a Storage-készletet láthatja.

1. [Hozzon létre egy kötetet](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   A S2D egyik funkciója, hogy automatikusan létrehoz egy tárolási készletet, amikor engedélyezi azt. Most már készen áll egy kötet létrehozására. A PowerShell-parancsmagot `New-Volume` automatizálja a kötet létrehozásának folyamatát, beleértve a formázást, a fürthöz való hozzáadást és a fürt megosztott kötetének (CSV) létrehozását. A következő példa egy 800 gigabájt (GB) CSV-fájlt hoz létre.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   A parancs végrehajtása után egy 800 GB-os kötet van csatlakoztatva fürterőforrásként. A kötet `C:\ClusterStorage\Volume1\`.

   A következő ábrán egy fürt megosztott kötete látható a S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3\. lépés: feladatátvevő fürt feladatátvételének tesztelése

A Feladatátvevőfürt-kezelőban ellenőrizze, hogy át tudja-e helyezni a tárolási erőforrást a másik fürtcsomóponton. Ha **Feladatátvevőfürt-kezelő** használatával tud csatlakozni a feladatátvevő fürthöz, és áthelyezi a tárolót az egyik csomópontról a másikra, készen áll a következő konfigurálására:.

## <a name="step-4-create-sql-server-fci"></a>4\. lépés: létrehozás SQL Server

Miután konfigurálta a feladatátvevő fürtöt és a fürt összes összetevőjét, beleértve a Storage-t, létrehozhatja a SQL Server.

1. Kapcsolódjon az első virtuális géphez RDP használatával.

1. **Feladatátvevőfürt-kezelő**ellenőrizze, hogy az összes fürt alapvető erőforrása az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást erre a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a `C:\SQLServer_<version number>_Full` helyen található. Kattintson a **telepítés**elemre.

1. A **SQL Server telepítési központban**kattintson a **telepítés**elemre.

1. Kattintson az **új SQL Server feladatátvevő fürt telepítése**elemre. A varázsló utasításait követve telepítse a SQL Server-t.

   A (z)-es adatkönyvtárak fürtözött tárolón kell lenniük. A S2D nem egy megosztott lemez, hanem egy, az egyes kiszolgálókon lévő kötetre mutató csatlakoztatási pont. A S2D mindkét csomópont között szinkronizálja a kötetet. A kötet a fürt megosztott kötete jelenik meg. Használja a CSV csatlakoztatási pontot az adatkönyvtárakhoz.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. A varázsló befejezése után a telepítő az első csomóponton egy SQL Server-t fog telepíteni.

1. Miután a telepítő sikeresen telepítette a-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**. Kattintson a **telepítés**elemre.

1. Kattintson **a csomópont hozzáadása SQL Server feladatátvevő fürthöz**elemre. A varázsló utasításait követve telepítse az SQL Servert, és adja hozzá ezt a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Server használatával Azure Marketplace-katalógust használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használta ezt a rendszerképet, telepítse külön a SQL Server-eszközöket. Lásd: [SQL Server Management Studio letöltése (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>5\. lépés: az Azure Load Balancer létrehozása

Az Azure Virtual Machines szolgáltatásban a fürtök egy terheléselosztó használatával egy olyan IP-címet tárolhatnak, amelynek egyszerre egy fürtcsomóponton kell lennie. Ebben a megoldásban a terheléselosztó a SQL Server-es verzió IP-címét tárolja.

[Hozzon létre és konfiguráljon egy Azure Load balancert](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása a Azure Portal

A terheléselosztó létrehozása:

1. A Azure Portalban nyissa meg az erőforráscsoportot a virtuális gépekkel.

1. Kattintson a **+Hozzáadás** gombra. Keresse meg **Load Balancer**a piactéren. Kattintson **Load Balancer**.

1. Kattintson a  **Create** (Létrehozás) gombra.

1. A terheléselosztó konfigurálása a alábbiakkal:

   - **Name (név**): a terheléselosztó azonosítására szolgáló név.
   - **Típus**: a terheléselosztó lehet nyilvános vagy privát. A privát terheléselosztó a VNET belülről is elérhető. A legtöbb Azure-alkalmazás saját Load balancert használhat. Ha az alkalmazásnak közvetlenül az interneten keresztül kell SQL Servera, használjon nyilvános Load balancert.
   - **Virtual Network**: ugyanaz a hálózat, mint a virtuális gépek.
   - **Alhálózat**: a virtuális gépekkel megegyező alhálózat.
   - **Magánhálózati IP-cím**: ugyanazt az IP-címet rendelte hozzá, amelyet az SQL Server-es fürt hálózati erőforrásához rendelt.
   - **előfizetés**: az Azure-előfizetése.
   - **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, mint a virtuális gépeket.
   - **Hely**: használja ugyanazt az Azure-helyet, mint a virtuális gépeket.
   Tekintse meg a következő képet:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>A terheléselosztó háttér-készletének konfigurálása

1. Térjen vissza az Azure-erőforráscsoporthoz a virtuális gépekkel, és keresse meg az új Load balancert. Előfordulhat, hogy frissítenie kell a nézetet az erőforráscsoporthoz. Kattintson a terheléselosztó elemre.

1. Kattintson a **háttér-készletek** elemre, majd kattintson a **+ Hozzáadás** elemre a háttérbeli készlet hozzáadásához.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk**területen jelölje be a **virtuális gép** elemet, majd válassza ki azokat a virtuális gépeket, amelyek fürtcsomópontokként fognak részt venni. Ügyeljen arra, hogy minden olyan virtuális gépet tartalmazzon, amely a (z)-t fogja üzemeltetni. 

1. A háttér-készlet létrehozásához kattintson **az OK** gombra.

### <a name="configure-a-load-balancer-health-probe"></a>Terheléselosztó állapot-mintavételének konfigurálása

1. A terheléselosztó panelen kattintson az **állapot**-mintavételek elemre.

1. Kattintson a **+Hozzáadás** gombra.

1. Az **állapot** - <a name="probe"> </a>mintavétel hozzáadása panelen állítsa be az állapot mintavételi paramétereit:

   - **Name (név**): az állapot mintavételének neve.
   - **Protokoll**: TCP.
   - **Port**: állítsa be azt a portot, amelyet a tűzfalban az állapot mintavételéhez hozott létre ebben a [lépésben](#ports). Ebben a cikkben a példa a következő TCP-portot használja: `59999`.
   - **Intervallum**: 5 másodperc.
   - Nem megfelelő **állapot küszöbértéke**: 2 egymást követő hiba.

1. Kattintson az OK gombra.

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A terheléselosztó panelen kattintson a terheléselosztási **szabályok**elemre.

1. Kattintson a **+Hozzáadás** gombra.

1. Állítsa be a terheléselosztási szabályok paramétereit:

   - **Name (név**): a terheléselosztási szabályok neve.
   - Előtérbeli **IP-cím**: használja az IP-címet a SQL Server-es hálózati erőforráshoz.
   - **Port**: állítsa be a SQL Server-es TCP-portot. Az alapértelmezett példány portja 1433.
   - **Háttér-port**: ez az érték ugyanazt a portot használja, mint a **port** értéke, ha engedélyezi a **lebegőpontos IP-címet (a közvetlen kiszolgáló visszaadását)** .
   - **Háttér-készlet**: használja a korábban konfigurált háttér-készlet nevét.
   - **Állapot**mintavétele: használja a korábban konfigurált állapot-mintavételt.
   - **Munkamenet-megőrzés**: nincs.
   - **Üresjárati időkorlát (perc)** : 4.
   - **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** : engedélyezve

1. Kattintson az **OK** gombra.

## <a name="step-6-configure-cluster-for-probe"></a>6\. lépés: fürt konfigurálása a mintavételhez

Állítsa be a fürt mintavételi portjának paraméterét a PowerShellben.

A fürt mintavételi portjának paraméterének beállításához módosítsa a következő parancsfájl változóit a környezetében lévő értékekkel. Távolítsa el a `<>` szögletes zárójeleket a parancsfájlból. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Az előző szkriptben állítsa be a környezet értékeit. Az alábbi lista a következő értékeket tartalmazza:

   - `<Cluster Network Name>`: a hálózat Windows Server feladatátvevő fürtjének neve. **Feladatátvevőfürt-kezelő** > **hálózatokban**kattintson a jobb gombbal a hálózatra, majd kattintson a **Tulajdonságok**elemre. A helyes érték az **általános** lap **név** területén található. 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server a (z) IP-cím-erőforrás neve. **Feladatátvevőfürt-kezelő** > **szerepkörben**az SQL Server-es verzió szerepkör alatt, a **kiszolgáló neve**alatt kattintson a jobb gombbal az IP-cím erőforrásra, majd kattintson a **Tulajdonságok**elemre. A helyes érték az **általános** lap **név** területén található. 

   - `<ILBIP>`: a ILB IP-címe. Ez a címnek a Azure Portal a ILB előtér-címeként van konfigurálva. Ez a SQL Server-alapú IP-cím is. **Feladatátvevőfürt-kezelő** megtalálhatja ugyanazon tulajdonságlapon, ahol a `<SQL Server FCI IP Address Resource Name>` található.  

   - `<nnnnn>`: a terheléselosztó állapotának mintavétele során konfigurált mintavételi port. A fel nem használt TCP-portok érvényesek. 

>[!IMPORTANT]
>A fürt paraméterének alhálózati maszkjának a TCP IP-szórási címnek kell lennie: `255.255.255.255`.

A fürt mintavételének beállítása után a PowerShellben láthatja a fürt összes paraméterét. Futtassa a következő parancsfájlt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>7\. lépés: a feladatátvétel tesztelése

Feladatátvételi teszt – a fürt működésének ellenőrzéséhez. Hajtsa végre a következő lépéseket:

1. Kapcsolódjon az egyik SQL Server és az RDP-vel rendelkező fürtcsomópontok valamelyikéhez.

1. Nyissa meg **Feladatátvevőfürt-kezelő**. Kattintson a **szerepkörök**elemre. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.

1. Kattintson a jobb gombbal a SQL Server.

1. Kattintson az **Áthelyezés** lehetőségre, majd a **lehető legalkalmasabb csomópontra**.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört és annak erőforrásait offline állapotba. Az erőforrások ezután a másik csomóponton helyezik át és online állapotba lépnek.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg a **SQL Server Management Studiot** , és kapcsolódjon a SQL Server.

>[!NOTE]
>Ha szükséges, [letöltheti SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Korlátozások

Az Azure Virtual Machines támogatja a Microsoft Elosztott tranzakciók koordinátora (MSDTC) szolgáltatást a Windows Server 2019-ben a fürtözött megosztott kötetek (CSV) és a [standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)tárolásával.

Azure-beli virtuális gépeken az MSDTC nem támogatott a Windows Server 2016-es és korábbi verzióiban, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Ha a Windows Server 2016 MSDTC-erőforrást hoz létre, az nem fog tudni megosztott tárterületet használni, még akkor sem, ha a tároló ott van. Ezt a problémát a Windows Server 2019-es verzióban javítottuk.
- Az alapszintű Load Balancer nem kezeli az RPC-portokat.

## <a name="see-also"></a>Lásd még:

[S2D beállítása a Távoli asztallal (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-konvergens megoldás a közvetlen tárolóhelyek szolgáltatással](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[A tárolóhelyek közvetlen áttekintése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[A S2D támogatása SQL Server](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
