---
title: Azure VM-méretek – GPU | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző GPU-optimalizált méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: bbd81730ea822381f1d0f0e15011fb4ed0c29e64
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735914"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-ra optimalizált virtuálisgép-méretek

A GPU-ra optimalizált virtuálisgép-méretek olyan speciális virtuális gépek, amelyek egyetlen, több vagy tört GPU-val érhetők el. Ezek a méretek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek. Ez a cikk a GPU-k, a vCPU, az adatlemezek és a hálózati adapterek számáról és típusáról nyújt információt. A rendszer a tároló átviteli sebességét és a hálózati sávszélességet is tartalmazza a csoportosítás minden egyes méretéhez.

- Az [NC sorozat](nc-series.md), a [NCv2 sorozat](ncv2-series.md), a [NCv3-](ncv3-series.md) sorozat mérete nagy számítási igényű és hálózati igényű alkalmazásokhoz és algoritmusokhoz van optimalizálva. Ilyenek például a CUDA és a OpenCL-alapú alkalmazások, a szimulációk, az AI és a Deep learning. A NCv3 sorozat az NVIDIA Tesla V100 GPU-val rendelkező nagy teljesítményű számítási feladatokra összpontosít. Az NC sorozat az Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell) processzort használja, a NCv2 és a NCv3 sorozatú virtuális gépek pedig az Intel Xeon E5-2690 v4 (Broadwell) processzort használják.

- Az [ND sorozat](nd-series.md)és a [NDv2-sorozat](ndv2-series.md) méretei a mélyreható tanuláshoz szükséges képzésekre és következtetésekre összpontosítanak. Az NVIDIA Tesla P40 GPU és az Intel Xeon E5-2690 v4 (Broadwell) processzort használják. Az NDv2 sorozat az Intel Xeon Platinum 8168 (Skylake) processzort használja.

- Az [NV-sorozat](nv-series.md) és a [NVv3-sorozat](nvv3-series.md) méretei a távoli vizualizációk, streaming-, játék-, kódolási és VDI-forgatókönyvekhez lettek optimalizálva, olyan keretrendszerek használatával, mint például az OpenGL és a DirectX. Ezeket a virtuális gépeket az NVIDIA Tesla M60 GPU támogatja.

- [NVv4 sorozat](nvv4-series.md) A virtuális gépek és a távoli vizualizációk számára optimalizált és tervezett virtuálisgép-méretek. A particionált GPU-k esetében a NVv4 a kisebb GPU-erőforrásokat igénylő számítási feladatok számára biztosít megfelelő méretet. Ezeket a virtuális gépeket az AMD Radeon ösztön MI25 GPU támogatja. A NVv4 virtuális gépek jelenleg csak a Windows vendég operációs rendszert támogatják.

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA vagy az AMD GPU-illesztőprogramokat.

- Az NVIDIA GPU-k által támogatott virtuális gépek esetén az [NVIDIA GPU illesztőprogram-bővítmény](/azure/virtual-machines/extensions/hpccompute-gpu-windows) telepíti a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](/azure/virtual-machines/extensions/hpccompute-gpu-windows) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](/azure/virtual-machines/extensions/overview).

   Azt is megteheti, hogy manuálisan telepíti az NVIDIA GPU-illesztőprogramokat. Lásd: [NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű n sorozatú virtuális gépeken](/azure/virtual-machines/windows/n-series-driver-setup) , illetve az [NVIDIA GPU-illesztőprogramok telepítése az n sorozatú virtuális gépeken Linux rendszerű](/azure/virtual-machines/linux/n-series-driver-setup) , támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések esetén.

- Az AMD GPU-k által támogatott virtuális gépek esetén lásd: az [AMD GPU-illesztőprogramok telepítése az N sorozatú virtuális gépeken](/azure/virtual-machines/windows/n-series-amd-driver-setup) , amelyeken a Windows operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések futnak.

## <a name="deployment-considerations"></a>Telepítési szempontok

- Az N sorozatú virtuális gépek elérhetőségét lásd: [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/).

- Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben helyezhetők üzembe.

- Az N sorozatú virtuális gépek különböznek a lemezük által támogatott Azure Storage-típustól. Az NC-és NV-alapú virtuális gépek csak a standard Disk Storage (HDD) által támogatott VM-lemezeket támogatják. A NCv2, a NCv3, az ND, a NDv2 és a NVv2 virtuális gépek csak a Premium Disk Storage (SSD) által támogatott virtuálisgép-lemezeket támogatják.

- Ha több N sorozatú virtuális gépet szeretne üzembe helyezni, vegye fontolóra az utólagos elszámolású előfizetést vagy más vásárlási lehetőséget. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- Előfordulhat, hogy az Azure-előfizetésében meg kell emelni a magok kvótáját (régiónként), és meg kell emelni az NC, a NCv2, a NCv3, az ND, a NDv2, az NV vagy a NVv2 magok külön kvótáját. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
