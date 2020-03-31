---
title: SQL Server FCI prémium fájlmegosztással – Azure virtuális gépek
description: Ez a cikk bemutatja, hogyan hozhat létre egy SQL Server feladatátvételi fürtpéldányt egy prémium szintű fájlmegosztás használatával az Azure virtuális gépeken.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303222"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>SQL Server feladatátvevő fürtpéldány konfigurálása prémium szintű fájlmegosztással az Azure virtuális gépeken

Ez a cikk bemutatja, hogyan hozhat létre SQL Server feladatátvételi fürtpéldányt (FCI) az Azure virtuális gépeken [prémium szintű fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md)használatával.

A prémium szintű fájlmegosztások SSD-vel támogatott, következetesen alacsony késleltetésű fájlmegosztások, amelyek teljes mértékben támogatottak az SQL Server 2012 vagy újabb rendszer feladatátvételi fürtpéldányaival Windows Server 2012-es vagy újabb rendszeren. A prémium fájlmegosztások nagyobb rugalmasságot biztosítanak, lehetővé téve a fájlmegosztás átméretezését és méretezését állásidő nélkül.


## <a name="before-you-begin"></a>Előkészületek

Van néhány dolog, amit tudnod kell, és a helyén, mielőtt elkezdené.

A következő technológiák operatív ismereteket kell, hogy megértse:

- [Windows fürttechnológiák](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürtpéldányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Egy dolog, hogy tudatában legyünk, hogy egy Azure IaaS virtuális gép feladatátvételi fürt, javasoljuk, hogy egy kiszolgálónkénti hálózati adapter (fürtcsomópont) és egyetlen alhálózat. Az Azure networking fizikai redundanciával rendelkezik, amely szükségtelenné teszi a további hálózati adaptereket és alhálózatokat egy Azure IaaS virtuális gép vendégfürtén. A fürtérvényesítési jelentés figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetőek el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure IaaS virtuálisgép feladatátvételi fürtjein.

Általános ismereteket kell, hogy kell, hogy legyenek ezek a technológiák:

- [Prémium szintű Azure-fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-erőforráscsoportok](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Jelenleg az SQL Server feladatátvételi fürtpéldányai az Azure virtuális gépeken csak az [SQL Server IaaS Ügynökbővítmény](virtual-machines-windows-sql-server-agent-extension.md) [könnyű felügyeleti módjával](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) támogatottak. A teljes bővítménymódról a könnyűre váltáshoz törölje a megfelelő virtuális gépek **SQL virtuálisgép-erőforrását,** majd regisztrálja őket az SQL virtuálisgép-erőforrás-szolgáltatóval könnyű módban. Amikor törli az **SQL virtuálisgép-erőforrást** az Azure Portalon, **törölje a jelet a megfelelő virtuális gép melletti jelölőnégyzetből.** A teljes bővítmény támogatja az olyan funkciókat, mint az automatikus biztonsági mentés, a javítás és a speciális portálkezelés. Ezek a szolgáltatások nem fognak működni az SQL virtuális gépeken, miután az ügynök újratelepítése könnyű felügyeleti módban.

A prémium szintű fájlmegosztások iOPS-t és kapacitásokat biztosítanak, amelyek számos számítási feladat igényeit elégszolgálják. Az I/O-igényes számítási feladatok hoz vegye figyelembe az [SQL Server feladatátvételi fürtpéldányait a közvetlen tárolóhelyekkel](virtual-machines-windows-portal-sql-create-failover-cluster.md), felügyelt prémium szintű lemezek vagy ultralemezek alapján.  

Ellenőrizze a környezet IOPS-tevékenységét, és ellenőrizze, hogy a prémium fájlmegosztások biztosítják-e a szükséges IOPS-t, mielőtt elkezdené a központi telepítést vagy az áttelepítést. A Windows Teljesítményfigyelő lemezszámlálóival figyelheti az SQL Server-adatok, Napló és Temp DB-fájlokhoz szükséges összes IOPS (Lemezátvitel/másodperc) és átviteli -át .

Számos számítási feladatok bursting IO, ezért érdemes ellenőrizni a nehéz használati időszakokban, és vegye figyelembe mind a maximális IOPS és az átlagos IOPS. A prémium fájlmegosztások a megosztás mérete alapján biztosítják az IOPS-t. Prémium fájlmegosztások is biztosít ingyenes bursting, amely lehetővé teszi, hogy tört az I-es háromszorosa az alapérték legfeljebb egy órán keresztül.

A prémium szintű fájlmegosztási teljesítményről a [Fájlmegosztás teljesítményszintjei](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)című témakörben talál további információt.

### <a name="licensing-and-pricing"></a>Licencelés és árképzés

Az Azure virtuális gépeken licencelheti az SQL Servert használatalapú fizetéses (PAYG) vagy bring-your-own-license (BYOL) virtuálisgép-lemezképek használatával. A választott képtípus befolyásolja a díjfelszámítás módját.

A felosztó-kioldás licencelésével az SQL Server feladatátvételi fürtpéldánya (FCI) az Azure virtuális gépeken az FCI összes csomópontja, beleértve a passzív csomópontokat is, díjat számít fel. További információt az [SQL Server Enterprise Virtual Machines Díjszabás című témakörben talál.](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)

Ha nagyvállalati szerződéssel rendelkezik a Frissítési Garanciával, minden aktív csomóponthoz használhat egy ingyenes passzív FCI-csomópontot. Az Azure-beli előnyök kihasználásához használja a BYOL virtuálisgép-lemezképeket, és használja ugyanazt a licencet az FCI aktív és passzív csomópontjain. További információ: [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Az Azure virtuális gépeken futó SQL Server pay-as-you-go és BYOL licencelésének összehasonlításához [olvassa el az SQL virtuális gépek kezelésének első lépéseit.](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)

Az SQL Server licenceléséről az [Árképzés](https://www.microsoft.com/sql-server/sql-server-2017-pricing)című témakörben talál teljes körű információt.

### <a name="filestream"></a>Fájlstream

A fájlfolyam nem támogatott prémium fájlmegosztással rendelkező feladatátvevő fürt esetében. A fájlfolyam használatához telepítse a fürtöt a [Közvetlen tárolóhely](virtual-machines-windows-portal-sql-create-failover-cluster.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések elvégzése előtt már rendelkeznie kell a következőkre:

- Microsoft Azure-előfizetés.
- Windows-tartomány az Azure virtuális gépein.
- Olyan tartományi felhasználói fiók, amely engedéllyel rendelkezik objektumok létrehozására az Azure virtuális gépeken és az Active Directoryban.
- Az SQL Server szolgáltatás futtatásához, és amely a fájlmegosztás csatlakoztatásakor bejelentkezhet a virtuális gépre, tartományi felhasználói fiók.  
- Az Azure virtuális hálózat és alhálózat elegendő IP-címterülettel rendelkezik az alábbi összetevők számára:
   - Két virtuális gép.
   - A feladatátvevő fürt IP-címe.
   - Minden FCI IP-cím.
- Az Azure-hálózaton konfigurált DNS, amely a tartományvezérlőkre mutat.
- A fürtözött meghajtóként használandó [prémium fájlmegosztás](../../../storage/files/storage-how-to-create-premium-fileshare.md) az adatbázis adatfájlokhoz tartozó tárolási kvótája alapján.
- Ha Windows Server 2012 R2 és régebbi rendszert használ, egy másik fájlmegosztásra lesz szüksége ahhoz, hogy a fájlmegosztás tanúsítójaként használhassa, mivel a felhőbeli tanúk támogatják a Windows 2016-ot és az újabb aktarendszert. Használhat egy másik Azure-fájlmegosztást, vagy használhat fájlmegosztást egy külön virtuális gépen. Ha egy másik Azure-fájlmegosztást szeretne használni, csatlakoztathatja azt ugyanazzal a folyamattal, mint a fürtözött meghajtóhoz használt prémium szintű fájlmegosztás. 

Ezekkel az előfeltételekkel megkezdheti a feladatátvevő fürt létrehozásához. Az első lépés a virtuális gépek létrehozása.

## <a name="step-1-create-the-virtual-machines"></a>1. lépés: A virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az előfizetésével.

1. [Hozzon létre egy Azure rendelkezésre állási készletet.](../tutorial-availability-sets.md)

   A rendelkezésre állási készlet csoportok virtuális gépek között tartalék tartományok és a tartományok frissítése. Ez biztosítja, hogy az alkalmazást ne befolyásolják egyetlen meghibásodási pont, például a hálózati kapcsoló vagy a kiszolgálóállvány áramellátása.

   Ha még nem hozta létre az erőforráscsoportot a virtuális gépek, ezt akkor, amikor létrehoz egy Azure rendelkezésre állási csoport. Ha az Azure Portalon hozza létre az elérhetőségi csoportot, tegye meg az alábbi lépéseket:

   1. Az Azure Portalon válassza **az Erőforrás létrehozása** az Azure Marketplace megnyitásához. Az **Elérhetőség készlet**keresése .
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

   >[!IMPORTANT]
   > A virtuális gép létrehozása után távolítsa el az előre telepített önálló SQL Server-példányt. Az előre telepített SQL Server adathordozóhasználatával hozza létre az SQL Server FCI-t, miután beállította a feladatátvevő fürtöt és a prémium szintű fájlmegosztást tárolóként.

   Azt is megteheti, hogy csak az operációs rendszert tartalmazó Azure Marketplace-rendszerképeket is használhat. Válasszon egy **Windows Server 2016 Datacenter-lemezképet,** és telepítse az SQL Server FCI-t, miután beállította a feladatátvevő fürtöt és a prémium szintű fájlmegosztást tárolóként. Ez a lemezkép nem tartalmaz SQL Server telepítési adathordozót. Helyezze az SQL Server telepítési adathordozóját egy olyan helyre, ahol futtathatja az egyes kiszolgálókon.

1. Miután az Azure létrehozza a virtuális gépeket, csatlakozzon mindegyikhez az RDP használatával.

   Amikor először csatlakozik egy virtuális géphez az RDP használatával, egy kérdés megkérdezi, hogy engedélyezi-e a számítógép számára a hálózaton való észlelést. Válassza az **Igen** lehetőséget.

1. Ha az SQL Server-alapú virtuálisgép-lemezképek egyikét használja, távolítsa el az SQL Server-példányt.

   1. A **Programok és szolgáltatások menüpontban**kattintson a jobb gombbal a Microsoft SQL Server **201_ (64 bites)** elemre, és válassza **az Eltávolítás/módosítás parancsot.**
   1. Válassza **az Eltávolítás**lehetőséget.
   1. Válassza ki az alapértelmezett példányt.
   1. A Database **Engine Services**összes szolgáltatásának eltávolítása. Ne távolítsa el **a megosztott szolgáltatásokat**. A következő képernyőképhez hasonló képernyőkép jelenik meg:

        ![Szolgáltatások kiválasztása](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Válassza a **Tovább**lehetőséget, majd az **Eltávolítás**lehetőséget.

1. <span id="ports"> </span> Nyissa meg a tűzfalportokat.  

   Minden virtuális gépen nyissa meg ezeket a portokat a Windows tűzfalon:

   | Cél | TCP-port | Megjegyzések
   | ------ | ------ | ------
   | SQL Server | 1433 | Normál port az SQL Server alapértelmezett példányaihoz. Ha a gyűjteményből származó képet használt, a port automatikusan megnyílik.
   | Állapotadat-mintavétel | 59999 | Bármely nyitott TCP-port. Egy későbbi lépésben konfigurálja a terheléselosztó [állapotminta](#probe) és a fürt, hogy használja ezt a portot.
   | Fájlmegosztás | 445 | A fájlmegosztási szolgáltatás által használt port.

1. [Adja hozzá a virtuális gépeket a már meglévő tartományhoz.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

A virtuális gépek létrehozása és konfigurálása után konfigurálhatja a prémium szintű fájlmegosztást.

## <a name="step-2-mount-the-premium-file-share"></a>2. lépés: A prémium fájlmegosztás csatlakoztatása

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és nyissa meg a tárfiókot.
1. Nyissa meg a **Fájlmegosztás oka** a **Fájlszolgáltatás** csoportban, és válassza ki az SQL-tárhelyhez használni kívánt prémium szintű fájlmegosztást.
1. A **Csatlakozás gombra** a fájlmegosztás kapcsolati karakterláncának beállításához válassza a Csatlakozás lehetőséget.
1. Jelölje ki a használni kívánt meghajtóbetűjelet a legördülő listából, majd másolja mindkét kódblokkot a Jegyzettömbbe.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Mindkét PowerShell-parancs másolása a fájlmegosztási csatlakozási portálról":::

1. Az RDP segítségével csatlakozzon az SQL Server virtuális géphez azzal a fiókkal, amelyet az SQL Server FCI a szolgáltatásfiókhoz fog használni.
1. Nyisson meg egy felügyeleti PowerShell parancskonzolt.
1. Futtassa azokkal a parancsokkal, amelyeket korábban mentett, amikor a portálon dolgozott.
1. Nyissa meg a megosztást a Fájlkezelő vagy a **Futtatás** párbeszédpanelen (Windows billentyű + r). Használja a `\\storageaccountname.file.core.windows.net\filesharename`hálózati elérési utat . Például: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Hozzon létre legalább egy mappát az újonnan csatlakoztatott fájlmegosztáson az SQL Data-fájlok helyhez.
1. Ismételje meg ezeket a lépéseket minden olyan SQL Server virtuális gépen, amely részt vesz a fürtben.

  > [!IMPORTANT]
  > - Fontolja meg egy külön fájlmegosztás a biztonsági mentési fájlokmenteni az IOPS és a hely kapacitása ez a megosztás az adatok és a napló fájlokat. A biztonsági másolatfájlokhoz prémium vagy normál fájlmegosztást is használhat.
  > - Ha Windows 2012 R2 és régebbi rendszert használ, kövesse ezeket a lépéseket a fájlmegosztáshoz való csatlakoztatáshoz, amelyet a fájlmegosztás tanúsítójaként fog használni. 

## <a name="step-3-configure-the-failover-cluster"></a>3. lépés: A feladatátvevő fürt konfigurálása

A következő lépés a feladatátvevő fürt konfigurálása. Ebben a lépésben a következő allépéseket hajthatja végre:

1. Adja hozzá a Windows Server feladatátvevő fürtözési szolgáltatását.
1. A fürt ellenőrzése.
1. Hozza létre a feladatátvevő fürtöt.
1. Hozza létre a felhőtanúsítót (Windows Server 2016 és újabb) vagy a fájlmegosztás tanúsítóját (Windows Server 2012 R2 és újabb rendszerekhez).


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

### <a name="validate-the-cluster"></a>A fürt ellenőrzése

Ellenőrizze a fürt a felhasználói felületen vagy a PowerShell használatával.

A fürt felhasználói felülettel történő érvényesítéséhez tegye a következő lépéseket a virtuális gépek egyikén:

1. A **Kiszolgálókezelő**csoportban válassza **az Eszközök**lehetőséget, majd a **Feladatátvevőfürt-kezelő**lehetőséget.
1. A **Feladatátvevőfürt-kezelő**csoportban válassza a **Művelet**lehetőséget, majd a **Konfiguráció ellenőrzése lehetőséget.**
1. Válassza a **Tovább lehetőséget.**
1. A **Kiszolgálók vagy a fürt kiválasztása csoportban**adja meg mindkét virtuális gép nevét.
1. A **Tesztelési beállítások csoportban**válassza az **Általam kiválasztott tesztek futtatása**lehetőséget. Válassza a **Tovább lehetőséget.**
1. A **Tesztkiválasztása csoportban**jelölje ki az összes tesztet, kivéve a **közvetlen tároló-** és **tárolóhelyeket,** az itt látható módon:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Fürtérvényesítési tesztek kiválasztása":::

1. Válassza a **Tovább lehetőséget.**
1. A **Megerősítés**csoportban válassza a **Tovább lehetőséget.**

A **Konfiguráció ellenőrzése varázsló** futtatja az érvényesítési teszteket.

A fürt PowerShell használatával történő érvényesítéséhez futtassa a következő parancsfájlt egy rendszergazdai PowerShell-munkamenetből az egyik virtuális gépen:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

A fürt ellenőrzése után hozza létre a feladatátvevő fürtöt.

### <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

A feladatátvevő fürt létrehozásához a következőkre van szükség:
- A fürtcsomópontokká vált virtuális gépek nevei.
- A feladatátvevő fürt neve
- A feladatátvevő fürt IP-címe. Olyan IP-címet használhat, amely et nem használják ugyanazon az Azure-beli virtuális hálózaton és alhálózaton, mint a fürtcsomópontok.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 –Windows Server 2016

A következő PowerShell-parancsfájl feladatátvevő fürtöt hoz létre a Windows Server 2012 és windows Server 2016 között. Frissítse a parancsfájlt a csomópontok (a virtuális gép nevei) és az Azure virtuális hálózatból elérhető IP-cím nevével.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

A következő PowerShell-parancsfájl feladatátvevő fürtöt hoz létre a Windows Server 2019 rendszerhez. További információt a [Feladatátvevő fürt: Fürthálózati objektum című témakörben talál.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) Frissítse a parancsfájlt a csomópontok (a virtuális gép nevei) és az Azure virtuális hálózatból elérhető IP-cím nevével.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Felhőtanúsító létrehozása (Win 2016 +)

Ha windows Server 2016-os vagy nagyobb, létre kell hoznia egy Cloud Witness alkalmazást. A Cloud Witness egy új típusú fürtkvórum tanúsító, amely egy Azure storage blobban van tárolva. Ez szükségtelent jelent egy külön virtuális gép, amely egy tanúsító megosztást, vagy egy külön fájlmegosztás használatával.

1. [Hozzon létre egy felhőtanúsítóakat a feladatátvevő fürthöz.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)

1. Hozzon létre egy blob tárolót.

1. Mentse a hozzáférési kulcsokat és a tároló URL-címét.

### <a name="configure-quorum"></a>Kvórum konfigurálása 

Windows Server 2016-os és nagyobb esetén konfigurálja úgy a fürtöt, hogy az imént létrehozott felhőtanúsító legyen. Kövesse az összes [lépést: Állítsa be a kvórum tanúsítót a felhasználói felületen.](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)

Windows Server 2012 R2 és régebbi rendszerek esetén kövesse a [Kvórum tanúsító konfigurálása a felhasználói felületen,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) de a **Kvórum tanúsító kiválasztása** lapon a **Fájlmegosztás tanúsító konfigurálása** lehetőséget. Adja meg a fájlmegosztás tanúsítójaként lefoglalt fájlmegosztási megosztást, függetlenül attól, hogy egy külön virtuális gépen konfigurált vagy az Azure-ból csatlakoztatott fájlmegosztásról van-e szó. 


## <a name="step-4-test-cluster-failover"></a>4. lépés: A fürt feladatátvételének tesztelése

Tesztelje a fürt feladatátvételét. A **Feladatátvevőfürt-kezelőben**kattintson a jobb gombbal a fürtre, és válassza **a További műveletek** > **a fürterőforrás-kijelölés** > **csomópontot**, majd válassza ki a fürt másik csomópontját. Helyezze át a fürt főerőforrását a fürt minden csomópontjára, majd helyezze vissza az elsődleges csomópontra. Ha a fürtöt sikeresen át tudja helyezni az egyes csomópontokra, készen áll az SQL Server telepítésére.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Fürt feladatátvételének tesztelése az alapvető erőforrás másik csomópontra való áthelyezésével":::

## <a name="step-5-create-the-sql-server-fci"></a>5. lépés: Az SQL Server FCI létrehozása

A feladatátvevő fürt konfigurálása után létrehozhatja az SQL Server FCI-t.

1. Csatlakozzon az első virtuális géphez az RDP használatával.

1. A **Feladatátvevőfürt-kezelőben**győződjön meg arról, hogy az összes alapvető fürterőforrás az első virtuális gépen található. Ha szükséges, helyezze át az összes erőforrást erre a virtuális gépre.

1. Keresse meg a telepítési adathordozót. Ha a virtuális gép az Azure Marketplace-lemezképek `C:\SQLServer_<version number>_Full`egyikét használja, az adathordozó a .. Válassza a **Telepítő**lehetőséget.

1. Az **SQL Server telepítőközpontjában**válassza a **Telepítés**lehetőséget.

1. Válassza **az Új SQL Server feladatátvevő fürt telepítését.** Az SQL Server FCI telepítéséhez kövesse a varázsló utasításait.

   Az FCI-adatkönyvtáraknak a prémium fájlmegosztáson kell lenniük. Adja meg a megosztás teljes elérési `\\storageaccountname.file.core.windows.net\filesharename\foldername`útját ezen a formában: . Megjelenik egy figyelmeztetés, amely arról szól, hogy egy fájlkiszolgálót adott meg adatkönyvtárként. Ez a figyelmeztetés várható. Győződjön meg arról, hogy az a felhasználói fiók, amelyet az RDP a virtuális gépbe, amikor a fájlmegosztás megőrzése során a fájlmegosztás ugyanaz a fiók, amelyet az SQL Server szolgáltatás használ az esetleges hibák elkerülése érdekében.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Fájlmegosztás használata SQL-adatkönyvtárként":::

1. A varázsló lépéseinek elvégzése után a telepítő telepíti az SQL Server FCI-t az első csomópontra.

1. Miután a telepítő telepítette az FCI-t az első csomópontra, csatlakozzon a második csomóponthoz az RDP használatával.

1. Nyissa meg az **SQL Server telepítőközpontját**. Válassza a **Telepítés**lehetőséget.

1. Válassza **a Csomópont hozzáadása SQL Server feladatátvevő fürthöz**lehetőséget. Kövesse a varázsló utasításait az SQL Server telepítéséhez és a kiszolgáló fci-hez való hozzáadásához.

   >[!NOTE]
   >Ha egy Azure Marketplace-katalógus lemezképet használt az SQL Server használatával, az SQL Server-eszközök is szerepelnek a lemezképben. Ha nem használta ezeket a lemezképeket, telepítse az SQL Server eszközeit külön-külön. Lásd: [SQL Server Management Studio (SSMS) letöltése.](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="step-6-create-the-azure-load-balancer"></a>6. lépés: Az Azure terheléselosztó létrehozása

Az Azure virtuális gépeken a fürtök terheléselosztót használnak egy OLYAN IP-cím tárolására, amelynek egyszerre csak egy fürtcsomóponton kell lennie. Ebben a megoldásban a terheléselosztó rendelkezik az SQL Server FCI IP-címével.

További információ: [Azure-os terheléselosztó létrehozása és konfigurálása.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

### <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása az Azure Portalon

A terheléselosztó létrehozása:

1. Az Azure Portalon nyissa meg a virtuális gépeket tartalmazó erőforráscsoport.

1. Válassza a **Hozzáadás** lehetőséget. Keressen az Azure Piactéren **a terheléselosztó.** Válassza **a Terheléselosztó lehetőséget.**

1. Kattintson a **Létrehozás** gombra.

1. Állítsa be a terheléselosztót a következő értékek használatával:

   - **Előfizetés**: Az Azure-előfizetés.
   - **Erőforráscsoport:** A virtuális gépeket tartalmazó erőforráscsoport.
   - **Név**: A terheléselosztót azonosító név.
   - **Régió:** A virtuális gépeket tartalmazó Azure-hely.
   - **Írja be:** Nyilvános vagy privát. A privát terheléselosztó a virtuális hálózaton belül érhető el. A legtöbb Azure-alkalmazás használhatja a privát terheléselosztót. Ha az alkalmazásnak közvetlenül az interneten keresztül kell hozzáférnie az SQL Serverhez, használjon nyilvános terheléselosztót.
   - **Termékváltozat**: standard.
   - **Virtuális hálózat**: Ugyanaz a hálózat, mint a virtuális gépek.
   - **IP-címhozzárendelés**: Statikus. 
   - **Privát IP-cím:** Az SQL Server FCI fürt hálózati erőforrásához rendelt IP-cím.

   Az alábbi képen a **Terheléselosztó létrehozása** felhasználói felületlátható:

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

1. Az **Állapotminta** hozzáadása <span id="probe"> </span> panelen állítsa be a következő állapotminta-paramétereket.

   - **Név**: Az állapotminta neve.
   - **Protokoll**: TCP.
   - **Port**: A tűzfalban létrehozott port az állapotminta ebben a [lépésben](#ports). Ebben a cikkben a példa `59999`tcp portot használ.
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>7. lépés: A fürt konfigurálása a mintavételhez

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

## <a name="step-8-test-fci-failover"></a>8. lépés: Az FCI feladatátvétel tesztelése

Tesztelje az FCI feladatátvételét a fürt működésének ellenőrzéséhez. Hajtsa végre a következő lépéseket:

1. Csatlakozzon az SQL Server FCI fürtcsomópontok egyikéhez az RDP használatával.

1. Nyissa meg **a Feladatátvevőfürt-kezelőt**. Válassza a **Szerepkörök lehetőséget.** Figyelje meg, hogy melyik csomópont az SQL Server FCI szerepkör tulajdonosa.

1. Kattintson a jobb gombbal az SQL Server FCI szerepkörére.

1. Válassza **az Áthelyezés**lehetőséget, majd a Legjobb csomópont **lehetőséget.**

**A feladatátvevőfürt-kezelő** megjeleníti a szerepkört, és az erőforrásai offline állapotba kerülnek. Az erőforrások ezután áthelyezik, és a másik csomóponton újra online állapotba kerülnek.

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

- [Windows fürttechnológiák](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server feladatátvevő fürtpéldányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
