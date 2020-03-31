---
title: Számítási igényű Azure-virtuális gépek használata batch-el
description: Hogyan használhatja ki a HPC és a GPU virtuálisgép-méreteit az Azure Batch-készletekben. Ismerje meg az operációs rendszer függőségeit, és tekintse meg a példamutatásokat.
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: d0238e0b5b882748218835d7f06a147d435a9f90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245055"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>RDMA- vagy GPU-példányok használata kötegkészletekben

Bizonyos batch-feladatok futtatásához kihasználhatja a nagyméretű számításhoz tervezett Azure virtuálisgép-méreteket. Példa:

* A többpéldányos [MPI-munkaterhelések](batch-mpi.md)futtatásához válassza a H sorozatú vagy más olyan méreteket, amelyek a távoli közvetlen memória-hozzáférés (RDMA) hálózati kapcsolatával rendelkeznek. Ezek a méretek egy InfiniBand hálózathoz csatlakoznak a csomópontok közötti kommunikációhoz, ami felgyorsíthatja az MPI-alkalmazásokat. 

* CUDA-alkalmazások esetén válassza az NVIDIA Tesla grafikus feldolgozóegység (GPU) kártyákat tartalmazó N-sorozatú méreteket.

Ez a cikk útmutatást és példákat tartalmaz az Azure speciális méreteinek kötegkészletekben való használatához. A specifikációkat és a hátteret lásd:

* Nagy teljesítményű számítási virtuális gép méretek ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-kompatibilis virtuális gépek méretei ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Előfordulhat, hogy bizonyos virtuálisgép-méretek nem érhetők el azokban a régiókban, ahol a Batch-fiókokat hozza létre. Ha ellenőrizni szeretné, hogy van-e méret, olvassa el a [Régiónként elérhető termékek](https://azure.microsoft.com/regions/services/) és [a Virtuálisgép méretének kiválasztása kötegkészlethez.](batch-pool-vm-sizes.md)

## <a name="dependencies"></a>Függőségek

A Batch nagy számítási igényű méreteinek RDMA- vagy GPU-képességeit csak bizonyos operációs rendszerek támogatják. (A támogatott operációs rendszerek listája az ilyen méretekben létrehozott virtuális gépek támogatott készletének egy része.) Attól függően, hogy hogyan hozza létre a batch készletet, előfordulhat, hogy további illesztőprogramot vagy más szoftvert kell telepítenie vagy konfigurálnia a csomópontokon. Az alábbi táblázatok ezeket a függőségeket összegezik. A részleteket a hivatkozott cikkekben találja. A kötegkészletek konfigurálásának lehetőségeiről a cikk későbbi részében olvashat.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-készletek - Virtuális gép konfigurációja

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, vagy<br/>CentOS-alapú HPC<br/>(Azure Piactér) | Intel MPI 5<br/><br/>Linux RDMA-illesztőprogramok | Csomópontközi kommunikáció engedélyezése, egyidejű feladatvégrehajtás letiltása |
| [NC, NCv2, NCv3, NDv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (sorozatonként változik) | Ubuntu 16.04 LTS, vagy<br/>CentOS 7.3 vagy 7.4<br/>(Azure Piactér) | NVIDIA CUDA vagy CUDA eszközkészlet-illesztőprogramok | N/A | 
| [NV, NVv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, vagy<br/>CentOS 7,3<br/>(Azure Piactér) | NVIDIA GRID illesztőprogramok | N/A |

<sup>*</sup>RDMA-képes N-sorozat méretei is NVIDIA Tesla GPU-k

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-készletek – Virtuális gép konfigurációja

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 vagy<br/>2012 (Azure Piactér) | Microsoft MPI 2012 R2 vagy újabb, vagy<br/> Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Csomópontközi kommunikáció engedélyezése, egyidejű feladatvégrehajtás letiltása |
| [NC, NCv2, NCv3, ND, NDv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (sorozatonként változik) | Windows Server 2016 vagy <br/>2012 R2 (Azure Piactér) | NVIDIA CUDA vagy CUDA eszközkészlet-illesztőprogramok| N/A | 
| [NV, NVv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 vagy<br/>2012 R2 (Azure Piactér) | NVIDIA GRID illesztőprogramok | N/A |

<sup>*</sup>RDMA-képes N-sorozat méretei is NVIDIA Tesla GPU-k

### <a name="windows-pools---cloud-services-configuration"></a>Windows-készletek – Felhőszolgáltatások konfigurációja

> [!NOTE]
> Az N-sorozat méretei nem támogatottak a Felhőszolgáltatás konfigurációjával rendelkező batch készletekben.
>

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 vagy<br/>2008 R2 (Guest OS család) | Microsoft MPI 2012 R2 vagy újabb, vagy<br/>Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Csomópontközi kommunikáció engedélyezése,<br/> egyidejű feladat végrehajtás letiltása |

## <a name="pool-configuration-options"></a>Készlet konfigurációs beállításai

A batch készlet speciális virtuálisgép-méretének konfigurálásához számos lehetősége van a szükséges szoftverek vagy illesztőprogramok telepítésére:

* A virtuális gép konfigurációjában lévő készletekhez válasszon egy előre konfigurált [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) virtuális géplemezt, amely előtelepített illesztőprogramokkal és szoftverekkel rendelkezik. Példák: 

  * [CentOS-alapú 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - tartalmazza az RDMA illesztőprogramokat és az Intel MPI 5.1-et

  * [Data Science Virtuális gép](../machine-learning/data-science-virtual-machine/overview.md) Linux vagy Windows - tartalmazza az NVIDIA CUDA illesztőprogramokat

  * Linux-lemezképek a Batch tároló számítási feladatokhoz, amelyek gpu- és RDMA-illesztőprogramokat is tartalmaznak:

    * [CentOS (GPU- és RDMA-illesztőprogramokkal) az Azure Batch tárolókészleteihez](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (GPU- és RDMA-illesztőprogramokkal) az Azure Batch tárolókészleteihez](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Hozzon létre egy [egyéni Windows vagy Linux virtuális gép lemezképet,](batch-sig-images.md) amelyre telepítettillesztőprogramot, szoftvert vagy a virtuális gép méretéhez szükséges egyéb beállításokat. 

* Hozzon létre egy Batch [alkalmazáscsomagot](batch-application-packages.md) egy tömörített illesztőprogramból vagy alkalmazástelepítőből, és konfigurálja a Batch-et úgy, hogy a csomagot készletcsomópontokra telepítse, és minden csomópont létrehozásakor egyszer telepítse. Ha például az alkalmazáscsomag telepítő, hozzon létre egy [kezdő feladat](batch-api-basics.md#start-task) parancssorát, amely csendben telepíti az alkalmazást az összes készletcsomópontra. Fontolja meg egy alkalmazáscsomag és egy készlet indítási feladat használatát, ha a munkaterhelés egy adott illesztőprogram-verziótól függ.

  > [!NOTE] 
  > Az indítási feladatnak emelt szintű (rendszergazdai) engedélyekkel kell futnia, és meg kell várnia a sikert. A hosszú ideig futó feladatok növelik a kötegkészlet kiépítésének idejét.
  >

* [A Batch Shipyard](https://github.com/Azure/batch-shipyard) automatikusan konfigurálja a GPU- és RDMA-illesztőprogramokat, hogy az Azure Batch-en átláthatómódon működjenek együtt a tárolóba adott számítási feladatokkal. Batch Hajógyár teljes mértékben hajtott konfigurációs fájlokat. Számos receptkonfiguráció áll rendelkezésre, amelyek lehetővé teszik a GPU és az RDMA számítási feladatokat, például a [CNTK GPU receptet,](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) amely előre konfigurálja a GPU-illesztőprogramokat az N sorozatú virtuális gépeken, és docker-lemezképként tölti be a Microsoft Cognitive Toolkit szoftvert.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Példa: NVIDIA GPU-illesztőprogramok Windows NC virtuálisgép-készleten

A CUDA-alkalmazások Windows NC-csomópontok készletén történő futtatásához telepítenie kell az NVDIA GPU-illesztőprogramokat. A következő mintalépések egy alkalmazáscsomagot használnak az NVIDIA GPU-illesztőprogramok telepítéséhez. Ezt a lehetőséget akkor választhatja, ha a munkaterhelés egy adott GPU-illesztőprogram-verziótól függ.

1. Töltsön le egy telepítőcsomagot a Windows Server 2016 GPU-illesztőprogramjaihoz az [NVIDIA webhelyéről](https://www.nvidia.com/Download/index.aspx) – például a [411.82-es verzióról.](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe) Mentse a fájlt helyileg egy rövid névvel, például *GPUDriverSetup.exe*.
2. Hozzon létre egy zip fájlt a csomagból.
3. Töltse fel a csomagot a Batch-fiókba. Lépésekért tekintse meg az [alkalmazáscsomagok](batch-application-packages.md) útmutatóját. Adjon meg egy alkalmazásazonosítót, például *a GPUDrivert*és egy verziót, például *a 411.82-es*verziót.
1. A Batch API-k vagy az Azure Portal használatával hozzon létre egy készletet a virtuális gép konfigurációjában a kívánt számú csomóponttal és méretezéssel. Az alábbi táblázat az NVIDIA GPU-illesztőprogramok csendes telepítéséhez szükséges mintabeállításokat mutatja be egy indítási feladat használatával:

| Beállítás | Érték |
| ---- | ----- | 
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Ajánlat** | WindowsServer |
| **Sku** | 2016-Adatközpont |
| **Csomópont mérete** | NC6 szabvány |
| **Alkalmazáscsomag-hivatkozások** | GPUDriver, 411.82-es verzió |
| **A feladat indítása engedélyezve** | True (Igaz)<br>**Parancssori** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Felhasználói identitás** - Készlet automatikus felhasználó, rendszergazda<br/>**Várakozás a sikerre** - Igaz

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Példa: NVIDIA GPU-illesztőprogramok Linux NC VM-készleten

A CUDA-alkalmazások Linux NC csomópontok készletén való futtatásához telepítenie kell a szükséges NVIDIA Tesla GPU illesztőprogramokat a CUDA Toolkit-ből. A következő mintalépések egyéni Ubuntu 16.04 LTS-lemezképet hoznak létre és telepítenek a GPU-illesztőprogramokkal:

1. Ubuntu 16.04 LTS-t futtató Azure NC-sorozatú virtuális gép üzembe helyezése. Például hozza létre a virtuális gép az USA déli középső régiójában. 
2. Adja hozzá az [NVIDIA GPU-illesztőprogramok bővítményt](../virtual-machines/extensions/hpccompute-gpu-linux.md
) a virtuális géphez az Azure Portalon, egy ügyfélszámítógépen, amely csatlakozik az Azure-előfizetéshez vagy az Azure Cloud Shellhez. Másik lehetőségként kövesse a lépéseket a virtuális géphez való csatlakozáshoz és a [CUDA-illesztőprogramok manuális telepítéséhez.](../virtual-machines/linux/n-series-driver-setup.md)
3. A lépéseket követve hozzon létre egy [megosztott képtár képet](batch-sig-images.md) batch.
4. Hozzon létre egy Batch-fiókot egy olyan régióban, amely támogatja az NC virtuális gépeket.
5. A Batch API-k vagy az Azure Portal használatával hozzon létre egy [készletet az egyéni lemezkép használatával,](batch-sig-images.md) és a kívánt számú csomópontot és skálázást. Az alábbi táblázat a kép mintakészletének beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni kép** | *A kép neve* |
| **Csomópontügynök Termékváltozat** | batch.node.ubuntu 16.04 |
| **Csomópont mérete** | NC6 szabvány |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Példa: Microsoft MPI Windows H16r virtuálisgép-készleten

A Windows MPI-alkalmazások Azure H16r virtuálisgép-csomópontok készletén való futtatásához konfigurálnia kell a HpcVmDrivers bővítményt, és telepítenie kell a [Microsoft MPI-t.](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) Az alábbiakban az egyéni Windows Server 2016-lemezképek szükséges illesztőprogramokkal és szoftverekkel történő telepítésének lépéseit olvashatja:

1. Telepítsen egy Windows Server 2016 rendszerű Azure H16r virtuális gépet. Például hozza létre a virtuális gép az USA nyugati régiójában. 
2. Adja hozzá a HpcVmDrivers bővítményt a virtuális géphez [egy Azure PowerShell-parancs futtatásával](../virtual-machines/sizes-hpc.md) egy ügyfélszámítógépről, amely csatlakozik az Azure-előfizetéshez, vagy az Azure Cloud Shell használatával. 
1. Távoli asztali kapcsolat létesítése a virtuális géppel.
1. Töltse le a Microsoft MPI legújabb verziójához szükséges [telepítőcsomagot](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe), és telepítse a Microsoft MPI-t.
1. A lépéseket követve hozzon létre egy [megosztott képtár képet](batch-sig-images.md) batch.
1. A Batch API-k vagy az Azure Portal használatával hozzon létre egy [készletet a megosztott képtár használatával,](batch-sig-images.md) és a kívánt számú csomópontot és skálázást. Az alábbi táblázat a kép mintakészletének beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni kép** | *A kép neve* |
| **Csomópontügynök Termékváltozat** | batch.node.windows amd64 |
| **Csomópont mérete** | H16r szabvány |
| **Internode kommunikáció engedélyezve** | True (Igaz) |
| **Feladatok maximális száma csomópontonként** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Példa: Intel MPI Linux H16r virtuálisgép-készleten

MpI-alkalmazások futtatásához linuxos H sorozatú csomópontok készletén az egyik lehetőség a [CentOS-alapú 7.4 HPC-lemezkép](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) használata az Azure Marketplace-en. A Linux RDMA-illesztőprogramok és az Intel MPI előre telepítve vannak. Ez a rendszerkép is támogatja a Docker-tároló számítási feladatok.

A Batch API-k vagy az Azure Portal használatával hozzon létre egy készletet ezzel a lemezképpel és a kívánt számú csomóponttal és méretezéssel. Az alábbi táblázat a mintakészlet beállításait mutatja be:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Ajánlat** | CentOS-HPC |
| **Sku** | 7.4 |
| **Csomópont mérete** | H16r szabvány |
| **Internode kommunikáció engedélyezve** | True (Igaz) |
| **Feladatok maximális száma csomópontonként** | 1 |

## <a name="next-steps"></a>További lépések

* MPI-feladatok futtatásához egy Azure Batch-készleten tekintse meg a [Windows](batch-mpi.md) vagy [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) példák.

* Példák a BATCH-es számítási feladatok a Batch-ben található számítási feladatokról: [A Batch Shipyard](https://github.com/Azure/batch-shipyard/) receptjei.
