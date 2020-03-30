---
title: SQL Server FCI – Azure virtuális gépek | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan hozhat létre egy SQL Server feladatátvételi fürtpéldányt az Azure virtuális gépeken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249801"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>SQL Server feladatátvételi fürtpéldány konfigurálása Azure-beli virtuális gépeken

Ez a cikk bemutatja, hogyan hozhat létre egy SQL Server feladatátvételi fürtpéldány (FCI) az Azure Resource Manager modell ben az Azure Resource Manager modell. Ez a megoldás [a Windows Server 2016 Datacenter edition Storage Spaces Direct-et](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) szoftveralapú virtuális SAN-ként használja, amely szinkronizálja a Windows-fürt csomópontok (Azure virtuális gépek) közötti tárolót (adatlemezeket). A Közvetlen tárolóhelyek új a Windows Server 2016-ban.

Az alábbi ábra a teljes megoldást mutatja be az Azure virtuális gépeken:

![A teljes megoldás](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Ez az ábra a következőket mutatja:

- Két Azure virtuális gép egy Windows Server feladatátvevő fürtben. Ha egy virtuális gép feladatátvevő fürtben van, *fürtcsomópontnak* vagy csomópontnak is *nevezik.*
- Minden virtuális gép két vagy több adatlemezzel rendelkezik.
- A Közvetlen tárolóhelyek szinkronizálja az adatlemezeken lévő adatokat, és a szinkronizált tárolókészletet tárolókészletként jelenik meg.
- A tárolókészlet egy fürt megosztott kötetét (CSV) mutatja be a feladatátvevő fürtnek.
- Az SQL Server FCI fürtszerepköre a CSV-t használja az adatmeghajtókhoz.
- Az SQL Server FCI IP-címét tároló Azure-terheléselosztó.
- Az Azure rendelkezésre állási készlete az összes erőforrást tartalmazza.

>[!NOTE]
>A diagramban lévő összes Azure-erőforrás ugyanabban az erőforráscsoportban van.

A Közvetlen tárolóhelyekről a [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)című témakörben talál.

A Közvetlen tárolóhelyek kétféle architektúrát támogat: konvergens és hiperkonvergens. A dokumentum architektúrája hiperkonvergens. A hiperkonvergens infrastruktúra a tárolót ugyanazokon a kiszolgálókon helyezi el, amelyek a fürtözött alkalmazást üzemeltetik. Ebben az architektúrában a tároló minden SQL Server FCI-csomóponton található.

## <a name="licensing-and-pricing"></a>Licencelés és árképzés

Az Azure virtuális gépeken licencelheti az SQL Servert használatalapú fizetéses (PAYG) vagy bring-your-own-license (BYOL) virtuálisgép-lemezképek használatával. A választott képtípus befolyásolja a díjfelszámítás módját.

A felosztó-kioldás licencelésével az SQL Server feladatátvételi fürtpéldánya (FCI) az Azure virtuális gépeken az FCI összes csomópontja, beleértve a passzív csomópontokat is, díjat számít fel. További információt az [SQL Server Enterprise Virtual Machines Díjszabás című témakörben talál.](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Ha nagyvállalati szerződéssel rendelkezik a Frissítési Garanciával, minden aktív csomóponthoz használhat egy ingyenes passzív FCI-csomópontot. Az Azure-beli előnyök kihasználásához használja a BYOL virtuálisgép-lemezképeket, és használja ugyanazt a licencet az FCI aktív és passzív csomópontjain. További információ: [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Az Azure virtuális gépeken futó SQL Server pay-as-you-go és BYOL licencelésének összehasonlításához [olvassa el az SQL virtuális gépek kezelésének első lépéseit.](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)

Az SQL Server licenceléséről az [Árképzés](https://www.microsoft.com/sql-server/sql-server-2017-pricing)című témakörben talál teljes körű információt.

### <a name="example-azure-template"></a>Példa Azure-sablonra

Ezt a teljes megoldást létrehozhatja az Azure-ban egy sablonból. Egy sablon példa érhető el a GitHub [Azure gyorsindítási sablonjaiban.](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) Ez a példa nem tervezett vagy tesztelt adott számítási feladatokhoz. A sablon futtatásával sql Server FCI-t hozhat létre a tárolóhelyek közvetlen tárolójával a tartományhoz csatlakoztatva. Kiértékelheti a sablont, és módosíthatja a saját céljainak megfelelően.

## <a name="before-you-begin"></a>Előkészületek

Van néhány dolog, amit tudnod kell, és a helyén, mielőtt elkezdené.

### <a name="what-to-know"></a>Mit kell tudni
A következő technológiák operatív ismereteket kell, hogy megértse:

- [Windows fürttechnológiák](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürtpéldányai](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Egy dolog, hogy tudatában legyünk, hogy egy Azure IaaS virtuális gép vendég feladatátvételi fürt, javasoljuk, hogy egy kiszolgálónkénti hálózati adapter (fürtcsomópont) és egyetlen alhálózat. Az Azure networking fizikai redundanciával rendelkezik, ami szükségtelenné teszi a további hálózati adaptereket és alhálózatokat egy Azure IaaS virtuális gép vendégfürtén. A fürtérvényesítési jelentés figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetőek el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure IaaS virtuális gép vendég feladatátvételi fürtjein.

Általános ismereteket kell, hogy kell, hogy legyenek ezek a technológiák:

- [Közvetlen tárolóhelyeket használó, hiperkonvergens megoldások a Windows Server 2016 rendszerben](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg az SQL Server feladatátvételi fürtpéldányai az Azure virtuális gépeken csak az [SQL Server IaaS Ügynökbővítmény](virtual-machines-windows-sql-server-agent-extension.md) [könnyű felügyeleti módjával](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) támogatottak. A teljes bővítménymódról a könnyűre váltáshoz törölje a megfelelő virtuális gépek **SQL virtuálisgép-erőforrását,** majd regisztrálja őket az SQL virtuálisgép-erőforrás-szolgáltatóval könnyű módban. Amikor törli az **SQL virtuálisgép-erőforrást** az Azure Portalon, **törölje a jelet a megfelelő virtuális gép melletti jelölőnégyzetből.** A teljes bővítmény támogatja az olyan funkciókat, mint az automatikus biztonsági mentés, a javítás és a speciális portálkezelés. Ezek a szolgáltatások nem fognak működni az SQL virtuális gépeken, miután az ügynök újratelepítése könnyű felügyeleti módban.

### <a name="what-to-have"></a>Mi van

A cikkben ismertetett lépések elvégzése előtt már rendelkeznie kell a következőkre:

- Microsoft Azure-előfizetés.
- Windows-tartomány az Azure virtuális gépein.
- Olyan fiók, amely rendelkezik az Azure virtuális gépeken és az Active Directoryban lévő objektumok létrehozására vonatkozó engedélyekkel.
- Az Azure virtuális hálózat és alhálózat elegendő IP-címterülettel rendelkezik az alábbi összetevők számára:
   - Mindkét virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Minden FCI IP-cím.
- Az Azure-hálózaton konfigurált DNS, amely a tartományvezérlőkre mutat.

Ezekkel az előfeltételekkel megkezdheti a feladatátvevő fürt létrehozásához. Az első lépés a virtuális gépek létrehozása.

## <a name="step-1-create-the-virtual-machines"></a>1. lépés: A virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az előfizetésével.

1. [Hozzon létre egy Azure rendelkezésre állási készletet.](../tutorial-availability-sets.md)

   A rendelkezésre állási készlet csoportok virtuális gépek között tartalék tartományok és a tartományok frissítése. Ez biztosítja, hogy az alkalmazás nem érinti az egyetlen meghibásodási pontok, mint például a hálózati kapcsoló vagy a tápegység egy rack szerverek.

   Ha még nem hozta létre az erőforráscsoportot a virtuális gépek, ezt akkor, amikor létrehoz egy Azure rendelkezésre állási csoport. Ha az Azure Portalon hozza létre az elérhetőségi csoportot, tegye meg az alábbi lépéseket:

   1. Az Azure Portalon válassza **az erőforrás létrehozása** az Azure Marketplace megnyitásához. Az **Elérhetőség készlet**keresése .
   1. Válassza **az Elérhetőségi csoport lehetőséget.**
   1. Kattintson a **Létrehozás** gombra.
   1. A **Rendelkezésre állási csoport létrehozása csoportban**adja meg az alábbi értékeket:
      - **Név**: Az elérhetőségi csoport neve.
      - **Előfizetés**: Az Azure-előfizetés.
      - **Erőforráscsoport**: Ha meglévő csoportot szeretne használni, kattintson **a Meglévő kijelölése** gombra, majd válassza ki a csoportot a listából. Ellenkező esetben válassza **az Új létrehozása lehetőséget,** és adja meg a csoport nevét.
      - **Hely**: Állítsa be azt a helyet, ahol a virtuális gépeket létre kívánja hozni.
      - **Tartalék tartományok**: Használja az alapértelmezett (**3**).
      - **Tartományok frissítése**: Használja az alapértelmezett (**5**).
   1. Az elérhetőségi csoport létrehozásához válassza a **Létrehozás** lehetőséget.

1. Hozza létre a virtuális gépeket a rendelkezésre állási csoportban.

   Két SQL Server virtuális gép kiépítése az Azure rendelkezésre állási készletében. További információt az SQL Server virtuális gép kiépítése az Azure Portalon című [témakörben talál.](virtual-machines-windows-portal-sql-server-provision.md)

   Mindkét virtuális gép elhelyezése:

   - Ugyanabban az Azure-erőforráscsoportban, mint a rendelkezésre állási készlet.
   - Ugyanazon a hálózaton, mint a tartományvezérlő.
   - Olyan alhálózaton, amely elegendő IP-címterülettel rendelkezik mind a virtuális gépek, mind az összes olyan fümi-t tartalmazó rendszerhez, amelyet végül a fürtön használhat.
   - Az Azure rendelkezésre állási készletében.

      >[!IMPORTANT]
      >Virtuális gép létrehozása után nem állíthatja be és nem módosíthatja a rendelkezésre állási készletet.

   Válasszon egy képet az Azure Piactérről. Használhatja az Azure Marketplace-lemezképet, amely tartalmazza a Windows Server és az SQL Server, vagy egy, amely csak a Windows Server. További információt [az SQL Server áttekintése az Azure virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-server-iaas-overview.md)

   Az Azure Gallery hivatalos SQL Server-lemezképei közé tartozik egy telepített SQL Server-példány, az SQL Server telepítőszoftver és a szükséges kulcs.

   Válassza ki a megfelelő lemezképet az SQL Server licencért való fizetés módjától függően:

   - **Pay-per-usage licenc .** Ezeknek a képeknek a másodpercenkénti költsége tartalmazza az SQL Server licencelését:
      - **SQL Server 2016 Enterprise windows Server 2016 Datacenter rendszeren**
      - **Sql Server 2016 standard windows Server 2016 Datacenter rendszeren**
      - **SQL Server 2016 fejlesztői windows Server 2016 Datacenter rendszeren**

   - **Bring-your-own-license (BYOL)**

      - **((BYOL) SQL Server 2016 Enterprise windows Server 2016 Datacenter rendszeren**
      - **((BYOL) Sql Server 2016 standard windows Server 2016 Datacenter rendszeren**

   >[!IMPORTANT]
   >A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server-példányt. Az előre telepített SQL Server adathordozóhasználatával hozza létre az SQL Server FCI-t a feladatátvevő fürt és a Közvetlen tárolóhelyek beállítása után.

   Azt is megteheti, hogy csak az operációs rendszert tartalmazó Azure Marketplace-rendszerképeket is használhat. Válasszon egy **Windows Server 2016 Datacenter-lemezképet,** és telepítse az SQL Server FCI-t a feladatátvevő fürt és a Közvetlen tárolóhelyek beállítása után. Ez a lemezkép nem tartalmaz SQL Server telepítési adathordozót. Helyezze az SQL Server telepítési adathordozóját egy olyan helyre, ahol futtathatja az egyes kiszolgálókon.

1. Miután az Azure létrehozza a virtuális gépeket, csatlakozzon mindegyikhez az RDP használatával.

   Amikor először csatlakozik egy virtuális géphez az RDP használatával, egy kérdés megkérdezi, hogy engedélyezi-e a számítógép számára a hálózaton való észlelést. Válassza az **Igen** lehetőséget.

1. Ha az SQL Server-alapú virtuálisgép-lemezképek egyikét használja, távolítsa el az SQL Server-példányt.

   1. A **Programok és szolgáltatások menüpontban**kattintson a jobb gombbal a Microsoft SQL Server **2016 (64 bites) elemre,** és válassza **az Eltávolítás/módosítás parancsot.**
   1. Válassza **az Eltávolítás**lehetőséget.
   1. Válassza ki az alapértelmezett példányt.
   1. A Database **Engine Services**összes szolgáltatásának eltávolítása. Ne távolítsa el **a megosztott szolgáltatásokat**. A következő képernyőképhez hasonló képernyőkép jelenik meg:

      ![Szolgáltatások kiválasztása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Válassza a **Tovább**lehetőséget, majd az **Eltávolítás**lehetőséget.

1. <a name="ports"></a>Nyissa meg a tűzfalportokat.

   Minden virtuális gépen nyissa meg ezeket a portokat a Windows tűzfalon:

   | Cél | TCP-port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port az SQL Server alapértelmezett példányaihoz. Ha a gyűjteményből származó képet használt, a port automatikusan megnyílik.
   | Állapotadat-mintavétel | 59999 | Bármely nyitott TCP-port. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotminta](#probe) és a fürt, hogy használja ezt a portot.  

1. Tároló hozzáadása a virtuális géphez. További információt a Tárolás hozzáadása című [témakörben talál.](../disks-types.md)

   Mindkét virtuális gépnek legalább két adatlemezre van szüksége.

   Nyers lemezeket csatolhat, ne NTFS formátumú lemezeket.
      >[!NOTE]
      >Ha NTFS formátumú lemezeket csatol, a Közvetlen tárolóhelyeket csak lemezjogosultsági ellenőrzés nélkül engedélyezheti.  

   Legalább két prémium Szintű SSD-t csatlakoztasson minden virtuális géphez. Legalább P30 (1 TB) lemezeket ajánlunk.

   Állítsa az állomás gyorsítótárazását **írásvédettre.**

   Az éles környezetekben használt tárolókapacitás a számítási feladattól függ. Az ebben a cikkben ismertetett értékek szemléltetésre és tesztelésre szolgálnak.

1. [Adja hozzá a virtuális gépeket a már meglévő tartományhoz.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

A virtuális gépek létrehozása és konfigurálása után beállíthatja a feladatátvevő fürtöt.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>2. lépés: A Windows Server feladatátvevő fürt konfigurálása közvetlen tárolóhelyekkel

A következő lépés a feladatátvevő fürt konfigurálása a közvetlen tárolóhelyekkel. Ebben a lépésben hajtsa végre az alábbi allépéseket:

1. Adja hozzá a Windows Server feladatátvevő fürtözési szolgáltatását.
1. A fürt ellenőrzése.
1. Hozza létre a feladatátvevő fürtöt.
1. Hozza létre a felhőtanút.
1. Tároló hozzáadása.

### <a name="add-windows-server-failover-clustering"></a>Windows Server feladatátvevő fürtözés hozzáadása

1. Csatlakozzon az RDP-vel rendelkező első virtuális géphez egy olyan tartományi fiókkal, amely a helyi rendszergazdák tagja, és amely engedéllyel rendelkezik objektumok létrehozására az Active Directoryban. Ezt a fiókot használja a konfiguráció többi részéhez.

1. [Adja hozzá a feladatátvevő fürtözést az egyes virtuális gépekhez.](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)

   A feladatátvevő fürtszolgáltatás felhasználói felületről történő telepítéséhez mindkét virtuális gépen tegye meg az alábbi lépéseket:
   1. A **Kiszolgálókezelőben**válassza a **Kezelés**lehetőséget, majd a **Szerepkörök és szolgáltatások hozzáadása**lehetőséget.
   1. A **Szerepkörök és szolgáltatások hozzáadása varázslóban**válassza a **Tovább gombot,** amíg el nem jut a **Szolgáltatások kijelölése**..
   1. A **Szolgáltatások kiválasztása csoportban**válassza a **Feladatátvevő fürtözés lehetőséget.** Tartalmazza az összes szükséges funkciót és a felügyeleti eszközöket. Válassza **a Szolgáltatások hozzáadása**lehetőséget.
   1. Válassza a **Tovább**lehetőséget, majd a Szolgáltatások telepítéséhez kattintson a **Befejezés** gombra.

   A feladatátvevő fürtszolgáltatás PowerShell használatával történő telepítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

A következő lépésekről további információt a Windows [Server 2016 rendszerben közvetlen tárolóhelyeket használó, hiperkonvergens megoldás](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)3.

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ellenőrizze a fürt a felhasználói felületen vagy a PowerShell használatával.

A fürt felhasználói felülettel történő érvényesítéséhez tegye a következő lépéseket a virtuális gépek egyikén:

1. A **Kiszolgálókezelő**csoportban válassza **az Eszközök**lehetőséget, majd a **Feladatátvevőfürt-kezelő**lehetőséget.
1. A **Feladatátvevőfürt-kezelő**csoportban válassza a **Művelet**lehetőséget, majd a **Konfiguráció ellenőrzése lehetőséget.**
1. Válassza a **Tovább lehetőséget.**
1. A **Kiszolgálók vagy a fürt kiválasztása csoportban**adja meg mindkét virtuális gép nevét.
1. A **Tesztelési beállítások csoportban**válassza az **Általam kiválasztott tesztek futtatása**lehetőséget. Válassza a **Tovább lehetőséget.**
1. A **Teszt kiválasztása csoportban**válassza ki az összes tesztet, kivéve a **Tárolás**lehetőséget, az itt látható módon:

   ![Fürtérvényesítési tesztek kiválasztása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Válassza a **Tovább lehetőséget.**
1. A **Megerősítés**csoportban válassza a **Tovább lehetőséget.**

A Konfiguráció ellenőrzése varázsló futtatja az érvényesítési teszteket.

A fürt PowerShell használatával történő érvényesítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

A fürt ellenőrzése után hozza létre a feladatátvevő fürtöt.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következőkre van szükség:
- A fürtcsomópontokká vált virtuális gépek nevei.
- A feladatátvevő fürt neve
- A feladatátvevő fürt IP-címe. Olyan IP-címet használhat, amely et nem használják ugyanazon az Azure-beli virtuális hálózaton és alhálózaton, mint a fürtcsomópontok.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 –Windows Server 2016

A következő PowerShell-parancsfájl feladatátvevő fürtöt hoz létre a Windows Server 2008 és Windows Server 2016 között. Frissítse a parancsfájlt a csomópontok (a virtuális gép nevei) és az Azure virtuális hálózatból elérhető IP-cím nevével.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

A következő PowerShell-parancsfájl feladatátvevő fürtöt hoz létre a Windows Server 2019 rendszerhez. További információt a [Feladatátvevő fürt: Fürthálózati objektum című témakörben talál.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) Frissítse a parancsfájlt a csomópontok (a virtuális gép nevei) és az Azure virtuális hálózatból elérhető IP-cím nevével.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása

A Cloud Witness egy új típusú fürtkvórum tanúsító, amely egy Azure storage blobban van tárolva. Ez szükségtelent jelent egy külön virtuális gép, amely rendelkezik egy tanúsító megosztás.

1. [Hozzon létre egy felhőtanúsítóakat a feladatátvevő fürthöz.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Hozzon létre egy blob tárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címét.

1. Konfigurálja a feladatátvevő fürt kvórumtanúsítóját. Lásd: [A kvórum tanúsító konfigurálása a felhasználói felületen](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Tároló hozzáadása

A Közvetlen tárolóhelyek lemezeinek üresnek kell lenniük. Nem tartalmazhatnak partíciókat vagy más adatokat. A lemezek tisztításához kövesse [az útmutató lépéseit.](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)

1. [Tárolóhelyek közvetlen engedélyezése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   A következő PowerShell-parancsfájl engedélyezi a közvetlen tárolóhelyeket:  

   ```powershell
   Enable-ClusterS2D
   ```

   A **Feladatátvevőfürt-kezelőben**most már láthatja a tárolókészletet.

1. [Kötet létrehozása](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   A Közvetlen tárolóhelyek automatikusan létrehoz egy tárolókészletet, amikor engedélyezi. Most már készen áll a kötet létrehozására. A PowerShell-parancsmag `New-Volume` automatizálja a kötet létrehozásának folyamatát. Ez a folyamat magában foglalja a formázást, a kötet fürthöz való hozzáadását és a fürt megosztott kötetének (CSV) létrehozását. Ez a példa egy 800 gigabájtos (GB) CSV-t hoz létre:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   A parancs befejezése után egy 800 GB-os kötet lesz fürterőforrásként csatlakoztatva. A hangerő `C:\ClusterStorage\Volume1\`a .

   Ez a képernyőkép egy közvetlen tárolóhelyekkel rendelkező fürt megosztott kötetét mutatja be:

   ![Fürt megosztott kötete](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3. lépés: Feladatátvevő fürt feladatátvételének tesztelése

A **Feladatátvevőfürt-kezelőben**ellenőrizze, hogy áthelyezheti-e a tárolóerőforrást a másik fürtcsomópontra. Ha a **feladatátvevő fürthöz a feladatátvevő fürtkezelővel** csatlakozhat, és a tárolót az egyik csomópontról a másikra helyezheti át, készen áll az FCI konfigurálására.

## <a name="step-4-create-the-sql-server-fci"></a>4. lépés: Az SQL Server FCI létrehozása

Miután konfigurálta a feladatátvevő fürtöt és az összes fürtösszetevőt, beleértve a tárolást is, létrehozhatja az SQL Server FCI-t.

1. Csatlakozzon az első virtuális géphez az RDP használatával.

1. A **Feladatátvevőfürt-kezelőben**győződjön meg arról, hogy az összes alapvető fürterőforrás az első virtuális gépen található. Szükség esetén helyezze át az összes erőforrást a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az Azure Marketplace-lemezképek `C:\SQLServer_<version number>_Full`egyikét használja, az adathordozó a .. Válassza a **Telepítő**lehetőséget.

1. Az **SQL Server installation centerben**válassza a **Telepítés**lehetőséget.

1. Válassza **az Új SQL Server feladatátvevő fürt telepítését.** Az SQL Server FCI telepítéséhez kövesse a varázsló utasításait.

   Az FCI-adatkönyvtáraknak fürtözött tárolón kell lenniük. A Közvetlen tárolóhelyek esetén ez nem megosztott lemez, hanem egy kötet csatlakoztatási pontja minden kiszolgálón. A Közvetlen tárolóhelyek szinkronizálja a kötetet a két csomópont között. A kötet fürt megosztott kötetként jelenik meg a fürt számára. Használja a CSV csatlakoztatási pontot az adatkönyvtárakhoz.

   ![Adatkönyvtárak](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Miután elvégezte a varázsló utasításait, a telepítő telepíti az SQL Server FCI-t az első csomópontra.

1. Miután a telepítő telepítette az FCI-t az első csomópontra, csatlakozzon a második csomóponthoz az RDP használatával.

1. Nyissa meg az **SQL Server telepítőközpontját**. Válassza a **Telepítés**lehetőséget.

1. Válassza **a Csomópont hozzáadása SQL Server feladatátvevő fürthöz**lehetőséget. Kövesse a varázsló utasításait az SQL Server telepítéséhez és a kiszolgáló fci-hez való hozzáadásához.

   >[!NOTE]
   >Ha egy SQL Server t tartalmazó Azure Marketplace-katalóguslemezképet használt, az SQL Server-eszközök is bekerültek a lemezképbe. Ha nem használta ezeket a lemezképeket, telepítse az SQL Server eszközeit külön-külön. Lásd: [SQL Server Management Studio (SSMS) letöltése.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="step-5-create-the-azure-load-balancer"></a>5. lépés: Az Azure terheléselosztó létrehozása

Az Azure virtuális gépeken a fürtök terheléselosztót használnak egy OLYAN IP-cím tárolására, amelynek egyszerre csak egy fürtcsomóponton kell lennie. Ebben a megoldásban a terheléselosztó rendelkezik az SQL Server FCI IP-címével.

További információ: [Azure-os terheléselosztó létrehozása és konfigurálása.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása az Azure Portalon

A terheléselosztó létrehozása:

1. Az Azure Portalon nyissa meg a virtuális gépeket tartalmazó erőforráscsoport.

1. Válassza a **Hozzáadás** lehetőséget. Keressen az Azure Piactéren **a terheléselosztó.** Válassza **a Terheléselosztó lehetőséget.**

1. Kattintson a **Létrehozás** gombra.

1. Konfigurálja a terheléselosztót a következőkkel:

   - **Előfizetés**: Az Azure-előfizetés.
   - **Erőforráscsoport:** A virtuális gépeket tartalmazó erőforráscsoport.
   - **Név**: A terheléselosztót azonosító név.
   - **Régió:** A virtuális gépeket tartalmazó Azure-hely.
   - **Írja be:** Nyilvános vagy privát. A privát terheléselosztó a virtuális hálózaton belül érhető el. A legtöbb Azure-alkalmazás használhatja a privát terheléselosztót. Ha az alkalmazásnak közvetlenül az interneten keresztül kell hozzáférnie az SQL Serverhez, használjon nyilvános terheléselosztót.
   - **Termékváltozat**: standard.
   - **Virtuális hálózat**: Ugyanaz a hálózat, mint a virtuális gépek.
   - **IP-címhozzárendelés**: Statikus. 
   - **Privát IP-cím:** Az SQL Server FCI fürt hálózati erőforrásához rendelt IP-cím.

 A következő képernyőképen a **Terheléselosztó létrehozása** felhasználói felület látható:

   ![A terheléselosztó beállítása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>A terheléselosztó háttérkészletének konfigurálása

1. Térjen vissza a virtuális gépeket tartalmazó Azure-erőforráscsoporthoz, és keresse meg az új terheléselosztót. Előfordulhat, hogy frissítenie kell az erőforráscsoport nézetét. Válassza ki a terheléselosztót.

1. Válassza **a Háttérkészletek**lehetőséget, majd a **Hozzáadás**lehetőséget.

1. Társítsa a háttérkészletet a virtuális gépeket tartalmazó rendelkezésre állási készlettel.

1. A **Célhálózati IP-konfigurációk csoportban**válassza a **VIRTUÁLIS GÉP** lehetőséget, és válassza ki azokat a virtuális gépeket, amelyek fürtcsomópontként vesznek részt. Ügyeljen arra, hogy tartalmazza az összes virtuális gépet, amely otthont ad az FCI.Sure to include all virtual machines that will host the FCI.

1. A háttérkészlet létrehozásához válassza az **OK gombot.**

### <a name="configure-a-load-balancer-health-probe"></a>Terheléselosztó állapotmintájának konfigurálása

1. A terheléselosztó panelen válassza az **Állapotszondák**lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. Az **Állapotminta** hozzáadása <a name="probe"> </a>panelen állítsa be az állapotminta paramétereit.

   - **Név**: Az állapotminta neve.
   - **Protokoll**: TCP.
   - **Port**: Állítsa be a tűzfalban létrehozott portot az állapotmintához ebben a [lépésben.](#ports) Ebben a cikkben a példa `59999`tcp portot használ.
   - **Időköz**: 5 másodperc.
   - **Nem megfelelő küszöbérték:** 2 egymást követő hibák.

1. Válassza **az OK gombot.**

### <a name="set-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. A terheléselosztó panelen válassza a **Terheléselosztási szabályok**lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. A terheléselosztási szabály paramétereinek beállítása:

   - **Név**: A terheléselosztási szabályok neve.
   - **Előtér-IP-cím**: Az SQL Server FCI fürt hálózati erőforrásának IP-címe.
   - **Port**: Az SQL Server FCI TCP portja. Az alapértelmezett példányport 1433.
   - **Háttérablak**: A **lebegő IP(közvetlen kiszolgálóvisszatérés)** engedélyezésekor ugyanazt a portot használja, mint a **Port** érték.
   - **Háttérkészlet:** A korábbian konfigurált háttérkészlet neve.
   - **Állapotminta**: A korábban konfigurált állapotminta.
   - **Munkamenet-megőrzés :** Nincs.
   - **Tétlen időhosszabbítás (perc):** 4.
   - **Lebegő IP (közvetlen kiszolgálóvisszatérés)**: Engedélyezve.

1. Válassza **az OK gombot.**

## <a name="step-6-configure-the-cluster-for-the-probe"></a>6. lépés: A fürt konfigurálása a mintavételhez

Állítsa be a fürt mintavételi port paramétert a PowerShellben.

A fürtmintavételi port paraméter ének beállításához frissítse a következő parancsfájl változóit a környezetből származó értékekkel. Távolítsa el a`<` szögtartókat ( és `>`) a szkriptből.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Az alábbi lista a frissítandó értékeket ismerteti:

   - `<Cluster Network Name>`: A hálózat Windows Server feladatátvevő fürtjének neve. A **Feladatátvevőfürt-kezelő** > **hálózatok párbeszédpanelen**kattintson a jobb gombbal a hálózatra, és válassza a Tulajdonságok **parancsot.** A helyes érték az **Általános** lap **Neve alatt** található.

   - `<SQL Server FCI IP Address Resource Name>`: Az SQL Server FCI IP-címerőforrás-neve. A **Feladatátvevőfürt-kezelői** > **szerepkörökben**az SQL Server FCI szerepkör **kiszolgálóneve**csoportban kattintson a jobb gombbal az IP-cím erőforrásra, és válassza a Tulajdonságok **parancsot.** A helyes érték az **Általános** lap **Neve alatt** található. 

   - `<ILBIP>`: Az ILB IP-címe. Ez a cím az Azure Portalon van konfigurálva az ILB előtér-címként. Ez az SQL Server FCI IP-címe is. A **feladatátvevői fürtkezelőben** megtalálhatja ugyanazon a `<SQL Server FCI IP Address Resource Name>`tulajdonságlapon, ahol a.  

   - `<nnnnn>`: A terheléselosztó állapotmintában konfigurált mintavételi port. Minden nem használt TCP-port érvényes.

>[!IMPORTANT]
>A fürtparaméter alhálózati maszkjának a TCP `255.255.255.255`IP-szórási címnek kell lennie: .

A fürtmintavétel beállítása után láthatja az összes fürtparamétert a PowerShellben. Futtassa ezt a parancsfájlt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>7. lépés: Az FCI feladatátvétel tesztelése

Tesztelje az FCI feladatátvételét a fürt működésének ellenőrzéséhez. Hajtsa végre a következő lépéseket:

1. Csatlakozzon az SQL Server FCI fürtcsomópontok egyikéhez az RDP használatával.

1. Nyissa meg **a Feladatátvevőfürt-kezelőt**. Válassza a **Szerepkörök lehetőséget.** Figyelje meg, hogy melyik csomópont az SQL Server FCI szerepkör tulajdonosa.

1. Kattintson a jobb gombbal az SQL Server FCI szerepkörére.

1. Válassza **az Áthelyezés**lehetőséget, majd a Legjobb csomópont **lehetőséget.**

**A feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Az erőforrások ezután áthelyezik, és a másik csomóponton online állapotba kerülnek.

### <a name="test-connectivity"></a>Kapcsolat tesztelése

A kapcsolat teszteléséhez jelentkezzen be egy másik virtuális gépre ugyanabban a virtuális hálózatban. Nyissa meg **az SQL Server Management Studio alkalmazást,** és csatlakozzon az SQL Server FCI nevéhez.

>[!NOTE]
>Szükség esetén [letöltheti az SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)alkalmazást.

## <a name="limitations"></a>Korlátozások

Az Azure virtuális gépei támogatják a Microsoft Distributed Transaction Coordinator (MSDTC) szolgáltatást a Windows Server 2019 rendszeren fürtözött megosztott köteteken (CSV) és [szabványos terheléselosztóval.](../../../load-balancer/load-balancer-standard-overview.md)

Az Azure virtuális gépein az MSDTC nem támogatott Windows Server 2016 vagy korábbi rendszerben, mert:

- A fürtözött MSDTC-erőforrás nem konfigurálható megosztott tároló használatára. Windows Server 2016 rendszerben, ha MSDTC-erőforrást hoz létre, az nem jelenik meg a használható megosztott tárolók között, még akkor sem, ha rendelkezésre áll-e tárterület. A windows Server 2019-ben kijavítottuk a problémát.
- Az alapvető terheléselosztó nem kezeli az RPC-portokat.

## <a name="see-also"></a>Lásd még

[Közvetlen tárolóhelyek beállítása távoli asztallal (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hiperkonvergens megoldás közvetlen tárolóhelyekkel](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[A Közvetlen tárolóhelyek áttekintése](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Sql Server támogatása közvetlen tárolóhelyekhez](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
