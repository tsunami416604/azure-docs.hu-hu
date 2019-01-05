---
title: Nagy számítási igényű Azure virtuális gépek használata a Batch-Csel |} A Microsoft Docs
description: Hogyan igénybe HPC- és GPU VM méretek az Azure Batch-készletek
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 3c3d534392431e79feabe37fe940ea87f586c660
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051696"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Rdma-t vagy a GPU-példányt használja a Batch-készletekben

Bizonyos kötegelt feladatok végrehajtásához, kihasználhatja az Azure Virtuálisgép-méretek nagy méretű számítási feladatokra tervezve. Példa:

* Több példány futtatásához [MPI számítási feladatok](batch-mpi.md), válassza ki a H-sorozatú, illetve más méretek, amely rendelkezik hálózati adapterrel a távoli közvetlen memória-hozzáférés (RDMA). Ezeket a méreteket a csomópontok közötti kommunikáció, amely meggyorsíthatja a MPI-alkalmazások egy InfiniBand hálózathoz csatlakozni. 

* CUDA-alkalmazások esetén válassza ki az N-sorozat méretei, beleértve az NVIDIA Tesla grafikus (GPU) egység kártyák processzor.

Ez a cikk útmutatást és példákat, használhatja a speciális méretek az Azure Batch-készletekben. Adatait tartalmazza, és a háttérben lásd:

* Nagy teljesítményű számítási Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-kompatibilis Virtuálisgép-méretek ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Egyes Virtuálisgép-méretek nem érhetők el a régióban, ahol létrehozhatja a Batch-fiókok. Ellenőrizze, hogy elérhető-e méret, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/) és [válassza ki a virtuális gép méretét a Batch-készlet](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Függőségek

Az rdma-t vagy GPU képességeit a Batch számításigényes méretek csak egyes operációs rendszerek támogatottak. (A támogatott operációs rendszerek listája az egy részét az ezeket a méreteket létrehozott virtuális gépek esetében támogatott.) Attól függően, hogyan hoz létre a Batch-készlet szüksége lehet telepíteni, vagy további illesztőprogram vagy más szoftver konfigurálása a csomópontokon. A következő táblázat összefoglalja a függőségek. Tekintse meg a hivatkozott cikkek nyújtanak bővebb részletekért. Beállítások konfigurálása a Batch-készletek lásd: a cikk későbbi részében.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-készletek – virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, vagy<br/>CentOS-alapú HPC<br/>(Az azure Marketplace-en) | Az Intel MPI 5<br/><br/>Linuxos RDMA-illesztőprogramok | Engedélyezze a csomópontok közötti kommunikáció, tiltsa le az egyidejű feladat a végrehajtás |
| [Hálózati vezérlő által, az NCv2, az NCv3, NDv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | Az NVIDIA Tesla GPU (eltérő sorozat) | Ubuntu 16.04 LTS, vagy<br/>CentOS 7.3 vagy 7.4<br/>(Az azure Marketplace-en) | NVIDIA CUDA vagy CUDA eszközkészlet illesztőprogramok | – | 
| [NV, NVv2 sorozat](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, vagy<br/>CentOS 7.3.<br/>(Az azure Marketplace-en) | Az NVIDIA GRID illesztőprogramok | – |
<sup>*</sup>RDMA-kompatibilis N-sorozat méretei is tartalmazhat az NVIDIA Tesla gpu-k

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-készletek – virtuálisgép-konfiguráció

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | A Windows Server 2016-ban, 2012 R2-ben, vagy<br/>2012 (az azure Marketplace-en) | A Microsoft MPI 2012 R2 vagy újabb, vagy<br/> Az Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Engedélyezze a csomópontok közötti kommunikáció, tiltsa le az egyidejű feladat a végrehajtás |
| [Hálózati vezérlő, NCv2, az NCv3, ND, NDv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | Az NVIDIA Tesla GPU (eltérő sorozat) | A Windows Server 2016-ban, vagy <br/>2012 R2 (az Azure Marketplace-en) | NVIDIA CUDA vagy CUDA eszközkészlet illesztőprogramok| – | 
| [NV, NVv2 sorozat](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | A Windows Server 2016-ban, vagy<br/>2012 R2 (az Azure Marketplace-en) | Az NVIDIA GRID illesztőprogramok | – |
<sup>*</sup>RDMA-kompatibilis N-sorozat méretei is tartalmazhat az NVIDIA Tesla gpu-k

### <a name="windows-pools---cloud-services-configuration"></a>Windows-készletek – Cloud services-konfiguráció

> [!NOTE]
> Az N-sorozat méretei nem támogatottak a Felhőszolgáltatás-konfigurációval a Batch-készletekben.
>

| Méret | Képesség | Operációs rendszerek | Szükséges szoftverek | Készlet beállításai |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, a9-es](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | A Windows Server 2016, 2012 R2, 2012, vagy<br/>2008 R2 (vendég operációs rendszerek) | A Microsoft MPI 2012 R2 vagy újabb, vagy<br/>Az Intel MPI 5<br/><br/>Windows RDMA-illesztőprogramok | Csomópontok közötti kommunikáció,<br/> egyidejű feladat a végrehajtás letiltása |

## <a name="pool-configuration-options"></a>Készlet konfigurációs lehetőségek

Speciális Virtuálisgép-méretét a Batch-készlet konfigurálásához szükséges szoftverek és -illesztőprogramok telepítése számos lehetősége van:

* A virtuális gép konfigurációs készletek, válasszon egy előre konfigurált [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/) Virtuálisgép-rendszerképet, amely az illesztőprogramok és szoftverek előre telepítve van. Példák: 

  * [7.4-es HPC centOS-alapú](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) -RDMA-illesztőprogramok és az Intel MPI 5.1

  * [Adatelemző virtuális gép](../machine-learning/data-science-virtual-machine/overview.md) Linux vagy Windows - NVIDIA CUDA illesztőprogramok tartoznak.

  * GPU- és RDMA illesztőprogramokat is tartalmazó kötegelt tárolóalapú számítási feladatok Linux-rendszerképeket:

    * [Az Azure Batch-készletekben tárolóban (a GPU- és RDMA-illesztőprogramok) centOS](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (GPU- és RDMA-illesztőprogramok) for Azure Batch-készletekben tároló](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Hozzon létre egy [egyéni Windows vagy Linux rendszerű virtuális gép rendszerkép](batch-custom-images.md) amely van telepítve, illesztőprogramok, szoftver- vagy más Virtuálisgép-méret megadása kötelező. 

* Hozzon létre egy Batch [alkalmazáscsomag](batch-application-packages.md) , a zip- vagy az alkalmazástelepítő, és konfigurálhatja a Batch-készlet csomópontjainak csomagok telepítését és telepítése után az egyes csomópontok létrehozásakor. Például ha az alkalmazáscsomag-telepítő, hozzon létre egy [kezdő tevékenység](batch-api-basics.md#start-task) parancssort az alkalmazás csendes telepítéséhez a készlet minden csomópontján. Fontolja meg egy alkalmazáscsomagot, és a egy készlet indítási tevékenységet, ha a számítási feladat egy adott illesztőprogram verziója függ.

  > [!NOTE] 
  > Az indítási tevékenység emelt szintű (rendszergazdai) engedélyekkel kell futtatni, és azt meg kell várnia a sikeres. Hosszan futó feladatokat megnöveli a Batch-készlet kiépítése az idő.
  >

* [Batch-hajógyárnak](https://github.com/Azure/batch-shipyard) automatikusan konfigurálja az Azure Batch a tárolóalapú számítási feladatokra transzparens módon működik a GPU- és RDMA illesztőprogramokat. Batch hajógyárnak teljes egészében a konfigurációs fájlokat az alkalmazások. Nincsenek sok minta recept konfiguráció érhető el, mint például a GPU- és RDMA feladatok futtatását teszik lehetővé a [CNTK GPU recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) amely előre GPU-illesztőprogramokat N-sorozatú virtuális gépeken, és betölti a Docker-rendszerkép a Microsoft Cognitive Toolkit szoftver.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Példa: A Windows hálózati vezérlő virtuális gép készlet NVIDIA GPU-illesztők

Windows NC-csomópontok készletét a CUDA-alkalmazások futtatásához kell NVDIA GPU-illesztők telepítése. Az alábbi minta lépéseket alkalmazáscsomag telepítéséhez az NVIDIA GPU-illesztőprogramokat használja. Ezt a lehetőséget választhatja azt is, ha a számítási feladat egy adott GPU illesztőprogramjának verziószámát függ.

1. A GPU-illesztőprogramokat, a Windows Server 2016-ot egy telepítési csomagjának letöltése a [NVIDIA webhely](https://www.nvidia.com/Download/index.aspx) – például [verzió 411.82](http://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Mentse a fájlt helyileg a például egy rövid nevet használó *GPUDriverSetup.exe*.
2. Hozzon létre a csomag zip-fájlban.
3. A csomag feltöltése a Batch-fiókhoz. Útmutató: a [alkalmazáscsomagok](batch-application-packages.md) útmutatást. Adjon meg egy alkalmazásazonosítót például *GPUDriver*, és a egy verziót például *411.82*.
1. A Batch API-k vagy az Azure portal használatával, hoz létre egy készletet a kívánt számú csomópontot és a méretezési csoport virtuálisgép-konfiguráció. Az alábbi táblázatban látható minta beállítások csendes használatával az indítási tevékenység NVIDIA GPU-illesztők telepítése:

| Beállítás | Érték |
| ---- | ----- | 
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Közzétevő** | MicrosoftWindowsServer |
| **Ajánlat** | WindowsServer |
| **Termékváltozat** | 2016-Datacenter |
| **A csomópont mérete** | NC6 Standard |
| **Alkalmazáscsomag-hivatkozások** | GPUDriver, 411.82 verzió |
| **Kezdő tevékenység engedélyezve** | True (Igaz)<br>**Parancssor** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Felhasználói identitás** -készlet automatikus felhasználó, rendszergazda<br/>**Várjon, amíg a sikeres** – igaz

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Példa: Egy Linux rendszerű NC virtuális gép készlet NVIDIA GPU-illesztők

Linux NC-csomópontok készletét a CUDA-alkalmazások futtatásához szüksége a CUDA-eszközkészlet telepítése szükséges az NVIDIA Tesla GPU-illesztőprogramokat. Az alábbi minta lépéseket hozzon létre, és a GPU-illesztőprogramokat az egyéni Ubuntu 16.04 LTS rendszerképet:

1. Egy Azure NC sorozatú virtuális gép, Ubuntu 16.04 LTS rendszert futtató üzembe helyezéséhez. Például hozzon létre a virtuális gép az USA déli középső régióban. 
2. Adja hozzá a [NVIDIA GPU-illesztők bővítmény](../virtual-machines/extensions/hpccompute-gpu-linux.md
) a virtuális géphez az Azure portal használatával, egy ügyfélszámítógép, amely csatlakozik az Azure-előfizetés vagy az Azure Cloud Shell. Másik lehetőségként kövesse a virtuális Géphez való csatlakozáshoz és [CUDA illesztőprogramok telepítése](../virtual-machines/linux/n-series-driver-setup.md) manuálisan.
3. A lépésekkel hozzon létre egy [pillanatkép- és Linux rendszerű virtuális gép egyéni rendszerkép](batch-custom-images.md) Batch.
4. Hozzon létre egy Batch-fiókot, amely támogatja a hálózati vezérlő virtuális gépek egy régióban.
5. A Batch API-k vagy az Azure portal használatával hoz létre egy készletet [az egyéni rendszerkép használatával](batch-custom-images.md) és a kívánt számú csomópontot és a méretezési csoport. Az alábbi táblázatban látható minta tárolókészlet beállításainak a lemezkép:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni rendszerkép** | *A lemezkép nevét* |
| **Csomóponti ügynök SKU** | Batch.node.ubuntu 16.04 |
| **A csomópont mérete** | NC6 Standard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Példa: A Microsoft MPI egy Windows H16r Virtuálisgép-készlet

Windows MPI-alkalmazások futtatásához az Azure H16r Virtuálisgép-csomópontok készletét a HpcVmDrivers bővítmény konfigurálásáról és telepítéséről szüksége [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi). Az alábbiakban a minta egy egyéni Windows Server 2016 rendszerképet a szükséges illesztőprogramok és szoftverek telepítésének lépései:

1. A Windows Server 2016-ot futtató Azure H16r virtuális gép üzembe helyezése. Például hozzon létre a virtuális gép az USA nyugati régióban. 
2. Adja hozzá a HpcVmDrivers bővítményt a virtuális gép által [egy Azure PowerShell-parancs futtatása](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
) számítógépről, amely csatlakozik az Azure-előfizetéshez, vagy az Azure Cloud Shell használatával. 
1. Távoli asztali kapcsolat létrehozása a virtuális géphez.
1. Töltse le a [telepítőcsomagját](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) a Microsoft MPI, és telepítse a Microsoft MPI legújabb verzióját.
1. A lépésekkel hozzon létre egy [pillanatkép és a Windows virtuális gép egyéni rendszerkép](batch-custom-images.md) Batch.
1. A Batch API-k vagy az Azure portal használatával hoz létre egy készletet [az egyéni rendszerkép használatával](batch-custom-images.md) és a kívánt számú csomópontot és a méretezési csoport. Az alábbi táblázatban látható minta tárolókészlet beállításainak a lemezkép:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Egyéni rendszerkép |
| **Egyéni rendszerkép** | *A lemezkép nevét* |
| **Csomóponti ügynök SKU** | batch.node.windows amd64 |
| **A csomópont mérete** | H16r Standard |
| **Csomópontok közötti kommunikáció engedélyezve** | True (Igaz) |
| **Csomópontonkénti tevékenységek maximális száma** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Példa: Az Intel MPI egy Linuxos H16r készlet

MPI-alkalmazások futtatására egy készlet Linuxos H-sorozatú csomópontokat, az egyik lehetőség az használatára, a [7.4-es HPC CentOS-alapú](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) rendszerképet az Azure Marketplace-ről. Linuxos RDMA-illesztőprogramok és az Intel MPI előtelepített. Ez a rendszerkép Docker tárolóalapú számítási feladatok is támogatja.

A Batch API-k vagy az Azure portal használatával hoz létre egy készletet, használja ezt a lemezképet és a kívánt számú csomópontot és a méretezési csoport. Az alábbi táblázat a minta-készlet beállításai:

| Beállítás | Érték |
| ---- | ---- |
| **Rendszerkép típusa** | Marketplace (Linux/Windows) |
| **Közzétevő** | OpenLogic |
| **Ajánlat** | CentOS-HPC |
| **Termékváltozat** | 7.4 |
| **A csomópont mérete** | H16r Standard |
| **Csomópontok közötti kommunikáció engedélyezve** | True (Igaz) |
| **Csomópontonkénti tevékenységek maximális száma** | 1 |

## <a name="next-steps"></a>További lépések

* MPI feladatok futtatása Azure Batch-készlet, tekintse meg a [Windows](batch-mpi.md) vagy [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) példákat.

* A GPU számítási feladatok Batch példákért lásd a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard/) receptek.