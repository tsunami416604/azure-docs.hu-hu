---
title: Kapacitásnövelés csomópontok hozzáadása a HPC Pack fürthöz |} Microsoft Docs
description: Megtudhatja, hogyan bontsa ki az Azure-igény szerinti HPC Pack fürtöt felhőszolgáltatásban fut szerepkör feldolgozópéldányok hozzáadásával
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
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915107"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Igény szerinti "kapacitásnövelés" csomópontok hozzáadása a HPC Pack fürthöz az Azure-ban
Ha úgy konfigurálja a [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) fürt Azure, érdemes egy módszerre, amellyel gyorsan méretezni a fürt kapacitás felfelé vagy lefelé, előre konfigurált számítási csomópont virtuális gépek halmaza fenntartása nélkül. Ez a cikk bemutatja, hogyan igény szerinti "kapacitásnövelés" csomópontokat (feldolgozói szerepkör példányok felhőszolgáltatásban fut), egy átjárócsomópont Azure számítási erőforrásokat. 

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Kapacitásnövelés csomópontok][burst]

A cikkben ismertetett Azure-csomópontok gyorsan hozzá egy felhőalapú HPC Pack átjárócsomópont VM vizsgálat vagy a koncepció igazolása telepítési nyújt segítséget. A magas szintű lépései megegyeznek-e az "Azure-kapacitásnövelés" lépéseket hozzáadása a felhő számítási kapacitás HPC Pack a helyi fürthöz. Az oktatóanyagok esetén lásd: [Microsoft HPC Pack hibrid számítási fürt beállítása](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Részletes útmutatást és az üzemi környezetek szempontokat, [Microsoft HPC Pack Azure-kapacitásnövelés](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure virtuális Gépen telepített HPC Pack átjárócsomópont** -önálló átjárócsomópont virtuális gép vagy egy, automatikusan nagyobb fürt része. Egy önálló átjárócsomópont létrehozásához lásd: [központi telepítése egy Azure virtuális gép egy HPC Pack Átjárócsomópont](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatizált HPC Pack fürt telepítési lehetőségeket, lásd: [beállítások létrehozását és felügyeletét a Windows HPC cluster az Azure-ban a Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Ha használja a [HPC Pack IaaS telepítési parancsfájl](hpcpack-cluster-powershell-script.md) a fürt létrehozása az Azure-ban is megadhat az Azure-kapacitásnövelés csomópontok az automatikus központi telepítésben. Tekintse meg, ha a cikkben szereplő példák.
  > 
  > 
* **Azure-előfizetés** – Azure csomópontokat ugyanahhoz az előfizetéshez használandó átjárócsomópont a virtuális gép, vagy egy másik előfizetést (vagy előfizetések).
* **Magok kvóta** -előfordulhat, hogy növelnie kell a beállított kvótát mag, különösen akkor, ha úgy dönt, hogy több Azure csomópontokat multicore méretek a telepítése. A kvóta növeléséhez [nyissa meg az online támogatás ügyfélkérés](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) díjmentesen.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>1. lépés: Egy felhőalapú szolgáltatás és az Azure-csomópontok a tárfiók létrehozása
Az Azure portálon vagy ezzel egyenértékű eszközök segítségével konfigurálja a következő erőforrásokat, amelyek szükségesek ahhoz, hogy telepítse az Azure csomópontokat:

* Egy új Azure cloud service (klasszikus)
* Egy új Azure storage-fiók (klasszikus)

> [!NOTE]
> Az előfizetéshez létező felhőalapú szolgáltatást nem használja fel. 
> 
> 

**Megfontolások**

* Minden létrehozni kívánt Azure csomópont sablon külön felhőszolgáltatás konfigurálása. Azonban használhatja ugyanazt a tárfiókot több csomópont sablonok nem támogatottak.
* Azt javasoljuk, hogy megtalálta a felhőszolgáltatás és a storage-fiók a központi telepítés azonos Azure-régióban.

## <a name="step-2-configure-an-azure-management-certificate"></a>2. lépés: Az Azure felügyeleti tanúsítvány konfigurálása
Adja hozzá Azure-csomópontok számítási erőforrásokat, az átjárócsomópont és feltöltése, a központi telepítéshez használt Azure-előfizetéshez tartozó tanúsítvány a felügyeleti tanúsítvány szükséges.

A jelen esetben válassza a **alapértelmezett HPC Azure felügyeleti tanúsítvánnyal** HPC Pack telepítését, és automatikusan konfigurálja a központi csomóponton. Ez a tanúsítvány akkor hasznos, tesztelési célokra és a koncepció igazolása központi telepítéseket. Ezt a tanúsítványt használni, töltse fel az előfizetés az átjárócsomóponthoz VM a C:\Program Files\Microsoft HPC Pack fájl 2012\Bin\hpccert.cer. A tanúsítvány feltöltése a [Azure-portálon](https://portal.azure.com):

1. Kattintson a **előfizetések** > *your_subscription_name*.

2. Kattintson a **felügyeleti tanúsítványok** > **feltöltése**.

A felügyeleti tanúsítvány konfigurálásának további lehetőségei, lásd: [forgatókönyvek az Azure felügyeleti tanúsítvány konfigurálása az Azure-kapacitásnövelés a telepítések](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>3. lépés: Telepítse az Azure csomópontokat a fürthöz
Adja hozzá, és indítsa el az Azure-csomópontok ebben a forgatókönyvben a lépései megegyeznek általában a lépéseket a helyszíni átjáró-csomóponti. További információkért tekintse meg a következő részeket [lépéseket az Azure-csomópontokra központi telepítése a Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Egy Azure csomópont-sablon létrehozása
* Az Azure csomópontokat hozzáadni a Windows HPC-fürt
* Indítsa el a (kiépíteni) az Azure-csomópontok

Után adja hozzá, és indítsa el a csomópontot, azok használata fürtben feladatok futtatásához készen állnak.

Ha Azure-csomópontok telepítésekor problémákat tapasztal, tekintse meg a [hibaelhárítása központi telepítések az Azure csomópontokat a Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>További lépések
* A kapacitásnövelés csomópontok számítási igényű példányméretének használatához tekintse meg a szempontok [nagy teljesítményű számítási Virtuálisgép-méretek](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ha szeretne automatikusan nő vagy az Azure számítási erőforrások szerint a fürt alkalmazások és szolgáltatások zsugorítani, lásd: [automatikusan nő, és az Azure számítási erőforrásokat HPC Pack-fürtben lévő zsugorítása](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
