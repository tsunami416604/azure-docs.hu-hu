---
title: Windows RDMA-fürt beállítása MPI-alkalmazások futtatására |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Windows HPC Pack-fürt mérete H16r, H16mr, a8-as vagy a9-es virtuális gépek futtatásához az MPI-alkalmazások az Azure RDMA hálózati használatára.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 52338cc21e46b544c2abb79cd7094615c837a2e8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233779"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>A HPC Pack segítségével Windows RDMA-fürt beállítása MPI-alkalmazások futtatására
Az Azure-ban Windows RDMA-fürt beállítása [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) és [RDMA-kompatibilis HPC Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) párhuzamos Message Passing Interface (MPI) alkalmazások futtatásához. RDMA-kompatibilis, a Windows Server-alapú csomópontok HPC Pack-fürt üzembe helyezésekor MPI-alkalmazások hatékonyan egy alacsony késleltetésű, nagy átviteli sebességű hálózat az Azure-ban, amely távoli közvetlen memória-hozzáféréses (RDMA) technológiát alapul keresztül kommunikálnak.

## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack-fürt üzembe helyezési beállítások
A Microsoft HPC Pack egy olyan eszköz, amennyiben további költségek nélkül hozhat létre a HPC-fürtök a helyszíni vagy az Azure-ban Windows vagy Linux-alapú HPC-alkalmazások futtatására. HPC Pack a Message Passing Interface a Windows (MS-MPI) a Microsoft általi implementációja futtatókörnyezetének tartalmazza. Ha RDMA-kompatibilis példányai egy támogatott Windows Server operációs rendszert használ, a HPC Pack biztosítja a hatékony lehetőség elérhető az Azure RDMA hálózati Windows MPI-alkalmazások futtatására. 

Ez a cikk bemutatja a két forgatókönyvek és hivatkozások részletes útmutatókra a Microsoft HPC Pack 2012 R2 Windows RDMA-fürt beállítása. 

* 1. forgatókönyv. Nagy számítási igényű feldolgozói szerepkör példányai (PaaS) üzembe helyezése
* 2. forgatókönyv. Számítási csomópontok nagy számítási igényű virtuális gépek (IaaS) üzembe helyezése

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>1. forgatókönyv: A nagy számítási igényű feldolgozói szerepkör példányai (PaaS) üzembe helyezése
A meglévő HPC Pack-fürthöz adjon hozzá további számítási erőforrásokat az Azure feldolgozói szerepkör példányai (Azure-csomópontok) futó felhőszolgáltatások (PaaS). Ez a funkció, más néven az "a csúcsterheléseket az Azure-bA" HPC Pack, a feldolgozói szerepkör példányai méretek széles támogatja. Az Azure-csomópontok hozzáadásakor adja meg az RDMA-kompatibilis méretek egyike.

Az alábbiakban szempontjait és lépéseit a csúcsterhelések átirányítása az RDMA-kompatibilis egy meglévő Azure-példányokon (jellemzően helyszíni) fürt. Hasonló eljárással feldolgozói szerepkörpéldányok hozzáadása egy Azure virtuális gép üzembe helyezett HPC Pack fő csomópontot.

> [!NOTE]
> Az Azure-bA a HPC Pack segítségével folyamatosan oktatóanyagért lásd: [egy hibrid fürt HPC packkel](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Vegye figyelembe a a következő lépések, amelyek kifejezetten a RDMA-kompatibilis Azure-csomópontok.
> 
> 

![A csúcsterhelések átirányítása az Azure][burst]

### <a name="steps"></a>Lépések
1. **Üzembe helyezhet és konfigurálhat egy HPC Pack 2012 R2 átjárócsomópont**
   
    A HPC Pack telepítési csomag letöltéséhez a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Követelmények és készíti elő az Azure adatlöketek központi telepítésével kapcsolatos utasításokat lásd: [a csúcsterhelések átirányítása az Azure Worker Instances with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Felügyeleti tanúsítvány konfigurálása az Azure-előfizetésben**
   
    A kapcsolat a fő csomópontot és az Azure közötti biztonságos tanúsítvány konfigurálása. Beállítások és eljárások: [forgatókönyvek az Azure felügyeleti tanúsítvány konfigurálása a HPC Pack](https://technet.microsoft.com/library/gg481759.aspx). Tesztelési célú telepítések esetén a HPC Pack-alapértelmezett Microsoft HPC Azure felügyeleti tanúsítványt telepít gyorsan feltöltheti az Azure-előfizetéshez.
3. **Új felhőalapú szolgáltatás és a egy storage-fiók létrehozása**
   
    Az Azure portal használatával hozzon létre egy felhőszolgáltatás (klasszikus) és a egy tárfiók (klasszikus) az üzembe helyezéshez. Hozzon létre egy régióban, ahol a H-sorozatú, a8-asnak vagy a9-es méretű használni kívánt elérhető ezeket az erőforrásokat. Lásd: [az Azure-termékek régiók szerint](https://azure.microsoft.com/regions/services/).

4. **Egy Azure-csomópont-sablon létrehozása**
   
    Használja a csomópont-sablon létrehozása varázsló a HPC Cluster Manager. Lépéseiért lásd: [hozzon létre egy Azure-csomópont sablon](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) a "Lépések való üzembe helyezése Azure csomópontok a Microsoft HPC Pack".
   
    A kezdeti vizsgálatot javasoljuk, hogy manuális rendelkezésre állási házirend beállítása a sablonban.
5. **Csomópontok hozzáadása a fürthöz**
   
    Használja a csomópont hozzáadása varázsló a HPC Cluster Manager. További információkért lásd: [Azure-csomópontok hozzáadása a Windows HPC-fürt](https://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    A csomópontok mérete megadásakor válassza ki az RDMA-kompatibilis példány méretek egyikét.
   
   > [!NOTE]
   > A nagy számítási igényű példányok az Azure-telepítés minden egyes hirtelen, a HPC Pack automatikusan telepíti a legalább két RDMA-kompatibilis példánnyal (például az a8-as) proxy csomópontként mellett az Azure feldolgozói szerepkör példányai, adja meg. A proxy csomópontot az előfizetés számára kiosztott és költségekkel együtt az Azure feldolgozói szerepkörpéldányok magot használja.
   > 
   > 
6. **Indítsa el a (kiépíteni) a csomópontok és hasznosítsa őket online feladatok futtatása**
   
    Válassza ki a csomópontokat, és használja a **Start** HPC Cluster Manager művelet. Amikor a kiépítés befejeződött, válassza ki a csomópontokat, és használja a **online állapotba hozás** HPC Cluster Manager művelet. A csomópontok, feladatok futtatása készen áll.
7. **A fürt-feladatok elküldése**
   
   A HPC Pack feladat beküldése eszközei fürt feladatok futtatásához. Lásd: [Microsoft HPC Pack: feladatkezelés](https://technet.microsoft.com/library/jj899585.aspx).
8. **Állítsa le (megszüntetési) a csomópontok**
   
   Amikor elkészült a futó feladatok, a csomópontok offline állapotba, és használja a **leállítása** HPC Cluster Manager művelet.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>2. forgatókönyv: Üzembe helyezése számítási csomópontok nagy számítási igényű virtuális gépek (IaaS)
Ebben a forgatókönyvben a HPC Pack fő csomópontot és a telepítése fürtben lévő számítási csomópontok a virtuális gépek Azure-beli virtuális hálózathoz. A HPC Pack biztosítja több [Azure-beli virtuális gépeken a központi telepítési beállítások](../../windows/hpcpack-cluster-options.md), többek között az automatizált üzembehelyezési szkriptek és az Azure gyorsindítási sablonok. Tegyük fel, a következő szempontjait és lépéseit útmutató kell használnia a [HPC Pack IaaS telepítési szkripttel](hpcpack-cluster-powershell-script.md) -HPC Pack 2012 R2-fürt az Azure-ban az üzembe helyezés automatizálásához.

![Fürt az Azure-beli virtuális gépeken][iaas]

### <a name="steps"></a>Lépések
1. **A fürt fő csomópontjának létrehozása és a számítási csomópont virtuális gépek a HPC Pack IaaS telepítési parancsfájllal futtatja az ügyfélszámítógépen**
   
    A HPC Pack IaaS telepítési szkripttel csomag letöltése az [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Az ügyfélszámítógép előkészítése, hozzon létre a parancsfájl konfigurációs fájlt, és futtassa a parancsfájlt, lásd: [HPC-fürt létrehozása HPC Pack IaaS telepítési szkripttel](hpcpack-cluster-powershell-script.md). 
   
    RDMA-kompatibilis beállításával kapcsolatos megfontolások a számítási csomópontok, lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) , és vegye figyelembe a következőket:
   
   * **Virtuális hálózat**: Adjon meg egy új virtuális hálózatot egy régióban, amelyben a használni kívánt H-sorozatú, a8-asnak vagy a9-es méret érhető el. Lásd: [az Azure-termékek régiók szerint](https://azure.microsoft.com/regions/services/).

   * **A Windows Server operációs rendszer**: támogatja az RDMA-kapcsolattal, adjon meg egy kompatibilis a Windows Server operációs rendszer például a Windows Server 2012 R2 a számítási csomópont virtuális gépeket.
   * **Felhőszolgáltatások**: mivel a szkript a klasszikus üzemi modellt használja, a fürt virtuális gépeit használatával helyezi üzembe az Azure cloud services (`ServiceName` beállítások a konfigurációs fájlban). Javasoljuk, hogy a fő csomópont egy cloud service-ben és a egy másik felhőalapú szolgáltatást a számítási csomópontok. 
   * **Átjárócsomópont csomópontméret**: Ebben az esetben érdemes lehet egy méretének legalább A4 (nagyon nagy) az átjárócsomópont.
   * **HpcVmDrivers bővítmény**: az üzembe helyezési parancsfájl telepíti az Azure Virtuálisgép-ügynök és a HpcVmDrivers bővítmény automatikusan mérete a8-asnak vagy a9-es számítási csomópontok egy Windows Server operációs rendszer központi telepítésekor. HpcVmDrivers illesztőprogramokat telepít a számítási csomópont virtuális gépek képesek csatlakozni az RDMA-hálózatot így. Az RDMA-kompatibilis H-sorozat virtuális gépei manuálisan kell telepítenie a HpcVmDrivers bővítményt. Lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Fürthálózat-konfiguráció**: A telepítési parancsfájl automatikusan beállítja a HPC Pack-fürthöz, a topológia 5 (a vállalati hálózaton lévő összes csomópont). Ebben a topológiában a virtuális gépek összes HPC Pack fürttelepítések szükség. Ne változtassa meg később a fürt hálózati topológiát.
1. **A számítási csomópontok online állapotba feladatok futtatásához**
   
    Válassza ki a csomópontokat, és használja a **online állapotba hozás** HPC Cluster Manager művelet. A csomópontok, feladatok futtatása készen áll.
3. **A fürt-feladatok elküldése**
   
    Csatlakozás az átjárócsomóponthoz küldhetők be feladatok, vagy állítsa be egy helyszíni számítógépen ehhez. További információ: [egy HPC-feladatok elküldése a fürt az Azure-ban](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **A csomópontok offline állapotba, és állítsa le (szabadítsa fel) őket**
   
    Ha elkészült a futó feladatok, a kapcsolat nélküli módban a csomópontok HPC Cluster Manager igénybe. Ezután az Azure felügyeleti eszközökhöz segítségével leállíthatja őket.

## <a name="run-mpi-applications-on-the-cluster"></a>A fürt MPI-alkalmazások futtatására
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Példa: Mpipingpong futtassa HPC Pack-fürthöz
Az RDMA-kompatibilis példányainak egy HPC Pack üzembe helyezés ellenőrzéséhez futtassa a HPC Pack **mpipingpong** parancsot a fürtön. **mpipingpong** ismételten a teljesítmény és a késés mérések számítása párosított csomópontok és statisztikákat az RDMA-kompatibilis alkalmazások hálózat közötti csomagokat küld. Ez a példa bemutatja egy jellegzetes futtatásához az MPI-feladatok (ebben az esetben **mpipingpong**) a fürt használatával **mpiexec** parancsot.

Ez a példa feltételezi, hogy "az Azure-bA adatlöket" konfigurációban hozzáadott Azure-csomópontok ([1. forgatókönyv](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Ha egy Azure-beli virtuális fürt telepített HPC Pack, szüksége lesz módosításához adjon meg egy másik csomópont csoportot, és további környezeti változókat az RDMA hálózati hálózati forgalmat a parancs szintaxisát.

A mpipingpong futtatunk a fürtön:

1. A fő csomópont, vagy egy megfelelően konfigurált ügyfélszámítógépen nyisson meg egy parancssort.
2. A négy csomópont egy Azure adatlöketek központi telepítésben lévő csomópontok közötti késés megbecsülni, írja be a mpipingpong futtathat egy kis csomag mérete, és számos ismétlések olyan feladatot küld el a következő parancsot:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    A parancs visszaadja az elküldött feladat Azonosítóját.
   
    Ha üzembe helyezte az Azure virtuális gépeken telepített HPC Pack-fürthöz, adja meg a csomópont csoportot, amely tartalmazza az önálló felhőalapú szolgáltatásként telepített csomópont virtuális gépek számítási és módosíthatja a **mpiexec** paranccsal a következőképpen:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Amikor a feladat befejeződik, (ebben az esetben az 1. feladat a feladat kimenete), a kimenet megtekintéséhez írja be a következő
   
    ```Command
    task view <JobID>.1
    ```
   
    ahol &lt; *JobID* &gt; az azonosítója, amelyhez el lett küldve.
   
    A kimeneti késés eredmények a következőhöz hasonló tartalmazza.
   
    ![Ping pong késés][pingpong1]
4. Szeretné becsülni az átviteli sebességet, Azure csúcsterhelési csomópontok között, írja be a következő parancsot a feladat futtatásához **mpipingpong** nagy csomagméretet és néhány ismétlését:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    A parancs visszaadja az elküldött feladat Azonosítóját.
   
    Az Azure virtuális gépeken telepített HPC Pack-fürthöz módosítsa a parancsot a 2. lépésben feljegyzett.
5. Ha a feladat befejeződik, a kimenet megtekintéséhez (ebben az esetben az 1. feladat a feladat kimenete), írja be a következőt:
   
    ```Command
    task view <JobID>.1
    ```
   
   A parancs kimenete átvitelisebesség-eredmény az alábbihoz hasonló.
   
   ![Ping pong átviteli sebesség][pingpong2]

### <a name="mpi-application-considerations"></a>MPI-alkalmazások szempontok
Az alábbiakban az Azure-ban a HPC packkel MPI-alkalmazások futtatásához szükséges jogosultságok. Néhány csak Azure-csomópontok (feldolgozói szerepkörpéldányok "az Azure-bA adatlöket" konfigurációban hozzáadott) központi telepítései érvényesek.

* Feldolgozói szerepkör példányai felhőszolgáltatásban vannak rendszeres időközönként építeni előzetes értesítés nélkül az Azure-ban (például, ha a rendszer karbantartási, és a egy példány nem). Ha egy példány van kiépíteni az MPI-feladatok futtatása, a példány elveszíti az adatokat, és állapotba ad vissza, ha először telepítették, ami okozhat az MPI-feladat sikertelen lesz. További csomópontok egyetlen MPI-feladatok, és annál hosszabb ideig használhatja a feladat fut, annál valószínűbb, hogy az egyik példányhoz van építeni egy feladat futása közben. Szintén megfontolandó szempontok, ha a telepítésben egyetlen csomópont megjelölni egy fájlkiszolgáló.
* MPI-feladatok futtatása az Azure-ban, nem kell az RDMA-kompatibilis példányok használatára. HPC Pack által támogatott bármely példányméret is használhatja. Az RDMA-kompatibilis példányok azonban használata akkor javasolt, viszonylag nagy méretű, kis-és nagybetűket, a késés és a hálózat kapcsolja össze a csomópontok a sávszélesség MPI-feladatok futtatásához. Ha további méretek használatával és a sávszélesség-késésérzékeny MPI-feladatok futtatása, ajánlott futó kis feladatok, amelyben fut egy feladat csak néhány csomópont.
* Az Azure-példányok üzembe helyezett alkalmazások feltételei vonatkoznak rá a licencelési az alkalmazáshoz kapcsolódó. Ellenőrizze a licencelési vagy egyéb korlátozásokról a felhőben futó minden kereskedelmi alkalmazás szállítójához. Nem minden szállító kínál használatalapú licencet.
* Azure-példányain való hozzáférést a helyszíni csomópontokat, a megosztások és a licenckiszolgálókat további beállítása szükséges. Ahhoz, hogy az Azure-csomópontok eléréséhez egy helyszíni licenckiszolgáló, például a site-to-site Azure virtuális hálózat konfigurálása is.
* MPI-alkalmazások futtatása az Azure-példányokon, minden egyes MPI alkalmazás regisztrálása a Windows tűzfal-példányokon futtatásával a **hpcfwutil** parancsot. Ez lehetővé teszi, hogy végre lehessen hajtani egy portot a tűzfalon által dinamikusan hozzárendelt MPI-kommunikáció.
  
  > [!NOTE]
  > Az Azure-környezetek hirtelen az összes új Azure csomópontra, a fürthöz hozzáadott automatikusan futtatni kívánt tűzfal kivétel parancs is konfigurálhatja. Futtatása után a **hpcfwutil** parancsot, és győződjön meg arról, hogy az alkalmazás működését egy indítási szkriptet az Azure-csomópont hozzáadása a parancsot. További információkért lásd: [indítási parancsfájl használata az Azure-csomópontok](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack környezeti változó CCP_MPI_NETMASK fürt MPI-kommunikációhoz elfogadható címtartományt használja. A HPC Pack 2012 R2-től kezdődően a CCP_MPI_NETMASK fürt környezeti változót csak MPI kommunikációt érinti tartományhoz csatlakoztatott fürt számítási csomópontjai között (a helyszínen vagy az Azure-beli virtuális gépeken). A változó befejeződésével Azure konfigurációhoz hozzáadott csomópontok figyelmen kívül hagyja.
* MPI-feladatok nem futtathatók az Azure-ban különböző felhőszolgáltatások (például a nagy sebességű, a másik csomópont sablonok vagy az Azure virtuális gép számítási csomópontok több felhőalapú szolgáltatásaiban üzembe helyezett Azure-környezetek) üzembe helyezett példányok között. Ha több Azure-csomópont-telepítés másik csomópont sablonokkal az elindított, az MPI-feladatok Azure-csomópontok csak egy készletét kell futtatni.
* Ha Azure-csomópontok hozzáadása a fürthöz, és online állapotba helyezés, a HPC-feladatok a Feladatütemező szolgáltatás azonnal megpróbálja feladatok indításához a csomópontokon. Ha csak a számítási feladatok egy része futtatható az Azure-ban, győződjön meg arról, hogy frissíteni vagy meghatározásához, mit feladattípusok futtathatja az Azure-ban feladat-sablonok létrehozása. Annak érdekében, hogy egy feladat sablonnal elküldött feladatok csak az Azure-csomópontok futtassa, például a csoportok csomópont tulajdonság hozzáadása a feladathoz sablonhoz, és a AzureNodes jelöli ki a szükséges érték. Az Add-HpcGroup HPC PowerShell-parancsmag segítségével hozhat létre egyéni csoportokat az Azure-csomópont.

## <a name="next-steps"></a>További lépések
* HPC Pack használatával alternatívájaként fejlesztése az Azure Batch szolgáltatással MPI-alkalmazások, számítási csomópontok az Azure-ban felügyelt készletein fussanak. Lásd: [többpéldányos tevékenységek használata Message Passing Interface (MPI) alkalmazások futtatásához az Azure Batchben](../../../batch/batch-mpi.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
