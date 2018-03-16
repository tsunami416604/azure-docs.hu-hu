---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/05/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 96826b2f8acd579cbfe30f2e524d94ce4867df30
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
GPU optimalizált VM értékek speciális virtuális gépek egy vagy több NVIDIA Feldolgozóegységekkel érhető el. Ezek méretek számítási igényű grafikai igényű és a képi megjelenítés munkaterhelések készültek. Ez a cikk tájékoztatást ad azokról a számát és típusát Feldolgozóegységekkel, Vcpu, adatlemezek, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség megadása ennél a csoportosításnál minden méretét. 

* **Hálózati vezérlő által, NCv2, NCv3 és ND** méretek számítási igényű és hálózati igényű alkalmazásokat és algoritmusok, például CUDA - és OpenCL-alapú alkalmazások és szimulációja, AI és részletes tanulási vannak optimalizálva. 
* **Portok HV** méretek optimalizált és távoli képi megjelenítés adatfolyam-, játék, kódolás és keretrendszerek, például a OpenGL és DirectX okhoz VDI-forgatókönyvek készült.  


## <a name="nc-series"></a>NC sorozat

NC sorozatú virtuális gépek szerint vannak kapcsolva a [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya. Felhasználók is gyorsabb adatok között, ami CUDA energia feltárása alkalmazások crunch, szimulációja összeomlás, ray követett megjelenítési, mély tanulási és egyebek. A NC24r konfiguráció egy kis késleltetésű számítástechnikai munkaterhelések szorosan összekapcsolt párhuzamos optimalizálva nagy átviteli hálózati adapter biztosít.


| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

## <a name="ncv2-series"></a>NCv2 sorozat

NCv2 sorozatú virtuális gépek szerint vannak kapcsolva [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) Feldolgozóegységekkel. Ezek Feldolgozóegységekkel biztosít több, mint a számítási teljesítmény NC-adatsorozat x 2. Az ügyfelek kihasználhatja a frissített Feldolgozóegységekkel hagyományos HPC munkaterhelések, például tározó modellezési DNS műveleti sorrend, fehérje elemzés, Monte Carlo szimulációja vagy mások számára. A NC24rs v2 konfiguráció egy kis késleltetésű számítástechnikai munkaterhelések szorosan összekapcsolt párhuzamos optimalizálva nagy átviteli hálózati adapter biztosít.

> [!IMPORTANT]
> A méret termékcsalád az előfizetéshez (core) vCPU kvóta kezdetben 0 értékre van állítva minden régióban. [A vCPU kvóta növelését](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [rendelkezésre álló terület](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = egy P100 kártya.

*RDMA-kompatibilis

## <a name="ncv3-series"></a>NCv3 sorozat

NCv3 sorozatú virtuális gépek szerint vannak kapcsolva [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) Feldolgozóegységekkel. Ezek Feldolgozóegységekkel 1,5 x NCv2-adatsorozat számítási teljesítményt biztosít. Az ügyfelek kihasználhatja a frissített Feldolgozóegységekkel hagyományos HPC munkaterhelések, például tározó modellezési DNS műveleti sorrend, fehérje elemzés, Monte Carlo szimulációja vagy mások számára. A NC24rs v3 konfiguráció egy kis késleltetésű számítástechnikai munkaterhelések szorosan összekapcsolt párhuzamos optimalizálva nagy átviteli hálózati adapter biztosít.

> [!IMPORTANT]
> A méret termékcsalád az előfizetéshez (core) vCPU kvóta kezdetben 0 értékre van állítva minden régióban. [A vCPU kvóta növelését](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [rendelkezésre álló terület](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 1344 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = egy V100 kártya.

*RDMA-kompatibilis

## <a name="nd-series"></a>ND sorozat

NT-sorozatú virtuális gépek a GPU termékcsalád AI és részletes tanulási munkaterhelések készült új mellett. Képzés és megállapítás kiváló teljesítményt biztosítanak. NT-példányok szerint vannak kapcsolva [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Feldolgozóegységekkel. Ezek a példányok egyszeres pontosságú lebegőpontos művelet, Microsoft kognitív eszközkészlet, TensorFlow, Caffe és egyéb keretrendszerekre okhoz AI munkaterhelések kiváló teljesítményt nyújtanak. Az ND sorozat jóval nagyobb GPU-memóriával rendelkezik (24 GB), így jelentősen nagyobb neurálishálózat-modellekhez is alkalmazható. A hálózati vezérlő által-sorozat, például az NT-sorozat kínál a konfiguráció az RDMA, a másodlagos alacsony késésű, nagy átviteli hálózaton és InfiniBand-kapcsolatokat, sok Feldolgozóegységekkel átfedés nagyméretű képzési feladatok futtatása.

> [!IMPORTANT]
> A méret termékcsalád az előfizetésében régiónként vCPU (core) kvóta kezdetben 0 értékre van állítva. [A vCPU kvóta növelését](../articles/azure-supportability/resource-manager-core-quotas-request.md) a termékcsalád az az [rendelkezésre álló terület](https://azure.microsoft.com/regions/services/).
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 336 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 | 8 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

## <a name="nv-series"></a>NV sorozat

A portok HV-sorozatú virtuális gépek szerint vannak kapcsolva [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) Feldolgozóegységekkel és NVIDIA rács technológia asztali az elérését gyorsítja fel, alkalmazások és virtuális asztalok ahol az ügyfelek képesek jelenítheti meg az adatok vagy szimulációja. Felhasználók is megjelenítheti a grafikus intenzív munkafolyamatok a felső szintű grafikus képességet, és emellett a kódolás és megjelenítési például az egyszeres pontosságú feladatokat futtató portok HV-példányokon. 

Minden GPU portok HV példányát rács licenccel rendelkezik. Ez a licenc szerződés keretein belül a portok HV-példány használata virtuális munkaállomásként egy-egy felhasználóhoz vagy 25 egyidejű felhasználók csatlakozhatnak egy virtuális alkalmazás forgatókönyv esetén a virtuális Gépet.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | A virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.

 
