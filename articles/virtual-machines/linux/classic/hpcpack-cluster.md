---
title: Linux virtuális gépek számítási HPC Pack-fürtben lévő |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre és HPC Pack-fürt használatára az Azure-ban a Linux nagy teljesítményű számítástechnikai (HPC) munkaterhelések
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
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Az Azure-beli HPC Pack-fürtök linuxos számítási csomópontjaival kapcsolatos alapvető tudnivalók
Állítson be egy [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) Azure egy központi fut a Windows Server és több csomópontot tartalmazó fürt számítási csomópontjain futó támogatott Linux-disztribúció. Megismerkedhet a beállítások áthelyezni az adatokat a központi Windows-csomópont, a fürt és a Linux-csomópontok között. Útmutató a fürt Linux HPC feladatok elküldéséhez.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Magas szinten a következő ábrán a HPC Pack fürt létrehozása és használata.

![Linux-csomópontok HPC Pack fürt][scenario]

Egyéb lehetőségek az alkalmazásokat és szolgáltatásokat futtathatnak Linux HPC az Azure-ban, lásd: [kötegelt és nagy teljesítményű számítástechnikai rendszerek technikai erőforrások](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>HPC Pack-fürt üzembe helyezése a Linux számítási csomópont
Ez a cikk bemutatja, két lehetőség HPC Pack-fürt üzembe helyezése az Azure-ban Linux számítási csomópontjain. Mindkét módszer a Windows Server Piactéri rendszerkép HPC Pack az átjárócsomópont létrehozásához használja. 

* **Az Azure Resource Manager-sablon** -sablont az Azure piactérről, vagy a következő gyorsindítási sablonon közösségi használatával automatizálhatja a Resource Manager üzembe helyezési modellben a fürt létrehozását. Például a [HPC Pack fürt Linux munkaterhelések](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) sablon az Azure piactéren létrehoz egy teljes HPC Pack fürt infrastruktúra Linux HPC a munkaterhelések.
* **PowerShell parancsfájl** -használja a [Microsoft HPC Pack IaaS telepítési parancsfájl](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) a teljes fürtre történő telepítés, a klasszikus üzembe helyezési modellel automatizálásához. Az Azure PowerShell-parancsfájl HPC Pack VM-lemezkép használja az Azure piactéren a gyors telepítése során, és a konfigurációs paraméterek központi telepítése Linux számítási csomópontok széles választékát nyújtja.

További információ a HPC Pack fürt telepítési lehetőségek az Azure-ban: [beállítások létrehozását és kezelését egy nagy teljesítményű számítástechnikai rendszerek (HPC) fürtön, az Azure-ban a Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés** -előfizetés használhatja az Azure globális vagy Azure Kína szolgáltatásban. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány percig.
* **Magok kvóta** -előfordulhat, hogy növelnie kell a beállított kvótát mag, különösen akkor, ha úgy dönt, hogy a Virtuálisgép-méretek multicore több fürtcsomóponton telepíteni. A kvóta növeléséhez, nyissa meg az online támogatás ügyfélkérés díjmentesen.
* **Linux-disztribúció** -HPC Pack jelenleg a következő Linux terjesztésekről támogatja a számítási csomópontok. Ezek terjesztéseket piactér verzióját használják, ahol az rendelkezésre áll, vagy adja meg a saját.
  
  * **CentOS-alapú**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1-es HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, a SLES 12 (prémium), a SLES 12 SP1, a SLES 12 SP1 (prémium) SLES 12 a HPC, SLES 12 a HPC (prémium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Az RDMA-kompatibilisek-e Virtuálisgép-méretek egyike az Azure RDMA hálózati használatához adja meg a SUSE Linux Enterprise Server 12 HPC vagy HPC CentOS-alapú lemezkép az Azure piactérről. További információkért lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

A fürt a HPC Pack IaaS telepítési parancsfájl használatával történő központi telepítéséről további Előfeltételek:

* **Ügyfélszámítógép** – a fürt üzembe helyezési parancsfájl futtatásához Windows rendszerű számítógépre van szüksége.
* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépen.
* **HPC Pack IaaS telepítési parancsfájl** - töltse le és csomagolja ki a parancsfájlt a legújabb verzióját a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A parancsfájl verziója futtatásával ellenőrizheti `.\New-HPCIaaSCluster.ps1 –Version`. Ez a cikk verzió 4.4.1 vagy később, a parancsfájl alapján.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>1. telepítési lehetőséget. A Resource Manager-sablon használata
1. Lépjen a [HPC Pack fürt Linux munkaterhelések](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) sablon az Azure piactéren, és kattintson a **telepítés**.
2. Az Azure portálon, tekintse át az adatokat, és kattintson a **létrehozása**.
   
    ![Portál létrehozásához][portal]
3. Az a **alapjai** panelen adjon meg egy nevet a fürt, amely az átjárócsomóponthoz VM is nevezi. Válasszon egy meglévő erőforráscsoportot, vagy olyan helyre, amely elérhető a telepítési csoport létrehozása. A hely van hatással, egyes Virtuálisgép-méretek és más Azure-szolgáltatásokkal (lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/)).
4. Az a **csomópont beállítások Head** panelen első üzembe helyezés esetén általában elfogadhatja az alapértelmezett beállításokat. 
   
   > [!NOTE]
   > A **utáni konfigurációs parancsprogram URL-Címének** nem kötelező beállítás megadásához egy nyilvánosan elérhető Windows PowerShell-parancsfájlt, amely után fut-e a virtuális gép központi csomóponton futtatni szeretné. 
   > 
   > 
5. Az a **számítási csomópont beállítások** panelen válassza egy elnevezési mintát a csomópontok, a szám és a csomópontokon, és a Linux-disztribúció méretét.
6. A a **infrastruktúrabeállításai** panelen írja be a virtuális hálózat és az Active Directory tartományhoz, a tartomány és a virtuális gép rendszergazdai hitelesítő adatokat és a egy elnevezési mintája a storage-fiókok.
   
   > [!NOTE]
   > HPC Pack az Active Directory-tartomány használatával hitelesíti a felhasználókat a fürt. 
   > 
   > 
7. Miután az érvényesítési tesztek futtatásához, és tekintse át a használati feltételeket, kattintson a **beszerzési**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Telepítési beállítás 2. Az infrastruktúra-szolgáltatási telepítési parancsfájl használata
A fürt a HPC Pack IaaS telepítési parancsfájl használatával történő központi telepítéséről további Előfeltételek a következők:

* **Ügyfélszámítógép** – a fürt üzembe helyezési parancsfájl futtatásához Windows rendszerű számítógépre van szüksége.
* **Az Azure PowerShell** - [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview) (0.8.10 verzió vagy újabb) az ügyfélszámítógépen.
* **HPC Pack IaaS telepítési parancsfájl** - töltse le és csomagolja ki a parancsfájlt a legújabb verzióját a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). A parancsfájl verziója futtatásával ellenőrizheti `.\New-HPCIaaSCluster.ps1 –Version`. Ez a cikk verzió 4.4.1 vagy később, a parancsfájl alapján.

**Konfigurációs XML-fájl**

A HPC Pack IaaS telepítési parancsfájl bemeneti XML-konfigurációs fájl használatával írják le a HPC-fürtben. A következő minta konfigurációs fájlt egy HPC Pack átjárócsomópont és a számítási csomópontok két mérete A7 CentOS 7.0 Linux kis fürt határozza meg. 

A környezet és a kívánt fürtkonfigurációt szükség szerint módosítsa a fájl, és mentse a neve, pl. HPCDemoConfig.xml. Például hogy kell az előfizetés nevet és egy egyedi tárfiók neve és a felhőalapú szolgáltatás neve. Emellett érdemes a számítási csomópontok különböző támogatott Linux képfájl kiválasztása. A konfigurációs fájlban elemeivel kapcsolatos további információkért tekintse meg a Manual.rtf a parancsfájl mappában és [HPC-fürt létrehozása a HPC Pack IaaS telepítési parancsfájl](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

**A HPC Pack IaaS telepítési parancsfájl futtatásához**

1. Rendszergazdaként nyissa meg a Windows PowerShell az ügyfélszámítógépen.
2. Módosítsa a mappát, ahol a parancsfájl könyvtárat telepítve (E:\IaaSClusterScript ebben a példában).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. A következő parancsot a HPC Pack fürt központi telepítése. Ebben a példában feltételezzük, hogy a konfigurációs fájlban található E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Mivel a **AdminPassword** nincs megadva az előző parancsban szereplő kéri a jelszó megadására, a felhasználó *MyAdminName*.
   
    b. A parancsfájl ezután elindítja a konfigurációs fájl ellenőrzése. Ez attól függően, hogy a hálózati kapcsolat több percig is eltarthat.
   
    ![Ellenőrzés][validate]
   
    c. Érvényesítést átadni, miután a parancsfájl létrehozása a fürt erőforrásait sorolja fel. Adja meg *Y* folytatja.
   
    ![További források][resources]
   
    d. A parancsfájl a HPC Pack fürt telepítése elindul, és a konfiguráció további manuális lépések nélkül befejeződött. A parancsprogram futását néhány percig.
   
    ![Üzembe helyezés][deploy]
   
   > [!NOTE]
   > Ebben a példában a parancsfájl naplófájlt állítja elő automatikusan, mivel a **- LogFile** paraméter nincs megadva. A naplók valós időben nem írt, de az érvényesítési és a telepítés végén gyűjtése. Ha a PowerShell-folyamat leáll, a parancsfájl futása közben, néhány naplók elvesznek.
   > 
   > 

## <a name="connect-to-the-head-node"></a>A átjárócsomópontjához csatlakozik
Miután telepítette az Azure, a HPC Pack fürt [csatlakozzon a távoli asztal](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az átjárócsomóponthoz VM tartományba megadott hitelesítő adatok, amikor központilag telepítette a fürt (például *hpc\\clusteradmin*). A fürt kezelése a központi csomópontból.

Az átjárócsomópont indítsa el a HPC Pack fürt állapotának ellenőrzése a HPC Cluster Manager. Kezelheti, és a figyelő Linux számítási csomópontok használata Windows ugyanúgy számítási csomópontjain. Például tekintse meg a Linux-csomópontok felsorolt **erőforrás-kezelés** (ezek a csomópontok telepítik a **LinuxNode** sablon).

![Csomópont-felügyelet][management]

Linux csomópontján is megjelenik a **Hőtérkép** nézet.

![Hőtérkép][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Adatok áthelyezése Linux csomópontot tartalmazó fürtben
Számos választási lehetőség áll áthelyezni az adatokat a Linux-csomópontok és a központi Windows-csomópont, a fürt között. Az alábbiakban a három általános módszer, a következő szakaszokban részletesebben:

* **Az Azure File** -közzétesz egy kezelt SMB-fájlmegosztás adatfájlok tárolására az Azure-tárfiókba. Csomópontok Windows és Linux-csomópontjai is csatlakoztathatja egy Azure fájlmegosztás meghajtó vagy mappa egyszerre, még akkor is, ha azok a különböző virtuális hálózatokon van telepítve.
* **Átjárócsomópont SMB-fájlmegosztás** -csatlakoztatja a központi csomópont szokásos Windows megosztott mappa Linux csomópontján.
* **HEAD csomópont NFS-kiszolgáló** -fájlmegosztási megoldást kínál a Windows és Linux használnak.

### <a name="azure-file-storage"></a>Az Azure File storage
A [Azure File](https://azure.microsoft.com/services/storage/files/) szolgáltatás elérhetővé teszi a standard SMB 2.1 protokollal fájlmegosztások. Azure virtuális gépek és felhőszolgáltatások megosztott csatlakoztatott megosztásokon keresztül alkalmazások összetevői között, és a helyszíni alkalmazások férhetnek hozzá olyan megosztáson található fájladatokat a File storage API használatával. 

Azure-fájlmegosztás létrehozása, és csatlakoztassa azt az átjárócsomópont részletes lépéseiért lásd: [Ismerkedés az Azure File storage on Windows](../../../storage/files/storage-how-to-use-files-windows.md). Azure fájlmegosztás csatlakoztatása a Linux-csomóponton, lásd: [Azure File storage használata Linux](../../../storage/files/storage-how-to-use-files-linux.md). Hozza létre tárolásakor kapcsolatokat, lásd: [Persisting kapcsolatok a Microsoft Azure fájlok](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

A storage-fiókok Azure-fájlmegosztás létrehozása a következő példában. A megosztás csatlakoztatásához a head csomóponton, nyisson meg egy parancssort, és írja be a következő parancsokat:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Ebben a példában allvhdsje a tárfiók nevének storageaccountkey a tárfiók kulcsára, amely rdma az Azure fájlmegosztás neve. Az Azure fájlmegosztás az átjárócsomóponthoz, Z: van csatlakoztatva.

Azure fájlmegosztás csatlakoztatása Linux csomóponton, futtassa a **clusrun** az átjárócsomópont parancs. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  az HPC Pack eszköz több csomóponton felügyeleti feladatokat hajthat végre. (Lásd még: [Clusrun Linux csomópontok](#Clusrun-for-Linux-nodes) ebben a cikkben.)

Nyisson meg egy Windows PowerShell-ablakot, és írja be a következő parancsokat:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Az első parancs létrehoz egy /rdma a LinuxNodes csoport minden csomópontján nevű mappát. A második parancs csatlakoztatja az Azure File megosztás allvhdsjw.file.core.windows.net/rdma alakzatot dir /rdma mappát, majd a fájl mód bitet 777. A második parancs a allvhdsje a tárfiók neve pedig storageaccountkey a tárfiók kulcsára.

> [!NOTE]
> A "\`" szimbólumra a második parancs a értéke egy escape szimbólum PowerShell. "\`," azt jelenti, hogy a "," (vessző karakter) a parancs része.
> 
> 

### <a name="head-node-share"></a>Átjárócsomópont megosztás
Másik lehetőségként csatlakoztatása egy megosztott mappába a központi csomópont Linux csomópontok. Megosztás biztosít a legegyszerűbben úgy osszanak meg fájlokat, de az átjárócsomópont és az összes Linux csomópontra az azonos virtuális hálózatban kell telepíteni. A lépések a következők.

1. Hozzon létre egy mappát az átjárócsomóponthoz, és ossza meg az olvasási/írási engedéllyel rendelkező összes felhasználó. Például, megosztás, az átjárócsomópont D:\OpenFOAM \\CentOS7RDMA-HN\OpenFOAM. A HN CentOS7RDMA Ez az átjárócsomópont állomásnevét.
   
    ![Fájlmegosztási engedélyek][fileshareperms]
   
    ![Fájlmegosztás][filesharing]
2. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Az első parancs létrehoz egy /openfoam a LinuxNodes csoport minden csomópontján nevű mappát. A második parancs csatlakoztatja a megosztott mappa //CentOS7RDMA-HN/OpenFOAM alakzatot dir mappát, majd fájl mód bitet 777. A felhasználónevet és jelszót a parancsban a felhasználónevet és jelszót egy fürt átjárócsomópontjához felhasználójának kell lennie. (Lásd: [hozzáadását vagy eltávolítását fürt felhasználók](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> A "\`" szimbólumra a második parancs a értéke egy escape szimbólum PowerShell. "\`," azt jelenti, hogy a "," (vessző karakter) a parancs része.
> 
> 

### <a name="nfs-server"></a>NFS server
Az NFS-szolgáltatás lehetővé teszi a megosztás és az SMB protokollt használó Windows Server 2012 operációs rendszert futtató számítógépekhez és az NFS-protokollt használó Linux-alapú számítógépek közötti áttelepítése. Az NFS-kiszolgáló és a többi csomópontnak kell ugyanabban a virtuális hálózatban üzembe helyezhető. Linux-csomópontokkal SMB-megosztáson képest jobb kompatibilitást biztosít. Például fájlhivatkozások támogatja.

1. Telepítse és állítsa be az NFS-kiszolgáló, kövesse a lépéseket a [Server, a rendszer első Fájlmegosztására-végpontok](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Hozzon létre például egy NFS-megosztás nevű nfs a következő tulajdonságokkal:
   
    ![NFS-engedélyezés][nfsauth]
   
    ![NFS-megosztás engedélyeinek][nfsshare]
   
    ![NFS-NTFS-engedélyek][nfsperm]
   
    ![NFS-felügyeleti tulajdonságai][nfsmanage]
2. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Az első parancs létrehoz egy /nfsshared a LinuxNodes csoport minden csomópontján nevű mappát. A második parancs csatlakoztatja az NFS-megosztás CentOS7RDMA-HN: / nfs alakzatot a mappát. Itt CentOS7RDMA-HN: / nfs értéke az NFS-megosztás távoli elérési útját.

## <a name="how-to-submit-jobs"></a>Hogyan feladatok elküldéséhez
Többféleképpen is lehet a HPC Pack fürt feladatok elküldéséhez:

* HPC Fürtkezelő vagy a HPC Job Manager eszköz grafikus felhasználói felületen
* HPC-webportál
* REST API

Feladat elküldése a fürthöz az Azure-ban HPC Pack grafikus eszközöket és a HPC-webportál keresztül ugyanaz, mint a Windows számítási csomópontjain. Lásd: [HPC Pack Feladatkezelő](https://technet.microsoft.com/library/ff919691.aspx) és [egy helyszíni ügyfélszámítógépről feladatok küldéséhez hogyan](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A REST API-n keresztül feladatok küldéséhez, tekintse meg [létrehozása és elküldése feladatok a REST API használatával a Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). A Linux-ügyfélben feladatok küldéséhez, is tekintse meg a Python minta a [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Linux-csomópontok Clusrun
A HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) parancsok végrehajtása Linux csomópontokon keresztül, a parancssor vagy a HPC Cluster Manager eszköz használható. A következő példák alapvető.

* Az aktuális felhasználónevek megjelenítése a fürt összes csomópontján.
  
    ```command
    clusrun whoami
    ```
* Telepítse a **gdb** hibakereső eszközt a **yum** összes a linuxnodes csomópontja csoportban, majd indítsa újra a csomópontok 10 perc múlva.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Hozzon létre egy héjparancsfájlt, a fürt minden egyes számának 1 és 10 minden egyes Linux csomóponton egy második megjelenítése, majd futtassa és kimeneti csomópontjából azonnal megjelenítése.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Szükség lehet egyes escape karakter használandó **clusrun** parancsok. Ebben a példában látható, használjon ^ parancssorban karaktert a ">" szimbólumot.
> 
> 

## <a name="next-steps"></a>További lépések
* Próbálkozzon a fürt a csomópontok nagyobb számot vertikális felskálázásával, vagy a Linux-munkaterhelések futtatásával a fürtön. Egy vonatkozó példáért lásd: [NAMD futtassa a Microsoft HPC Pack Linux számítási csomópontok az Azure-ban](hpcpack-cluster-namd.md).
* Próbálja meg a fürt [RDMA-kompatibilisek-e, a számítási igényű virtuális gépek](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) MPI munkaterhelések futtatásához. Egy vonatkozó példáért lásd: [OpenFOAM futtassa a Microsoft HPC Pack egy Linux RDMA a fürtön, az Azure-ban](hpcpack-cluster-openfoam.md).
* Ha érdekli a helyszíni HPC Pack fürtben lévő Linux-csomópont működik, tekintse meg a [TechNet útmutatást](https://technet.microsoft.com/library/mt595803.aspx).

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
