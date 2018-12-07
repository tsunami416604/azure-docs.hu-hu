---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: bc311ca5c49584ae3715fa4507e1fd678bcc93bb
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53007946"
---
GPU-optimalizált virtuális gépek méretek a következők specializált virtuális gépek egy vagy több NVIDIA gpu-k használatával érhető el. Ezeket a méreteket képi megjelenítés, nagy számítási igényű és magas grafikai igényű számítási feladatokhoz tervezték. Ez a cikk ismerteti a száma, és a GPU-k, vcpu-k, az adatlemezeket és a hálózati adapter típusa. Tároló átviteli sebesség és a hálózati sávszélesség is szerepelnek az ennél a csoportosításnál méreteire vonatkoztatva. 

* **Hálózati vezérlő, NCv2, az NCv3, ND és NDv2** méretek nagy számítási és hálózatigényű alkalmazásokra és algoritmusokra vannak optimalizálva. Néhány példa a CUDA - és OpenCL-alapú alkalmazásokat és szimulációkat, mesterséges Intelligencia és a Deep Learning. Az NCv3 sorozat a nagy teljesítményű számítási feladatokhoz, amely NVIDIA Tesla V100 GPU összpontosít.  Az ND sorozat a Deep Learning képzési és következtetéses forgatókönyveihez lett kialakítva. NVIDIA Tesla P40 GPU-val van felszerelve.
* **NV és NVv2** méretek a távoli képi megjelenítés, streamelési, játék, kódolási és VDI-forgatókönyvekhez OpenGL, DirectX és hasonló keretrendszereket használó kialakítva és optimalizálva.  Ezek a virtuális gépek az NVIDIA Tesla M60 GPU élvezik.


## <a name="nc-series"></a>NC sorozat

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

NC sorozat virtuális gépei működteti a [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya. Felhasználók is gyorsabb adatokat az energiafeltérképező alkalmazások üzemeltet, összeomlás-szimulációk, ray követett Renderelés, a mély tanulás és egyebek. A NC24r-konfigurációt egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.


| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 8 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 16 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 32 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 32 | 64 | 4 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

## <a name="ncv2-series"></a>NCv2 sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

NCv2 sorozatú virtuális gépek működteti [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) gpu-kkal. Az gpu-k biztosíthat az NC sorozat számítási teljesítményének több mint 2. Ügyfelek kihasználhatja az frissített gpu-k hagyományos HPC számítási feladatokhoz, például a olajfúrás-modellezés, DNS-sel szekvenálás, fehérjeanalízis, Monte Carlo-szimulációk és mások. A NC24rs v2 konfiguráció egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.

> [!IMPORTANT]
> Az ezen virtuálisgépméret-családhoz az előfizetés vCPU-(mag-) kvóta kezdetben értéke 0 minden régióban. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = egy P100 kártya.

*RDMA-kompatibilis

## <a name="ncv3-series"></a>NCv3 sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Az NCv3 sorozatú virtuális gépek működteti [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) gpu-kkal. Az gpu-k 1,5 x az NCv2 sorozat számítási teljesítményt tud biztosítani. Ügyfelek kihasználhatja az frissített gpu-k hagyományos HPC számítási feladatokhoz, például a olajfúrás-modellezés, DNS-sel szekvenálás, fehérjeanalízis, Monte Carlo-szimulációk és mások. A NC24rs v3 konfiguráció egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.

> [!IMPORTANT]
> Az ezen virtuálisgépméret-családhoz az előfizetés vCPU-(mag-) kvóta kezdetben értéke 0 minden régióban. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = egy V100 kártya.

*RDMA-kompatibilis

## <a name="ndv2-series-preview"></a>NDv2 sorozat (előzetes verzió)


A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Infiniband: Nem támogatott.


NDv2 sorozatú virtuális gép egy újdonága a HPC, mesterséges Intelligencia és machine learning-munkaterhelések igényeinek megfelelően kialakított GPU-család. 8 összekapcsolt NVIDIA Tesla V100 NVLINK GPUt és 40 Intel Skylake magot tartalmaznak, ehhez 672 GiB rendszermemória párosul. Az NDv2-példányok kitűnő FP32- és FP64-teljesítmény nyújtanak egyebek között a Cuda, a TensorFlow, a Pytorch, a Caffe keretrendszereket használó HPC és AI számítási feladatokhoz.

[Regisztráljon, és hozzáférhet ezek a gépek előzetes verzió ideje alatt](https://aka.ms/ndv2signup).
<br>


| Méret              | vCPU | GPU              | Memory (Memória)  | Hálózati adapterek (max) | Legfeljebb Lemezméret           | Legfeljebb az adatlemezeket (egyenként 1023 GB) | Maximális sávszélesség | 
|-------------------|-------------|-------------------|--------|------------------|---------|------------|--------------------------|--------------------|--------------------------------|-----------------------------------------|-----------------------|------------|
| Standard_ND40s_v2 | 40     | 8 V100 (NVlilnk) | 672 GB | 8          | Ideiglenes 1344 / 2948XIO | 32    | 24,000 MB/s             | 

## <a name="nd-series"></a>ND sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Az ND sorozatú virtuális gépek, és a mesterséges Intelligencia, Deep Learning számítási feladatokhoz készült a GPU-termékcsalád új mellett. Kiváló teljesítmény tanuláshoz és következtetésekhez kínálnak. ND példányok működteti [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) gpu-kkal. Ezek a példányok remek teljesítményt egyszeres pontosságú lebegőpontos műveletekhez, AI a Microsoft Cognitive Toolkit, TensorFlow, Caffe és más keretrendszerekhez biztosítanak. Az ND sorozat jóval nagyobb GPU-memóriával rendelkezik (24 GB), így jelentősen nagyobb neurálishálózat-modellekhez is alkalmazható. Az NC sorozathoz hasonlóan az ND sorozat távoli közvetlen memória, egy másodlagos alacsony késleltetésű, nagy átviteli sebességű hálózati konfigurációval kínál és InfiniBand-kapcsolattal nagy méretű betanítási feladatokat futtathatnak több gpu-k futtatásához.

> [!IMPORTANT]
> Ez virtuálisgépméret-családhoz tartozó vCPU-(mag-) kvóta az előfizetésben régiónként kezdetben értéke 0. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

## <a name="nv-series"></a>NV sorozat

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

Működteti az NV-sorozat virtuális gépei [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu-k és az NVIDIA GRID technológiát kínál a desktopban gyorsított alkalmazások és virtuális asztalok ügyfelek amelyeknél jeleníthetik meg adataikat vagy szimulációikat. Jeleníthetik meg a grafikus kiemelkedő grafikai képesség, és emellett a különálló precíziós számítási feladatokat, például a kódolás és megjelenítési szoftverek futtatása az NV-példányokat a számításigényes munkafolyamatokat, a felhasználók. 

NV-példányokat az egyes GPU rács licenccel rendelkezik. Ez a licenc rugalmasságot biztosít az NV-példány használata virtuális munkaállomás, egy-egy felhasználóhoz, vagy 25 párhuzamos felhasználó csatlakozhat a virtuális gép virtuális alkalmazás esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomásait | A virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.

## <a name="nvv2-series-preview"></a>NVv2 sorozat (előzetes verzió)

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

A NVv2-sorozat virtuális gépei által kezelt [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu-k és az NVIDIA GRID technológia és az Intel Broadwell processzorokat. Ezek a virtuális gépek célozzák meg GPU gyorsított grafikai alkalmazásnál, és virtuális asztalok, ahol az ügyfelek szeretnék megjelenítheti az adatokat, szimulálása eredmények megtekintése érdekében CAD, vagy a renderelési és a stream tartalmát. Ezenkívül egyszeres pontosságú számítási feladatok is futtathatók az ilyen gépeken, például kódolás és renderelés. NVv2 virtuális gépek Premium Storage támogatja, és kapható kétszer a rendszer memória (RAM), az NV-sorozat elődjéhez képest.  

Minden egyes GPU NVv2 esetekben rács licencet tartalmaz. Ez a licenc rugalmasságot biztosít az NV-példány használata virtuális munkaállomás, egy-egy felhasználóhoz, vagy 25 párhuzamos felhasználó csatlakozhat a virtuális gép virtuális alkalmazás esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Grafikus Processzor-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomásait | A virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = fél M60 kártya.

 
