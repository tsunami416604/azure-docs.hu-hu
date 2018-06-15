---
title: Számítási igényű Azure virtuális gépek használata a kötegelt |} Microsoft Docs
description: How Azure Batch készletek RDMA-kompatibilisek-e vagy GPU-engedélyezve van a Virtuálisgép-méretek előnyeit
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: 5a73e926b5979e573ccb0402ff2d23eae2463232
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
ms.locfileid: "29762441"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Használja az RDMA-kompatibilisek-e vagy GPU-kompatibilis példány kötegelt készletek

Bizonyos kötegelt feladatok futtatása, érdemes a nagyméretű számítási készült Azure Virtuálisgép-méretek előnyeit. Ahhoz például, hogy több példány futtatása [MPI terhelések](batch-mpi.md), dönthet úgy, A8 és A9, vagy H-sorozat méreteket egy hálózati csatoló a távoli közvetlen memória hozzáférés (RDMA). Ezen méretét az InfiniBand hálózati csomópontok közötti kommunikációhoz, amely meggyorsíthatja MPI alkalmazások csatlakozni. Vagy CUDA alkalmazások esetén dönthet úgy, amely tartalmazza az NVIDIA Tesla grafikus processzor a processzorok kártyák N-sorozat mérete.

Ez a cikk útmutatást és példákat használhatja néhány speciális méretű Azure Batch készletek tartalmazza. A specifikációk és a háttérben lásd:

* Nagy teljesítményű számítási Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Virtuálisgép-méretek GPU-t ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Előfizetés és korlátai

* **Kvótái és korlátai** – a [magok kvóta értéke Batch-fiók](batch-quota-limit.md#resource-quotas) adhat hozzá a Batch-készlet a megadott méretet a csomópontok számát korlátozhatja. A kvóta eléréséhez, ha úgy dönt, hogy az RDMA-kompatibilis, GPU-t, vagy más multicore Virtuálisgép-méretek nagy valószínűséggel áll. 

  Emellett az egyes virtuális gép kártevőcsaládok, például NCv2 NCv3 vagy ND, a Batch-fiók használata korlátozott korlátozott kapacitás miatt. Ezek családok használata csak érhető el az alapértelmezett 0 magok a kvóta növelését kérésével.  

  Ha szeretné, [a kvóta növelését](batch-quota-limit.md#increase-a-quota) díjmentesen.

* **Régiónkénti elérhetőség** - számítási igényű virtuális gépek nem állnak rendelkezésre a régiókban, ahol a Batch-fiókokat hozhat létre. Ellenőrizze, hogy rendelkezésre áll-e a mérete, lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Függőségek

Az RDMA és a GPU képességek számítási igényű méretű csak bizonyos operációs rendszereken támogatottak. Az operációs rendszertől függően szükség lehet telepítéséhez és konfigurálásához további illesztőprogram vagy más szoftvereket. A következő táblázat összefoglalja, ezeket a függőségeket. Tekintse meg a csatolt cikkek részleteiről. Kötegelt készletek konfigurálási beállításairól tekintse meg a cikk későbbi részében.


### <a name="linux-pools---virtual-machine-configuration"></a>Linux-készletek - virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, vagy<br/>CentOS-alapú HPC<br/>(Az azure piactéren) | Intel MPI 5 | Csomópontok közötti kommunikáció engedélyezéséhez egyidejű feladat a végrehajtás letiltása |
| [NC, NCv2, NCv3, ND adatsorozat *](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (függ a sorozat) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 vagy 7.4, vagy<br/>7.3 vagy 7.4 centOS<br/>(Az azure piactéren) | NVIDIA CUDA eszközkészlet illesztőprogramok | – | 
| [Portok HV-sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, vagy<br/>7.3 centOS<br/>(Az azure piactéren) | NVIDIA rács illesztőprogramok | – |

* Szükség lehet a RDMA-kapcsolatot az RDMA-kompatibilis N sorozatú virtuális gépeken futó [további konfigurációs](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) , amely terjesztési függ.



### <a name="windows-pools---virtual-machine-configuration"></a>Windows-készletek - virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016-ot, 2012 R2-ben vagy<br/>2012 (az azure piactéren) | Microsoft MPI 2012 R2 vagy újabb verzióját, vagy<br/> Intel MPI 5<br/><br/>HpcVMDrivers Azure Virtuálisgép-bővítmény | Csomópontok közötti kommunikáció engedélyezéséhez egyidejű feladat a végrehajtás letiltása |
| [NC, NCv2, NCv3, ND adatsorozat *](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (függ a sorozat) | Windows Server 2016 vagy <br/>2012 R2 (az Azure piactéren) | NVIDIA Tesla illesztőprogramok vagy CUDA eszközkészlet illesztőprogramok| – | 
| [Portok HV-sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 vagy<br/>2012 R2 (az Azure piactéren) | NVIDIA rács illesztőprogramok | – |

* RDMA-kapcsolatot az RDMA-kompatibilis N sorozatú virtuális gépeken támogatott, a Windows Server 2016 vagy a Windows Server 2012 R2 (az Azure piactérről) HpcVMDrivers bővítmény és Microsoft MPI vagy Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Windows-készletek - Felhőszolgáltatások konfigurálása

> [!NOTE]
> N-sorozat méretek kötegelt készletek és a felhőalapú szolgáltatás konfigurációja nem támogatottak.
>

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016-ot, 2012 R2, 2012, vagy<br/>2008 R2 (Vendég operációsrendszer-család) | Microsoft MPI 2012 R2 vagy újabb verzióját, vagy<br/>Intel MPI 5<br/><br/>HpcVMDrivers Azure Virtuálisgép-bővítmény | Csomópontok közötti kommunikáció engedélyezése<br/> Tiltsa le az egyidejű feladat a végrehajtás |





## <a name="pool-configuration-options"></a>Tárolókészlet konfigurációs beállítások

Konfigurálhatja a speciális Virtuálisgép-méretet a Batch-készlet, a kötegelt API-k és eszközök biztosítanak telepítéséhez szükséges szoftverek és illesztőprogramok, többek között számos lehetőség közül választhat:

* [Indítsa el a feladat](batch-api-basics.md#start-task) -erőforrás-fájlként egy csomag feltöltése az Azure storage-fiók ugyanabban a régióban, mint a Batch-fiók. Hozzon létre egy kezdő tevékenység parancssor az erőforrásfájl, csendes telepítésre szolgáló a készlet indításakor. További információkért lásd: a [REST API-dokumentáció](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > A kezdő tevékenység emelt szintű (admin) engedélyekkel kell futtatni, és azt kell várnia, hogy sikeres.
  >

* [Alkalmazáscsomag](batch-application-packages.md) - zip telepítési csomag hozzáadása a Batch-fiókhoz, és egy alkalmazáscsomag-hivatkozást konfigurálása a készletben. Ez a beállítás feltölti és unzips a csomag a készlet összes csomópontján. Ha a csomag telepítőhöz, hozzon létre egy kezdő feladat parancssort az alkalmazás csendes telepítéséhez az összes alkalmazáskészlet csomóponton. Szükség esetén telepítse a csomagot, a feladatok ütemezése egy csomópontján futtatni.

* [Egyéni készlet kép](batch-custom-images.md) – egyéni Windows vagy Linux rendszerű Virtuálisgép-lemezkép létrehozása, amely tartalmazza az illesztőprogramokat, a szoftver, vagy a Virtuálisgép-méretet a szükséges egyéb beállításokat. 

* [A Batch-hajógyárnak](https://github.com/Azure/batch-shipyard) automatikusan konfigurálja a GPU és RDMA dolgozhatnak a tárolóalapú munkafolyamatok Azure Batch. Kötegelt hajógyárnak teljesen célja a konfigurációs fájlok. Nincsenek sok minta módszereivel konfiguráció érhető el, amelyek lehetővé teszik a GPU és RDMA munkaterhelésekhez, mint a [CNTK GPU módszereivel](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) amely GPU illesztőprogramok N sorozatú virtuális gépeken a következő előre konfigurálhatja, és betölti a Docker-lemezkép Microsoft kognitív eszközkészlet szoftverként.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Példa: Microsoft MPI címkészlet A8 méretű VM

Windows MPI alkalmazások futtatásához Azure A8 csomópontok készletét, telepítendő a támogatott MPI megvalósítása. A minta telepítésének lépéseit az alábbiakban [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) Windows tárolókészlet egy kötegelt alkalmazáscsomagot használ.

1. Töltse le a [telepítőcsomagját](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) a Microsoft MPI legújabb verzióját.
2. Hozzon létre egy zip-fájl, a csomag.
3. A csomag feltöltése a Batch-fiókhoz. Útmutató: a [alkalmazáscsomagok](batch-application-packages.md) útmutatást. Adja meg, mint az alkalmazás azonosítóját *MSMPI*, és többek között verzióval *8.1*. 
4. A kötegelt API-k vagy az Azure-portál használatával, a készletet létrehozni a felhőszolgáltatások konfigurálása a kívánt számú csomópontok és a skála. A következő táblázatban példa beállításai MPI beállítása a felügyelet nélküli módban, a kezdő tevékenység használatával:

| Beállítás | Érték |
| ---- | ----- | 
| **Rendszerkép típusa** | Cloud Services |
| **Operációsrendszer-család** | Windows Server 2012 R2 (4 operációsrendszer-család) |
| **Csomópont mérete** | A8 Standard |
| **Fürtök csomóponton belüli kommunikációjához kommunikáció engedélyezve** | True (Igaz) |
| **Csomópontonkénti tevékenységek maximális száma** | 1 |
| **Alkalmazás csomaghivatkozásokhoz** | MSMPI |
| **Indítsa el a feladat engedélyezve van** | True (Igaz)<br>**Parancssor** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Felhasználói azonosító** -készlet autouser, a rendszergazda<br/>**Várjon, amíg a sikeres** – igaz

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Példa: NVIDIA Tesla illesztőprogramok NC Virtuálisgép-készlet

Linux NC csomópontok készletét CUDA alkalmazások futtatásához, telepítendő CUDA eszközkészlet 9.0 a csomóponton. Az eszközkészlet telepíti a szükséges NVIDIA Tesla GPU-illesztőprogramokat. Az alábbiakban a GPU-illesztőprogramok egyéni Ubuntu 16.04 LTS lemezkép telepítéséhez minta lépéseket:

1. Egy Azure NC sorozatú virtuális gép futó Ubuntu 16.04 LTS telepíthető. A US Dél központi régióban hozzon létre például a virtuális Gépet. Győződjön meg arról, hogy a felügyelt lemezes virtuális gép létrehozása.
2. A lépések segítségével csatlakoztassa a virtuális Gépet és [CUDA illesztőprogramok telepítése](../virtual-machines/linux/n-series-driver-setup.md).
3. A Linux-ügynök kiosztásának megszüntetése, majd [a Linux virtuális gép lemezképének](../virtual-machines/linux/capture-image.md).
4. Batch-fiók létrehozása, amely támogatja a hálózati vezérlő által virtuális gépek régióban.
5. A kötegelt API-k vagy az Azure-portálon hozzon létre egy címkészletet [az egyéni lemezkép használatával](batch-custom-images.md) és a kívánt számú csomópontok és a skála. Az alábbi táblázat a kép tartozó minta beállításainak:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Kép: egyéni |
| **Kép: egyéni** | A lemezkép nevét |
| **Csomópont ügynök SKU** | Batch.node.ubuntu 16.04 |
| **Csomópont mérete** | NC6 Standard |



## <a name="next-steps"></a>További lépések

* MPI-feladatok futtatása az Azure Batch-készlet, tekintse meg a [Windows](batch-mpi.md) vagy [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) példák.

* A kötegelt GPU-munkaterhelések, tekintse meg a [kötegelt hajógyárnak](https://github.com/Azure/batch-shipyard/) receptet.