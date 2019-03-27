---
title: MATLAB fürt virtuális gépein |} A Microsoft Docs
description: A számításigényes párhuzamos MATLAB számítási feladatok futtatásához MATLAB elosztott számítástechnikai Server-fürtök létrehozása a Microsoft Azure virtual machines használatához
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
ms.openlocfilehash: a9de0f0021d92c59c44e85f0487cc9a08b6a3099
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497362"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>MATLAB elosztott számítástechnikai kiszolgáló-fürtök létrehozása az Azure virtuális gépeken
A számításigényes párhuzamos MATLAB számítási feladatok futtatásához egy vagy több MATLAB elosztott számítástechnikai Server-fürtök létrehozása a Microsoft Azure virtual machines használatához. A MATLAB elosztott számítástechnikai kiszolgálói szoftver telepítése a virtuális gép alap lemezképeként használhatja, és az Azure gyorsindítási sablon vagy Azure PowerShell-szkript használata (a rendelkezésre álló [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) üzembe helyezése és felügyelete a fürtön. Az üzembe helyezést követően csatlakozzon a fürthöz, a számítási feladatok futtatásához.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>MATLAB és MATLAB elosztott számítástechnikai kiszolgáló
A [MATLAB](https://www.mathworks.com/products/matlab/) platform mérnöki és tudományos problémák megoldására van optimalizálva. A nagy méretű szimulációkat és adatfeldolgozási feladatok MATLAB felhasználók MathWorks párhuzamos termékek számítási segítségével gyorsítsa fel a nagy számítási igényű számítási feladatok olyan számítási fürtök és a rács szolgáltatások kihasználásával. [Párhuzamos számítási eszközkészlet](https://www.mathworks.com/products/parallel-computing/) MATLAB felhasználók alkalmazások párhuzamosíthatja, és kihasználhatja a Többmagos feldolgozók, gpu-kat, és számítási fürtök. [MATLAB elosztott számítástechnikai kiszolgáló](https://www.mathworks.com/products/distriben/) lehetővé teszi, hogy a felhasználók MATLAB a számítási fürt követhetők.

Az Azure virtual machines használatával is létrehozhat, amelyek ugyanazokat párhuzamos munkák küldje el a helyszíni fürtök, például az interaktív feladatok, a batch-feladatok, a független feladatokra és a kommunikáció számára elérhető mechanizmusok MATLAB elosztott számítástechnikai névkiszolgáló-fürtjétől a feladatok. Kiépítés képest számos előnye az Azure-t a MATLAB platform együtt van, és a hagyományos helyszíni hardveres: méretű számos virtuális gép létrehozása a fürtök igény szerinti így fizetnie csak azokat a számítási erőforrásokat használ, és a lehetővé teszi nagy mennyiségű modell tesztelhető.  

## <a name="prerequisites"></a>Előfeltételek
* **Ügyfélszámítógép** – Windows-alapú számítógépről való üzembe helyezést követően az Azure és a MATLAB elosztott számítási fürt kommunikációra lesz szüksége.
* **Az Azure PowerShell** – lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) telepítheti az ügyfélszámítógépen.
* **Azure-előfizetés** – Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) mindössze néhány perc alatt. A nagyobb fürtök esetén fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket.
* **Vcpu-kvóta** -szüksége lehet egy nagy fürt, vagy egynél több MATLAB elosztott számítástechnikai Server-fürt üzembe helyezéséhez a vCPU-kvóta növeléséhez. A kvóta növeléséhez [nyisson meg egy támogatási kérést online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.
* **MATLAB párhuzamos számítási eszközkészlet és MATLAB elosztott számítástechnikai kiszolgáló licencek** – a parancsfájlok feltételeztük, hogy a [MathWorks üzemeltetett License Manager](https://www.mathworks.com/help/install/license-management.html) használt összes licencét.  
* **MATLAB elosztott számítástechnikai kiszolgálószoftver** -egy virtuális Gépet, amely a fürt virtuális gépeihez az alap Virtuálisgép-lemezképet lesz lesz telepítve.

## <a name="high-level-steps"></a>Magas szintű lépései
MATLAB elosztott számítástechnikai kiszolgálófürtök számára az Azure virtual machines használatához a következő magas szintű lépések szükségesek. Részletes útmutatást a gyorsindítási sablon és a parancsfájlok kísérő a dokumentációban szerepelnek [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Alap Virtuálisgép-lemezkép létrehozása**  

   * Letöltheti és telepítheti a virtuális gép MATLAB elosztott számítástechnikai kiszolgáló szoftvereket.

     > [!NOTE]
     > A folyamat eltarthat néhány óra múlva, de csak kell tennie azt követően minden egyes MATLAB verziójához használja.   
     >
     >
2. **Egy vagy több fürtök létrehozása**  

   * A megadott PowerShell-szkripttel, vagy a gyorsindítási sablon használatával hozzon létre egy fürtöt az alap Virtuálisgép-rendszerképből.   
   * A megadott PowerShell-parancsfájlt, amely lehetővé teszi, hogy a listában, szüneteltetés, folytatás a fürtökkel kezelheti, és a fürtök törlése.

## <a name="cluster-configurations"></a>Fürtkonfigurációk
Jelenleg a fürt létrehozási parancsprogrammal és a sablon lehetővé teszi egy egyetlen MATLAB elosztott számítástechnikai kiszolgálói topológia létrehozása. Ha azt szeretné, hozzon létre egy vagy több további fürt minden egyes fürt munkavégző, különböző méretű virtuális gépek használó virtuális gépek eltérő számú kellene, és így tovább.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB ügyfél és a fürt az Azure-ban
Az ügyfélcsomópont MATLAB, MATLAB Feladatütemező csomópont és MATLAB elosztott számítástechnikai kiszolgáló "feldolgozó" csomópontok összes határozzák Azure virtuális gépek egy virtuális hálózatban, az alábbi ábrán látható módon.


* A fürt használatára, az ügyfél-csomópontnak által a távoli asztal csatlakozni. Az ügyfél-csomópontnak az MATLAB ügyfél futtatja.
* Az ügyfél-csomópontnak rendelkezik egy fájlmegosztást, amely szerint az összes feldolgozó érhető el.
* A licenc ellenőrzi a minden MATLAB szoftver MathWorks üzemeltetett License Manager használható.
* Alapértelmezés szerint egy MATLAB elosztott számítástechnikai kiszolgáló munkavégző vCPU / virtuális gépek feldolgozón jön létre, de bármilyen számot megadhat.

## <a name="use-an-azure-based-cluster"></a>Egy Azure-alapú fürt használatára
És más típusú MATLAB elosztott számítástechnikai névkiszolgáló-fürtjétől szeretné használni a profil-kezelőt a MATLAB ügyfél (a virtuális gép ügyfélen) Feladatütemező MATLAB fürt profil létrehozásához.

![Profil kezelő](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>További lépések
* Üzembe helyezése és kezelése az Azure-ban MATLAB elosztott számítástechnikai névkiszolgáló-fürtjétől részletes utasításokért lásd: a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) a sablonok és a parancsfájlokat tartalmazó tárházban.
* Nyissa meg a [MathWorks hely](https://www.mathworks.com/) MATLAB és MATLAB elosztott számítástechnikai kiszolgáló részletes dokumentációt.
