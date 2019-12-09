---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 82e62b6d0925aa53fc8456addb4732b16e69080b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935911"
---
A GPU-ra optimalizált virtuálisgép-méretek olyan speciális virtuális gépek, amelyek egy vagy több NVIDIA GPU-val érhetők el. Ezek a méretek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek. Ez a cikk a GPU-k, a vCPU, az adatlemezek és a hálózati adapterek számáról és típusáról nyújt információt. A rendszer a tároló átviteli sebességét és a hálózati sávszélességet is tartalmazza a csoportosítás minden egyes méretéhez.

* Az **NC, a NCv2, a NCv3** -méretek nagy számítási igényű és hálózati igényű alkalmazásokhoz és algoritmusokhoz vannak optimalizálva. Ilyenek például a CUDA-és OpenCL-alapú alkalmazások, a szimulációk, az AI-k és a Deep learning. A NCv3 sorozat az NVIDIA Tesla V100 GPU-val rendelkező nagy teljesítményű számítási feladatokra összpontosít. Az NC sorozat az Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell) processzort használja, a NCv2 és a NCv3 sorozatú virtuális gépek pedig az Intel Xeon E5-2690 v4 (Broadwell) processzort használják.

* **ND és NDv2** Az ND sorozat a mélyreható tanuláshoz szükséges képzésekre és következtetésekre összpontosít. Az NVIDIA Tesla P40 GPU és az Intel Xeon E5-2690 v4 (Broadwell) processzort használja. Az NDv2 sorozat az Intel Xeon Platinum 8168 (Skylake) processzort használja.

* Az **NV-és NVv3** -méretek a távoli vizualizációk, streaming-, játék-, kódolási és VDI-forgatókönyvekhez lettek optimalizálva, például az OpenGL és a DirectX használatával.  Ezeket a virtuális gépeket az NVIDIA Tesla M60 GPU támogatja.

* A **NVv4** -méretek a VDI és a távoli vizualizációk számára lettek optimalizálva. A partioned GPU-k esetében a NVv4 a kisebb GPU-erőforrásokat igénylő számítási feladatok számára biztosít megfelelő méretet.  Ezeket a virtuális gépeket az AMD Radeon ösztön MI25 GPU támogatja.


## <a name="nc-series"></a>NC sorozat

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az NC sorozatú virtuális gépeket az [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya és az Intel Xeon E5-2690 v3 (Haswell) processzor látja el. A felhasználók gyorsabban végezhetik el az adatgyűjtést, ha a CUDA használatával hasznosítják az energetikai feltárási alkalmazásokat, az összeomlási szimulációkat, a Ray által követett renderelést, a mély tanulást stb. A NC24r konfiguráció alacsony késésű, nagy átviteli sebességű hálózati adaptert biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

## <a name="ncv2-series"></a>NCv2 sorozat

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Az NCv2 sorozatú virtuális gépeket [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU-k működtetik. Ezek a GPU-k az NC sorozat számítási teljesítményének több mint kétszeresét biztosítják. Az ügyfelek igénybe vehetik a frissített GPU-ket a hagyományos HPC-számítási feladatokhoz, mint például a tározó modellezése, a DNS-szekvencia, a protein-elemzés, a Monte Carlo-szimulációk és egyebek. A GPU-k mellett az Intel Xeon E5-2690 v4 (Broadwell) processzorok is a NCv2 sorozatú virtuális gépeket használják.

A NC24rs v2-konfiguráció alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz.

> [!IMPORTANT]
> Ennél a méretnél az előfizetéshez tartozó vCPU (mag) kvóta kezdetben az egyes régiókban 0 értékre van állítva. [VCPU-kvóta növelésének kérése](../articles/azure-supportability/resource-manager-core-quotas-request.md) a család számára egy [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Temp Storage (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = egy P100 kártya.

*RDMA-kompatibilis

## <a name="ncv3-series"></a>NCv3 sorozat

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Az NCv3 sorozatú virtuális gépeket [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU-k működtetik. Ezek a GPU-k 1,5 x-t biztosítanak a NCv2 sorozat számítási teljesítményéhez. Az ügyfelek igénybe vehetik a frissített GPU-ket a hagyományos HPC-számítási feladatokhoz, mint például a tározó modellezése, a DNS-szekvencia, a protein-elemzés, a Monte Carlo-szimulációk és egyebek. Az NC24rs v3 konfiguráció alacsony késésű, nagy átviteli sebességű hálózati adaptert biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz. A GPU-k mellett az Intel Xeon E5-2690 v4 (Broadwell) processzorok is a NCv3 sorozatú virtuális gépeket használják.

> [!IMPORTANT]
> Ennél a méretnél az előfizetéshez tartozó vCPU (mag) kvóta kezdetben az egyes régiókban 0 értékre van állítva. [VCPU-kvóta növelésének kérése](../articles/azure-supportability/resource-manager-core-quotas-request.md) a család számára egy [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Temp Storage (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = egy V100 kártya.

*RDMA-kompatibilis

## <a name="ndv2-series-preview"></a>NDv2 sorozat (előzetes verzió)

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

InfiniBand: nem támogatott

A NDv2 sorozatú virtuális gép a HPC-, AI-és gépi tanulási munkaterhelések igényeihez tervezett GPU-család új bővítménye. A szolgáltatás 8 NVIDIA Tesla V100 NVLINK összekapcsolt GPU-k és 40 Intel Xeon Platinum 8168 (Skylake) maggal és 672 GiB rendszermemóriával rendelkezik. Az NDv2-példányok kitűnő FP32- és FP64-teljesítmény nyújtanak egyebek között a Cuda, a TensorFlow, a Pytorch, a Caffe keretrendszereket használó HPC és AI számítási feladatokhoz.

Az előzetes verzió használatakor [regisztráljon, és máris hozzáférhet ezekhez a gépekhez](https://aka.ms/ndv2signup).
<br>

| Méret | vCPU | Memória: GiB | Temp Storage (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati sávszélesség | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40s_v2 | 40 | 672 | 2948 | 8 V100 (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>ND sorozat

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Az ND sorozatú Virtual Machines az AI-hoz készült GPU-család, valamint a Deep learning számítási feladatainak egy új kiegészítése. Kiváló teljesítményt nyújtanak a képzéshez és a következtetésekhez. Az ND-példányokat az [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU-k és az Intel Xeon E5-2690 v4 (Broadwell) processzorok működtetik. Ezek a példányok kiváló teljesítményt biztosítanak az egyszeres pontosságú lebegőpontos műveletekhez, a Microsoft Cognitive Toolkit, TensorFlow, Cafe és más keretrendszereket használó AI-munkaterhelésekhez. Az ND sorozat jóval nagyobb GPU-memóriával rendelkezik (24 GB), így jelentősen nagyobb neurálishálózat-modellekhez is alkalmazható. Az NC sorozathoz hasonlóan az ND sorozat egy olyan konfigurációt kínál, amely egy másodlagos, kis késleltetésű, nagy átviteli sebességű hálózattal rendelkezik a RDMA-on keresztül, és InfiniBand a kapcsolatot, így nagy léptékű, több GPU-ra kiterjedő képzési feladatokat is futtathat.

> [!IMPORTANT]
> Ennél a méretnél az előfizetéshez tartozó régiónként az vCPU (Core) kvóta beállítása kezdetben 0. [VCPU-kvóta növelésének kérése](../articles/azure-supportability/resource-manager-core-quotas-request.md) a család számára egy [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

## <a name="nv-series"></a>NV sorozat

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az NV-sorozatú virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és az NVIDIA Grid Technology működteti az asztali gyorsított alkalmazások és virtuális asztalok számára, amelyekben az ügyfelek képesek megjeleníteni az adatvagy szimulációkat. A felhasználók képesek megjeleníteni az NV-példányokon elérhető grafikai igényű munkafolyamatokat, így kiváló grafikai képességeket szerezhetnek, és emellett egyetlen pontosságú számítási feladatot is futtathatnak, például a kódolást és a renderelést. Az NV sorozatú virtuális gépeket az Intel Xeon E5-2690 v3 (Haswell) processzorok is működtetik.

Az NV-példányokban minden GPU egy RÁCSos licenccel rendelkezik. Ez a licenc lehetővé teszi, hogy az NV-példányokat virtuális munkaállomásként használja egyetlen felhasználó számára, vagy 25 egyidejű felhasználó csatlakozhat a virtuális GÉPHEZ egy virtuális alkalmazási forgatókönyv esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.

## <a name="nvv3-series--sup1sup"></a>NVv3 – <sup>1</sup> . sorozat

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

A NVv3 sorozatú virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és az NVIDIA Grid Technology működteti Intel E5-2690 v4 (Broadwell) processzorokkal. Ezek a virtuális gépek a GPU-gyorsított grafikus alkalmazásokat és virtuális asztalokat célozzák meg, ahol az ügyfelek szeretnék megjeleníteni az adatokat, szimulálni az eredményeket a megtekintésre, a CAD-re való munkavégzésre vagy a tartalmak megjelenítésére és továbbítására. Ezenkívül egyszeres pontosságú számítási feladatok is futtathatók az ilyen gépeken, például kódolás és renderelés. A NVv3 Virtual Machines támogatja a Premium Storaget, és a korábbi NV-sorozattal összehasonlítva kétszer a rendszermemóriát (RAM) is elérheti.  

A NVv3-példányok minden GPU-je tartalmaz egy RÁCSos licencet. Ez a licenc lehetővé teszi, hogy az NV-példányokat virtuális munkaállomásként használja egyetlen felhasználó számára, vagy 25 egyidejű felhasználó csatlakozhat a virtuális GÉPHEZ egy virtuális alkalmazási forgatókönyv esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = fél M60 kártya.

<sup>1</sup> a NVv3 sorozatú virtuális gépek Intel Hyper-Threading technológiával rendelkeznek

## <a name="nvv4-series-preview--sup1sup"></a>NVv4 sorozat (előzetes verzió) <sup>1</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

A NVv4 sorozatú virtuális gépeket az [AMD Radeon ösztön MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-k és az AMD EPYC 7V12 (Róma) CPU-k működtetik. A NVv4 sorozatú Azure a részleges GPU-val rendelkező virtuális gépek bevezetését ismerteti. Válassza ki a megfelelő méretű virtuális gépet GPU-gyorsított grafikus alkalmazások és virtuális asztalok számára a GPU-k 1/8-étől kezdődően 2 GiB frame-pufferrel egy teljes GPU-val, 16 GiB frame-pufferrel. A NVv4 Virtual Machines jelenleg csak a Windows vendég operációs rendszert támogatja.

Az előzetes verzió használatakor [regisztráljon, és máris hozzáférhet ezekhez a gépekhez](https://aka.ms/nvv4signup).
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> a NVv4 sorozatú virtuális gépek AMD szimultán többszálú technológiával rendelkeznek

