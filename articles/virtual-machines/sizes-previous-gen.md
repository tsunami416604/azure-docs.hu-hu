---
title: Azure Linux virtuális gépek méretei - előző generációk | Microsoft dokumentumok
description: Az Azure-ban linuxos virtuális gépekhez elérhető korábbi generációs méretek listája. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: ac49d2da9d05c9677dbb6f5328874ab3a45ff661
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081540"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>A virtuális gépek méretének előző generációi

Ez a szakasz a virtuális gépméretek előző generációiról nyújt tájékoztatást. Ezek a méretek továbbra is használhatók, de vannak újabb generációk is.

## <a name="f-series"></a>F-sorozat

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

ACU: 210–250

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs-sorozat <sup>1</sup>

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

ACU: 210–250

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

<sup>1</sup> A Fs sorozatú virtuális gépek en lehetséges maximális lemezátviteli sebessége (IOPS vagy Mb/s) a csatlakoztatott lemez(ek) száma, mérete és csíkozása korlátozhatja.  További információt a Nagy teljesítményű [Windows](windows/premium-storage-performance.md) vagy Linux tervezése című [témakörben talál.](linux/premium-storage-performance.md)  


## <a name="nvv2-series"></a>NVv2 sorozat

**Újabb méretajánlás**: [NVv3 sorozat](nvv3-series.md)

Az NVv2 sorozatú virtuális gépeket [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és NVIDIA GRID technológia hajtja Intel Broadwell CPU-kkal. Ezek a virtuális gépek gpu-gyorsuló grafikus alkalmazásokhoz és virtuális asztalokhoz vannak célozva, ahol az ügyfelek meg szeretnék jeleníteni az adataikat, szimulálni szeretnék az eredményeket a CAD megtekintéséhez, a CAD-hez való munkához, vagy a tartalom rendereléséhez és streameléséhez. Emellett ezek a virtuális gépek egyetlen pontosságú számítási feladatokat is futtathatnak, például kódolást és renderelést. Az NVv2 virtuális gépek támogatják a Prémium alapú tárolást, és az elődjében, az NV-sorozathoz képest kétszer annyi rendszermemóriát (RAM) kapnak.  

Az NVv2 példányokban minden GPU-hoz GRID licenc tartozik. Ez a licenc rugalmasságot biztosít egy NV-példány egyetlen felhasználó virtuális munkaállomásaként való használatához, vagy 25 egyidejű felhasználó csatlakozhat a virtuális géphez egy virtuális alkalmazásforgatókönyvhöz.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>A virtuális gépek méretének régebbi generációi

Ez a szakasz a virtuális gépméretek régebbi generációiról nyújt tájékoztatást. Ezek a méretek továbbra is támogatottak, de nem kapnak további kapacitást. Vannak újabb vagy alternatív méretek, amelyek általában elérhetők. Kérjük, olvassa el [a Linux-alapú virtuális gépek mérete az Azure-ban,](linux/sizes.md) hogy válassza ki a virtuális gép mérete, amely a legjobban megfelel az ön igényének.  

A Linux virtuális gépek átméretezésével kapcsolatos további információkért [lásd: Linux virtuális gép átméretezése.](linux/change-vm-size.md)  

<br>

### <a name="basic-a"></a>Alapszintű A  

**Újabb méretajánlás**: [Av2-sorozat](av2-series.md)

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

| Méret – Méret\név | vCPU | Memory (Memória)|Hálózati adapterek (max)| Ideiglenes lemez max. mérete | Legfeljebb adatlemezek (1023 GB-os mindegyik)| Legfeljebb IOPS (300 lemezenként) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4, CLI és PowerShell használatával

A klasszikus üzemi modellben egyes virtuálisgép-méretek neve eltérő a CLI-ben és a PowerShellben:

* A Standard_A0 neve ExtraSmall
* A Standard_A1 neve Small
* A Standard_A2 neve Medium
* A Standard_A3 neve Large
* A Standard_A4 neve ExtraLarge

### <a name="a-series"></a>A-sorozat  

**Újabb méretajánlás**: [Av2-sorozat](av2-series.md)

ACU: 50–100

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| &nbsp;<sup>1.</sup> Standard_A0 | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> Az A0 méret túl fizetett a fizikai hardveren. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok  

**Újabb méretajánlás**: [Av2-sorozat](av2-series.md)

ACU: 225

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1.</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9&nbsp;<sup>1.</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1 1</sup> Mpi alkalmazások esetén a dedikált RDMA háttérhálózatot az FDR InfiniBand hálózat engedélyezi, amely rendkívül alacsony késleltetést és nagy sávszélességet biztosít.  

> [!NOTE]
> Az A8 – A11-es virtuális gépek a tervek szerint 3/2021-re tervezik a nyugdíjba vonulást. További információ: [HPC Migration Guide](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-sorozat  

**Újabb méretajánlás**: [Dv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> A Virtuálisgép-család a következő PROCESSZOR-sorozatok egyikén futtatható: 2,2 GHz-es Intel Xeon® E5-2660 v2, 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-sorozat - memória optimalizált  

**Újabb méretajánlás**: [Dv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> A Virtuálisgép-család a következő PROCESSZOR-sorozatok egyikén futtatható: 2,2 GHz-es Intel Xeon® E5-2660 v2, 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Előzetes: DC-sorozat

**Újabb méretajánlás**: [DCsv2 sorozat](dcv2-series.md)

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

A DC sorozat a 3,7 GHz-es Intel XEON E-2176G processzor legújabb generációját használja SGX technológiával, és az Intel Turbo Boost technológiával akár 4,7 GHz-re is felmehet. 

| Méret          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Max hálózati adapterek / várható hálózati sávszélesség (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> A DC sorozatú virtuális gépek 2 `Gen2` generációs virtuális gépek, és csak a képeket [támogatják.](./linux/generation-2.md#creating-a-generation-2-vm)


### <a name="ds-series"></a>DS-sorozat  

**Újabb méretajánlás**: [Dsv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> A Virtuálisgép-család a következő PROCESSZOR-sorozatok egyikén futtatható: 2,2 GHz-es Intel Xeon® E5-2660 v2, 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-sorozat - memória optimalizált  

**Újabb méretajánlás**: [Dsv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott gyorsítótárazási és ideiglenes tárolási átviteli sebesség: IOPS/MBps (gyorsítótár mérete GiB-ben) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> A DS sorozatú virtuális gépek en lehetséges maximális lemezátviteli sebességet (IOPS vagy Mb/s) korlátozhatja a csatlakoztatott lemez(ek) száma, mérete és csíkozása.  További információt a Nagy teljesítményű [Windows](windows/premium-storage-performance.md) vagy Linux tervezése című [témakörben talál.](linux/premium-storage-performance.md)
<sup>2</sup> VM család futtatható a következő CPU:2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) vagy 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-sorozat

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

Az Ls-sorozat nem támogatja a helyi gyorsítótár létrehozását a tartós adatlemezek által elérhető IOPS növelése érdekében. A helyi lemez nagy átviteli és IOPS-működése ideálissá teszi az Ls sorozatú virtuális gépeket a NoSQL-áruházak, például az Apache Cassandra és a MongoDB számára, amelyek több virtuális gépen replikálják az adatokat, hogy egyetlen virtuális gép meghibásodása esetén perzisztenciát érjenek el.

ACU: 180–240

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória (GiB) | Temp tároló (GiB) | Adatlemezek max. száma | Maximális ideiglenes tárolási átviteli sebesség (IOPS/Mb/s) | Maximális nem gyorsítótárazott lemezátviteli sebesség (IOPS/Mb/s) | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1.</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Az Ls sorozatú virtuális gépeken lehetséges maximális lemezátviteli-átalót korlátozhatja a csatlakoztatott lemezek száma, mérete és csíkozása. További információt a Nagy teljesítményű [Windows](windows/premium-storage-performance.md) vagy Linux tervezése című [témakörben talál.](linux/premium-storage-performance.md)

<sup>1</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.

### <a name="gs-series"></a>GS-sorozat

ACU: 180 - 240 <sup>1</sup>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| &nbsp;<sup>3.</sup> Standard_GS4 | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> A GS sorozatú virtuális gépek en lehetséges maximális lemezátviteli sebességet (IOPS vagy Mb/s) korlátozhatja a csatlakoztatott lemez(ek) száma, mérete és csíkozása. További információt a Nagy teljesítményű [Windows](windows/premium-storage-performance.md) vagy Linux tervezése című [témakörben talál.](linux/premium-storage-performance.md)

<sup>2</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.

<sup>3</sup> Korlátozott magméretek állnak rendelkezésre.

<br>

### <a name="g-series"></a>G-sorozat

ACU: 180–240

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális ideiglenes tárolási átviteli sebesség: IOPS/Olvasás i.BBps/Írás i.BBps | Maximális adatlemezek/átviteli sebesség: IOPS | Maximális hálózati adapterek/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1.</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> Példány egyetlen ügyfélnek dedikált hardverhez van elkülönítve.
<br>

## <a name="nv-series"></a>NV sorozat
**Újabb méretajánlás**: [NVv3 és](nvv3-series.md) [NVv4 sorozat](nvv4-series.md)

Az NV sorozatú virtuális gépeket [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és NVIDIA GRID technológia hajtja asztali gyorsított alkalmazásokhoz és virtuális asztalokhoz, ahol az ügyfelek képesek az adataik vagy szimulációik megjelenítésére. A felhasználók képesek elképzelni a nagy grafikai igényű munkafolyamataikat az NV-példányokon, hogy kiváló grafikus képességet kapjanak, és emellett egyetlen pontosságú számítási feladatokat, például kódolást és renderelést is futtathatnak. Az NV sorozatú virtuális gépeket Intel Xeon E5-2690 v3 (Haswell) PROCESSZOROK is működtetik.

Az NV-példányokban minden GPU GRID licenccel rendelkezik. Ez a licenc rugalmasságot biztosít egy NV-példány egyetlen felhasználó virtuális munkaállomásaként való használatához, vagy 25 egyidejű felhasználó csatlakozhat a virtuális géphez egy virtuális alkalmazásforgatókönyvhöz.

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = fél M60 kártya.
<br>

## <a name="other-sizes"></a>Egyéb méretek

* [Általános célú](sizes-general.md)
* [Számításra optimalizált](sizes-compute.md)
* [Memóriaoptimalizált](sizes-memory.md)
* [Tárolásra optimalizált](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Nagy teljesítményű számítás](sizes-hpc.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
