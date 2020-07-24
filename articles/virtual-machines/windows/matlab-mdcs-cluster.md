---
title: MATLAB-fürtök a virtuális gépeken
description: A Microsoft Azure Virtual Machines használatával MATLAB Distributed Computing Server-fürtöket hozhat létre a nagy számítási igényű párhuzamos MATLAB-munkaterhelések futtatásához
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a3f3dbd74ef74f091ca923f8c09680c6913ac300
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074238"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>MATLAB Distributed Computing Server-fürtök létrehozása Azure-beli virtuális gépeken
Microsoft Azure virtuális gépek használatával hozzon létre egy vagy több MATLAB Distributed Computing Server-fürtöt a nagy számítási igényű párhuzamos MATLAB-munkaterhelések futtatásához. Telepítse a MATLAB Distributed Computing Server szoftvert egy virtuális gépre alaprendszerképként való használatra, és használjon egy Azure-beli rövid útmutató sablont vagy Azure PowerShell szkriptet (amely a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)érhető el) a fürt üzembe helyezéséhez és kezeléséhez. Az üzembe helyezés után kapcsolódjon a fürthöz a számítási feladatok futtatásához.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Tudnivalók a MATLAB és a MATLAB Distributed Computing Serverről
A [MATLAB](https://www.mathworks.com/products/matlab/) platform mérnöki és tudományos problémák megoldására van optimalizálva. A nagy léptékű szimulációkkal és adatfeldolgozási feladatokkal rendelkező MATLAB-felhasználók MathWorks párhuzamos számítástechnikai termékekkel növelhetik a számítási igényű munkaterheléseket a számítási fürtök és a Grid-szolgáltatások kihasználásával. A [Parallel Computing Toolbox](https://www.mathworks.com/products/parallel-computing/) lehetővé teszi, hogy a MATLAB-felhasználók integrálással az alkalmazásokat, és kihasználhassanak több magos processzort, GPU-t és számítási fürtöt. A [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) lehetővé teszi, hogy a MATLAB-felhasználók számos számítógépet használjanak egy számítási fürtben.

Az Azure Virtual Machines használatával olyan MATLAB elosztott számítástechnikai kiszolgáló fürtöket hozhat létre, amelyek rendelkeznek az összes elérhető mechanizmussal a párhuzamos munka helyszíni fürtökként való elküldéséhez, például interaktív feladatok, kötegelt feladatok, független feladatok, valamint a feladatok továbbítása. Ha az Azure-t a MATLAB platformmal együtt használja, számos előnnyel jár, mint a hagyományos helyszíni hardverek használata: a virtuálisgép-méretek széles köre, a fürtök igény szerinti létrehozása, így csak a felhasznált számítási erőforrásokért kell fizetnie, és a modelleket a méretekben is tesztelheti.  

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** – szükség van egy Windows-alapú ügyfélszámítógépre, amely az üzembe helyezés után kommunikál az Azure-val és a MATLAB Distributed Computing Server-fürttel.
* **Azure PowerShell** – megtudhatja, [hogyan telepítheti és konfigurálhatja az Azure PowerShell](/powershell/azure/) az ügyfélszámítógépen való telepítéséhez.
* **Azure-előfizetés** – ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) . Nagyobb fürtök esetén vegye figyelembe az utólagos elszámolású előfizetést vagy más vásárlási lehetőségeket.
* **vCPU kvóta** – előfordulhat, hogy a vCPU-kvótát a nagyméretű fürt vagy több MATLAB Distributed Computing Server-fürt üzembe helyezéséhez kell emelnie. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **MATLAB, Parallel Computing Toolbox és MATLAB Distributed Computing Server-licencek** – a parancsfájlok feltételezik, hogy a [MathWorks üzemeltetett licenckezelő](https://www.mathworks.com/help/install/license-management.html) minden licenchez használatos.  
* **MATLAB Distributed Computing Server szoftver** – a rendszer telepíti a virtuális gépre, amelyet a fürt virtuális gépei alapszintű virtuálisgép-rendszerképként fog használni.

## <a name="high-level-steps"></a>Magas szintű lépések
Ha Azure-beli virtuális gépeket szeretne használni a MATLAB Distributed Computing Server-fürtökhöz, a következő magas szintű lépésekre van szükség. A részletes utasítások a gyors üzembe helyezési sablont és a [githubon](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)lévő szkripteket kísérő dokumentációban találhatók.

1. **Alap VM-rendszerkép létrehozása**  

   * Töltse le és telepítse a MATLAB Distributed Computing Server szoftvert erre a virtuális gépre.

     > [!NOTE]
     > Ez a folyamat néhány órát is igénybe vehet, de csak egyszer kell megtennie a MATLAB minden egyes verziójára.   
     >
     >
2. **Hozzon létre egy vagy több fürtöt**  

   * Használja a megadott PowerShell-parancsfájlt, vagy a gyors üzembe helyezési sablonnal hozzon létre egy fürtöt az alap VM-rendszerképből.   
   * A fürtöket a megadott PowerShell-parancsfájl segítségével kezelheti, amely lehetővé teszi fürtök listázását, szüneteltetését, folytatását és törlését.

## <a name="cluster-configurations"></a>Fürtkonfigurációk
Jelenleg a fürt létrehozására szolgáló parancsfájl és sablon lehetővé teszi, hogy egyetlen MATLAB elosztott számítástechnikai kiszolgálói topológiát hozzon létre. Ha szeretné, hozzon létre egy vagy több további fürtöt, és minden egyes fürt eltérő virtuálisgép-méretekkel rendelkezik, és így tovább.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-ügyfél és-fürt az Azure-ban
A MATLAB-ügyfél csomópontja, a MATLAB Feladatütemező csomópontja és a MATLAB Distributed Computing Server "Worker" csomópontok mind Azure-beli virtuális gépekként vannak konfigurálva egy virtuális hálózaton, ahogy az a következő ábrán látható.


* A fürt használatához kapcsolódjon Távoli asztal az ügyfél-csomóponthoz. Az ügyfél csomópontja futtatja a MATLAB-ügyfelet.
* Az ügyfél csomópontja olyan fájlmegosztást tartalmaz, amely az összes feldolgozóval elérhető.
* A MathWorks üzemeltetett licenckezelő szolgáltatás az összes MATLAB szoftver licencének ellenőrzéséhez használatos.
* Alapértelmezés szerint a rendszer a munkavégző virtuális gépeken egy vCPU elosztott számítástechnikai kiszolgáló-feldolgozót hoz létre, de bármilyen számot megadhat.

## <a name="use-an-azure-based-cluster"></a>Azure-alapú fürt használata
Más típusú MATLAB Distributed Computing Server-fürtökhöz hasonlóan a MATLAB-ügyfélen (az ügyfél virtuális gépen) a fürtszolgáltatás-kezelőt kell használnia, hogy létrehozzon egy MATLAB Feladatütemező-fürtöt.

![Fürterőforrás-kezelő](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>További lépések
* A MATLAB Distributed Computing Server-fürtök az Azure-ban való üzembe helyezésével és felügyeletével kapcsolatos részletes utasításokért tekintse meg a sablonokat és parancsfájlokat tartalmazó [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) -tárházat.
* Nyissa meg a [MathWorks webhelyét](https://www.mathworks.com/) a MATLAB és a MATLAB Distributed Computing Server részletes dokumentációjában.
