---
title: A virtuális gépek fürtök MATLAB |} Microsoft Docs
description: MATLAB elosztott számítási fürt a számítási igényű párhuzamos MATLAB alkalmazásokat és szolgáltatásokat futtathatnak létrehozásához használja a Microsoft Azure virtuális gépek
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 695833fb12c0c7a130e98fe9b3bdfa502672ab29
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Azure virtuális gépeken MATLAB elosztott számítási fürtök létrehozása
A Microsoft Azure virtuális gépek segítségével hozzon létre egy vagy több MATLAB elosztott számítási fürtök a számítási igényű párhuzamos MATLAB munkaterhelések futtatásához. A MATLAB elosztott számítástechnikai kiszolgáló szoftver telepítése egy virtuális Gépre alapjául szolgáló lemezképhez és egy Azure gyors üzembe helyezés-sablon vagy az Azure PowerShell-parancsfájl használata (érhető el a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) telepítése és a fürt kezelése. A központi telepítést követően csatlakozzon a fürthöz, a munkaterhelések futtatásához.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>MATLAB és MATLAB elosztott számítástechnikai kiszolgáló
A [MATLAB](http://www.mathworks.com/products/matlab/) platform mérnöki és tudományos problémák megoldásához van optimalizálva. A felügyeleti teendők központjaként szimulációja és adatfeldolgozási feladatok MATLAB felhasználók használhatják a termékek számítástechnikai MathWorks párhuzamos rács szolgáltatások és számítási fürtök kihasználásával a számítási-igényes munkaterhelések felgyorsítása érdekében. [A számítástechnikai eszközkészlet párhuzamos](http://www.mathworks.com/products/parallel-computing/) MATLAB felhasználók alkalmazások parallelize és előnyeit több magos processzorral, Feldolgozóegységekkel, és számítási fürtök. [MATLAB elosztott számítástechnikai kiszolgáló](http://www.mathworks.com/products/distriben/) lehetővé teszi a MATLAB felhasználók sok számítógép egy számítási fürt használatára.

Az Azure virtuális gépek használata esetén, amelyek ugyanazokat párhuzamos munkát küldeni a helyszíni fürtökként, például az interaktív feladatok, a kötegelt feladatok, a független feladatokat és a kommunikáció mechanizmusok MATLAB elosztott számítástechnikai kiszolgáló tárolófürtöket hozhat létre feladatok. Kiépítés képest számos előnyt Azure a MATLAB platform együtt történő rendelkezik, és a hagyományos helyszíni hardver: virtuális gép számos méretének, fürtök igény, csak fizetnie a számítási erőforrásokat használ, létrehozását és a lehetővé teszi léptékű modellek tesztelése.  

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** -Windows-alapú ügyfélszámítógép a központi telepítést követően Azure és a MATLAB elosztott számítástechnikai kiszolgálófürt kommunikálni lesz szüksége.
* **Az Azure PowerShell** -lásd [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) telepíteni az ügyfélszámítógépen.
* **Azure-előfizetés** – Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) néhány percig. Nagyobb fürtök esetén fontolja meg a használatalapú előfizetés vagy egyéb beszerzési lehetőségek.
* **Vcpu kvóta** -szükség lehet egy nagy méretű fürt, vagy egynél több MATLAB elosztott számítástechnikai Server-fürt központi telepítése a vCPU kvóta növeléséhez. A kvóta növeléséhez [nyissa meg az online támogatás ügyfélkérés](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **MATLAB, párhuzamos számítástechnikai eszközkészlet és MATLAB elosztott számítástechnikai kiszolgáló licencek** -a parancsfájlok feltételeztük, hogy a [MathWorks üzemeltetett Licenckezelő](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) használt összes licencet.  
* **MATLAB elosztott számítástechnikai kiszolgáló szoftver** -, amely a fürt virtuális gépek a virtuális gép alapjául szolgáló lemezképhez lesz lesz telepítve.

## <a name="high-level-steps"></a>Magas szintű lépései
A MATLAB elosztott számítási fürtök számára az Azure virtuális gépek használatához a következő magas szintű lépések szükségesek. Részletes utasításokat a következő gyorsindítási sablonon és parancsfájlok kísérő a dokumentációban szerepelnek [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Alapszintű VM-lemezkép létrehozása**  

   * Töltse le és telepítse a virtuális gép MATLAB elosztott számítástechnikai kiszolgáló szoftvereket.

     > [!NOTE]
     > A folyamat eltarthat néhány óra múlva, de csak akkor kell erre, miután minden MATLAB verzióját használja.   
     >
     >
2. **Egy vagy több fürtök létrehozása**  

   * A megadott PowerShell-parancsfájl vagy a fürt létrehozása az alapszintű Virtuálisgép-lemezkép a gyors üzembe helyezés sablon használatával.   
   * A megadott PowerShell parancsfájlt, amely lehetővé teszi, hogy a listában, szüneteltetése, folytatása a fürtök kezeléséhez, és a fürtök törlése.

## <a name="cluster-configurations"></a>Fürtkonfigurációk
Jelenleg a fürt létrehozási parancsprogrammal és sablon lehetővé teszi olyan topológiák létrehozását is egyetlen MATLAB elosztott számítástechnikai kiszolgáló. Ha azt szeretné, hozzon létre egy vagy több további fürtök, a virtuális gépek, a másik Virtuálisgép-méretek használatával worker másik számmal rendelkező fürtökön, és így tovább.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB ügyfél és a fürt az Azure-ban
A MATLAB ügyfél-csomópontnak MATLAB Feladatütemező csomópont és MATLAB elosztott számítástechnikai kiszolgáló "munkavégző" csomópont összes beállításuk Azure virtuális gépek virtuális hálózatban, az alábbi ábrán látható módon.


* A fürt használatához az ügyfél-csomópontnak által a távoli asztal csatlakozni. Az ügyfél-csomópontnak az MATLAB ügyfél futtatja.
* Az ügyfél-csomópontnak rendelkezik egy fájlmegosztást, amely minden dolgozók is elérhetők.
* A licenc ellenőrzi a összes MATLAB szoftver MathWorks üzemeltetett Licenckezelő használható.
* Alapértelmezés szerint egy MATLAB elosztott számítástechnikai kiszolgáló munkavégző / vCPU jön létre, a virtuális gépek dolgozó, de több is megadhat.

## <a name="use-an-azure-based-cluster"></a>Azure-alapú fürt használatára
Mint MATLAB elosztott számítástechnikai kiszolgálófürtök, más típusú kell használnia a profil-kezelőt a MATLAB ügyfél (a virtuális gép ügyfélen) MATLAB Feladatütemező fürt profil létrehozásához.

![Profil kezelő](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>További lépések
* Telepíthetnek és kezelhetnek az Azure-ban MATLAB elosztott számítástechnikai kiszolgáló fürtök részletes utasításokért lásd: a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) a sablonok és a parancsfájlokat tartalmazó tárházba.
* Lépjen a [MathWorks hely](http://www.mathworks.com/) MATLAB és MATLAB elosztott számítástechnikai kiszolgálóra vonatkozó részletes dokumentációt.
