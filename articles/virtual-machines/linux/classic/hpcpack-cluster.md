---
title: Linux virtuális gépek számítási HPC Pack-fürtökben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és az Azure-beli HPC Pack-fürt használata Linux rendszerű nagy teljesítményű feldolgozási (HPC) munkaterhelések
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 4156071c36b06be586b05ee98e9eeb0a9138e4bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246855"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Az Azure-beli HPC Pack-fürtök linuxos számítási csomópontjaival kapcsolatos alapvető tudnivalók
Állítsa be a [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) fürt az Azure-ban, amely tartalmazza a fő csomópontot futtató Windows Server és több számítási csomóponton, támogatott Linux disztribúciót futtat. A lehetőségek többek között a Linux-csomópontok és a Windows a fürt fő csomópontjának adatok áthelyezéséhez. Ismerje meg, hogyan lehet a fürt Linux-alapú HPC-feladatok elküldése.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Magas szinten a következő ábrán a HPC Pack-fürt létrehozása és használata.

![A Linux-csomópontok HPC Pack-fürthöz][scenario]

A Linux-alapú HPC számítási feladatok futtatásához az Azure-ban egyéb lehetőségekről [műszaki forrásanyagok a batch- és nagy teljesítményű feldolgozási](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>A Linuxos számítási csomópontok HPC Pack-fürt üzembe helyezése
Ez a cikk bemutatja, az Azure-ban a Linux és a HPC Pack-fürt üzembe helyezése két alternatívája közül választhat a számítási csomópontokon. Mindkét módszer a HPC Pack segítségével Windows Server Piactéri lemezképet használatával fő csomópontjának létrehozása. 

* **Az Azure Resource Manager-sablon** – egy sablont az Azure Marketplace-ről, vagy a Közösségtől gyorsindítási sablon használatával automatizálhatja a Resource Manager-alapú üzemi modellben a fürt létrehozása. Ha például a [HPC Pack-fürt Linuxos számítási feladatokhoz](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) az Azure Marketplace-sablon létrehoz egy teljes HPC Pack-fürt infrastruktúra Linuxos HPC számítási feladatok.
* **PowerShell-parancsprogram** -használatát a [Microsoft HPC Pack IaaS telepítési szkripttel](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) automatizálhatja a teljes fürt üzembe helyezése a klasszikus üzemi modellben. Az Azure PowerShell-szkript HPC Pack VM-lemezkép használja a gyors üzembe helyezés az Azure Marketplace-en, és a egy átfogó Linuxos számítási csomópontok üzembe helyezéséhez a konfigurációs paramétereket biztosít.

További információ a HPC Pack-fürt központi telepítési beállítások az Azure-ban: [beállításokat hozhat létre és kezelhet egy nagy teljesítményű feldolgozási (HPC-) fürt az Azure-ban a Microsoft HPC Packkel](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés** -előfizetést is használhatja az Azure globális vagy Azure China szolgáltatásban. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.
* **Magkvóta** -magok száma, a kvóta növeléséhez előfordulhat, hogy szüksége, különösen akkor, ha úgy dönt, hogy üzembe helyezése több fürtcsomópont, Többmagos Virtuálisgép-mérettel. A kvóta növeléséhez, nyissa meg az online támogatási kérést díjmentesen.
* **Linux-disztribúciók** – jelenleg a HPC Pack támogatja a következő Linux-disztribúciókat számítási csomópontok. Ezeket a disztribúciók Marketplace-en-verziókat használhat, ha rendelkezésre áll, vagy adja meg a saját.
  
  * **CentOS-alapú**: 6.5, 6.6, 6.7, 7.0-ban, 7.1-es, 7.2, 6.5-ös HPC, HPC 7.1-es
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), HPC, HPC (prémium szintű) SLES 12 SLES 12
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Az RDMA-kompatibilis Virtuálisgép-méretek valamelyikét használni az Azure RDMA-hálózatot, adjon meg egy SUSE Linux Enterprise Server 12 HPC vagy CentOS-alapú HPC-rendszerképet az Azure Marketplace-ről. További információkért lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

A fürt üzembe helyezése a HPC Pack IaaS telepítési szkripttel használatával további előfeltételei:

* **Ügyfélszámítógép** – a fürt üzembe helyezési parancsfájl futtatása Windows rendszerű számítógépre van szüksége.
* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépre.
* **HPC Pack IaaS telepítési szkripttel** – töltse le és csomagolja ki a legújabb verzióját a szkriptet a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A verzió, a parancsfájl futtatásával ellenőrizheti `.\New-HPCIaaSCluster.ps1 –Version`. Ez a cikk 4.4.1-es vagy újabb verzióiban a parancsfájl alapján.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>1. telepítési lehetőség. A Resource Manager-sablonnal
1. Nyissa meg a [Linuxos számítási feladatokhoz a HPC Pack-fürt](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) az Azure Marketplace-en, majd kattintson a sablon **telepítés**.
1. Az Azure Portalon, tekintse át az adatokat, és kattintson a **létrehozás**.
   
    ![Portál létrehozása][portal]
1. Az a **alapjai** panelen adjon meg egy nevet a fürt, amely is a fő csomópont Virtuálisgép-neveket. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre a központi telepítési csoportot, amely az Ön számára elérhető helyen. A hely hatással van az egyes Virtuálisgép-méretek és más Azure-szolgáltatások rendelkezésre állásának (lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/)).
1. Az a **csomópont beállítások Átjárócsomópont** panelen egy első üzembe helyezés esetében általában elfogadhatja az alapértelmezett beállításokat. 
   
   > [!NOTE]
   > A **beállítás utáni parancsprogram URL-cím** egy nyilvánosan elérhető Windows PowerShell-parancsprogram, amely után fut-e a virtuális gép központi csomóponton futtatni kívánt megadása nem kötelező beállítás. 
   > 
   > 
1. Az a **számítási csomópont beállítások** panelen válassza ki egy elnevezési mintát a csomópontok, a számát és méretét a csomópontok és a Linux-disztribúció.
1. Az a **infrastruktúra-beállításokat** panelen adja meg a virtuális hálózat és az Active Directory tartományi, a tartomány és a virtuális gép rendszergazdai hitelesítő adatait és a egy elnevezési mintát, a storage-fiókok.
   
   > [!NOTE]
   > HPC Pack fürt felhasználók hitelesítése az Active Directory-tartomány használ. 
   > 
   > 
1. Miután az érvényesítési tesztek futtatásához, és tekintse át a használati feltételeket, kattintson a **beszerzési**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>2. központi telepítési lehetőség. Használja az IaaS telepítési szkripttel
A fürt üzembe helyezéséhez a HPC Pack IaaS telepítési szkripttel használatával további Előfeltételek a következők:

* **Ügyfélszámítógép** – a fürt üzembe helyezési parancsfájl futtatása Windows rendszerű számítógépre van szüksége.
* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépre.
* **HPC Pack IaaS telepítési szkripttel** – töltse le és csomagolja ki a legújabb verzióját a szkriptet a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A verzió, a parancsfájl futtatásával ellenőrizheti `.\New-HPCIaaSCluster.ps1 –Version`. Ez a cikk 4.4.1-es vagy újabb verzióiban a parancsfájl alapján.

**Konfigurációs XML-fájl**

A HPC Pack IaaS telepítési szkripttel bemenetként egy konfigurációs XML-fájl használatával írja le a HPC-fürtöt. A következő minta konfigurációs fájlt egy kisebb fürtöt egy HPC Pack fő csomópontot és két mérete A7 CentOS 7.0 Linuxos számítási csomópontokból álló adja meg. 

A fájl módosítása a környezetben és a kívánt fürtkonfigurációt igény szerint, és mentse egy nevet, például HPCDemoConfig.xml. Például hogy kell adnia az előfizetése nevéhez és a egy egyedi tárfióknév és a felhőalapú szolgáltatás neve. Emellett érdemes a számítási csomópontok különböző támogatott Linux-rendszerképek kiválasztása. A konfigurációs fájlban elemeivel kapcsolatos további információkért tekintse meg a parancsfájl mappában található fájl Manual.rtf és [HPC-fürt létrehozása HPC Pack IaaS telepítési szkripttel](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**A HPC Pack IaaS telepítési szkripttel futtatása**

1. Rendszergazdaként nyissa meg a Windows PowerShell az ügyfélszámítógépen.
1. Változás könyvtárba, ahol a szkriptet az telepíteni (E:\IaaSClusterScript ebben a példában).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
1. Futtassa a következő parancsot a HPC Pack-fürt üzembe helyezéséhez. Ez a példa feltételezi, hogy a konfigurációs fájlban található E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Mivel a **AdminPassword** nincs megadva az előző parancsban szereplő kéri, adja meg a jelszót a felhasználó *MyAdminName*.
   
    b. A szkript elindít, a konfigurációs fájl ellenőrzése. Azt a hálózati kapcsolat függően több percig is eltarthat.
   
    ![Ellenőrzés][validate]
   
    c. Ellenőrzések adhatók át, miután a parancsfájl létrehozása a fürt erőforrásainak sorolja fel. Adja meg *Y* folytatásához.
   
    ![További források][resources]
   
    d. A parancsfájl elindítja a HPC Pack-fürt üzembe helyezéséhez, és elvégzi a konfiguráció további manuális lépések nélkül. A parancsprogram futását néhány percig.
   
    ![Üzembe helyezés][deploy]
   
   > [!NOTE]
   > Ebben a példában a parancsfájl létrehoz egy naplófájlt automatikusan, mivel a **- naplófájl** paraméter nincs megadva. A naplók valós időben nem írt, de az érvényesítés, és az üzembe helyezés végén összegyűjtött. Ha a PowerShell-folyamat leáll, a parancsfájl futása közben, az egyes naplók is elvesznek.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Csatlakozás az átjárócsomóponthoz
Az Azure-ban a HPC Pack-fürt üzembe helyezése után [csatlakozzon a távoli asztal](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az átjárócsomóponthoz virtuális gép a tartományi hitelesítő adatainak használata, amikor központilag telepítette a fürt a megadott (például *hpc\\clusteradmin*). A fürt fő csomópontjának felügyelt.

A fő csomópontot a indítsa el a HPC Cluster Manager a HPC Pack-fürt állapotának ellenőrzéséhez. Kezelheti, és a Linux számítási csomópontok dolgozik Windows ugyanúgy figyelő számítási csomópontokon. Például tekintse meg a Linux-csomópontok szereplő **erőforrás-kezelés** (ezek a csomópontok telepítik a **LinuxNode** sablon).

![Csomópont-felügyelet][management]

A Linux-csomópontok is megjelenik a **Hőtérkép** megtekintése.

![Hőtérkép][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Adatok áthelyezése a fürt Linux-csomópontok
Számos választási lehetőség áll, többek között a Linux-csomópontok és a Windows a fürt fő csomópontjának adatok áthelyezése. A következő szakaszokban részletesen ismertetett három gyakran használt módszerek a következők:

* **Azure-beli** -közzéteszi egy felügyelt SMB-fájlmegosztás az Azure storage-adatfájlok tárolására. Windows és Linux-csomópontok csatlakoztathat Azure-fájlmegosztás meghajtó vagy mappa egyszerre, még akkor is, ha azok különböző virtuális hálózatokban lévő üzembe helyezésük.
* **Átjárócsomópont SMB-megosztás** – a fő csomópont szabványos Windows megosztott mappa csatlakoztatja a Linux-csomópontokat.
* **A fő csomópont NFS-kiszolgáló** -fájlmegosztási megoldást kínál Windows és Linux rendszerű vegyes környezetekben.

### <a name="azure-file-storage"></a>Az Azure File storage
A [Azure File](https://azure.microsoft.com/services/storage/files/) szolgáltatás elérhetővé teszi a szabványos SMB 2.1 protokollt használó fájlmegosztások. Az Azure virtuális gépek és felhőszolgáltatások oszthatnak meg több csatlakoztatott megosztások révén, és a helyszíni alkalmazások férhetnek hozzá a fájltároló API révén fájladatokat oszthatnak meg. 

Azure-fájlmegosztás létrehozásához, és csatlakoztassa azt a fő csomópontot részletes lépéseiért lásd: [a Windows Azure File storage használatának első lépései](../../../storage/files/storage-how-to-use-files-windows.md). Az Azure-fájlmegosztás csatlakoztatása a Linux-csomópontokon, lásd: [Azure File storage használata linuxszal](../../../storage/files/storage-how-to-use-files-linux.md). Persisting Connections-kapcsolatok beállításához, tekintse meg a [Persisting kapcsolatok a Microsoft Azure Files](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

A következő példában hozzon létre egy Azure-fájlmegosztást a storage-fiók. A megosztás csatlakoztatásához a központi csomóponton, nyisson meg egy parancssort, és írja be a következő parancsokat:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Ebben a példában allvhdsje a tárfiók nevét, storageaccountkey a tárfiók kulcsát, és rdma-t az Azure fájlmegosztás neve. Az Azure-fájlmegosztást az átjárócsomóponthoz, Z: van csatlakoztatva.

Az Azure-fájlmegosztás csatlakoztatása a Linux-csomópontokat, futtassa a **clusrun** parancsot a fő csomópontot. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  hasznos HPC Pack eszköz a több csomóponton felügyeleti feladatok elvégzésére. (Lásd még: [a Linuxos csomópontokhoz Clusrun](#Clusrun-for-Linux-nodes) ebben a cikkben.)

Nyisson meg egy Windows PowerShell-ablakot, és adja meg a következő parancsokat:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Az első parancs a LinuxNodes csoport minden csomópontján /rdma nevű mappát hoz létre. A második parancs csatlakoztatja az Azure File megosztás allvhdsjw.file.core.windows.net/rdma dir /rdma mappát az alakzatot, és a fájl mód bits 777 beállítása. A második parancs allvhdsje a tárfiók nevét, és a storageaccountkey a tárfiók kulcsára.

> [!NOTE]
> A "\`" szimbólumot a második parancs a PowerShell-escape szimbólum. "\`," azt jelenti, hogy a "," (vessző karakter) a parancs egy részét.
> 
> 

### <a name="head-node-share"></a>Átjárócsomópont megosztás
Azt is megteheti csatlakoztassa a fő csomópont egy megosztott mappát a Linux-csomópontok. Egy megosztás biztosítja a legegyszerűbb módja a fájlok megosztása, de a fő csomópontot és az összes Linux-csomópontok azonos virtuális hálózatban kell telepíteni. A lépések a következők.

1. Hozzon létre egy mappát a fő csomópontot, és ossza meg mindenki számára olvasási/írási engedéllyel. Például megoszthatja az átjárócsomóponthoz, mint a D:\OpenFOAM \\CentOS7RDMA-HN\OpenFOAM. CentOS7RDMA-HN Íme az átjárócsomóponthoz állomásnevét.
   
    ![Fájlmegosztási engedélyek][fileshareperms]
   
    ![Fájlmegosztás][filesharing]
1. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Az első parancs a LinuxNodes csoport minden csomópontján /openfoam nevű mappát hoz létre. A második parancs csatlakoztatja a megosztott mappa //CentOS7RDMA-HN/OpenFOAM dir mappát az alakzatot, és fájlt mód bits 777 beállítása. A felhasználónevet és jelszót a parancsban a felhasználónevet és jelszót a fő csomópontot a fürt felhasználói kell lennie. (Lásd: [hozzáadása vagy eltávolítása fürt felhasználók](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> A "\`" szimbólumot a második parancs a PowerShell-escape szimbólum. "\`," azt jelenti, hogy a "," (vessző karakter) a parancs egy részét.
> 
> 

### <a name="nfs-server"></a>NFS-kiszolgáló
Az NFS-szolgáltatás lehetővé teszi megosztani, és migrálni a fájlok az SMB protokoll használatával, a Windows Server 2012 operációs rendszert futtató és az NFS protokoll használatával Linux-alapú számítógépek között. Az NFS-kiszolgáló és az összes csomóponton telepíteni szeretné ugyanabban a virtuális hálózatban kell. Linux-csomópontokat az SMB-megosztáson képest jobb kompatibilitást biztosít. Ha például fájl hivatkozások támogatja.

1. Telepítse és állítsa be az NFS-kiszolgáló, kövesse a [Server, a rendszer első Fájlmegosztására teljes körű](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Hozzon létre például az NFS-megosztások nevű nfs a következő tulajdonságokkal:
   
    ![NFS-engedélyezés][nfsauth]
   
    ![NFS-megosztás engedélyeinek][nfsshare]
   
    ![Az NFS NTFS-engedélyek][nfsperm]
   
    ![NFS-kezelési tulajdonságok][nfsmanage]
1. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Az első parancs a LinuxNodes csoport minden csomópontján /nfsshared nevű mappát hoz létre. A második parancs csatlakoztatja az NFS-megosztások CentOS7RDMA-HN: / nfs arra a mappára. Itt CentOS7RDMA-HN: nfs van a távoli elérési útját az NFS-megosztások.

## <a name="how-to-submit-jobs"></a>Hogyan küldhetők be feladatok
Többféleképpen is küldhetők be feladatok a HPC Pack-fürthöz:

* HPC Cluster Manager vagy a HPC Job Manager eszköz grafikus felhasználói felületen
* HPC-webportál
* REST API

Feladat küldése a fürthöz az Azure-beli HPC Pack GUI-eszközöket és a HPC-webportál olyan ugyanaz, mint a Windows a számítási csomópontokon. Lásd: [HPC Pack Feladatkezelő](https://technet.microsoft.com/library/ff919691.aspx) és [hogyan küldhetők be feladatok egy a helyszíni ügyfélszámítógépről](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Küldhetők be feladatok a REST API-n keresztül, tekintse meg [létrehozása és a feladatok elküldése a REST API használatával a Microsoft HPC Pack](https://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Feladatok beküldése az egy Linux-ügyfél, is tekintse meg a Python-mintához a [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>A Linuxos csomópontokhoz Clusrun
A HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) hajtsa végre a parancsokat a Linux-csomópontok vagy az parancssor vagy a HPC Cluster Manager eszköz használható. Az alábbiakban néhány egyszerű példát.

* Jelenlegi felhasználói nevek megjelenítése a fürt minden csomópontján.
  
    ```command
    clusrun whoami
    ```
* Telepítse a **gdb** debugger eszköz a **yum** az összes csomópontja a linuxnodes csoportból, és 10 perc után indítsa újra a csomópontokat.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Azt a héjparancsfájlt, minden egyes számok 1-10 minden egyes Linux csomóponton egy második megjelenítése a fürt létrehozása, futtassa, és azonnal megjeleníti a kimenetét csomópontjából.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Előfordulhat, hogy kell használnia az egyes escape-karaktereket **clusrun** parancsokat. Az ebben a példában is látható, ahogy ^ karaktert a parancssorban a ">" szimbólumot.
> 
> 

## <a name="next-steps"></a>További lépések
* Próbálkozzon a nagyobb mennyiségű csomópont a fürt vertikális felskálázásával, vagy a fürt egy Linux rendszerű számítási feladatok futtatása. Egy vonatkozó példáért lásd: [NAMD futtatása a Microsoft HPC Packkel Linux számítási csomópontok az Azure-ban](hpcpack-cluster-namd.md).
* Próbálja ki a fürt [RDMA-kompatibilis, nagy számítási igényű virtuális gépek](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) MPI számítási feladatok futtatásához. Egy vonatkozó példáért lásd: [OpenFOAM futtatása a Linux rdma-t a Microsoft HPC Pack-fürt az Azure-ban](hpcpack-cluster-openfoam.md).
* Ha érdekli a helyszíni HPC Pack-fürt Linux-csomópontok használata, tekintse meg a [TechNet útmutatást](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
