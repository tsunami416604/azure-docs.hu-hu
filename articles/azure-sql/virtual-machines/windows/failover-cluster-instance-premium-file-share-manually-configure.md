---
title: SQL Server és prémium szintű fájlmegosztás – Azure Virtual Machines
description: Ez a cikk azt ismerteti, hogyan hozható létre SQL Server feladatátvevő fürt példánya egy prémium szintű fájlmegosztás használatával az Azure Virtual Machinesban.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 01787fbf3339a7e079b705fb4be27ba1e30aee1b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342875"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>SQL Server feladatátvevő fürt példányának konfigurálása prémium fájlmegosztás esetén az Azure-ban Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan hozható létre egy SQL Server feladatátvevő fürt példánya (verzió) az Azure-Virtual Machines egy [prémium fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md)használatával.

A prémium szintű fájlmegosztás SSD-alapú, következetesen alacsony késésű fájlmegosztás, amely teljes mértékben támogatott a Windows Server 2012 vagy újabb rendszerű feladatátvevő fürt példányaival SQL Server 2012-es vagy későbbi verzióiban. A prémium szintű fájlmegosztás nagyobb rugalmasságot biztosít, ami lehetővé teszi a fájlmegosztás átméretezését és méretezését leállás nélkül.


## <a name="before-you-begin"></a>Előkészületek

A Kezdés előtt néhány dologra van szükség.

Ennek a technológiának operatív ismeretekkel kell rendelkeznie:

- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Az egyik teendő, hogy az Azure IaaS VM feladatátvevő fürtön egyetlen NIC-kiszolgálót (fürtcsomópont) és egyetlen alhálózatot ajánlunk. Az Azure-hálózatkezelés olyan fizikai redundanciával rendelkezik, amely szükségtelen hálózati adaptereket és alhálózatokat tesz lehetővé az Azure IaaS VM-vendég fürtön. A fürt ellenőrzési jelentése figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetők el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure IaaS VM feladatátvevő fürtökön.

Ezen technológiák általános megismerése is szükséges:

- [Prémium szintű Azure-fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg SQL Server Azure Virtual Machines-beli feladatátvevő fürt példányai csak a [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md) [egyszerűsített felügyeleti módjával](sql-vm-resource-provider-register.md#management-modes) támogatottak. Ha a teljes bővítmény módból egyszerűre szeretne váltani, törölje a megfelelő virtuális gépekhez tartozó **SQL** -virtuálisgép-erőforrást, majd az egyszerűsített módban regisztrálja őket az SQL VM erőforrás-szolgáltatóval. Ha a Azure Portal használatával törli az SQL-alapú **virtuális gép** erőforrását, **törölje a megfelelő virtuális gép melletti jelölőnégyzet**jelölését. A teljes bővítmény olyan funkciókat támogat, mint például az automatikus biztonsági mentés, a javítások és a speciális portálok kezelése. Ezek a funkciók nem fognak működni az SQL virtuális gépeken, miután az ügynököt egyszerűsített felügyeleti módban újratelepítette.

A prémium szintű fájlmegosztás biztosítja a IOPS és az átviteli kapacitást, amely megfelel a sok számítási feladat igényeinek. Az i/o-igényű számítási feladatokhoz a felügyelt prémium lemezeken vagy az ultra Disks [szolgáltatáson alapuló SQL Server feladatátvevő fürt példányainak közvetlen tárolóhelyek](failover-cluster-instance-storage-spaces-direct-manually-configure.md)-mel kell rendelkezniük.  

Ellenőrizze a környezet IOPS tevékenységét, és ellenőrizze, hogy a prémium szintű fájlmegosztás biztosítja-e a szükséges IOPS az üzembe helyezés vagy az áttelepítés megkezdése előtt. A Windows Teljesítményfigyelő lemezei számlálók segítségével figyelheti az adatokhoz, a naplóhoz és a temp DB-fájlokhoz SQL Server szükséges teljes IOPS (lemez átvitel/másodperc) és átviteli sebesség (bájt/s).

Sok számítási feladat kitörte az IO-t, ezért érdemes a nagy mennyiségű használati időszakot is megfigyelni, és a maximális IOPS és az átlagos IOPS is figyelembe venni. A prémium fájlmegosztás a megosztás méretétől függően biztosít IOPS. A prémium szintű fájlmegosztás ingyenes kibontást is biztosít, amely lehetővé teszi, hogy akár egy óráig is tripla az alapértéket.

További információ a prémium szintű fájlmegosztás teljesítményéről: [fájlmegosztás teljesítményi szintjei](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licencelés és díjszabás

Az Azure Virtual Machines-on az utólagos elszámolású (TB) vagy a saját Licences (BYOL) virtuálisgép-lemezképek használatával SQL Server licenceket is megteheti. A kiválasztott rendszerkép típusa befolyásolja, hogy milyen díjat számítunk fel.

Az utólagos elszámolású licencelési szolgáltatással az Virtual Machines Azure-ban található SQL Server egy feladatátvevő fürt példánya (a (z)-t, a passzív csomópontokat is beleértve. További információ: [SQL Server Enterprise Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Ha frissítési garanciával rendelkező Nagyvállalati Szerződés rendelkezik, az egyes aktív csomópontok esetében egyetlen szabad passzív verzió-csomópontot is használhat. Ahhoz, hogy használhassa ezt az előnyt az Azure-ban, használja a BYOL virtuálisgép-rendszerképeket, és használja ugyanazt a licencet a (z) és a (z)%-os aktív és passzív További információ: [nagyvállalati szerződés](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Az Azure Virtual Machines SQL Server utólagos elszámolású és BYOL licencelésének összehasonlítását lásd: Ismerkedés [az SQL virtuális gépekkel](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

A licencelési SQL Serverával kapcsolatos teljes információkért tekintse meg a [díjszabást](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Fájlstream

A FileStream nem támogatott egy prémium szintű fájlmegosztást használó feladatátvevő fürt esetén. A FileStream használatához [közvetlen tárolóhelyek](failover-cluster-instance-storage-spaces-direct-manually-configure.md)használatával telepítse a fürtöt.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek elvégzése előtt a következőket kell tennie:

- Egy Microsoft Azure-előfizetéssel.
- Egy Windows-tartomány az Azure Virtual Machineson.
- Olyan tartományi felhasználói fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel az Azure Virtual Machines és a Active Directoryban.
- Egy tartományi felhasználói fiók a SQL Server szolgáltatás futtatásához, és a fájlmegosztás csatlakoztatásakor bejelentkezhet a virtuális gépre.  
- Az alábbi összetevőkhöz elegendő IP-címmel rendelkező Azure-beli virtuális hálózat és alhálózat:
   - Két virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Egy IP-cím minden egyes egyes adattömbhöz.
- DNS konfigurálva az Azure-hálózaton, amely a tartományvezérlőkre mutat.
- A fürtözött meghajtóként használandó [prémium fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md) az adatfájlok adatbázisának tárolási kvótája alapján.
- Ha a Windows Server 2012 R2-es vagy újabb verzióját használja, szüksége lesz egy másik fájlmegosztást, amelyet tanúsító fájlmegosztásként kíván használni, mivel a Felhőbeli tanúk a Windows 2016 és újabb rendszereken támogatottak. Használhat egy másik Azure-fájlmegosztást, vagy használhat egy fájlmegosztást egy különálló virtuális gépen is. Ha másik Azure-fájlmegosztást fog használni, csatlakoztathatja azt ugyanazzal a folyamattal, mint a fürtözött meghajtóhoz használt prémium fájlmegosztás esetében. 

Ezeknek az előfeltételeknek a megkezdése után elkezdheti felépíteni a feladatátvevő fürtöt. Első lépésként hozza létre a virtuális gépeket.

## <a name="step-1-create-the-virtual-machines"></a>1. lépés: a virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az előfizetésével.

1. [Hozzon létre egy Azure-beli rendelkezésre állási készletet](../../../virtual-machines/linux/tutorial-availability-sets.md).

   A rendelkezésre állási csoport a tartalék tartományok és a frissítési tartományok között csoportosítja a virtuális gépeket. Gondoskodik arról, hogy az alkalmazás ne legyen hatással egyetlen meghibásodási pontra sem, például a hálózati kapcsolóra vagy a kiszolgálók állványának energiagazdálkodási egységére.

   Ha még nem hozta létre az erőforráscsoportot a virtuális gépek számára, akkor azt egy Azure-beli rendelkezésre állási csoport létrehozásakor teheti meg. Ha a Azure Portal használatával hozza létre a rendelkezésre állási készletet, hajtsa végre a következő lépéseket:

   1. A Azure Portal az **erőforrás létrehozása** elemre kattintva nyissa meg az Azure Marketplace-t. Keressen rá a **rendelkezésre állási csoportra**.
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

   Hozzon létre két SQL Server virtuális gépet az Azure rendelkezésre állási készletében. Útmutatásért lásd: [SQL Server virtuális gép kiépítése a Azure Portalban](create-sql-vm-portal.md).

   Mindkét virtuális gép elhelyezése:

   - Ugyanabban az Azure-erőforráscsoportban, mint a rendelkezésre állási csoport.
   - A tartományvezérlővel megegyező hálózaton.
   - Olyan alhálózaton, amely elegendő IP-címmel rendelkezik a virtuális gépekhez és a fürtön esetlegesen használt összes FCIs.
   - Az Azure rendelkezésre állási készletében.

      >[!IMPORTANT]
      >A virtuális gép létrehozása után a rendelkezésre állási csoport nem állítható be és nem módosítható.

   Válasszon egy rendszerképet az Azure Marketplace-ről. Használhat olyan Azure Marketplace-rendszerképet, amely tartalmazza a Windows Servert és a SQL Servert, vagy használhatja azt is, hogy csak a Windows Servert tartalmazza. Részletekért lásd: [Az Azure Virtual Machines SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md).

   Az Azure katalógusában található hivatalos SQL Server rendszerképek közé tartozik egy telepített SQL Server példány, a SQL Server telepítési szoftver és a szükséges kulcs.

   >[!IMPORTANT]
   > A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server példányt. Az előre telepített SQL Server adathordozó létrehozásával létrehozhatja a SQL Server-t, miután beállította a feladatátvevő fürtöt és a prémium szintű fájlmegosztást tárolóként.

   Azt is megteheti, hogy csak az operációs rendszert tartalmazó Azure Marketplace-lemezképeket használja. Válasszon ki egy **Windows Server 2016 Datacenter** -rendszerképet, és telepítse a SQL Server a következőt, miután beállította a feladatátvevő fürtöt és a prémium szintű fájlmegosztást tárolóként. Ez a rendszerkép nem tartalmaz SQL Server telepítési adathordozót. Helyezze a SQL Server telepítési adathordozót egy olyan helyre, ahol az egyes kiszolgálókon futtathatja.

1. Miután az Azure létrehozta a virtuális gépeket, csatlakozzon mindegyikhez RDP használatával.

   Amikor az RDP használatával először csatlakozik egy virtuális géphez, a rendszer megkérdezi, hogy szeretné-e, hogy a számítógép felderíthető legyen a hálózaton. Válassza az **Igen** lehetőséget.

1. Ha a SQL Server-alapú virtuálisgép-rendszerképek egyikét használja, távolítsa el a SQL Server példányt.

   1. A **programok és szolgáltatások**területen kattintson a jobb gombbal az **Microsoft SQL Server 201_ (64 bites)** elemre, és válassza az **Eltávolítás/módosítás**parancsot.
   1. Válassza az **Eltávolítás**lehetőséget.
   1. Válassza ki az alapértelmezett példányt.
   1. Távolítsa el az összes funkcióját az **adatbázismotor-szolgáltatások**területen. Ne távolítsa el a **megosztott szolgáltatásokat**. A következő képernyőképhez hasonlóan fog megjelenni:

        ![Szolgáltatások kiválasztása](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Válassza a **tovább**, majd az **Eltávolítás**lehetőséget.

1. <span id="ports"> </span> Nyissa meg a tűzfal portjait.  

   Az egyes virtuális gépeken nyissa meg ezeket a portokat a Windows tűzfalon:

   | Cél | TCP-port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port a SQL Server alapértelmezett példányaihoz. Ha a katalógusból rendszerképet használt, a rendszer automatikusan megnyitja a portot.
   | Állapotadat-mintavétel | 59999 | Bármilyen nyitott TCP-port. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotának](#probe) mintavételét és a fürtöt, hogy ezt a portot használja.
   | Fájlmegosztás | 445 | A fájlmegosztási szolgáltatás által használt port.

1. [Adja hozzá a virtuális gépeket a már meglévő tartományhoz](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

A virtuális gépek létrehozása és konfigurálása után beállíthatja a prémium szintű fájlmegosztást.

## <a name="step-2-mount-the-premium-file-share"></a>2. lépés: a prémium fájlmegosztás csatlakoztatása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg a Storage-fiókját.
1. Nyissa meg a **file** shares elemet a **Fájlszolgáltatások** területen, és válassza ki az SQL-tárolóhoz használni kívánt prémium fájlmegosztást.
1. Válassza a **Kapcsolódás** lehetőséget a fájlmegosztás kapcsolati karakterláncának megadásához.
1. Válassza ki a legördülő listából a használni kívánt meghajtóbetűjelet, majd másolja mindkét kódrészletet a Jegyzettömbbe.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Mindkét PowerShell-parancs másolása a fájlmegosztás csatlakozási portálján":::

1. Az RDP használatával csatlakozzon a SQL Server VMhoz azzal a fiókkal, amelyet a SQL Server a szolgáltatásfiók használni fog.
1. Nyisson meg egy rendszergazdai PowerShell-parancssori konzolt.
1. Futtassa a korábban mentett parancsokat a portálon végzett munka során.
1. Nyissa meg a megosztást a fájlkezelő vagy a **Futtatás** párbeszédpanel használatával (Windows billentyű + r). Használja a hálózati elérési utat `\\storageaccountname.file.core.windows.net\filesharename` . Például: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Hozzon létre legalább egy mappát az újonnan csatlakoztatott fájlmegosztás számára az SQL-adatfájlok a alkalmazásba történő elhelyezéséhez.
1. Ismételje meg ezeket a lépéseket minden olyan SQL Server VM, amely részt vesz a fürtben.

  > [!IMPORTANT]
  > - Érdemes lehet külön fájlmegosztást használni a biztonságimásolat-fájlokhoz, hogy mentse a megosztás IOPS és tárterületét az adatfájlok és a naplófájlok számára. A biztonsági másolati fájlok prémium vagy standard fájlmegosztást is használhatnak.
  > - Ha Windows 2012 R2 vagy régebbi operációs rendszert használ, kövesse ugyanezen lépéseket a tanúsító fájlmegosztásként használni kívánt fájlmegosztás csatlakoztatásához. 

## <a name="step-3-configure-the-failover-cluster"></a>3. lépés: a feladatátvevő fürt konfigurálása

A következő lépés a feladatátvevő fürt konfigurálása. Ebben a lépésben a következő allépéseket hajtja végre:

1. Adja hozzá a Windows Server feladatátvételi fürtszolgáltatást.
1. Ellenőrizze a fürtöt.
1. Hozza létre a feladatátvevő fürtöt.
1. Hozzon létre egy Felhőbeli tanúsító (Windows Server 2016 és újabb) vagy a tanúsító fájlmegosztás (Windows Server 2012 R2 és régebbi verziók esetében).


### <a name="add-windows-server-failover-clustering"></a>Windows Server feladatátvételi fürtszolgáltatás hozzáadása

1. Kapcsolódjon az első virtuális géphez RDP használatával egy olyan tartományi fiókkal, amely tagja a helyi rendszergazdáknak, és amely jogosult objektumok létrehozására Active Directoryban. Ezt a fiókot használja a többi konfigurációhoz.

1. [Adja hozzá a feladatátvételi fürtszolgáltatást az egyes virtuális gépekhez](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ellenőrizze a fürtöt a felhasználói felületen vagy a PowerShell használatával.

A fürt a felhasználói felületen való ellenőrzéséhez hajtsa végre a következő lépéseket az egyik virtuális gépen:

1. A **Kiszolgálókezelő**területen válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő**lehetőséget.
1. A **Feladatátvevőfürt-kezelő**alatt válassza a **művelet**, majd a **Konfiguráció ellenőrzése**lehetőséget.
1. Kattintson a **Tovább** gombra.
1. A **kiszolgálók vagy fürt kijelölése**területen adja meg mindkét virtuális gép nevét.
1. A **tesztelési beállítások**területen válassza a **csak a kiválasztott tesztek futtatása**lehetőséget. Kattintson a **Tovább** gombra.
1. A **teszt kiválasztása**területen válassza a minden teszt lehetőséget a **tárolás** és a **közvetlen tárolóhelyek**kivételével, ahogy az itt látható:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Fürt-ellenőrzési tesztek kiválasztása":::

1. Kattintson a **Tovább** gombra.
1. A **megerősítés**területen válassza a **tovább**lehetőséget.

A **Konfiguráció ellenőrzése varázsló** futtatja az ellenőrző teszteket.

A fürt a PowerShell használatával történő ellenőrzéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

A fürt érvényesítése után hozza létre a feladatátvevő fürtöt.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következők szükségesek:
- A fürtcsomópontok lesznek a virtuális gépek nevei.
- A feladatátvevő fürt neve
- A feladatátvevő fürt IP-címe. Olyan IP-címet is használhat, amely nem szerepel ugyanazon az Azure-beli virtuális hálózaton és az alhálózaton, mint a fürtcsomópontok.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 a Windows Server 2016 használatával

A következő PowerShell-parancsfájl egy feladatátvevő fürtöt hoz létre a Windows Server 2012 rendszerhez a Windows Server 2016 használatával. Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

A következő PowerShell-szkript létrehoz egy feladatátvevő fürtöt a Windows Server 2019-hez. További információ: [feladatátvevő fürt: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Frissítse a parancsfájlt a csomópontok nevével (a virtuális gépek neveivel) és egy elérhető IP-címmel az Azure virtuális hálózatból.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Felhőbeli tanúsító létrehozása (Win 2016 +)

Ha a Windows Server 2016-es és újabb operációs rendszert használ, létre kell hoznia egy Felhőbeli tanúsító. A Felhőbeli tanúsító az Azure Storage-blobokban tárolt fürtözött kvórum tanúsító új típusa. Ezzel a művelettel megszűnik a tanúsító megosztást üzemeltető különálló virtuális gép, illetve egy különálló fájlmegosztás használata.

1. [Hozzon létre egy Felhőbeli tanúsító a feladatátvevő fürthöz](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Hozzon létre egy BLOB-tárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címét.

### <a name="configure-quorum"></a>Kvórum konfigurálása 

A Windows Server 2016-es és újabb rendszereken konfigurálja úgy a fürtöt, hogy az imént létrehozott Felhőbeli tanúsító használja. Kövesse az összes lépést a [kvórum tanúsító konfigurálásához a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

A Windows Server 2012 R2 és régebbi verziók esetében kövesse a [kvórum tanúsító konfigurálása a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) , de a **kvórum tanúsító kijelölése** lapon jelölje be a tanúsító **fájlmegosztás beállítása** lehetőséget. Adja meg a tanúsító fájlmegosztás számára lefoglalt fájlmegosztást, függetlenül attól, hogy egy különálló virtuális gépen van-e konfigurálva, vagy az Azure-ból van-e csatlakoztatva. 


## <a name="step-4-test-cluster-failover"></a>4. lépés: a fürt feladatátvételének tesztelése

A fürt feladatátvételének tesztelése. A **Feladatátvevőfürt-kezelőban**kattintson a jobb gombbal a fürtre, és válassza a **További műveletek**elemet  >  az**alapszintű fürterőforrás**  >  **kiválasztása csomópontra**, majd válassza ki a fürt másik csomópontját. Helyezze át az alapszintű fürt erőforrását a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha sikeresen át tudja helyezni a fürtöt az egyes csomópontokra, készen áll a SQL Server telepítésére.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Fürt feladatátvételének tesztelése az alapvető erőforrás más csomópontokra való áthelyezésével":::

## <a name="step-5-create-the-sql-server-fci"></a>5. lépés: hozza létre a SQL Server-t.

Miután konfigurálta a feladatátvevő fürtöt, létrehozhatja a SQL Server-t.

1. Kapcsolódjon az első virtuális géphez RDP használatával.

1. **Feladatátvevőfürt-kezelő**ellenőrizze, hogy az összes fő fürterőforrás az első virtuális gépen van-e. Ha szükséges, helyezze át az összes erőforrást erre a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az egyik Azure Marketplace-lemezképet használja, az adathordozó a következő helyen található: `C:\SQLServer_<version number>_Full` . Válassza a **beállítás**lehetőséget.

1. A **SQL Server telepítési központban**válassza a **telepítés**lehetőséget.

1. Válassza az **új SQL Server feladatátvevő fürt telepítése**lehetőséget. A varázsló utasításait követve telepítse a SQL Server-t.

   Az adatkönyvtárak a prémium fájlmegosztás esetében szükségesek. Adja meg a megosztás teljes elérési útját a következő formában: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Megjelenik egy figyelmeztetés, amely arról tájékoztat, hogy a fájlkiszolgáló adatkönyvtárként van megadva. Ez a figyelmeztetés várható. Győződjön meg arról, hogy a fájlmegosztás megtartása után a virtuális géphez RDP-t használó felhasználói fiók ugyanaz a fiók, amelyet a SQL Server szolgáltatás használ a lehetséges hibák elkerülése érdekében.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Fájlmegosztás használata SQL-adatkönyvtárakként":::

1. A varázsló lépéseinek elvégzése után a telepítő telepíti az első csomóponton egy SQL Server-t.

1. Miután a telepítő telepíti a (z)-t az első csomóponton, csatlakozzon a második csomóponthoz RDP használatával.

1. Nyissa meg a **SQL Server telepítési központot**. Válassza a **telepítés**lehetőséget.

1. Válassza **a csomópont hozzáadása SQL Server feladatátvevő fürthöz**lehetőséget. A varázsló utasításait követve telepítse a SQL Servert, és adja hozzá a kiszolgálót a modulhoz.

   >[!NOTE]
   >Ha SQL Server használatával Azure Marketplace-katalógust használt, SQL Server eszközöket tartalmazott a rendszerképben. Ha nem használja ezeket a képeket, telepítse külön a SQL Server-eszközöket. Lásd: [SQL Server Management Studio letöltése (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>6. lépés: az Azure Load Balancer létrehozása

Az Azure Virtual Machines a fürtök terheléselosztást használnak egy olyan IP-cím tárolására, amely egyszerre egy fürtcsomópont számára szükséges. Ebben a megoldásban a terheléselosztó a SQL Server-es verzió IP-címét tárolja.

További információ: [Azure Load Balancer létrehozása és konfigurálása](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása a Azure Portal

A terheléselosztó létrehozása:

1. A Azure Portal lépjen a virtuális gépeket tartalmazó erőforráscsoporthoz.

1. Válassza a **Hozzáadás** lehetőséget. Keresse meg **Load Balancer**az Azure Marketplace piactéren. Válassza a **Load Balancer**lehetőséget.

1. Kattintson a **Létrehozás** gombra.

1. Állítsa be a terheléselosztó értékét a következő értékek használatával:

   - **Előfizetés**: az Azure-előfizetése.
   - **Erőforráscsoport**: a virtuális gépeket tartalmazó erőforráscsoport.
   - **Name (név**): a terheléselosztó azonosítására szolgáló név.
   - **Régió**: az Azure-beli hely, amely a virtuális gépeket tartalmazza.
   - **Típus**: nyilvános vagy magánjellegű. A magánhálózati terheléselosztó a virtuális hálózaton belülről érhető el. A legtöbb Azure-alkalmazás saját Load balancert használhat. Ha az alkalmazásnak közvetlenül az interneten keresztül kell SQL Servera, használjon nyilvános Load balancert.
   - **SKU**: standard.
   - **Virtuális hálózat**: ugyanaz a hálózat, mint a virtuális gépek.
   - **IP-cím hozzárendelése**: statikus. 
   - **Magánhálózati IP-cím**: az SQL Server-alapú fürt hálózati erőforrásához hozzárendelt IP-cím.

   A következő képen a **Load Balancer** felhasználói felületének létrehozása látható:

   ![A terheléselosztó beállítása](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>A terheléselosztó háttér-készletének konfigurálása

1. Térjen vissza az Azure-erőforráscsoporthoz, amely tartalmazza a virtuális gépeket, és keresse meg az új Load balancert. Előfordulhat, hogy frissítenie kell a nézetet az erőforráscsoporthoz. Válassza ki a Load balancert.

1. Válassza ki a **háttérbeli készletek**elemet, majd kattintson a **Hozzáadás**gombra.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk**területen válassza a **virtuális gép** lehetőséget, és válassza ki azokat a virtuális gépeket, amelyek fürtcsomópontokként fognak részt venni. Ügyeljen arra, hogy minden olyan virtuális gépet tartalmazzon, amely a (z)-t fogja üzemeltetni.

1. A háttér-készlet létrehozásához kattintson **az OK gombra** .

### <a name="configure-a-load-balancer-health-probe"></a>Terheléselosztó állapotmintájának konfigurálása

1. A terheléselosztó panelen válassza az **állapot**-mintavétel lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. Az **állapotfelmérés hozzáadása** panelen állítsa be a következő <span id="probe"> </span> állapot-mintavételi paramétereket.

   - **Name (név**): az állapot mintavételének neve.
   - **Protokoll**: TCP.
   - **Port**: az [ebben a lépésben](#ports)a tűzfalban az állapot-mintavételhez létrehozott port. Ebben a cikkben a példa a TCP-portot használja `59999` .
   - **Intervallum**: 5 másodperc.
   - Nem megfelelő **állapot küszöbértéke**: 2 egymást követő hiba.

1. Kattintson az **OK** gombra.

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A terheléselosztó panelen válassza a **terheléselosztási szabályok**lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. Állítsa be a terheléselosztási szabály paramétereit:

   - **Name (név**): a terheléselosztási szabályok neve.
   - Előtérbeli **IP-cím**: az SQL Server-es hálózati erőforrás IP-címe.
   - **Port**: a SQL Server%-os TCP-port. Az alapértelmezett példány portja 1433.
   - **Háttér-port**: ugyanazt a portot használja, mint a **port** értéke, ha engedélyezi a **lebegőpontos IP-címet (a közvetlen kiszolgáló visszaadását)**.
   - **Háttér-készlet**: a korábban konfigurált háttér-készlet neve.
   - **Állapot**mintavétele: a korábban konfigurált állapot-mintavétel.
   - **Munkamenet-megőrzés**: nincs.
   - **Üresjárati időkorlát (perc)**: 4.
   - **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)**: engedélyezve.

1. Kattintson az **OK** gombra.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>7. lépés: a fürt konfigurálása a mintavételhez

Állítsa be a fürt mintavételi portjának paraméterét a PowerShellben.

A fürt mintavételi portjának paraméterének beállításához módosítsa a következő parancsfájl változóit a környezetében lévő értékekkel. Távolítsa el a szögletes zárójeleket ( `<` és `>` ) a parancsfájlból.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A következő lista azokat az értékeket ismerteti, amelyeket frissítenie kell:

   - `<Cluster Network Name>`: A Windows Server feladatátvételi fürt neve a hálózaton. **Feladatátvevőfürt-kezelő**  >  **hálózatok**területen kattintson a jobb gombbal a hálózatra, és válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található.

   - `<SQL Server FCI IP Address Resource Name>`: Az SQL Server-es IP-cím erőforrás neve. **Feladatátvevőfürt-kezelő**  >  **szerepkörökben**, a **kiszolgáló neve**alatt, a SQL Server-es verzió szerepkör alatt kattintson a jobb gombbal az IP-cím erőforrásra, és válassza a **Tulajdonságok**lehetőséget. A helyes érték az **általános** lap **név** területén található.

   - `<ILBIP>`: A ILB IP-címe. Ez a címnek a Azure Portal a ILB előtér-címeként van konfigurálva. Ez a SQL Server-alapú IP-cím is. **Feladatátvevőfürt-kezelő** megtalálhatja ugyanazon tulajdonságlapon, ahol a található `<SQL Server FCI IP Address Resource Name>` .  

   - `<nnnnn>`: A terheléselosztó állapotának mintavétele során konfigurált mintavételi port. A fel nem használt TCP-portok érvényesek.

>[!IMPORTANT]
>A fürt paraméterének alhálózati maszkjának a TCP IP-szórási címnek kell lennie: `255.255.255.255` .

A fürt mintavételének beállítása után a PowerShellben láthatja a fürt összes paraméterét. Futtassa a következő parancsfájlt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>8. lépés: a feladatátvétel tesztelése

Feladatátvételi teszt – a fürt működésének ellenőrzéséhez. Hajtsa végre a következő lépéseket:

1. Az RDP használatával csatlakozzon az egyik SQL Server a csomóponthoz.

1. Nyissa meg **Feladatátvevőfürt-kezelő**. Válassza a **szerepkörök**lehetőséget. Figyelje meg, hogy melyik csomópont tulajdonosa a SQL Server.

1. Kattintson a jobb gombbal a SQL Server.

1. Válassza az **Áthelyezés**, majd a **lehető legjobb csomópont**lehetőséget.

**Feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Ezután az erőforrások átkerülnek a másik csomópontba, majd ismét elérhetővé válnak.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózaton. Nyissa meg a **SQL Server Management Studiot** , és kapcsolódjon a SQL Server.

>[!NOTE]
>Ha szükséges, [letöltheti SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Korlátozások

Az Azure Virtual Machines támogatja a Microsoft Elosztott tranzakciók koordinátora (MSDTC) szolgáltatást a Windows Server 2019 rendszeren a fürtözött megosztott kötetek (CSV) és a [standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)tárolásával.

Az Azure Virtual Machines-on az MSDTC nem támogatott a Windows Server 2016-es vagy újabb verzióiban, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Windows Server 2016 rendszeren, ha MSDTC-erőforrást hoz létre, az nem fog tudni használni megosztott tárterületet, még akkor sem, ha rendelkezésre áll tárterület. Ezt a problémát a Windows Server 2019-es verzióban javítottuk.
- Az alapszintű Load Balancer nem kezeli az RPC-portokat.

## <a name="see-also"></a>További információ

- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
