---
title: Az SQL Server FCI - Azure-beli virtuális gépek |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre SQL Server feladatátvevő fürt-példány az Azure Virtual machines szolgáltatásban.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 19910782142bf78c10dda155f40a5c41bdd64958
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842753"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Azure virtuális gépeken futó SQL Server feladatátvevő Fürtpéldányának konfigurálása

Ez a cikk bemutatja, hogyan hozhat létre egy SQL Server feladatátvevő fürtbeli példány (FCI) az Azure virtuális gépeken a Resource Manager-modellben. Ez a megoldás használ [a közvetlen tárolóhelyek a Windows Server 2016 Datacenter edition \(S2D\) ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) egy szoftveralapú virtuális TÁROLÓHÁLÓZAT, amely a tároló (adatlemezek) szinkronizálja a csomópontok között (az Azure VM-EK), egy Windows-fürt. S2D-t a Windows Server 2016 rendszerben jelent meg.

Az alábbi ábrán a kész megoldás Azure-beli virtuális gépeken:

![Rendelkezésre állási csoport](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Az előző ábrán látható:

- Két Azure-beli virtuális gépek Windows feladatátvevő fürt. Ha egy virtuális gép feladatátvevő fürtben más néven egy *fürtcsomópont*, vagy *csomópontok*.
- Minden egyes virtuális géphez van két vagy több adatlemezt.
- S2D szinkronizálja az adatokat lemezen lévő adatokat, és megadja a szinkronizált tároló-tárolókészletként.
- A tárolókészlet mutat be, hogy a feladatátvevő fürt fürt megosztott kötete (CSV).
- Az SQL Server FCI fürtszerepkör az adatmeghajtók a CSV-t használ.
- Az Azure load balancer, amely tárolja az IP-címet az SQL Server FCI-hez.
- Az Azure rendelkezésre állási csoport összes erőforrást tárolja.

   >[!NOTE]
   >A diagram az összes Azure-erőforrások találhatók ugyanabban az erőforráscsoportban vannak.

Az S2D kapcsolatos részletekért lásd: [a közvetlen tárolóhelyek a Windows Server 2016 Datacenter edition \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Az S2D architektúrák - konvergens és hiperkonvergens két típusát támogatja. Ebben a dokumentumban az architektúra a hiperkonvergens. Egy hiperkonvergens infrastruktúrával ugyanazokat a kiszolgálókat, amelyek a fürtözött alkalmazást helyez el a storage. Ebben az architektúrában a storage szolgáltatás, minden egyes SQL Server FCI-csomóponton.

## <a name="licensing-and-pricing"></a>Licencelés és díjszabás

Az Azure Virtual machines szolgáltatásban használatalapú fizetés (Használatalapú) használatával az SQL Server licencelése, de a saját licenc használata (BYOL) Virtuálisgép-lemezképeket. A lemezkép választja típusa határozza meg, hogyan számlázzuk.

A Használatalapú licencelés, a Feladatátvevőfürt-példány (FCI) az SQL Server Azure virtuális gépeken terhel az FCI-t, beleértve a passzív csomópontokat az összes csomópont. További információkért lásd: [SQL Server Enterprise Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Frissítési garanciával rendelkező nagyvállalati szerződéssel rendelkező ügyfelek minden aktív csomópont egy ingyenes passzív FCI csomópont használandó áll. Kihasználhatja az értékelem az Azure-ban, BYOL VM-rendszerképek használatához, majd a azonos licenc mindkét csomópontján az aktív és passzív az FCI-t. További információkért lásd: [nagyvállalati szerződés](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Hasonlítsa össze a Használatalapú és BYOL Azure virtuális gépeken futó SQL Server-példányok licencelésének lásd [SQL virtuális gépek – első lépések](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Tudnivalók az licencelési SQL Server teljes körű információkért lásd: [díjszabási](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>A példában az Azure-sablon

Az Azure-ban a teljes megoldást hozhat létre egy sablonból. Például egy sablont, a Githubon elérhető [Azure gyorsindítási sablonok](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ebben a példában van kialakítva, vagy nem tesztelt bármely adott számítási feladathoz. A sablon az S2D-tároló áttelepítése a tartományhoz csatlakoztatott, hozzon létre egy SQL Server FCI futtathatja. Kiértékelni a sablon, és módosítsa a célokra.

## <a name="before-you-begin"></a>Előkészületek

Nincsenek néhány dolgot tudnia kell, és néhány dolgot, hogy a szükséges helyen, mielőtt folytatja a műveletet.

### <a name="what-to-know"></a>Tudnivalók a
Rendelkeznie kell a következő technológiákat működési megismerése:

- [Windows-fürttechnológiák](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürt példányok](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Egy fontos különbség az, hogy az Azure IaaS virtuális gépek Vendég feladatátvevő fürtön, javasoljuk, hogy egyik hálózati Adapterre (fürtcsomópont) kiszolgáló és a egy önálló alhálózati száma. Az Azure-hálózatok rendelkezik fizikai redundanciát, így további hálózati adapterek és alhálózatok a szükségtelen az Azure IaaS virtuális gépek Vendég-fürtön. A fürt ellenőrzési jelentésében figyelmeztetést ad, hogy a csomópontok használata csak egyetlen hálózaton elérhető legyen, bár ez a figyelmeztetés figyelmen kívül hagyhatja biztonságosan Azure IaaS virtuális gépek Vendég feladatátvevő fürtökön. 

Emellett rendelkeznie kell a következő technológiákat egy általános ismertetése:

- [A Windows Server 2016 közvetlen tárolóhelyeket használó hiperkonvergens megoldás](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nem támogatott az SQL Server FCI az Azure-ban. Azt javasoljuk, hogy az FCI-ben részt vevő virtuális gépek távolítsa el a bővítményt. Ez a bővítmény szolgáltatásai, például az automatikus biztonsági mentés és a javítás és a egy portál funkciók az SQL támogatja. Ezek a funkciók nem működnek az SQL virtuális gépek, az ügynök eltávolítása után.

### <a name="what-to-have"></a>Mi szükséges

Ez a cikk utasításait követve előtt már rendelkeznie kell:

- A Microsoft Azure-előfizetés.
- Windows-tartomány Azure-beli virtuális gépeken.
- Az Azure-beli virtuális gépen objektumok létrehozásához szükséges engedéllyel rendelkező fiók.
- Egy Azure virtuális hálózat és a megfelelő IP-alhálózat címtere a következő összetevőket:
   - A virtuális gépeket is.
   - A feladatátvevő fürt IP-cím.
   - Minden egyes FCI IP-címet.
- A DNS konfigurálva a tartományvezérlők mutató, az Azure Network.

Ezek az előfeltételek teljesülnek folytathatja a feladatátvevő fürt létrehozásához. Az első lépés, hogy a virtuális gépek létrehozása.

## <a name="step-1-create-virtual-machines"></a>1. lépés: Virtuális gépek létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az előfizetéséhez.

1. [Hozzon létre egy Azure rendelkezésre állási csoport](../tutorial-availability-sets.md).

   A rendelkezésre állási csoportok a virtuális gépek beállítása a tartalék tartományok között, és frissítési tartományok. A rendelkezésre állási csoport gondoskodik arról, hogy az alkalmazás nem befolyásolja a hibaérzékeny pontokat, például a hálózati kapcsoló vagy egy kiszolgálóállvány kiszolgálók teljesítménye.

   Ha létrehozta az erőforráscsoport nem a virtuális gépekhez, tegye azt egy Azure rendelkezésre állási csoport létrehozásakor. Ha az Azure Portalon hozhat létre a rendelkezésre állási csoport használata esetén kövesse az alábbi lépéseket:

   - Az Azure Portalon kattintson a **+** az Azure Marketplace megnyitásához. Keresse meg **rendelkezésre állási csoport**.
   - Kattintson a **rendelkezésre állási csoport**.
   - Kattintson a **Create** (Létrehozás) gombra.
   - Az a **rendelkezésre állási csoport létrehozása** panelen állítsa be a következő értékeket:
      - **Név**: A rendelkezésre állási csoport nevét.
      - **Előfizetés**: Az Azure-előfizetése.
      - **Erőforráscsoport**: Ha egy meglévő csoportot szeretne, kattintson a **meglévő** és a legördülő listából válassza ki a csoportot. Ellenkező esetben válasszon **hozzon létre új** , és adja meg a csoport nevét.
      - **Hely**: Állítsa be a helyet, ha azt tervezi, hogy a virtuális gépek létrehozásakor.
      - **Tartalék tartományok**: Használja az alapértelmezett (3).
      - **Frissítési tartományok**: Használja az alapértelmezett (5).
   - Kattintson a **létrehozás** létrehozni a rendelkezésre állási beállítása.

1. A virtuális gépek létrehozása rendelkezésre állási csoportban.

   Az Azure rendelkezésre állási csoport két SQL Server virtuális gép kiépítése. Útmutatásért lásd: [az Azure Portalon az SQL Server virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

   Mindkét virtuális gép helye:

   - Ugyanazon Azure-ban, amely a rendelkezésre állási csoportban szerepel.
   - Ugyanazon a hálózaton, a tartományvezérlő.
   - A virtuális gépek és végül használhat a fürtön lévő összes példányoktól elegendő IP-címtér egy alhálózaton.
   - Az Azure rendelkezésre állási csoportban.   

      >[!IMPORTANT]
      >Nem állíthatók be, vagy rendelkezésre állási csoportot a virtuális gép létrehozása után módosíthatja.

   Kép kiválasztása az Azure Marketplace-ről. A Marketplace-en is használhatja az adott rendszerkép tartalmazza a Windows Server és SQL Server, vagy csak a Windows Server. További információkért lásd: [áttekintése az SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md)

   A hivatalos SQL Server-rendszerképeket az Azure katalógusában a telepített SQL Server-példány, valamint az SQL Server telepítési szoftver, valamint a szükséges kulcsot tartalmaznak.

   Válassza ki a megfelelő kép alapján kell fizetnie az SQL Server-licencét módját:

   - **Fizessen a használati licencek**: A másodpercenkénti költség rendszerképek tartalmazza az SQL Server-licenc:
      - **Az SQL Server 2016 Enterprise Windows Server Datacenter 2016**
      - **Az SQL Server 2016 Standard a Windows Server Datacenter 2016**
      - **Az SQL Server 2016 fejlesztői a Windows Server Datacenter 2016**

   - **Bring-your-saját licenc (használata BYOL)**

      - **{BYOL} Az SQL Server 2016 Enterprise Windows Server Datacenter 2016**
      - **{BYOL} Az SQL Server 2016 Standard a Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Miután létrehozta a virtuális gép, távolítsa el az előre telepített önálló SQL Server-példányon. Az előre telepített SQL Server-adathordozó használatával fog létrehozni az SQL Server FCI, Miután konfigurálta a feladatátvevő fürt és az S2D.

   Azt is megteheti használhatja az Azure Marketplace-rendszerképek, csak az operációs rendszerrel. Válasszon egy **Windows Server 2016 Datacenter** kép, és telepítse az SQL Server FCI, Miután konfigurálta a feladatátvevő fürt és az S2D-t. Ez a rendszerkép nem tartalmaz az SQL Server telepítési adathordozóról. Helyezze el a telepítési adathordozó egy olyan helyre, ahol futtathatja az SQL Server telepítése, minden olyan kiszolgáló esetén.

1. Miután az Azure létrehozza a virtuális gépek, minden virtuális gép RDP-vel csatlakozni.

   Amikor először csatlakozik a virtuális gép RDP-vel, a számítógép megkérdezi, hogy ez a PC-t a hálózaton észlelhető. Kattintson a **Yes** (Igen) gombra.

1. Ha az SQL Server-alapú virtuálisgép-rendszerképek egyikét használja, távolítsa el az SQL Server-példány.

   - A **programok és szolgáltatások**, kattintson a jobb gombbal **Microsoft SQL Server 2016 (64 bites)** kattintson **Eltávolítás/módosítás**.
   - Kattintson a **eltávolítása**.
   - Válassza ki az alapértelmezett példányt.
   - Távolítsa el az összes szolgáltatása mellett **adatbázismotor-szolgáltatások**. Ne távolítsa el az **közös szolgáltatások**. Tekintse meg a következő képen látható:

      ![Szolgáltatások eltávolítása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Kattintson a **tovább**, és kattintson a **eltávolítása**.

1. <a name="ports"></a>Nyissa meg a tűzfal portjait.

   Minden egyes virtuális gépen nyissa meg a következő portokat a Windows tűzfalon.

   | Cél | TCP-Port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port az SQL Server alapértelmezett példánya esetében. Ha a gyűjteményből használt lemezkép, a port automatikusan megnyílik.
   | Állapotadat-mintavétel | 59999 | Bármely nyitott TCP-port. Egy későbbi lépésben, a load balancer konfigurálása [állapotadat-mintavétel](#probe) és a fürt ezt a portot használja.  

1. Adja hozzá a tárolót a virtuális géphez. Részletes információkért lásd: [adja hozzá a tárolási](../disks-types.md).

   Mindkét virtuális gép legalább két adatlemezt kell.

   Nyers lemezt - nem NTFS formátumú lemezeket.
      >[!NOTE]
      >Ha NTFS fájlrendszerű lemezek csatolásához csak engedélyezheti az S2D nincs lemez jogosultsági ellenőrzéssel.  

   Legalább két prémium szintű SSD-k minden virtuális Géphez csatolása. Javasoljuk, hogy legalább P30 (1 TB-os) lemez.

   Set-állomás gyorsítótárazását a **csak olvasható**.

   A tárolási kapacitást az éles környezetben használja a számítási feladat függ. Ebben a cikkben leírt értékek mintaalkalmazásokban szemléltető jellegű és teszteléshez.

1. [A virtuális gépeket ad hozzá a már meglévő tartomány](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Miután a virtuális gépek létrehozása és konfigurálása, konfigurálhatja a feladatátvevő fürt.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>2. lépés: Az S2D a Windows feladatátvevő fürt konfigurálása

A következő lépés, hogy a feladatátvevő fürt konfigurálása az S2D-t. Ebben a lépésben a következő részlépések fogja végrehajtani:

1. Adja hozzá a Windows feladatátvételi fürtszolgáltatás
1. A fürt ellenőrzése
1. A feladatátvevő fürt létrehozása
1. A felhőbeli tanúsító létrehozása
1. Tároló hozzáadása

### <a name="add-windows-failover-clustering-feature"></a>Adja hozzá a Windows feladatátvételi fürtszolgáltatás

1. Első lépésként csatlakoznia kell az első virtuális gépen, amely tagja a helyi rendszergazdák, és jogosult objektumokat létrehozni az Active Directory tartományi fiókkal RDP-vel. Ezt a fiókot használja a további konfigurálást.

1. [Vegye fel a Feladatátvételi fürtszolgáltatást minden egyes virtuális géphez](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Feladatátvételi fürtszolgáltatás telepítése a felhasználói felületről, hajtsa végre a következő lépéseket mindkét virtuális gépeken.
   - A **Kiszolgálókezelő**, kattintson a **kezelés**, és kattintson a **szerepkörök és szolgáltatások hozzáadása**.
   - A **adja hozzá szerepkörök és szolgáltatások varázsló**, kattintson a **tovább** mindaddig, amíg kap **szolgáltatások kiválasztása**.
   - A **szolgáltatások kiválasztása**, kattintson a **feladatátvételi fürtszolgáltatás**. Az összes szükséges szolgáltatásokat és a felügyeleti eszközök közé tartozik. Kattintson a **funkciót hozzáadhat**.
   - Kattintson a **tovább** majd **Befejezés** a szolgáltatások telepítéséhez.

   A Feladatátvételi fürtszolgáltatás telepítése a PowerShell-lel, futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből a virtuális gépek egyike.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Referenciaként a következő lépésekhez kövesse a 3. lépését [a Windows Server 2016 közvetlen tárolóhelyeket használó hiperkonvergens megoldás](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ez az útmutató utasításokat alatt hivatkozik [fürt ellenőrzése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Ellenőrizze a fürtöt, a felhasználói felületen vagy a PowerShell használatával.

A felhasználói felületen keresztül a fürt érvényesítésének, kövesse az alábbi lépéseket a virtuális gépek közül.

1. A **Kiszolgálókezelő**, kattintson a **eszközök**, majd kattintson a **Feladatátvevőfürt-kezelőben**.
1. A **Feladatátvevőfürt-kezelőben**, kattintson a **művelet**, majd kattintson a **konfiguráció ellenőrzése...** .
1. Kattintson a **tovább**.
1. A **kiszolgálók kiválasztása vagy a fürt**, mindkét virtuális gép nevét írja.
1. A **tesztelési beállítások**, válassza a **csak a kijelölt tesztek futtatása**. Kattintson a **tovább**.
1. A **kijelölés tesztelése**, például kivételével az összes teszt **tárolási**. Tekintse meg a következő képen látható:

   ![Vizsgálat ellenőrzése](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Kattintson a **tovább**.
1. A **megerősítő**, kattintson a **tovább**.

A **konfiguráció ellenőrzése varázsló** az ellenőrző teszteket futtat.

A PowerShell-lel a fürt ellenőrzéséhez futtassa a következő szkriptet egy rendszergazdai PowerShell-munkamenetből a virtuális gépek egyik.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Miután a fürt érvényesítésének, a feladatátvevő fürt létrehozásához.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ez az útmutató hivatkozik [a feladatátvevő fürt létrehozása](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

A feladatátvevő fürt létrehozásához az alábbiak szükségesek:
- A virtuális gépek, amelyek a fürtcsomópontok nevei.
- A feladatátvevő fürt nevét
- A feladatátvevő fürt IP-címét. A fürtcsomópontokkal azonos Azure virtuális hálózat és alhálózat nem használatos IP-címet is használhatja.

Az alábbi PowerShell-lel egy feladatátvevő fürtöt hoz létre. Frissítse a parancsfájlt a csomópontok (virtuális gép neve) és a egy elérhető IP-címet az Azure virtuális hálózat neve:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása

Felhőbeli tanúsító a fürt kvórum tanúsítójának tárolva az Azure Storage Blob egy új típusú. Ez kiküszöböli a tanúsító fájlmegosztás üzemeltetése különálló virtuális gépek.

1. [Felhőbeli tanúsító a feladatátvevő fürt létrehozása](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Hozzon létre egy blobtárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címe.

1. A feladatátvevő fürt kvórum tanúsító konfigurálása. Látható, [a kvórum tanúsító konfigurálása a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) a felhasználói felületen.

### <a name="add-storage"></a>Tároló hozzáadása

A lemezek az S2D-t kell üres és nem tartalmazhatnak partíciókat vagy más adatokat. Lemezek kövesse tiszta [a jelen útmutató lépéseit](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Közvetlen tárolóhelyek engedélyezése Store \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell lehetővé teszi a közvetlen tárolóhelyek.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   A **Feladatátvevőfürt-kezelőben**, most már megtekintheti a tárolókészlethez.

1. [Hozzon létre egy kötetet](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Az S2D-t a szolgáltatások egyike, hogy automatikusan létrehoz egy tárolókészletet Ha engedélyezi azt. Most már készen áll a kötet létrehozásához. A PowerShell-parancsmag segítségével `New-Volume` automatizálja a kötet létrehozását, beleértve a formázást, felvétele a fürtbe, és egy fürt megosztott kötete (CSV) létrehozása. A következő példában létrehozunk egy 800 gigabájt (GB), fürt megosztott kötetei szolgáltatás.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Ez a parancs befejeződése után a-800 GB-os kötet csatlakoztatva van, egy fürtöt erőforrásként. A kötet jelenleg `C:\ClusterStorage\Volume1\`.

   Az alábbi ábrán látható egy fürt megosztott kötetén az S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3. lépés: A feladatátvevő fürt feladatátvételi teszt

A Feladatátvevőfürt-kezelő, győződjön meg arról, hogy a tárolási erőforrások áthelyezheti a többi fürtcsomóponton. Ha a feladatátvevő fürt csatlakozhat **Feladatátvevőfürt-kezelőben** és a tárterület áthelyezését egyik csomópontról a másikra, készen áll az FCI konfigurálása.

## <a name="step-4-create-sql-server-fci"></a>4. lépés: Az SQL Server FCI létrehozása

Miután konfigurálta a feladatátvevő fürt és a fürt-összetevők, beleértve a tárolási, az SQL Server FCI hozhat létre.

1. Csatlakozzon RDP-vel az első virtuális gépen.

1. A **Feladatátvevőfürt-kezelőben**, ellenőrizze, hogy az összes fürt alapvető erőforrásai vannak az első virtuális gépen. Ha szükséges, helyezze át az összes erőforrás a virtuális gép.

1. Keresse meg a telepítési adathordozón. A virtuális gép az Azure Marketplace-rendszerképek egyikét használja, ha az adathordozó a `C:\SQLServer_<version number>_Full`. Kattintson a **telepítő**.

1. Az a **SQL Server telepítési központjának**, kattintson a **telepítési**.

1. Kattintson a **új SQL Server feladatátvevő fürt telepítése**. Kövesse a varázsló utasításait az SQL Server FCI telepítéséhez.

   Az FCI adatkönyvtárak kell lennie a fürtözött tárhelyet. Az S2D nincs megosztott lemezzel, de minden kiszolgálón kötetre csatlakoztatási pontját. Az S2D szinkronizálja a kötet mindkét csomópont között. A kötet a fürt jeleníti meg egy megosztott fürtkötethez. Használja a fürt megosztott kötetei szolgáltatás csatlakoztatási pont az adatok könyvtárakat.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. A varázsló befejezése után a telepítőprogram telepíti egy SQL Server FCI első csomópontjára.

1. A telepítő sikeres telepítése után az FCI első csomópontjára a második csomópont RDP-vel csatlakozni.

1. Nyissa meg a **SQL Server telepítési központjának**. Kattintson a **telepítési**.

1. Kattintson a **csomópont hozzáadása az SQL Server feladatátvevő fürtre történő**. Az SQL server telepítése, és adja a kiszolgálót az FCI-t a varázsló utasításait követve.

   >[!NOTE]
   >Ha az Azure Marketplace-en image z galerie használt SQL Server, SQL Server-eszközök szerepeltek a lemezképpel. Ha nem használja ezt a képet, az SQL Server-eszközök külön kell telepítenie. Lásd: [töltse le az SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>5. lépés: Azure-terheléselosztó létrehozása

Az Azure virtual machines, a fürtök használja egy terheléselosztó, amely tárolja az IP-címet, amely egyszerre csak egy fürtcsomóponton kell. Ebben a megoldásban a terheléselosztó IP-címét tárolja az SQL Server FCI-hez.

[Hozzon létre, és a egy Azure load balancer konfigurálása](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása az Azure Portalon

A load balancer létrehozása:

1. Az Azure Portalon nyissa meg az erőforráscsoport, a virtuális gépekkel.

1. Kattintson a **+Hozzáadás** gombra. Keressen a piactéren **Load Balancer**. Kattintson a **Load Balancer**.

1. Kattintson a **Create** (Létrehozás) gombra.

1. Az a load balancer konfigurálása:

   - **Név**: Egy név, amely azonosítja a terheléselosztóhoz.
   - **Típus**: A terheléselosztó a nyilvános vagy saját lehet. Privát load balancer az egyazon vneten érhetők el. A legtöbb Azure-alkalmazások privát terheléselosztó használható. Ha az alkalmazásnak az SQL Server elérését kell közvetlenül az interneten keresztül, használja a nyilvános load balancer.
   - **Virtuális hálózat**: Ugyanazon a hálózaton a virtuális gépeket.
   - **Alhálózat**: A virtuális gépek azonos alhálózatban.
   - **Magánhálózati IP-cím**: Az azonos IP-cím, amelyet az SQL Server FCI fürt hálózati erőforráshoz rendelt.
   - **Előfizetés**: Az Azure-előfizetése.
   - **Erőforráscsoport**: A virtuális gépek ugyanazt az erőforráscsoportot használja.
   - **Hely**: Használja az ugyanazon Azure-beli hely, a virtuális gépek.
   Tekintse meg a következő képen látható:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>A terheléselosztó háttérkészletének konfigurálása

1. Térjen vissza az Azure-erőforráscsoport, a virtuális gépekkel, és keresse meg az új terheléselosztót. Előfordulhat, hogy az az erőforráscsoport a nézet frissítéséhez. Kattintson a terheléselosztóra.

1. Kattintson a **háttérkészletek** kattintson **+ Hozzáadás** háttérkészlet hozzáadása.

1. A háttérkészlet társítása a rendelkezésre állási csoport, amely tartalmazza a virtuális gépeket.

1. A **cél IP-konfigurációja**, ellenőrizze **virtuális gép** , és válassza ki a virtuális gépeket, amely fürtcsomópontként részt fog venni. Mindenképp adja hozzá az összes, az FCI-t futtató virtuális gépet. 

1. Kattintson a **OK** hozhat létre a háttérkészlethez.

### <a name="configure-a-load-balancer-health-probe"></a>A terheléselosztó állapotmintája konfigurálása

1. A load balancer panelen kattintson a **állapotadat-mintavételek**.

1. Kattintson a **+Hozzáadás** gombra.

1. Az a **állapotadat-mintavétel hozzáadása** panelen <a name="probe"> </a>állapota Hálózatfigyelő paramétereinek a beállításához:

   - **Név**: Az állapotminta neve.
   - **Protokoll**: TCP.
   - **Port**: Az elérhető TCP-port beállítása. Ezt a portot egy megnyitott tűzfallal portra van szüksége. Használja a [ugyanazt a portot](#ports) a tűzfal beállítása az állapotmintához.
   - **Intervallum**: 5 másodperc.
   - **Nem kifogástalan állapot küszöbértéke**: 2 egymást követő hibák.

1. Kattintson az OK gombra.

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A load balancer panelen kattintson a **terheléselosztási szabályok**.

1. Kattintson a **+Hozzáadás** gombra.

1. Állítsa be a terheléselosztási szabályok paraméterek:

   - **Név**: A terheléselosztási szabályok nevét.
   - **Előtérbeli IP-cím**: IP-címet használja az SQL Server FCI fürt hálózati erőforráshoz.
   - **Port**: Állítsa be az SQL Server FCI TCP-porton. Az alapértelmezett példány port az 1433-as.
   - **Háttérport**: Ezt az értéket használja ugyanazt a portot, mint a **Port** értéket, ha engedélyezi a **fix IP-(közvetlen kiszolgálói válasz)**.
   - **Háttérkészlet**: Használja a korábban konfigurált háttérkészlet neve.
   - **Az állapotfigyelő mintavételező**: Használja az állapotmintát, korábban konfigurált.
   - **Munkamenet megőrzését**: Nincs.
   - **Üresjárat időkorlátja (perc)**: 4.
   - **Nem fix IP (közvetlen kiszolgálói válasz)**: Engedélyezve

1. Kattintson az **OK** gombra.

## <a name="step-6-configure-cluster-for-probe"></a>6. lépés: Fürt mintavétel konfigurálása

Állítsa be a fürt mintavételi port paraméternek a PowerShellben.

Beállítása a fürt mintavételi port paraméternek, frissítse a változók a következő szkriptet a környezete értékeivel. Távolítsa el a csúcsos zárójeleket `<>` a parancsfájlból. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Az előző szkriptben a környezet értékeinek beállítását. Az alábbi lista részletesen ismerteti az értékeket:

   - `<Cluster Network Name>`: A Windows Server feladatátvevő fürt nevét a hálózaton. A **Feladatátvevőfürt-kezelőben** > **hálózatok**, kattintson a jobb gombbal a hálózaton, és kattintson a **tulajdonságok**. A helyes érték alatt **neve** a a **általános** fülre. 

   - `<SQL Server FCI IP Address Resource Name>`: Az SQL Server FCI IP-cím erőforrás neve. A **Feladatátvevőfürt-kezelőben** > **szerepkörök**, az SQL Server FCI szerepkör alatt a **kiszolgálónév**, kattintson a jobb gombbal az IP-cím erőforráshoz, majd kattintson a **Tulajdonságok**. A helyes érték alatt **neve** a a **általános** fülre. 

   - `<ILBIP>`: Az ILB IP-cím. Ez a cím előtér ILB-címét az Azure Portalon van konfigurálva. Ez akkor is az SQL Server FCI IP-címet. Annak a **Feladatátvevőfürt-kezelőben** ugyanazon az oldalon tulajdonságok hol található a `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: A mintavételi portot, a terheléselosztó állapotmintája konfigurálva van. Minden nem használt TCP-portra érvényes. 

>[!IMPORTANT]
>Az alhálózati maszkot a fürt paraméternek kell lennie a szórási TCP IP-cím: `255.255.255.255`.

Miután beállította a fürt mintavétel láthatja az összes fürt paramétert a PowerShellben. Futtassa a következő parancsfájlt:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>7. lépés: Az FCI feladatátvételi teszt

Az FCI ellenőrzése a fürt funkció feladatátvételi tesztje. Kövesse az alábbi lépéseket:

1. Csatlakozzon RDP-vel az SQL Server FCI fürtcsomópontok egyike.

1. Nyissa meg **Feladatátvevőfürt-kezelőben**. Kattintson a **szerepkörök**. Figyelje meg, melyik csomóponton az SQL Server FCI-szerepkört.

1. Kattintson a jobb gombbal az SQL Server FCI-szerepkört.

1. Kattintson a **áthelyezése** kattintson **lehető legalkalmasabb csomópontra**.

**A Feladatátvevőfürt-kezelő** jeleníti meg, a szerepkör és erőforrásainak kapcsolat nélküli módba. Az erőforrások Ezután helyezze át, és a többi csomóponton online állapotba kerül.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat tesztelése, jelentkezzen be egy másik virtuális géphez ugyanazon a virtuális hálózaton. Nyissa meg **SQL Server Management Studio** , és kapcsolódhat az SQL Server FCI nevét.

>[!NOTE]
>Ha szükséges, akkor az [töltse le az SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Korlátozások

A fürt megosztott kötetei (CSV) tárolással az Azure Virtual Machines támogatja a Windows Server verzióját 2019 Microsoft elosztott tranzakciók koordinátora (MSDTC) és a egy [a standard load balancer](../../../load-balancer/load-balancer-standard-overview.md).

Azure-beli virtuális gépeken az MSDTC nem támogatott a Windows Server 2016-os vagy korábbi mert:

- A fürtözött MSDTC-erőforrást nem lehet konfigurálni a megosztott tárolók használatához. Windows Server 2016-MSDTC-erőforrás létrehozásakor, nem jelenik meg minden megosztott tároló elérhetővé használatra, akkor is, ha a tároló nem létezik. Ez a hiba elhárítása a Windows Server 2019.
- Az alapszintű load balancer RPC-portok nem kezeli.

## <a name="see-also"></a>Lásd még:

[A telepítő az S2D a távoli asztal (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[A közvetlen tárolóhelyek hiperkonvergens megoldás](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[A közvetlen tárolóhelyek áttekintése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[S2D-t az SQL Server támogatása](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
