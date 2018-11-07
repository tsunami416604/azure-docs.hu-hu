---
title: Csúcsterhelési csomópontok hozzáadása HPC Pack-fürthöz |} A Microsoft Docs
description: Ismerje meg, hogyan bontsa ki az Azure igény szerinti HPC Pack-fürthöz egy cloud service-ben futó feldolgozói szerepkörpéldányok hozzáadásával
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: 7d42c026975a18c7574e4bc64ec28ab3ed0082bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248452"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>"Adatlöket" igény szerinti csomópontok hozzáadása HPC Pack-fürthöz az Azure-ban
Ha beállította a [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) fürt az Azure-ban, érdemes oly módon való gyors a fürt kapacitásának kisebbre vagy nagyobbra méretezhetők, virtuális gépek előre konfigurált számítási csomópontok készletét fenntartása nélkül. Ez a cikk bemutatja, hogyan (feldolgozói szerepkörpéldányok egy cloud service-ben futó) "adatlöket" igény szerinti csomópontok hozzáadása egy átjárócsomóponthoz, az Azure-beli számítási erőforrásként. 

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Csúcsterhelési csomópontok][burst]

A jelen cikkben ismertetett lépések segítségével adhat hozzá az Azure-csomópontok gyorsan egy felhőalapú HPC Pack átjárócsomópont virtuális gép tesztelési vagy proof-of-concept központi telepítés. A magas szintű lépései ugyanazok, mint az "a csúcsterhelések átirányítása az Azure" lépéseket hozzáadásához felhőbeli számítási kapacitást a helyszíni HPC Pack-fürthöz. Foglalkozó oktatóanyagért lásd: [Microsoft HPC packkel hibrid számítási fürt beállítása](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Részletes útmutatást és szempontokat, éles környezetekben üzemelő példányok, [a csúcsterhelések átirányítása az Azure, Microsoft HPC packkel](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure-beli virtuális gépen telepített HPC Pack átjárócsomópont** -egy különálló fő csomópontot virtuális gép vagy egy nagyobb fürt részét képező is használhatja. Egy különálló fő csomópontjának létrehozása: [üzembe helyezése egy Azure VM-HPC Pack Átjárócsomópont](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A automatikus HPC Pack fürt üzembe helyezési lehetőségekről [beállításokat hozhat létre és kezelhet egy Windows HPC-fürtön az Azure, Microsoft HPC packkel](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Ha használja a [HPC Pack IaaS telepítési szkripttel](hpcpack-cluster-powershell-script.md) a fürt létrehozása az Azure-ban is megadhat az Azure csúcsterhelési csomópontok az automatikus központi telepítésben. Tekintse meg a cikkben szereplő példákat.
  > 
  > 
* **Azure-előfizetés** – Azure-csomópontok hozzáadásához kiválaszthatja a fő csomópont virtuális gép üzembe helyezéséhez használt ugyanabban az előfizetésben vagy egy másik előfizetést (vagy előfizetések).
* **Magkvóta** -magok száma, a kvóta növeléséhez előfordulhat, hogy szüksége, különösen akkor, ha úgy dönt, hogy a Többmagos méretű számos Azure-csomópontok üzembe helyezése. A kvóta növeléséhez [nyisson meg egy támogatási kérést online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>1. lépés: Hozzon létre egy felhőalapú szolgáltatás és a egy storage-fiókját az Azure-csomópontok
Az Azure portal vagy a megfelelő eszközök segítségével konfigurálja a következő erőforrások üzembe helyezése az Azure-csomópont szükséges:

* Egy új Azure cloud Services (klasszikus)
* Új Azure storage-fiókot (klasszikus)

> [!NOTE]
> Ne használja újra egy létező felhőszolgáltatás az előfizetésben. 
> 
> 

**Megfontolások**

* Minden egyes Azure-csomópont sablont kíván létrehozni a különálló felhőszolgáltatás konfigurálása. Több csomópont-sablonok esetében azonban használhatja ugyanazt a tárfiókot.
* Azt javasoljuk, hogy, keresse meg a felhőszolgáltatás és a központi telepítés a tárfiók ugyanazon Azure-régióban.

## <a name="step-2-configure-an-azure-management-certificate"></a>2. lépés: Az Azure felügyeleti tanúsítvány konfigurálása
Azure-csomópontok hozzáadása a számítási erőforrások, a fő csomópontot és feltöltése az üzembe helyezéshez használt Azure-előfizetést egy megfelelő tanúsítványt a felügyeleti tanúsítvány szükséges.

Ebben az esetben beállíthatja a **alapértelmezett HPC az Azure felügyeleti tanúsítvány** , amely a HPC Pack automatikusan telepíti és konfigurálja a központi csomóponton. Ez a tanúsítvány hasznos tesztelési célokra és proof-of-concept központi telepítések. Ezt a tanúsítványt szeretne használni, töltse fel az előfizetés az átjárócsomóponthoz virtuális gép a C:\Program Files\Microsoft HPC Pack fájl 2012\Bin\hpccert.cer. A tanúsítvány feltöltéséhez az [az Azure portal](https://portal.azure.com):

1. Kattintson a **előfizetések** > *your_subscription_name*.

2. Kattintson a **felügyeleti tanúsítványok** > **feltöltése**.

További beállítások konfigurálása a felügyeleti tanúsítvány, lásd: [forgatókönyvek az Azure felügyeleti tanúsítvány konfigurálása az Azure-kapacitásnövelés üzembe helyezéséhez](https://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>3. lépés: Azure-csomópontok üzembe helyezése a fürtön
Adja hozzá, és indítsa el az Azure-csomópontok ebben a forgatókönyvben a lépések megegyeznek általában a lépéseket egy a helyszíni fő csomóponttal. További információkért tekintse meg a következő szakaszok a [lépései az Azure-csomópontok üzembe helyezése a Microsoft HPC Packkel](https://technet.microsoft.com/library/gg481758.aspx):

* Egy Azure-csomópont-sablon létrehozása
* A Windows HPC-fürtöt Azure-csomópontok hozzáadása
* Indítsa el a (kiépíteni) az Azure-csomópontok

Után adja hozzá, és indítsa el a csomópontokat, hogy készen állnak a fürt feladatok futtatásához.

Ha az Azure-csomópontok üzembe helyezésekor problémákat tapasztal, tekintse meg [hibaelhárítása központi telepítések az Azure-csomópont Microsoft HPC Pack](https://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>További lépések
* Egy nagy számítási példány mérete a csúcsterhelési csomópontok használja, tekintse meg a szempontok [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ha szeretne automatikusan növelheti vagy csökkentheti az Azure számítási erőforrások fürtterhelés függvényében, lásd: [automatikus méretnövelése és -csökkentése az Azure számítási erőforrásokat a HPC Pack-fürtökben](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
