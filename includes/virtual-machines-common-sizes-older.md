---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: d89a9c4c4498e249dbfbd453ef9772d18ffd213f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550145"
---
Ez a szakasz a virtuálisgép-méretek régebbi generációs szoftvereknél jobban információkat biztosít. Ezeket a méreteket továbbra is támogatottak, de nem kap további kapacitást. Nincsenek újabb vagy alternatív méret általánosan elérhető. Tekintse meg [méretek a Windows virtuális gépek az Azure-ban](../articles/virtual-machines/windows/sizes.md) vagy [az Azure-ban Linux rendszerű virtuális gépek méretei](../articles/virtual-machines/linux/sizes.md) , válassza ki a virtuális gép méretét, amely a legjobb igényeinek.  

További információ a Linux rendszerű virtuális gép átméretezése: [Azure parancssori felületével Linux virtuális gépek átméretezése](../articles/virtual-machines/linux/change-vm-size.md). Ha Windows virtuális gépeket használ, és előnyben részesítik a PowerShell használatát, lásd: [Windows virtuális gép átméretezése](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Alapszintű A  

**Újabb mérete javaslat**: [Az Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

|Méret – Méret\név | vCPU |Memory (Memória)|Hálózati adapterek (max)|Ideiglenes lemez max. mérete |Legfeljebb az adatlemezeket (egyenként 1023 GB)|Legfeljebb IOPS (300 lemezenként)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="a-series"></a>A-sorozat  

**Újabb mérete javaslat**: [Az Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD): GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> az A0 méret esetében ideálisnál a fizikai hardveren. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok  

**Újabb mérete javaslat**: [Az Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD): GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IO | Hálózati adapterek maximális száma|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>MPI-alkalmazások, a dedikált RDMA-háttérhálózatot engedélyezve van FDR InfiniBand hálózat, amely ultra rendkívül alacsony késést és magas sávszélességet kínál.  

<br>

### <a name="d-series"></a>D-sorozat  

**Újabb mérete javaslat**: [Dv3-sorozat](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> Virtuálisgép-család is futtathatja a következő Processzor egyikét: 2.2-es GHz-es Intel E5-2660 Xeon®, v2 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy XEON® 2,3 GHz-es Intel E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>A D sorozat – memóriahasználatra optimalizált  

**Újabb mérete javaslat**: [Dv3-sorozat](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> Virtuálisgép-család is futtathatja a következő Processzor egyikét: 2.2-es GHz-es Intel E5-2660 Xeon®, v2 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy XEON® 2,3 GHz-es Intel E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-sorozat  

**Újabb mérete javaslat**: [A DSv3-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> Virtuálisgép-család is futtathatja a következő Processzor egyikét: 2.2-es GHz-es Intel E5-2660 Xeon®, v2 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy XEON® 2,3 GHz-es Intel E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-sorozat – memóriahasználatra optimalizált  

**Újabb mérete javaslat**: [A DSv3-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) a DS sorozat virtuális gép is lehet a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége.  További információkért lásd: [magas teljesítmény-központú tervezés](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> Virtuálisgép-család is futtathatja a következő Processzor egyikét: 2.2-es GHz-es Intel E5-2660 Xeon®, v2 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) vagy XEON® 2,3 GHz-es Intel E5-2673 v4 (Broadwell)  

<br>
