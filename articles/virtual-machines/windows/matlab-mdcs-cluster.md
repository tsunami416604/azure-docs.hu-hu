---
title: MATLAB-fürtök virtuális gépeken
description: A Microsoft Azure virtuális gépeivel MATLAB elosztott számítástechnikai kiszolgálófürtöket hozhat létre a nagy számítási igényű párhuzamos MATLAB-számítási feladatok futtatásához
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038145"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>MATLAB elosztott számítástechnikai kiszolgálófürtjeinek létrehozása az Azure virtuális gépein
A Microsoft Azure virtuális gépeivel egy vagy több MATLAB elosztott számítástechnikai kiszolgálófürtöt hozhat létre a nagy számítási igényű párhuzamos MATLAB-számítási feladatok futtatásához. Telepítse a MATLAB distributed Computing Server szoftvert egy virtuális gépre alaplemezképként való használatra, és használjon Egy Azure gyorsindítási sablont vagy egy [(a GitHubon](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)elérhető) Azure PowerShell-parancsfájlt a fürt üzembe helyezéséhez és kezeléséhez. Üzembe helyezés után csatlakozzon a fürthöz a számítási feladatok futtatásához.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>A MATLAB és a MATLAB Distributed Computing Server szolgáltatásról
A [MATLAB](https://www.mathworks.com/products/matlab/) platform mérnöki és tudományos problémák megoldására van optimalizálva. A nagyléptékű szimulációkkal és adatfeldolgozási feladatokkal rendelkező MATLAB-felhasználók a MathWorks párhuzamos számítástechnikai termékeit használhatják a számítási igényű munkaterhelések felgyorsítására a számítási fürtök és a hálózati szolgáltatások kihasználásával. [A Párhuzamos számításkezelő eszközkészlet](https://www.mathworks.com/products/parallel-computing/) lehetővé teszi a MATLAB-felhasználók számára, hogy párhuzamosítsák az alkalmazásokat, és kihasználják a többmagos processzorok, GPU-k és számítási fürtök előnyeit. [A MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) lehetővé teszi a MATLAB-felhasználók számára, hogy számos számítógépet használjanak egy számítási fürtben.

Az Azure virtuális gépeinek használatával matlab elosztott számítástechnikai kiszolgálófürtöket hozhat létre, amelyek ugyanazokat a mechanizmusokat biztosítják a párhuzamos munka elküldéséhez, mint a helyszíni fürtök, például interaktív feladatok, kötegelt feladatok, független feladatok és kommunikáció Feladatok. Az Azure-nak a MATLAB platformmal való együttes használata számos előnnyel jár a hagyományos helyszíni hardverek kiépítéséhez és használatához képest: a virtuális gépméretek széles választéka, a fürtök igény szerinti létrehozása, így csak a használt számítási erőforrásokért kell fizetnie, és a képes tesztelni modellek et méretarányosan.  

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** – Az Azure-ral és a MATLAB distributed Computing Server fürttel való kommunikációhoz windows-alapú ügyfélszámítógépre van szükség a telepítés után.
* **Azure PowerShell** – Tekintse meg [az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview) az ügyfélszámítógépen való telepítéséhez.
* **Azure-előfizetés** – Ha nem rendelkezik előfizetéssel, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/) Nagyobb fürtök esetén fontolja meg a felosztó-kiosztó előfizetést vagy más vásárlási lehetőségeket.
* **vCPU-kvóta** – Előfordulhat, hogy növelnie kell a vCPU-kvótát egy nagy fürt vagy egynél több MATLAB elosztott számítástechnikai kiszolgáló fürt telepítéséhez. A kvóta növeléséhez [nyisson meg egy online ügyfélszolgálati kérelmet](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **MATLAB, Parallel Computing Toolbox és MATLAB Distributed Computing Server licencek** – A parancsfájlok feltételezik, hogy a [MathWorks hosted license manager](https://www.mathworks.com/help/install/license-management.html) minden licenchez használható.  
* **MATLAB Distributed Computing Server szoftver** – a fürt virtuális gépei alap virtuálisgép-lemezképeként használt virtuális gépre lesz telepítve.

## <a name="high-level-steps"></a>Magas szintű lépések
Az Azure virtuális gépek használatához a MATLAB elosztott számítástechnikai kiszolgáló fürtjeihez a következő magas szintű lépések szükségesek. A részletes utasítások a [GitHubon](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)található rövid útmutató sablont és parancsfájlokat kísérő dokumentációban találhatók.

1. **Alap virtuálisgép-lemezkép létrehozása**  

   * Töltse le és telepítse a MATLAB Distributed Computing Server szoftvert erre a virtuális gépre.

     > [!NOTE]
     > Ez a folyamat eltarthat egy pár órát, de csak egyszer kell csinálni minden egyes változata MATLAB használata.   
     >
     >
2. **Egy vagy több fürt létrehozása**  

   * Használja a mellékelt PowerShell-parancsfájlt, vagy használja a gyorsindítási sablont egy fürt létrehozásához az alap virtuálisgép-lemezképből.   
   * A fürtök kezelése a megadott PowerShell-parancsfájl használatával, amely lehetővé teszi a fürtök listáját, szüneteltetése, folytatása és törlése.

## <a name="cluster-configurations"></a>Fürtkonfigurációk
Jelenleg a fürtlétrehozási parancsfájl és sablon lehetővé teszi egyetlen MATLAB elosztott számítási kiszolgáló topológiájának létrehozását. Ha szeretné, hozzon létre egy vagy több további fürtöt, és minden fürt különböző számú dolgozó virtuális géptel rendelkezik, különböző virtuálisgép-méreteket használva stb.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-ügyfél és fürt az Azure-ban
A MATLAB-ügyfélcsomópont, a MATLAB feladatütemező csomópontja és a MATLAB elosztott számítási kiszolgáló "feldolgozó" csomópontjai mind Azure virtuális gépként vannak konfigurálva egy virtuális hálózatban, az alábbi ábrán látható módon.


* A fürt használatához csatlakozzon a Távoli asztal segítségével az ügyfélcsomóponthoz. Az ügyfélcsomópont a MATLAB-ügyfelet futtatja.
* Az ügyfélcsomópont rendelkezik egy fájlmegosztással, amelyhez minden dolgozó hozzáférhet.
* MathWorks Hosted License Manager használják a licenc ellenőrzések minden MATLAB szoftver.
* Alapértelmezés szerint egy MATLAB elosztott számítástechnikai kiszolgáló dolgozója vCPU-nként jön létre a dolgozó virtuális gépeken, de bármilyen számot megadhat.

## <a name="use-an-azure-based-cluster"></a>Azure-alapú fürt használata
A MATLAB elosztott számítástechnikai kiszolgáló fürtjeihez ugyanúgy, mint a MATLAB-ügyfél fürtprofilkezelője a MATLAB-ügyfélben (az ügyfél virtuális gépén) a MATLAB feladatütemező fürtprofil létrehozásához.

![Fürtprofil-kezelő](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>További lépések
* A MATLAB distributed Computing Server fürtök Azure-beli telepítéséről és kezeléséről részletes útmutatást a sablonokat és parancsfájlokat tartalmazó [GitHub-tárházban](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) talál.
* A MATLAB és a MATLAB Distributed Computing Server részletes dokumentációját a [MathWorks webhelyen](https://www.mathworks.com/) találja.
