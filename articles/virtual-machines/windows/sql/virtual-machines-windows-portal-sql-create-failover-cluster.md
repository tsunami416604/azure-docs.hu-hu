---
title: SQL Server-os – Azure Virtual Machines | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozható létre SQL Server feladatátvevő fürt példánya az Azure Virtual Machines szolgáltatásban.
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
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965432"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>SQL Server feladatátvevő fürt példányának konfigurálása Azure-beli virtuális gépeken

Ez a cikk azt ismerteti, hogyan hozható létre SQL Server feladatátvevő fürt példánya (verzió) az Azure Virtual Machines szolgáltatásban a Azure Resource Manager modellben. Ez a megoldás a [Windows Server 2016 Datacenter edition közvetlen tárolóhelyek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) szoftvert használja szoftveres virtuális San-ként, amely szinkronizálja a tárolót (adatlemezeket) a csomópontok (Azure-beli virtuális gépek) között egy Windows-fürtben. Közvetlen tárolóhelyek új volt a Windows Server 2016-ben.

Az alábbi ábrán az Azure Virtual Machines teljes megoldása látható:

![A teljes megoldás](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Ez az ábra a következőket mutatja be:

- Két Azure-beli virtuális gép egy Windows Server rendszerű feladatátvevő fürtben. Ha egy virtuális gép feladatátvevő fürtben található, akkor azt is nevezik *fürtcsomópont* vagy *csomópontnak*.
- Mindegyik virtuális gép két vagy több adatlemezzel rendelkezik.
- Közvetlen tárolóhelyek szinkronizálja az adatokat az adatlemezeken, és a szinkronizált tárolót tárolóként jeleníti meg.
- A tárolási készlet egy Fürt megosztott kötete (CSV) a feladatátvevő fürthöz.
- Az SQL Server-os szerepköralapú fürt szerepkör a CSV-t használja az adatmeghajtókhoz.
- Egy Azure Load Balancer, amely a SQL Server-alapú IP-címet fogja tárolni.
- Az Azure-beli rendelkezésre állási csoport minden erőforrást tárol.

>[!NOTE]
>A diagram összes Azure-erőforrása ugyanabban az erőforráscsoporthoz van.

A Közvetlen tárolóhelyekával kapcsolatos további információkért lásd: [Windows Server 2016 Datacenter edition közvetlen tárolóhelyek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Közvetlen tárolóhelyek két típusú architektúrát támogat: a konvergens és a Hyper-konvergált. A jelen dokumentumban található architektúra Hyper-konvergált. A Hyper-konvergens infrastruktúra a tárolót a fürtözött alkalmazást futtató kiszolgálókon helyezi el. Ebben az architektúrában a tárterület minden SQL Server-os csomóponton található.

## <a name="licensing-and-pricing"></a>Licencelés és díjszabás

Az Azure Virtual Machines szolgáltatásban az utólagos elszámolású (TB) vagy a saját Licences (BYOL) virtuálisgép-rendszerképek használatával engedélyezheti SQL Server. A kiválasztott rendszerkép típusa befolyásolja, hogy milyen díjat számítunk fel.

Az utólagos elszámolású licenceléssel az Azure Virtual Machines szolgáltatásban a SQL Server feladatátvételi fürtszolgáltatása a (z) és a passzív csomópontokat is beleértve További információ: [SQL Server Enterprise Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Ha frissítési garanciával rendelkező Nagyvállalati Szerződés rendelkezik, az egyes aktív csomópontok esetében egyetlen szabad passzív verzió-csomópontot is használhat. Ahhoz, hogy használhassa ezt az előnyt az Azure-ban, használja a BYOL virtuálisgép-rendszerképeket, és használja ugyanazt a licencet a (z) és a (z)%-os aktív és passzív További információ: [nagyvállalati szerződés](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Az Azure Virtual Machines szolgáltatásbeli SQL Server utólagos elszámolású és BYOL licencelésének összehasonlítását lásd: Ismerkedés [az SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)virtuális gépekkel.

A licencelési SQL Serverával kapcsolatos teljes információkért tekintse meg a [díjszabást](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Példa Azure-sablonra

Ezt a teljes megoldást létrehozhatja az Azure-ban egy sablonból. Egy sablon például elérhető a GitHub [Azure gyorsindító-sablonokban](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ez a példa nincs megtervezve vagy tesztelve az adott számítási feladatokhoz. A sablon futtatásával létrehozhat egy SQL Server a tartományhoz csatlakoztatott Közvetlen tárolóhelyek-tárolóval. Kiértékelheti a sablont, és módosíthatja azt a célra.

## <a name="before-you-begin"></a>Előzetes teendők

A Kezdés előtt néhány dologra van szükség.

### <a name="what-to-know"></a>Tudnivalók
Ennek a technológiának operatív ismeretekkel kell rendelkeznie:

- [Windows-fürtök technológiái](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányai](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Az egyik tudnivaló az, hogy az Azure IaaS VM vendég feladatátvevő fürtön egyetlen NIC-t (fürtcsomópont) és egyetlen alhálózatot ajánlunk. Az Azure-hálózatkezelés fizikai redundanciával rendelkezik, ami felesleges hálózati adaptereket és alhálózatokat tesz lehetővé az Azure IaaS VM-vendég fürtön. A fürt ellenőrzési jelentése figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetők el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure IaaS VM vendég feladatátvevő fürtökön.

Ezen technológiák általános megismerése is szükséges:

- [A Windows Server 2016-ben Közvetlen tárolóhelyekt használó Hyper-konvergens megoldások](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg az Azure Virtual Machines szolgáltatásban SQL Server feladatátvevő fürt példányai csak a [SQL Server IaaS-ügynök bővítményének](virtual-machines-windows-sql-server-agent-extension.md) [egyszerűsített felügyeleti módjával](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) támogatottak. Ha a teljes bővítmény módból egyszerűre szeretne váltani, törölje a megfelelő virtuális gépekhez tartozó **SQL** -virtuálisgép-erőforrást, majd az egyszerűsített módban regisztrálja őket az SQL VM erőforrás-szolgáltatóval. Ha a Azure Portal használatával törli az SQL-alapú **virtuális gép** erőforrását, **törölje a megfelelő virtuális gép melletti jelölőnégyzet**jelölését. A teljes bővítmény olyan funkciókat támogat, mint például az automatikus biztonsági mentés, a javítások és a speciális portálok kezelése. Ezek a funkciók nem fognak működni az SQL virtuális gépeken, miután az ügynököt egyszerűsített felügyeleti módban újratelepítette.

### <a name="what-to-have"></a>Mi a teendő

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- Microsoft Azure előfizetés.
- Egy Windows-tartomány az Azure Virtual Machines szolgáltatásban.
- Egy olyan fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel mind az Azure-beli virtuális gépeken, mind pedig a Active Directory.
- Az alábbi összetevőkhöz elegendő IP-címmel rendelkező Azure-beli virtuális hálózat és alhálózat:
   - Mindkét virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Egy IP-cím minden egyes egyes adattömbhöz.
- DNS konfigurálva az Azure-hálózaton, amely a tartományvezérlőkre mutat.

Ezeknek az előfeltételeknek a megkezdése után elkezdheti felépíteni a feladatátvevő fürtöt. Első lépésként hozza létre a virtuális gépeket.

## <a name="step-1-create-the-virtual-machines"></a>1\. lépés: a virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az előfizetésével.

1. [Hozzon létre egy Azure-beli rendelkezésre állási készletet](../tutorial-availability-sets.md).

   A rendelkezésre állási csoport a tartalék tartományok és a frissítési tartományok között csoportosítja a virtuális gépeket. Gondoskodik arról, hogy az alkalmazás ne legyen hatással egyetlen meghibásodási pontra sem, például a hálózati kapcsolóra vagy a kiszolgálók állványának energiagazdálkodási egységére.

   Ha még nem hozta létre az erőforráscsoportot a virtuális gépek számára, akkor azt egy Azure-beli rendelkezésre állási csoport létrehozásakor teheti meg. Ha a Azure Portal használatával hozza létre a rendelkezésre állási készletet, hajtsa végre a következő lépéseket:

   1. Az Azure Portal az **erőforrás létrehozása** elemre kattintva nyissa meg az Azure Marketplace-t. Keressen rá a **rendelkezésre állási csoportra**.
   1. Válassza a **rendelkezésre állási csoport**lehetőséget.
   1. Kattintson a **Létrehozás** gombra.
   1. A **rendelkezésre állási csoport létrehozása**területen adja meg a következő értékeket:
      - **Name (név**): a rendelkezésre állási csoport neve.
      - **Előfizetés**: az Azure-előfizetése.
      - **Erőforráscsoport**: Ha meglévő csoportot szeretne használni, kattintson a **meglévő kiválasztása** elemre, majd válassza ki a csoportot a listából. Ellenkező esetben válassza az **új létrehozása** lehetőséget, és adja meg a csoport nevét.
      - **Hely**: állítsa be azt a helyet, ahol létre szeretné hozni a virtuális gépeket.
      - Tartalék **tartományok**: használja az alapértelmezett (**3**) értéket.
      - **Frissítési tartományok**: használja az alapértelmezett (**5**) értéket.
   1. Válassza a **Létrehozás** lehetőséget a rendelkezésre állási csoport létrehozásához.

1. Hozza létre a virtuális gépeket a rendelkezésre állási csoportból.

   Hozzon létre két SQL Server virtuális gépet az Azure rendelkezésre állási készletében. Útmutatásért lásd: [SQL Server virtuális gép kiépítése a Azure Portalban](virtual-machines-windows-portal-sql-server-provision.md).

   Mindkét virtuális gép elhelyezése:

   - Ugyanabban az Azure-erőforráscsoportban, mint a rendelkezésre állási csoport.
   - A tartományvezérlővel megegyező hálózaton.
   - Olyan alhálózaton, amely elegendő IP-címmel rendelkezik a virtuális gépekhez és a fürtön esetlegesen használt összes FCIs.
   - Az Azure rendelkezésre állási készletében.

      >[!IMPORTANT]
      >A virtuális gép létrehozása után a rendelkezésre állási csoport nem állítható be és nem módosítható.

   Válasszon egy rendszerképet az Azure Marketplace-ről. Használhat olyan Azure Marketplace-rendszerképet, amely tartalmazza a Windows Servert és a SQL Servert, vagy használhatja azt is, hogy csak a Windows Servert tartalmazza. Részletekért lásd: [Az Azure Virtual machines SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).

   Az Azure katalógusában található hivatalos SQL Server rendszerképek közé tartozik egy telepített SQL Server példány, a SQL Server telepítési szoftver és a szükséges kulcs.

   Válassza ki a megfelelő képet, attól függően, hogyan szeretné kifizetni a SQL Server licencet:

   - **Pay-per-használat licencelése**. A lemezképek másodpercenkénti díja tartalmazza a SQL Server licencelést:
      - **SQL Server 2016 Enterprise Windows Server 2016 Datacenter rendszeren**
      - **SQL Server 2016 standard Windows Server 2016 Datacenter rendszeren**
      - **SQL Server 2016 fejlesztői Windows Server 2016 Datacenter rendszeren**

   - **Saját licenc használata (BYOL)**

      - **BYOL SQL Server 2016 Enterprise Windows Server 2016 Datacenter rendszeren**
      - **BYOL SQL Server 2016 standard Windows Server 2016 Datacenter rendszeren**

   >[!IMPORTANT]
   >A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server példányt. Az előre telepített SQL Server adathordozóval hozza létre a SQL Server a feladatátvevő fürt és a Közvetlen tárolóhelyek beállítása után.

   Azt is megteheti, hogy csak az operációs rendszert tartalmazó Azure Marketplace-lemezképeket használja. Válasszon egy **Windows Server 2016 Datacenter** -rendszerképet, és telepítse a SQL Server a következőt a feladatátvevő fürt beállítása és közvetlen tárolóhelyek után:. Ez a rendszerkép nem tartalmaz SQL Server telepítési adathordozót. Helyezze a SQL Server telepítési adathordozót egy olyan helyre, ahol az egyes kiszolgálókon futtathatja.

1. Miután az Azure létrehozta a virtuális gépeket, csatlakozzon mindegyikhez RDP használatával.

   Amikor az RDP használatával először csatlakozik egy virtuális géphez, a rendszer megkérdezi, hogy szeretné-e, hogy a számítógép felderíthető legyen a hálózaton. Válassza az **Igen** lehetőséget.

1. Ha a SQL Server-alapú virtuálisgép-rendszerképek egyikét használja, távolítsa el a SQL Server példányt.

   1. A **programok és szolgáltatások**területen kattintson a jobb gombbal a **Microsoft SQL Server 2016 (64 bites)** elemre, és válassza az **Eltávolítás/módosítás**parancsot.
   1. Válassza az **Eltávolítás** lehetőséget.
   1. Válassza ki az alapértelmezett példányt.
   1. Távolítsa el az összes funkcióját az **adatbázismotor-szolgáltatások**területen. Ne távolítsa el a **megosztott szolgáltatásokat**. A következő képernyőképhez hasonlóan fog megjelenni:

      ![Szolgáltatások kiválasztása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Válassza a **tovább**, majd az **Eltávolítás**lehetőséget.

1. <a name="ports"></a>Nyissa meg a tűzfal portjait.

   Az egyes virtuális gépeken nyissa meg ezeket a portokat a Windows tűzfalon:

   | Rendeltetés | TCP-port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port a SQL Server alapértelmezett példányaihoz. Ha a katalógusból rendszerképet használt, a rendszer automatikusan megnyitja a portot.
   | Állapotadat-mintavétel | 59999 | Bármilyen nyitott TCP-port. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotának](#probe) mintavételét és a fürtöt, hogy ezt a portot használja.  

1. Adja hozzá a tárolót a virtuális géphez. Részletes információkért lásd: [tár hozzáadása](../disks-types.md).

   Mindkét virtuális gépnek legalább két adatlemezre van szüksége.

   Nyers lemezek csatolása, nem NTFS fájlrendszerű lemezek.
      >[!NOTE]
      >Ha NTFS fájlrendszerű lemezeket csatlakoztat, a Közvetlen tárolóhelyek csak a lemezes jogosultság-ellenőrzés nélkül engedélyezheti.  

   Csatoljon legalább két prémium SSD-t az egyes virtuális gépekhez. Legalább P30 (1 TB) lemezt ajánlunk.

   Állítsa be a gazdagép gyorsítótárazását **írásvédett**értékre.

   Az éles környezetekben használt tárolókapacitás a munkaterheléstől függ. A cikkben ismertetett értékek a demonstrációra és tesztelésre szolgálnak.

1. [Adja hozzá a virtuális gépeket a már meglévő tartományhoz](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

A virtuális gépek létrehozása és konfigurálása után beállíthatja a feladatátvevő fürtöt.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>2\. lépés: a Windows Server feladatátvevő fürt konfigurálása Közvetlen tárolóhelyek

A következő lépés a feladatátvevő fürt konfigurálása Közvetlen tárolóhelyek használatával. Ebben a lépésben a következő allépéseket hajtja végre:

1. Adja hozzá a Windows Server feladatátvételi fürtszolgáltatást.
1. Ellenőrizze a fürtöt.
1. Hozza létre a feladatátvevő fürtöt.
1. Hozzon létre egy Felhőbeli tanúsító.
1. Adja hozzá a tárolót.

### <a name="add-windows-server-failover-clustering"></a>Windows Server feladatátvételi fürtszolgáltatás hozzáadása

1. Kapcsolódjon az első virtuális géphez RDP használatával egy olyan tartományi fiókkal, amely tagja a helyi rendszergazdáknak, és amely jogosult objektumok létrehozására Active Directoryban. Ezt a fiókot használja a többi konfigurációhoz.

1. [Adja hozzá a feladatátvételi fürtszolgáltatást az egyes virtuális gépekhez](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Ha a felhasználói felületen szeretné telepíteni a feladatátvételi fürtszolgáltatást, hajtsa végre a következő lépéseket mindkét virtuális gépen:
   1. A **Kiszolgálókezelőben**válassza a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása**lehetőséget.
   1. A **szerepkörök és szolgáltatások hozzáadása varázslóban**kattintson a **tovább** gombra, amíg ki nem **választja a funkciókat**.
   1. A **szolgáltatások kiválasztása**területen válassza a **feladatátvételi fürtszolgáltatás**lehetőséget. Adja meg az összes szükséges funkciót és a felügyeleti eszközöket. Válassza a **szolgáltatások hozzáadása**lehetőséget.
   1. Válassza a **tovább**, majd a **Befejezés** lehetőséget a funkciók telepítéséhez.

   A feladatátvételi fürtszolgáltatás PowerShell használatával történő telepítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

A következő lépésekkel kapcsolatos további információkért tekintse meg a [Hyper-konvergens megoldás 3. lépésében ismertetett utasításokat a Windows Server 2016 közvetlen tárolóhelyek használatával](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt a felhasználói felületen való ellenőrzéséhez hajtsa végre a következő lépéseket az egyik virtuális gépen:

1. A **Kiszolgálókezelő**területen válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő**lehetőséget.
1. A **Feladatátvevőfürt-kezelő**alatt válassza a **művelet**, majd a **Konfiguráció ellenőrzése**lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése**területen adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások**területen válassza a **csak a kiválasztott tesztek futtatása**lehetőséget. Kattintson a **Tovább** gombra.
1. A **teszt kiválasztása**területen válassza a minden teszt lehetőséget a **tárolás**kivételével, ahogy az itt látható:

   ![Fürt-ellenőrzési tesztek kiválasztása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kattintson a **Tovább** gombra.
1. A **megerősítés**területen válassza a **tovább**lehetőséget.

A konfiguráció ellenőrzése varázsló futtatja az ellenőrző teszteket.

A fürt a PowerShell használatával történő ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

A fürt érvényesítése után hozza létre a feladatátvevő fürtöt.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következők szükségesek:
- A fürtcsomópontok lesznek a virtuális gépek nevei.
- A feladatátvevő fürt neve
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem szerepel ugyanazon az Azure-beli virtuális hálózaton és az alhálózaton, mint a fürtcsomópontok.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 a Windows Server 2016 használatával

A következő PowerShell-parancsfájl egy feladatátvevő fürtöt hoz létre a Windows Server 2008 rendszerhez a Windows Server 2016 használatával. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

A következő PowerShell-szkript létrehoz egy feladatátvevő fürtöt a Windows Server 2019-hez. További információ: [feladatátvevő fürt: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása

A Felhőbeli tanúsító az Azure Storage-blobokban tárolt fürtözött kvórum tanúsító új típusa. Ezzel a művelettel megszűnik a tanúsító megosztást üzemeltető különálló virtuális gép szükségessége.

1. [Hozzon létre egy Felhőbeli tanúsító a feladatátvevő fürthöz](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Hozzon létre egy BLOB-tárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címét.

1. Konfigurálja a feladatátvevő fürtöt tanúsító kvórumot. Lásd: [a kvórum tanúsító beállítása a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Tárhely hozzáadása

A Közvetlen tárolóhelyek lemezének üresnek kell lennie. Nem tartalmazhatnak partíciókat vagy egyéb adatforrásokat. A lemezek tisztításához kövesse az [útmutató lépéseit](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. A [közvetlen tárolóhelyek engedélyezése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell-parancsfájl lehetővé teszi a Közvetlen tárolóhelyek:  

   ```powershell
   Enable-ClusterS2D
   ```

   **Feladatátvevőfürt-kezelő**a Storage-készletet láthatja.

1. [Hozzon létre egy kötetet](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   A Közvetlen tárolóhelyek automatikusan létrehoz egy tárolási készletet, amikor engedélyezi azt. Most már készen áll egy kötet létrehozására. A PowerShell-parancsmag `New-Volume` automatizálja a kötet létrehozásának folyamatát. Ez a folyamat formázást tartalmaz, hozzáadja a kötetet a fürthöz, és létrehoz egy Fürt megosztott kötete (CSV). Ez a példa egy 800 gigabájt (GB) CSV-fájlt hoz létre:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   A parancs végrehajtása után egy 800 GB-os kötet van csatlakoztatva fürt erőforrásként. A kötet a következő helyen található: `C:\ClusterStorage\Volume1\`.

   A képernyőképen a Közvetlen tárolóhelyek Fürt megosztott kötete látható:

   ![Fürt megosztott kötete](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3\. lépés: feladatátvevő fürt feladatátvételének tesztelése

A **Feladatátvevőfürt-kezelőban**ellenőrizze, hogy át tudja-e helyezni a tárolási erőforrást a másik fürtcsomóponton. Ha **Feladatátvevőfürt-kezelő** használatával tud csatlakozni a feladatátvevő fürthöz, és áthelyezi a tárolót az egyik csomópontról a másikra, készen áll a következő konfigurálására:.

## <a name="step-4-create-the-sql-server-fci"></a>4\. lépés: hozza létre a SQL Server-t

Miután konfigurálta a feladatátvevő fürtöt és a fürt összes összetevőjét, beleértve a Storage-t is, létrehozhatja a SQL Server-t.

1. Kapcsolódjon az első virtuális géphez RDP használatával.

1. **Feladatátvevőfürt-kezelő**ellenőrizze, hogy az összes alapvető fürterőforrás az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást az adott virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full`. Válassza a **beállítás**lehetőséget.

1. **SQL Server telepítési központban**válassza a **telepítés**lehetőséget.

1. Válassza az **új SQL Server feladatátvevő fürt telepítése**lehetőséget. A varázsló utasításait követve telepítse a SQL Server-t.

   A (z)-es adatkönyvtárak fürtözött tárolón kell lenniük. A Közvetlen tárolóhelyek nem egy megosztott lemez, hanem egy, az egyes kiszolgálókon lévő kötetre mutató csatlakoztatási pont. Közvetlen tárolóhelyek szinkronizálja a kötetet mindkét csomópont között. A kötet Fürt megosztott köteteként jelenik meg a fürtben. Használja a CSV csatlakoztatási pontot az adatkönyvtárakhoz.

   ![Adatkönyvtárak](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Miután elvégezte a varázsló utasításait, a telepítő telepíti az első csomóponton egy SQL Server-t.

1. Miután a telepítő telepíti a (z)-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**. Válassza a **telepítés**lehetőséget.

1. Válassza **a csomópont hozzáadása SQL Server feladatátvevő fürthöz**lehetőséget. A varázsló utasításait követve telepítse a SQL Servert, és adja hozzá a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Servert tartalmazó Azure Marketplace Gallery-rendszerképet használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használja ezeket a képeket, telepítse külön a SQL Server-eszközöket. Lásd: [SQL Server Management Studio letöltése (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>5\. lépés: az Azure Load Balancer létrehozása

Az Azure Virtual Machines szolgáltatásban a fürtök egy terheléselosztó használatával egy olyan IP-címet tárolhatnak, amelynek egyszerre egy fürtcsomóponton kell lennie. Ebben a megoldásban a terheléselosztó a SQL Server-es verzió IP-címét tárolja.

További információ: [Azure Load Balancer létrehozása és konfigurálása](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása a Azure Portal

A terheléselosztó létrehozása:

1. A Azure Portal lépjen a virtuális gépeket tartalmazó erőforráscsoporthoz.

1. Válassza a **Hozzáadás** lehetőséget. Keresse meg **Load Balancer**az Azure Marketplace piactéren. Válassza a **Load Balancer**lehetőséget.

1. Kattintson a **Létrehozás** gombra.

1. A terheléselosztó konfigurálása a alábbiakkal:

   - **Előfizetés**: az Azure-előfizetése.
   - **Erőforráscsoport**: a virtuális gépeket tartalmazó erőforráscsoport.
   - **Name (név**): a terheléselosztó azonosítására szolgáló név.
   - **Régió**: az Azure-beli hely, amely a virtuális gépeket tartalmazza.
   - **Típus**: nyilvános vagy magánjellegű. A magánhálózati terheléselosztó a virtuális hálózaton belülről érhető el. A legtöbb Azure-alkalmazás saját Load balancert használhat. Ha az alkalmazásnak közvetlenül az interneten keresztül kell SQL Servera, használjon nyilvános Load balancert.
   - **SKU**: standard.
   - **Virtuális hálózat**: ugyanaz a hálózat, mint a virtuális gépek.
   - **IP-cím hozzárendelése**: statikus. 
   - **Magánhálózati IP-cím**: az SQL Server-alapú fürt hálózati erőforrásához hozzárendelt IP-cím.

 A következő képernyőfelvételen a **Load Balancer** felhasználói felületének létrehozása látható:

   ![A terheléselosztó beállítása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>A terheléselosztó háttér-készletének konfigurálása

1. Térjen vissza az Azure-erőforráscsoporthoz, amely tartalmazza a virtuális gépeket, és keresse meg az új Load balancert. Előfordulhat, hogy frissítenie kell a nézetet az erőforráscsoporthoz. Válassza ki a Load balancert.

1. Válassza ki a **háttérbeli készletek**elemet, majd kattintson a **Hozzáadás**gombra.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk**területen válassza a **virtuális gép** lehetőséget, és válassza ki azokat a virtuális gépeket, amelyek fürtcsomópontokként fognak részt venni. Ügyeljen arra, hogy minden olyan virtuális gépet tartalmazzon, amely a (z)-t fogja üzemeltetni.

1. A háttér-készlet létrehozásához kattintson **az OK gombra** .

### <a name="configure-a-load-balancer-health-probe"></a>Terheléselosztó állapotmintájának konfigurálása

1. A terheléselosztó panelen válassza az **állapot**-mintavétel lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. Az **állapot** - <a name="probe"> </a>mintavétel hozzáadása panelen állítsa be az állapot mintavételi paramétereit.

   - **Name (név**): az állapot mintavételének neve.
   - **Protokoll**: TCP.
   - **Port**: állítsa be azt a portot, amelyet a tűzfalban az állapot mintavételéhez hozott létre ebben a [lépésben](#ports). Ebben a cikkben a példában a `59999`TCP-portot használja.
   - **Intervallum**: 5 másodperc.
   - Nem megfelelő **állapot küszöbértéke**: 2 egymást követő hiba.

1. Kattintson az **OK** gombra.

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A terheléselosztó panelen válassza a **terheléselosztási szabályok**lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. A terheléselosztási szabály paramétereinek beállítása:

   - **Name (név**): a terheléselosztási szabályok neve.
   - Előtérbeli **IP-cím**: az SQL Server-es hálózati erőforrás IP-címe.
   - **Port**: a SQL Server%-os TCP-port. Az alapértelmezett példány portja 1433.
   - **Háttér-port**: ugyanazt a portot használja, mint a **port** értéke, ha engedélyezi a **lebegőpontos IP-címet (a közvetlen kiszolgáló visszaadását)** .
   - **Háttér-készlet**: a korábban konfigurált háttér-készlet neve.
   - **Állapot**mintavétele: a korábban konfigurált állapot-mintavétel.
   - **Munkamenet-megőrzés**: nincs.
   - **Üresjárati időkorlát (perc)** : 4.
   - **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** : engedélyezve.

1. Kattintson az **OK** gombra.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>6\. lépés: a fürt konfigurálása a mintavételhez

Állítsa be a fürt mintavételi portjának paraméterét a PowerShellben.

A fürt mintavételi portjának paraméterének beállításához módosítsa a következő parancsfájl változóit a környezetében lévő értékekkel. Távolítsa el a szögletes zárójeleket (`<` és `>`) a parancsfájlból.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A következő lista azokat az értékeket ismerteti, amelyeket frissítenie kell:

   - `<Cluster Network Name>`: a hálózat Windows Server feladatátvevő fürtjének neve. **Feladatátvevőfürt-kezelő** > **hálózatok**területen kattintson a jobb gombbal a hálózatra, és válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található.

   - `<SQL Server FCI IP Address Resource Name>`: a SQL Server-es IP-cím-erőforrás neve. A **Feladatátvevőfürt-kezelő** > **szerepkörök**területen, a SQL Server a **kiszolgáló neve**alatt kattintson a jobb gombbal az IP-cím erőforrásra, majd válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található. 

   - `<ILBIP>`: a ILB IP-címe. Ez a címnek a Azure Portal a ILB előtér-címeként van konfigurálva. Ez a SQL Server-alapú IP-cím is. **Feladatátvevőfürt-kezelő** megtalálhatja ugyanazt a tulajdonságlapot, ahol a `<SQL Server FCI IP Address Resource Name>`található.  

   - `<nnnnn>`: a terheléselosztó állapotának mintavételéhez konfigurált mintavételi port. A fel nem használt TCP-portok érvényesek.

>[!IMPORTANT]
>A fürt paraméterének alhálózati maszkjának a TCP IP-szórási címnek kell lennie: `255.255.255.255`.

A fürt mintavételének beállítása után a PowerShellben láthatja a fürt összes paraméterét. Futtassa a következő parancsfájlt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>7\. lépés: a feladatátvétel tesztelése

Feladatátvételi teszt – a fürt működésének ellenőrzéséhez. Tegye a következőket:

1. Az RDP használatával csatlakozzon az egyik SQL Server a csomóponthoz.

1. Nyissa meg **Feladatátvevőfürt-kezelő**. Válassza a **szerepkörök**lehetőséget. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.

1. Kattintson a jobb gombbal a SQL Server.

1. Válassza az **Áthelyezés**, majd a **lehető legjobb csomópont**lehetőséget.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Az erőforrások ezután a másik csomóponton helyezik át és online állapotba lépnek.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg a **SQL Server Management Studiot** , és kapcsolódjon a SQL Server.

>[!NOTE]
>Ha szükséges, [letöltheti SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Korlátozások

Az Azure Virtual Machines támogatja a Microsoft Elosztott tranzakciók koordinátora (MSDTC) szolgáltatást a Windows Server 2019-ben a fürtözött megosztott kötetek (CSV) és egy [standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)tárolásával.

Az Azure Virtual Machines szolgáltatásban az MSDTC nem támogatott a Windows Server 2016-es vagy újabb verzióiban, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Windows Server 2016 rendszeren, ha MSDTC-erőforrást hoz létre, az nem fog tudni használni megosztott tárterületet, még akkor sem, ha rendelkezésre áll tárterület. Ezt a problémát a Windows Server 2019-es verzióban javítottuk.
- Az alapszintű Load Balancer nem kezeli az RPC-portokat.

## <a name="see-also"></a>Lásd még:

[Közvetlen tárolóhelyek beállítása a Távoli asztallal (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-konvergens megoldás Közvetlen tárolóhelyek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Közvetlen tárolóhelyek áttekintése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Közvetlen tárolóhelyek SQL Server támogatása](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
