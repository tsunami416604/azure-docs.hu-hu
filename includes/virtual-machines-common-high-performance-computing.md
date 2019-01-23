---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 01/15/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7a136f34a7dd9fada23a4225b60223220c92c665
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440473"
---
Szervezet rendelkezik a nagyméretű számítástechnikai igényeinek. Ezek a Big Compute számítási feladatok közé tartozik a mérnöki tervezés és elemzés, pénzügyi kockázatszámítások, képrenderelés, összetett modellezés, Monte Carlo-szimulációk és több. 

Az Azure-felhő használatával hatékonyan futtatni a nagy számítási igényű Linux és Windows számítási feladatok, a hagyományos HPC-szimulációkat párhuzamos kötegelt feladatok. Futtassa a HPC és batch számítási feladatait az Azure-infrastruktúrán, az Ön által választott számítási szolgáltatások, a rács kezelők, a Marketplace-megoldásokat és a gyártó által szolgáltatott (SaaS) alkalmazások. Az Azure biztosít rugalmas megoldásokat és a virtuális gépek vagy a magok ezreire és majd vertikális leskálázás, amikor kevesebb erőforrásra van szüksége. 

## <a name="solution-options"></a>Megoldás beállításai

* **Saját munka megoldások**
    * Állítsa be a saját fürt környezetet az Azure-beli virtuális gépek vagy [a virtual machine scale sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Átemelés és shift egy helyi fürtöt, vagy további kapacitás az Azure-ban új fürt üzembe helyezése. 
    * Vezető üzembe helyezése Azure Resource Manager-sablonok használatával [munkaterhelés kezelők](#workload-managers), infrastruktúra, és [alkalmazások](#hpc-applications). 
    * Válasszon [HPC és GPU VM méretek](#hpc-and-gpu-sizes) , amelyek tartalmaznak speciális hardver- és hálózati kapcsolatok MPI vagy GPU számítási feladatokhoz. 
    * Adjon hozzá [nagy teljesítményű tárolási](#hpc-storage) I/O-igényes számítási feladatokhoz.
* **A hibrid megoldások**
    * Az Azure-infrastruktúrához ("adatlöket") csúcs számítási feladatok kiszervezéséhez a helyszíni megoldás bővítése
    * A meglévő használata a felhőalapú számítási igény szerinti [munkaterhelés manager](#workload-manager).
    * Kihasználhatja [HPC és GPU VM méretek](#hpc-and-gpu-sizes) MPI vagy GPU számítási feladatokhoz.
* **Big Compute-megoldások szolgáltatásként**
    * Fejleszthet egyéni Big Compute-megoldások és -munkafolyamatok [Azure CycleCloud](#azure-cyclecloud), [Azure Batch](#azure-batch), és a kapcsolódó [Azure-szolgáltatások](#related-azure-services).
    * A szállítókkal, mint az Azure-kompatibilis termékgondozó csoportja és a szimuláció megoldások futtatása [Altair](http://www.altair.com/), [átméretezése](https://www.rescale.com/azure/), és [Cycle Computing](https://cyclecomputing.com/) (mostantól [csatlakozott A Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
    * Használja a [Cray szuperszámítógépet](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure) Azure-ban üzemeltetett szolgáltatásként.
* **Marketplace-megoldásokat**
    * A méretezési [HPC-alkalmazások](#hpc-applications) és [megoldások](#marketplace-solutions) érhető el a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/). 
    
A következő szakaszok további információt a támogató technológiákat és útmutatást mutató hivatkozásokat.

## <a name="marketplace-solutions"></a>Piactér-megoldások

Látogasson el a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/) a Linux és Windows VM-lemezképek és megoldások kialakítása HPC. Példák erre vonatkozóan:

* [CentOS-alapú HPC RogueWave](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview)
* [SUSE Linux Enterprise Server HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO rács kiszolgáló motor](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Az Azure Data Science Windows és Linux rendszerű virtuális gép](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

## <a name="hpc-applications"></a>HPC-alkalmazások

Egyéni vagy kereskedelmi HPC-alkalmazásokat futtathat az Azure-ban. Ebben a szakaszban néhány példa a további virtuális gépek hatékony méretezés, vagy a számítási magok vannak benchmarked. Látogasson el a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace) telepíthető kész megoldásokhoz.

> [!NOTE]
> Ellenőrizze a licencelési vagy egyéb korlátozásokról a felhőben futó minden kereskedelmi alkalmazás szállítójához. Nem minden szállító kínál használatalapú licencet. A megoldás a felhőben egy licenckiszolgálóra kell előfordulhat, hogy, vagy egy helyszíni licenckiszolgáló csatlakozni.

### <a name="engineering-applications"></a>Mérnöki csapathoz alkalmazások

* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB elosztott számítási kiszolgáló](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a>Grafikus és megjelenítési szoftverek

* [Megjelenítési alkalmazások](../articles/batch/batch-rendering-service.md) Azure Batch, beleértve az Autodesk Maya, 3ds Max és Arnold, a Chaos Group V-Ray és a Blender

### <a name="ai-and-deep-learning"></a>Mesterséges Intelligencia és a deep learning

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Mélytanulási virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Mély tanulás Batch hajógyárnak receptek](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC és GPU VM-méretek

Az Azure számos méretei [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuális gépekhez, beleértve a nagy számítási igényű számítási feladatokhoz készült méretek. Például H16r, H16mr virtuális gépek és hálózathoz is csatlakoztathatja a nagy átviteli sebességű háttér-rdma-t. A felhő hálózati is megvannak szorosan összefüggő párhuzamos alkalmazások teljesítményének javítása [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) vagy az Intel MPI-t. 

Az N sorozatú virtuális gépek szolgáltatás NVIDIA gpu-k többek között a tanulás a mesterséges intelligencia (AI) és a vizualizációs nagy számítási igényű, vagy a magas grafikai igényű alkalmazásokhoz tervezve. 

További információ:

* Nagy teljesítményű számítási méretei [Linux](../articles/virtual-machines/linux/sizes-hpc.md) és [Windows](../articles/virtual-machines/windows/sizes-hpc.md) virtuális gépek 
* GPU-kompatibilis méretei [Linux](../articles/virtual-machines/linux/sizes-gpu.md) és [Windows](../articles/virtual-machines/windows/sizes-gpu.md) virtuális gépek 

## <a name="azure-cyclecloud"></a>Azure CycleCloud

Hatékonyan felügyelheti gyakori számítási feladatok egyszerű létrehozása és optimalizálás a HPC-fürtök az Azure virtuális gépek [Azure CycleCloud](https://docs.microsoft.com/azure/cyclecloud/overview).

Az alábbiak végrehajtásának módját ismerheti meg:

* [Telepítése és beállítása a CycleCloud Resource Manager-sablonnal](https://docs.microsoft.com/azure/cyclecloud/quickstart-install-cyclecloud)

* [CycleCloud manuális beállítása](https://docs.microsoft.com/azure/cyclecloud/installation)

## <a name="azure-batch"></a>Azure Batch

[A batch](../articles/batch/batch-technical-overview.md) van egy platform szolgáltatást futtató nagy méretű párhuzamos és nagy teljesítményű számítástechnika (HPC) alkalmazásokat futtathat hatékonyan a felhőben. Az Azure Batch ütemezi számításigényes munkák futtatását a virtuális gépek felügyelt készletének, és képes automatikusan méretezni a számítási erőforrásokat a feladatok igényeinek kielégítése érdekében. 

SaaS-szolgáltatók vagy fejlesztők számára készült HPC-alkalmazások vagy a tárolók számítási feladatainak integrálása az Azure-ral, adatok készíthetők elő az Azure-ban, a Batch SDK-k és eszközök használatával és feladat-végrehajtási folyamatok alakíthatók ki. 

Az alábbiak végrehajtásának módját ismerheti meg:

* [Bevezetés a Batch-Csel történő fejlesztésbe](../articles/batch/quick-run-dotnet.md)
* [Az Azure Batch-kódminta használata](https://github.com/Azure/azure-batch-samples)
* [A Batch alacsony prioritású virtuális gépek használata](../articles/batch/batch-low-pri-vms.md)
* [Tárolóalapú HPC számítási feladatok futtatása a Batch hajógyárnak](https://github.com/Azure/batch-shipyard)
* [Párhuzamos R számítási feladatok futtatása a Batch](https://github.com/Azure/doAzureParallel)
* [Igény szerinti Spark-feladatokat futtathat Batch](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>Munkaterhelés-kezelők

Az alábbi parancsok példák, amely képes futni az Azure infrastruktúra-fürt és a számítási feladatok kezelők. Önálló fürtök létrehozása az Azure virtuális gépek vagy adatlöketek Azure virtuális gépekre a helyszíni fürtök. 
* [Alces repülési számítási](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Világos kezelő](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM spektrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) 

## <a name="hpc-storage"></a>HPC-tároló

Nagy méretű Batch- és HPC számítási feladatok, amelyek túllépik a hagyományos felhőalapú fájlrendszerek képességeit igényeknek adatok tárolási és hozzáférési rendelkeznek. 

További információ:

* [Az Azure-ban Avere vFXT](../articles/avere-vfxt/avere-vfxt-overview.md) 
* [Az Azure-ban virtuális fájlrendszerek párhuzamos](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/) beleértve [Lustre](http://lustre.org/) és [BeeGFS](http://www.beegfs.com/content/).

## <a name="related-azure-services"></a>Kapcsolódó Azure-szolgáltatások

Az Azure virtual machines, a virtuálisgép-méretezési csoportok, a Batch és a kapcsolódó számítási szolgáltatások rendszer alapját a legtöbb Azure HPC-megoldások. Azonban a megoldás kihasználhatja számos kapcsolódó Azure-szolgáltatások. Ez egy részleges lista:

### <a name="storage"></a>Storage

* [BLOB, table és queue storage](../articles/storage/storage-introduction.md)
* [A File storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Adatok és analitika

* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Storage Gen1](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>MI és gépi tanulás

* [Machine Learning-szolgáltatás](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>Hálózat

* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Containers

* [Az Azure Kubernetes Service (AKS)](../articles/aks/intro-kubernetes.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)
* [Tárolópéldányok](../articles/container-instances/container-instances-overview.md)

## <a name="customer-stories"></a>Ügyfelek történetei

Példák az ügyfelek Azure HPC-megoldások az üzleti problémákat kell megoldani:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertsonnak](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://customers.microsoft.com/story/metlife-insurance-azure-cloud-services-windows-server-analytics-platform-server-en)
* [A Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [NeuroInitiative](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
* [Schlumberger](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="next-steps"></a>További lépések

* További információ az Azure-megoldások [nagy teljesítményű számítási](https://azure.microsoft.com/solutions/high-performance-computing/), [renderelési](https://azure.microsoft.com/solutions/big-compute/rendering/), [banki és tőkepiacok](https://finance.azure.com/), és [genomics](https://enterprise.microsoft.com/industries/health/genomics/).

* Tudjon meg többet [Big Compute-megoldások architektúráit](https://azure.microsoft.com/solutions/architecture/?solution=high-performance-computing) az Azure-ban.
