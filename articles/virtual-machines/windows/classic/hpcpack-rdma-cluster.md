---
title: "MPI-alkalmazások futtatásához Windows RDMA fürt beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Windows HPC Pack fürtöt H16r, H16mr, A8 vagy A9 virtuális gépeket az Azure RDMA hálózati MPI alkalmazások futtatásához használandó méretét."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 437c475735ec3823de51c5f9e996a5303fe9cfa7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>MPI-alkalmazások futtatására HPC Pack Windows RDMA fürt beállítása
Az Azure-ban Windows RDMA fürt beállítása [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) és [RDMA-kompatibilis HPC Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) párhuzamos Message Passing Interface (MPI) alkalmazások futtatásához. Az RDMA-kompatibilis, a Windows Server-alapú HPC Pack-fürtben lévő csomópontok beállításakor MPI alkalmazások hatékonyan kis késéssel, a távoli közvetlen memória-hozzáférés (RDMA) technológia alapuló Azure magas teljesítmény hálózati protokollt használó kommunikációra.

Ha a Linux virtuális gépek az Azure RDMA hálózati hozzáférés, lásd: MPI feladatokat futtató [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack fürt üzembe helyezési lehetőségei
A Microsoft HPC Pack egy olyan eszköz, feltéve létrehozásához további költségek nélkül HPC-fürtök a helyszíni vagy a Windows vagy Linux HPC alkalmazások futtatásához Azure-ban. HPC Pack az üzenet továbbításához felület for Windows (MS-MPI) a Microsoft általi implementációja futtatókörnyezetének tartalmazza. Ha használja az RDMA-kompatibilis osztályt egy támogatott Windows Server operációs rendszert futtató, a HPC Pack az Azure RDMA hálózati elérő Windows MPI-alkalmazások futtatására hatékony lehetőséget biztosít. 

Ez a cikk bemutatja két forgatókönyvek és hivatkozások részletes útmutató a Microsoft HPC Pack 2012 R2 és Windows RDMA fürt beállítása. 

* 1. forgatókönyv. Számítási igényű feldolgozói szerepkör-példányok (PaaS) telepítése
* 2. forgatókönyv. Telepítse a számítási igényű VMs (IaaS) számítási csomópontokat

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>1. forgatókönyv: Központi telepítése a számítási igényű munkavégző szerepkörpéldányokat (PaaS)
HPC Pack meglévő fürtök adjon hozzá további számítási erőforrásokat Azure feldolgozói szerepkör példányát (Azure-csomópontok) (PaaS) felhőszolgáltatásban fut. Ez a szolgáltatás, más néven "kapacitásnövelés Azure" HPC Pack, a feldolgozói szerepkör-példányok számos különböző méretű támogatja. Az Azure-csomópontok hozzáadásakor meg kell adni az RDMA-kompatibilis méretek egyikét.

Az alábbiakban szempontjait és lépéseit az RDMA-kompatibilisek-kapacitásnövelés a meglévő Azure-példányokon (jellemzően helyszíni) fürthöz. Hasonló eljárással feldolgozópéldányok szerepkör hozzáadása a HPC Pack átjárócsomópont, amely egy Azure virtuális gép telepítve van.

> [!NOTE]
> Az oktatóanyagnak, amellyel HPC Pack Azure-kapacitásnövelés, lásd: [HPC Pack hibrid fürt beállítása](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Vegye figyelembe a az alábbi lépéseket, amelyek kifejezetten az RDMA-kompatibilisek-e alkalmazni az Azure-csomópontok.
> 
> 

![Az Azure-kapacitásnövelés][burst]

### <a name="steps"></a>Lépések
1. **Telepíthet és konfigurálhat egy átjáró HPC Pack 2012 R2-csomóponti**
   
    Töltse le a HPC Pack telepítési csomagot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Követelmények és az Azure-kapacitásnövelés telepítésének előkészítéséhez utasításokat lásd: [az Azure Worker-példány és a Microsoft HPC Pack kapacitásnövelés](https://technet.microsoft.com/library/gg481749.aspx).
2. **Felügyeleti tanúsítvány konfigurálása az Azure-előfizetés**
   
    Konfiguráljon egy tanúsítványt, az átjárócsomópont és az Azure közötti kapcsolat biztonságossá tétele érdekében. Beállítások és eljárások: [forgatókönyvek az Azure felügyeleti tanúsítvány konfigurálása HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Tesztelési célú telepítések esetén HPC Pack alapértelmezett Microsoft HPC Azure felügyeleti tanúsítványt telepít gyorsan feltöltheti az Azure-előfizetése.
3. **Új felhőalapú szolgáltatás és a storage-fiók létrehozása**
   
    Az Azure-portál használatával hozzon létre egy (klasszikus) felhőalapú szolgáltatás és a storage-fiók (klasszikus) a központi telepítéshez. Hozzon létre egy régiót, amennyiben rendelkezésre áll-e a használni kívánt H-sorozat, A8 és A9 méretű ezeket az erőforrásokat. Lásd: [régiónként Azure termékek](https://azure.microsoft.com/regions/services/).

4. **Egy Azure csomópont-sablon létrehozása**
   
    Használja a csomópont-sablon létrehozása varázsló HPC-Fürtkezelőben. Útmutató: [hozzon létre egy Azure csomópont sablont](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) "Lépéseket való telepítése Azure csomópontok a Microsoft HPC Pack".
   
    Kezdeti tesztek javasoljuk, hogy a sablonban manuális rendelkezésre állási házirend beállítása.
5. **Csomópontok hozzáadása a fürthöz**
   
    Használja a csomópont hozzáadása varázsló HPC-Fürtkezelőben. További információkért lásd: [Azure csomópontok hozzáadása a Windows HPC-fürt](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    A csomópontok méretének megadása esetén válasszon ki egy RDMA-kompatibilisek-e a példány mérete.
   
   > [!NOTE]
   > Minden egyes kapacitásnövelés a számítási igényű példányok az Azure-telepítéssel, a HPC Pack automatikusan telepíti a legalább két RDMA-kompatibilisek-példányok (például A8) proxy csomópontok mellett adja meg, hogy Azure feldolgozói szerepkör-példányok. A proxy csomópontok az előfizetés számára kiosztott és költségek mellett Azure feldolgozói szerepkör-példányok magok használja.
   > 
   > 
6. **Indítsa el a (kiépíteni) a csomópontok és azok online feladatok futtatása**
   
    Válassza ki a csomópontokat, és használja a **Start** HPC-Fürtkezelőben művelet. Ha kiépítése befejeződött, válassza ki a csomópontokat, és használja a **online állapotba hozás** HPC-Fürtkezelőben művelet. A csomópontok készen feladatok futtatásához.
7. **A fürt feladatok elküldéséhez**
   
   HPC Pack feladat elküldése eszközök segítségével fürtben feladatok futtatásához. Lásd: [Microsoft HPC Pack: feladatkezelés](http://technet.microsoft.com/library/jj899585.aspx).
8. **Állítsa le a (deprovision) a csomópontok**
   
   Amikor elkészült futó feladatot, helyezze offline állapotba a csomópontot, és használja a **leállítása** művelet HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>2. forgatókönyv: Központi telepítése a számítási csomópontok számítási igényű VMs (IaaS)
Ebben az esetben telepít, a HPC Pack átjárócsomópont és számítási fürtcsomópontok virtuális gépeken egy Azure virtuális hálózatban. HPC Pack biztosít több [az Azure virtuális gépeken a központi telepítési beállítások](../../linux/hpcpack-cluster-options.md)automatikus telepítési parancsfájl és Azure gyors üzembe helyezési sablonokat is beleértve. Például, a következő szempontjait és lépéseit ismerteti, hogy használja a [HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md) az automatikus központi telepítése az Azure-ban HPC Pack 2012 R2 fürt.

![Az Azure virtuális gépeken fürt][iaas]

### <a name="steps"></a>Lépések
1. **Hozzon létre egy fürt átjárócsomópontjába, és a számítási csomópont virtuális gépek az ügyfélszámítógépen a HPC Pack IaaS telepítési parancsfájl futtatásával**
   
    Töltse le a HPC Pack IaaS telepítési parancsfájl-csomagot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Az ügyfélszámítógép előkészítése, hozzon létre a parancsfájl konfigurációs fájlban, és futtassa a parancsfájlt, lásd: [HPC-fürt létrehozása a HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md). 
   
    RDMA-kompatibilis telepítendő szempontok a számítási csomópontok, lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) és vegye figyelembe a következőket:
   
   * **Virtuális hálózati**: Adjon meg egy új virtuális hálózat egy régióban, amelyben a használni kívánt H-sorozat, A8 és A9 méretű érhető el. Lásd: [régiónként Azure termékek](https://azure.microsoft.com/regions/services/).

   * **Windows Server operációs rendszer**: RDMA kapcsolat támogatásához adja meg a kompatibilis Windows Server operációs rendszert Windows Server 2012 R2 például a számítási csomópont virtuális gépeket.
   * **A felhőalapú szolgáltatások**: a parancsfájl a klasszikus üzembe helyezési modellt használ, mert a fürt virtuális gépek vannak telepítve, az Azure felhőalapú szolgáltatások segítségével (`ServiceName` beállítások a konfigurációs fájlban). Azt javasoljuk, hogy az átjárócsomópont egy felhőalapú szolgáltatás és a másik felhőalapú szolgáltatást a számítási csomópontok telepítésével. 
   * **Csomópont méretének HEAD**: A jelen esetben fontolja meg a méretet legalább A4 (Extra nagy) az átjárócsomópont.
   * **HpcVmDrivers bővítmény**: A telepítési parancsfájl telepíti az Azure Virtuálisgép-ügynök és a HpcVmDrivers bővítmény automatikusan mérete A8 és A9 számítási csomópontok a Windows Server operációs rendszer központi telepítésekor. HpcVmDrivers telepítse az illesztőprogramot a számítási csomópont virtuális gépek, amelyekhez csatlakozhatnak az RDMA hálózati. Az RDMA-kompatibilis H sorozatú virtuális gépeken manuálisan kell telepítenie a HpcVmDrivers bővítményt. Lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Fürthálózat-konfiguráció**: A telepítési parancsfájl automatikusan beállítja a HPC Pack fürt topológia 5 (a vállalati hálózaton található összes csomópontra). Ez a topológia összes HPC Pack fürtök telepítésének a virtuális gépek szükség. Ne változtassa meg később a fürt hálózati topológiától.
1. **A számítási csomópontok online állapotba kerüljön feladatok futtatása**
   
    Válassza ki a csomópontokat, és használja a **online állapotba hozás** HPC-Fürtkezelőben művelet. A csomópontok készen feladatok futtatásához.
3. **A fürt feladatok elküldéséhez**
   
    A feladatok küldéséhez átjárócsomópontjához csatlakozik, vagy egy helyszíni számítógép beállítása ehhez. További információ: [feladatok küldje el a HPC cluster az Azure-ban](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Offline állapotba a csomópontot, és állítsa le (felszabadítása) őket**
   
    Futó feladatok befejezése után vegye offline állapotba a csomópontot a HPC Cluster Manager. Ezt követően az Azure felügyeleti eszközök segítségével le azokat.

## <a name="run-mpi-applications-on-the-cluster"></a>MPI-alkalmazások futtatása a fürtön
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Példa: Mpipingpong futtatása HPC Pack fürtökön
Az RDMA-kompatibilis példányok HPC Pack telepítését bemutató ellenőrzéséhez futtassa a HPC Pack **mpipingpong** parancsot a fürtön. **mpipingpong** küld csomagokat közötti párosított csomópontok ismételten késést és átviteli mérések számítása és az RDMA-kompatibilis alkalmazások hálózati statisztikája. Ez a példa bemutatja egy tipikus mintája egy MPI feladat fut (ebben az esetben **mpipingpong**) a fürt használatával **mpiexec** parancsot.

Ebben a példában feltételezzük, hogy "az Azure-bA kapacitásnövelés" konfigurációban hozzáadott Azure-csomópontok ([1. forgatókönyv](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Ha az Azure virtuális gépek fürt telepített HPC Pack, meg kell módosítania a határozzon meg egy másik csomópont, és át tudja irányítani a hálózati forgalmat és az RDMA hálózati további környezeti változók beállítására szolgáló parancs szintaxisa.

A fürt futtatásra mpipingpong:

1. A head csomóponton, vagy egy megfelelően konfigurált ügyfélszámítógépen nyisson meg egy parancssort.
2. Azoknak az Azure-kapacitásnövelés központi telepítése négy csomópont közül a csomópontok közötti késés becsléséhez, írja be a következő parancs futtatásával mpipingpong futtatásához egy kis csomagméret és sok a közelítés a feladat elküldése:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    A parancs a feladat elküldött Azonosítóját adja vissza.
   
    Ha telepítette az Azure virtuális gépeken telepített HPC Pack fürtöt, adjon meg egy csomópont csoportot tartalmazó számítási csomópont virtuális gépek telepítve egyetlen felhőszolgáltatás, és módosítsa a **mpiexec** parancsot a következőképpen:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. A feladat befejezése után (ebben az esetben az 1. feladat a feladat kimenetének), a kimenet megtekintéséhez írja be a következő
   
    ```Command
    task view <JobID>.1
    ```
   
    Ha &lt; *JobID* &gt; azonosítója a feladatot, amely el lett küldve.
   
    A kimenet tartalmazza a késés eredmény az alábbihoz hasonló.
   
    ![Ping pong késés][pingpong1]
4. Becsléséhez Azure-kapacitásnövelés csomópontok közötti átviteli sebesség, írja be a következő parancs futtatásával futtatandó feladat elküldése **mpipingpong** a csomagok nagy méretű és néhány ismétlési:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    A parancs a feladat elküldött Azonosítóját adja vissza.
   
    Egy Azure virtuális gépeken telepített HPC Pack fürt módosítsa a parancs, a 2.
5. A feladat befejezése után (ebben az esetben az 1. feladat a feladat kimenetének), a kimenet megtekintéséhez írja be a következőt:
   
    ```Command
    task view <JobID>.1
    ```
   
   A parancs kimenete átviteli eredmény az alábbihoz hasonló.
   
   ![Ping pong átviteli sebesség][pingpong2]

### <a name="mpi-application-considerations"></a>MPI alkalmazás kapcsolatos szempontok
Az alábbiakban szempontok az Azure-ban HPC Pack MPI-alkalmazások futtatására. Néhány csak Azure-csomópontok (szerepkör feldolgozópéldányok "kapacitásnövelés Azure" konfigurációban hozzáadott) példányainak vonatkoznak.

* Szerepkör feldolgozópéldányok felhőszolgáltatásban vannak rendszeresen újra kiépíteni minden külön értesítés nélkül az Azure-ban (például karbantartási, és a példány sikertelen). Egy példány van újra kiépíteni, egy MPI-feladat futtatása, ha a példány elveszíti az adatokat, és a állapotba tér vissza, ha először telepítették, amelyek esetén a MPI feladat sikertelen lesz. A további csomópontokat, amelyekkel az egyetlen MPI feladat, és minél hosszabb a feladat lefutott, annál valószínűleg, amely egy példányt újra kiépíteni, a feladat futása közben. Figyelembe venni ezt a központi telepítésben lévő egyetlen csomópont fájlkiszolgálóként megadásakor.
* MPI-feladatok futtatása az Azure-ban, nincs RDMA-kompatibilis példányokat használja. HPC Pack által támogatott bármely példányméretének is használhatja. Azonban az RDMA-kompatibilis példányok ajánlottak futó viszonylag nagy méretű MPI-feladatok, amelyek a késés és a hálózat kapcsolja össze a csomópontok a sávszélesség-és nagybetűket. Ha más méretek és sávszélesség-késésérzékeny MPI-feladatok futtatása, ajánlott futó kis feladatok, amelyben fut egy feladat csak néhány csomópont.
* Azure-példányokon központilag telepített alkalmazások az alkalmazáshoz kapcsolódó licencfeltételek vonatkoznak. Ellenőrizze a licencelési vagy más korlátozásokat a felhőben futó összes kereskedelmi alkalmazás gyártójával. Nem minden szállító kínál használatalapú licencet.
* Azure-példányokon hozzáférés a helyszíni csomópontok, megosztásokat és licenckiszolgálókat további beállítása szükséges. Ahhoz, hogy az Azure-csomópontok egy helyszíni licenckiszolgáló eléréséhez, például a pont-pont Azure virtuális hálózat konfigurálása is.
* MPI-alkalmazások futtatásához Azure-példányokon minden MPI alkalmazás regisztrálása a Windows tűzfal-példányokon futtatásával a **hpcfwutil** parancsot. Ez lehetővé teszi a MPI kommunikáció egy portot a tűzfalon által dinamikusan hozzárendelt kerül sor.
  
  > [!NOTE]
  > Az Azure-környezetekhez kapacitásnövelés beállíthatja a tűzfal kivétel parancsot minden új Azure csomópontokat a fürthöz hozzáadott automatikusan futnak. Futtatása után a **hpcfwutil** parancsot, és győződjön meg arról, hogy az alkalmazás akkor működik, a parancs hozzáadását az Azure-csomópontok indítási parancsfájlt. További információkért lásd: [indítási parancsfájl használata Azure csomópontok](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack CCP_MPI_NETMASK fürt környezeti változó használatával MPI kommunikációhoz elfogadható címtartományt adjon meg. A HPC Pack 2012 R2-től kezdődően a CCP_MPI_NETMASK fürt környezeti változó csak MPI kommunikációt érinti a tartományhoz csatlakoztatott számítási fürtcsomópontok közötti (helyi vagy az Azure virtuális gépeken). A változót hozzá Azure-alapú konfigurációs egy kapacitásnövelés csomópontok figyelmen kívül hagyja.
* MPI-feladatok futtatása nem Azure-példányokon különböző felhőszolgáltatások (például a nagy sebességű másik csomópont sablonok vagy Azure virtuális gép számítási csomópontok több felhőszolgáltatásra üzembe helyezett Azure használatával végzett központi telepítésekhez) üzembe helyezett. Ha több Azure csomópont-telepítés másik csomópont sablonok kezdődnek, csak egy Azure-csomópontok készletét a MPI feladatot kell futtatnia.
* Azure-csomópontok hozzáadása a fürthöz, és online állapotba kerüljön, a HPC Job Feladatütemező szolgáltatás azonnal megpróbálja elindítson feladatokat a csomóponton. Ha csak egy részét a számítási feladatok Azure futtathatja, győződjön meg arról, frissítés, vagy adja meg, milyen típusú futtatható Azure-sablonok feladat létrehozása. Például győződjön meg arról, hogy egy feladat sablon az elküldött csak akkor futnak a feladatok Azure csomópontján, hogy a csomópont csoportok tulajdonság hozzáadása a feladat sablon pedig a AzureNodes a szükséges érték. Az Azure-csomópontok egyéni csoportok létrehozása, az Add-HpcGroup HPC PowerShell parancsmaggal.

## <a name="next-steps"></a>További lépések
* HPC Pack használata helyett az Azure Batch szolgáltatás MPI alkalmazások futtatásához a számítási csomópontok az Azure-ban kezelt készletek fejlesztést. Lásd: [többpéldányos feladatok segítségével futtassa a Message Passing Interface (MPI) alkalmazások az Azure Batch](../../../batch/batch-mpi.md).
* Ha szeretné futtatni a Linux MPI alkalmazások az Azure RDMA hálózati hozzáférés, lásd: [MPI alkalmazások futtatásához hozzon létre egy Linux RDMA fürt](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
