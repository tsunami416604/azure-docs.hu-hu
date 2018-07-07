---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 07/06/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 268da0e5d078f7b6b4b36929dbf6755068adb444
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37907174"
---
GPU-optimalizált virtuális gépek méretek a következők specializált virtuális gépek egy vagy több NVIDIA gpu-k használatával érhető el. Ezeket a méreteket képi megjelenítés, nagy számítási igényű és magas grafikai igényű számítási feladatokhoz tervezték. Ez a cikk ismerteti a száma, és a GPU-k, vcpu-k, az adatlemezeket és a hálózati adapter típusa. Tároló átviteli sebesség és a hálózati sávszélesség is szerepelnek az ennél a csoportosításnál méreteire vonatkoztatva. 

* **Hálózati vezérlő, NCv2, az NCv3 és ND** méretek nagy számítási és hálózatigényű alkalmazásokra és algoritmusokra vannak optimalizálva. Néhány példa a CUDA - és OpenCL-alapú alkalmazásokat és szimulációkat, mesterséges Intelligencia és a Deep Learning. 
* **NV** méretek a távoli képi megjelenítés, streamelési, játék, kódolási és VDI-forgatókönyvekhez OpenGL, DirectX és hasonló keretrendszereket használó kialakítva és optimalizálva.  


## <a name="nc-series"></a>NC sorozat

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

NC sorozat virtuális gépei működteti a [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya. Felhasználók is gyorsabb adatokat az energiafeltérképező alkalmazások üzemeltet, összeomlás-szimulációk, ray követett Renderelés, a mély tanulás és egyebek. A NC24r-konfigurációt egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.


| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

## <a name="ncv2-series"></a>NCv2 sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

NCv2 sorozatú virtuális gépek működteti [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) gpu-kkal. Az gpu-k biztosíthat az NC sorozat számítási teljesítményének több mint 2. Ügyfelek kihasználhatja az frissített gpu-k hagyományos HPC számítási feladatokhoz, például a olajfúrás-modellezés, DNS-sel szekvenálás, fehérjeanalízis, Monte Carlo-szimulációk és mások. A NC24rs v2 konfiguráció egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.

> [!IMPORTANT]
> Az ezen virtuálisgépméret-családhoz az előfizetés vCPU-(mag-) kvóta kezdetben értéke 0 minden régióban. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = egy P100 kártya.

*RDMA-kompatibilis

## <a name="ncv3-series"></a>NCv3 sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Az NCv3 sorozatú virtuális gépek működteti [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) gpu-kkal. Az gpu-k 1,5 x az NCv2 sorozat számítási teljesítményt tud biztosítani. Ügyfelek kihasználhatja az frissített gpu-k hagyományos HPC számítási feladatokhoz, például a olajfúrás-modellezés, DNS-sel szekvenálás, fehérjeanalízis, Monte Carlo-szimulációk és mások. A NC24rs v3 konfiguráció egy alacsony késleltetésű, nagy átviteli sebességű hálózati adaptert szorosan összefüggő párhuzamos számítási feladatokhoz biztosít.

> [!IMPORTANT]
> Az ezen virtuálisgépméret-családhoz az előfizetés vCPU-(mag-) kvóta kezdetben értéke 0 minden régióban. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = egy V100 kártya.

*RDMA-kompatibilis

## <a name="nd-series"></a>ND sorozat

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Az ND sorozatú virtuális gépek a GPU-család mesterséges Intelligencia és a Deep Learning számítási feladatokhoz készült új mellett. Kiváló teljesítmény tanuláshoz és következtetésekhez kínálnak. ND példányok működteti [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) gpu-kkal. Ezek a példányok remek teljesítményt egyszeres pontosságú lebegőpontos műveletekhez, AI a Microsoft Cognitive Toolkit, TensorFlow, Caffe és más keretrendszerekhez biztosítanak. Az ND sorozat jóval nagyobb GPU-memóriával rendelkezik (24 GB), így jelentősen nagyobb neurálishálózat-modellekhez is alkalmazható. Az NC sorozathoz hasonlóan az ND sorozat távoli közvetlen memória, egy másodlagos alacsony késleltetésű, nagy átviteli sebességű hálózati konfigurációval kínál és InfiniBand-kapcsolattal nagy méretű betanítási feladatokat futtathatnak több gpu-k futtatásához.

> [!IMPORTANT]
> Ez virtuálisgépméret-családhoz tartozó vCPU-(mag-) kvóta az előfizetésben régiónként kezdetben értéke 0. [Egy vCPU-kvóta növelésére](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [elérhető régióban](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

## <a name="nv-series"></a>NV sorozat

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

Működteti az NV-sorozat virtuális gépei [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu-k és az NVIDIA GRID technológiát kínál a desktopban gyorsított alkalmazások és virtuális asztalok ügyfelek amelyeknél jeleníthetik meg adataikat vagy szimulációikat. Jeleníthetik meg a grafikus kiemelkedő grafikai képesség, és emellett a különálló precíziós számítási feladatokat, például a kódolás és megjelenítési szoftverek futtatása az NV-példányokat a számításigényes munkafolyamatokat, a felhasználók. 

NV-példányokat az egyes GPU rács licenccel rendelkezik. Ez a licenc rugalmasságot biztosít az NV-példány használata virtuális munkaállomás, egy-egy felhasználóhoz, vagy 25 párhuzamos felhasználó csatlakozhat a virtuális gép virtuális alkalmazás esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomásait | A virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.

 
