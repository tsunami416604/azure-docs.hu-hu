---
title: Nagy számítási igényű Azure virtuális gépek használata a Batch-Csel |} A Microsoft Docs
description: Hogyan igénybe RDMA-kompatibilis vagy a GPU-kompatibilis Virtuálisgép-méretek az Azure Batch-készletek
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
ms.openlocfilehash: 6969f0c6a05ebf5b34fb746d2a83b884687ad710
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258255"
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>RDMA-kompatibilis vagy a GPU-kompatibilis példányok használata Batch-készletekben

Egyes Batch-feladatok futtatásához, érdemes kihasználni az Azure Virtuálisgép-méretek nagy méretű számítási feladatokra tervezve. Ha például több példány futtatásához [MPI számítási feladatok](batch-mpi.md), kiválaszthatja az A8, A9, vagy H-sorozat méretei, amelyek egy hálózati csatoló a távoli közvetlen memória-hozzáférés (RDMA). Ezeket a méreteket a csomópontok közötti kommunikáció, amely meggyorsíthatja a MPI-alkalmazások egy InfiniBand hálózathoz csatlakozni. Vagy a CUDA-alkalmazások, kiválaszthatja az N-sorozat méretei, beleértve az NVIDIA Tesla grafikus (GPU) egység kártyák processzor.

Ez a cikk útmutatást és példákat, használhatja a speciális méretek az Azure Batch-készletekben. Adatait tartalmazza, és a háttérben lásd:

* Nagy teljesítményű számítási Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-kompatibilis Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Előfizetés és korlátozások

* **Kvóták és korlátozások** – a [Batch-fiókonként magkvóta](batch-quota-limit.md#resource-quotas) korlátozhatja a Batch-készlet is hozzáadhat adott méretű csomópontok számát. Ön nagyobb eséllyel kvóta elérni, ha úgy dönt, hogy a Többmagos virtuális gépek méreteit RDMA-kompatibilis, GPU-kompatibilis, vagy más. 

  Emellett egyes Batch-fiókban, például az NCv2, az NCv3 és ND, VM-családban használata korlátozott korlátozott kapacitás miatt. Ezek családok használatát csak akkor használható az alapértelmezett 0 magok kvótanövelést kérésével.  

  Ha szeretné, [a kvóta növelésére](batch-quota-limit.md#increase-a-quota) díjmentesen.

* **Régiónkénti elérhetőség** – nagy számítási igényű virtuális gépek nem feltétlenül érhető el a régióban, ahol létrehozhatja a Batch-fiókok. Ellenőrizze, hogy elérhető-e méret, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Függőségek

Számításigényes méretek RDMA-lehetőséget és GPU képességeit csak bizonyos operációs rendszerek támogatottak. Az operációs rendszertől függően szükség lehet további illesztőprogram vagy más szoftverek konfigurálásához. A következő táblázat összefoglalja a függőségek. Tekintse meg a hivatkozott cikkek nyújtanak bővebb részletekért. Beállítások konfigurálása a Batch-készletek lásd: a cikk későbbi részében.


### <a name="linux-pools---virtual-machine-configuration"></a>Linux-készletek – virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, vagy<br/>CentOS-alapú HPC<br/>(Az azure Marketplace-en) | Az Intel MPI 5 | Engedélyezze a csomópontok közötti kommunikáció, tiltsa le az egyidejű feladat a végrehajtás |
| [Hálózati vezérlő által, az NCv2, az NCv3, ND sorozat *](../virtual-machines/linux/n-series-driver-setup.md) | Az NVIDIA Tesla GPU (eltérő sorozat) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.4, vagy a 7.3 vagy<br/>CentOS 7.3 vagy 7.4<br/>(Az azure Marketplace-en) | NVIDIA CUDA eszközkészlet illesztőprogramok | – | 
| [NV sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3, vagy<br/>CentOS 7.3.<br/>(Az azure Marketplace-en) | Az NVIDIA GRID illesztőprogramok | – |

* RDMA-kompatibilis N-sorozat virtuális gépei RDMA-kapcsolatot igényelhetnek [további konfigurációs](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) terjesztési, amelyek eltérő.



### <a name="windows-pools---virtual-machine-configuration"></a>Windows-készletek – virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | A Windows Server 2016-ban, 2012 R2-ben, vagy<br/>2012 (az azure Marketplace-en) | A Microsoft MPI 2012 R2 vagy újabb, vagy<br/> Az Intel MPI 5<br/><br/>HpcVMDrivers Azure Virtuálisgép-bővítménnyel | Engedélyezze a csomópontok közötti kommunikáció, tiltsa le az egyidejű feladat a végrehajtás |
| [Hálózati vezérlő által, az NCv2, az NCv3, ND sorozat *](../virtual-machines/windows/n-series-driver-setup.md) | Az NVIDIA Tesla GPU (eltérő sorozat) | A Windows Server 2016-ban, vagy <br/>2012 R2 (az Azure Marketplace-en) | Az NVIDIA Tesla illesztőprogramok és a CUDA eszközkészlet-illesztőprogramok| – | 
| [NV sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | A Windows Server 2016-ban, vagy<br/>2012 R2 (az Azure Marketplace-en) | Az NVIDIA GRID illesztőprogramok | – |

* RDMA-kapcsolatot RDMA-kompatibilis N-sorozat virtuális gépei HpcVMDrivers bővítmény és a Microsoft MPI vagy az Intel MPI-t a Windows Server 2016 vagy Windows Server 2012 R2 (az Azure Marketplace-en) használata támogatott.

### <a name="windows-pools---cloud-services-configuration"></a>Windows-készletek – Cloud services-konfiguráció

> [!NOTE]
> Az N-sorozat méretei nem támogatottak a Felhőszolgáltatás-konfigurációval a Batch-készletekben.
>

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | A Windows Server 2016, 2012 R2, 2012, vagy<br/>2008 R2 (vendég operációs rendszerek) | A Microsoft MPI 2012 R2 vagy újabb, vagy<br/>Az Intel MPI 5<br/><br/>HpcVMDrivers Azure Virtuálisgép-bővítménnyel | Csomópontok közötti kommunikáció,<br/> egyidejű feladat a végrehajtás letiltása |





## <a name="pool-configuration-options"></a>Készlet konfigurációs lehetőségek

Konfigurálhatja a Batch-készlet speciális Virtuálisgép-méretét, a Batch API-k és eszközök meg több módon is telepítheti a szükséges szoftvereket vagy illesztőprogramok, többek között:

* [Kezdő tevékenység](batch-api-basics.md#start-task) -egy erőforrás-fájljaként csomag feltöltése az Azure storage-fiókban a Batch-fiók ugyanabban a régióban. Hozzon létre egy indítási feladat parancssori az erőforrásfájl csendes telepítése a készlet indításakor. További információkért tekintse meg a [REST API dokumentációját](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > Az indítási tevékenység emelt szintű (rendszergazdai) engedélyekkel kell futtatni, és azt meg kell várnia a sikeres.
  >

* [Alkalmazáscsomag](batch-application-packages.md) – tömörített telepítőcsomag hozzáadása a Batch-fiókhoz, és a egy csomaghivatkozás konfigurálása a készletben. Ez a beállítás tölt fel, és a csomagot a készlet minden csomópontján unzips. Ha a csomag telepítő, hozzon létre egy indítási feladat parancssori az alkalmazás csendes telepítéséhez a készlet minden csomópontján. Szükség esetén telepítse a csomagot, amikor a feladat futni a csomóponton úgy van ütemezve.

* [Egyéni készlet kép](batch-custom-images.md) - illesztőprogramok, szoftverek, tartalmazó egyéni Windows vagy Linux rendszerű virtuális gép rendszerképének létrehozása vagy a Virtuálisgép-méret szükséges egyéb beállításokat. 

* [Batch-hajógyárnak](https://github.com/Azure/batch-shipyard) automatikusan konfigurálja a GPU- és rdma-t az Azure Batch a tárolóalapú számítási feladatokra transzparens módon működik. Batch hajógyárnak teljes egészében a konfigurációs fájlokat az alkalmazások. Nincsenek sok minta recept konfiguráció érhető el, mint például a GPU- és RDMA feladatok futtatását teszik lehetővé a [CNTK GPU recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) amely előre GPU-illesztőprogramokat N-sorozatú virtuális gépeken, és betölti a Docker-rendszerkép a Microsoft Cognitive Toolkit szoftver.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Példa: A Microsoft MPI-a8-as virtuális gép készlet

A8-as Azure-csomópontok készletét a Windows MPI-alkalmazások futtatásához telepíteni szeretné a támogatott MPI-megvalósítás. Minta telepítésének lépéseit az alábbiakban [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) egy Windows tárolókészlet a Batch-alkalmazás csomagokkal.

1. Töltse le a [telepítőcsomagját](https://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) Microsoft MPI legújabb verzióját.
2. Hozzon létre a csomag zip-fájlban.
3. A csomag feltöltése a Batch-fiókhoz. Útmutató: a [alkalmazáscsomagok](batch-application-packages.md) útmutatást. Adjon meg egy alkalmazásazonosítót például *MSMPI*, és a egy verziót például *8.1*. 
4. A Batch API-k vagy az Azure portal használatával, hoz létre egy készletet a kívánt számú csomópontot és a méretezési csoport és a cloud services-konfigurációt. Az alábbi táblázat a felügyelet nélküli módban, az indítási tevékenység használata MPI beállításához minta beállításai:

| Beállítás | Érték |
| ---- | ----- | 
| **Rendszerkép típusa** | Cloud Services |
| **Operációsrendszer-család** | A Windows Server 2012 R2 (operációs rendszerek 4) |
| **A csomópont mérete** | Standard a8 csomag |
| **Csomópontok közötti kommunikáció engedélyezve** | True (Igaz) |
| **Csomópontonkénti tevékenységek maximális száma** | 1 |
| **Alkalmazáscsomag-hivatkozások** | MSMPI |
| **Kezdő tevékenység engedélyezve** | True (Igaz)<br>**Parancssor** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Felhasználói identitás** -készlet automatikus felhasználó, rendszergazda<br/>**Várjon, amíg a sikeres** – igaz

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Példa: NVIDIA Tesla illesztőprogramok NC virtuális gép készlet

Linux NC-csomópontok készletét a CUDA-alkalmazások futtatásához szüksége CUDA eszközkészlet 9.0 telepítéséhez a csomópontokon. Az eszközkészlet telepíti a szükséges NVIDIA Tesla GPU-illesztőprogramokat. Az alábbiakban a mintául szolgáló lépéseket a GPU-illesztőprogramokat a egy egyéni Ubuntu 16.04 LTS-rendszerképének üzembe helyezéséhez:

1. Egy Azure NC sorozatú virtuális gép, Ubuntu 16.04 LTS rendszert futtató üzembe helyezéséhez. Például hozzon létre a virtuális gép az USA déli középső régióban. Győződjön meg arról, hogy egy felügyelt lemezt a virtuális gép létrehozása.
2. Kövesse a lépéseket a virtuális Géphez való csatlakozáshoz és [CUDA illesztőprogramok telepítése](../virtual-machines/linux/n-series-driver-setup.md).
3. A Linux-ügynök megszüntetése, majd [a Linux rendszerű virtuális gép lemezképének rögzítése](../virtual-machines/linux/capture-image.md).
4. Hozzon létre egy Batch-fiókot, amely támogatja a hálózati vezérlő virtuális gépek egy régióban.
5. A Batch API-k vagy az Azure portal használatával hoz létre egy készletet [az egyéni rendszerkép használatával](batch-custom-images.md) és a kívánt számú csomópontot és a méretezési csoport. Az alábbi táblázatban látható minta tárolókészlet beállításainak a lemezkép:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni rendszerkép** | A lemezkép nevét |
| **Csomóponti ügynök SKU** | Batch.node.ubuntu 16.04 |
| **A csomópont mérete** | NC6 Standard |



## <a name="next-steps"></a>További lépések

* MPI feladatok futtatása Azure Batch-készlet, tekintse meg a [Windows](batch-mpi.md) vagy [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) példákat.

* A GPU számítási feladatok Batch példákért lásd a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard/) receptek.