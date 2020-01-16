---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 1867164954a3f9dff7a8a8c04e249a13edccb84a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021097"
---
Ez a szakasz a virtuális gépek méreteinek régebbi generációit ismerteti. Ezek a méretek továbbra is támogatottak, de nem kapnak további kapacitást. Az általánosan elérhető újabb vagy alternatív méretek. Az Azure-ban található [Windows rendszerű virtuális gépek méreteit](../articles/virtual-machines/windows/sizes.md) és a [Linux virtuális gépek](../articles/virtual-machines/linux/sizes.md) méreteit az Azure-ban az igényeinek leginkább megfelelő virtuálisgép-méretek kiválasztásával tekintheti meg.  

A Linux rendszerű virtuális gépek átméretezésével kapcsolatos további információkért lásd: [linuxos virtuális gép átméretezése az Azure CLI](../articles/virtual-machines/linux/change-vm-size.md)-vel. Ha Windows rendszerű virtuális gépeket használ, és inkább a PowerShellt szeretné használni, tekintse meg [a Windows rendszerű virtuális gép átméretezése](../articles/virtual-machines/windows/resize-vm.md)  

<br>

### <a name="basic-a"></a>Alapszintű A  

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

|Méret – Méret\név | vCPU |Memória|Hálózati adapterek (max)|Ideiglenes lemez max. mérete |Legfeljebb adatlemezek (1023 GB)|Legfeljebb IOPS (300 lemezenként)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4, CLI és PowerShell használatával

A klasszikus üzemi modellben egyes virtuálisgép-méretek neve eltérő a CLI-ben és a PowerShellben:

* A Standard_A0 neve ExtraSmall
* A Standard_A1 neve Small
* A Standard_A2 neve Medium
* A Standard_A3 neve Large
* A Standard_A4 neve ExtraLarge

### <a name="a-series"></a>A-sorozat  

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50–100

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> az a0 méret a fizikai hardveren felül van előfizetve. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok  

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> Az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a QDR InfiniBand hálózata teszi lehetővé, amely rendkívül alacsony késést és nagy sávszélességet biztosít.  

<br>

### <a name="d-series"></a>D sorozat  

**Újabb méretre vonatkozó javaslat**: [Dv3 sorozat](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D sorozat – optimalizált memória  

**Újabb méretre vonatkozó javaslat**: [Dv3 sorozat](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-sorozat  

**Újabb méretre vonatkozó javaslat**: [DSv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-sorozat – optimalizált memória  

**Újabb méretre vonatkozó javaslat**: [DSv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> a DS-SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható.  Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls sorozat

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

Az ls-sorozat nem támogatja a helyi gyorsítótár létrehozását, hogy növelje a tartós adatlemezek által elérhető IOPS. A helyi lemez nagy átviteli sebessége és IOPS révén az ls sorozatú virtuális gépek ideálisak az olyan NoSQL-tárolók számára, mint például az Apache Cassandra és a MongoDB, amelyek több virtuális gépen replikálják az adatmegőrzést, így az egyetlen virtuális gép meghibásodása esetén is kitartható.

ACU: 180–240

Premium Storage: támogatott

Premium Storage gyorsítótárazás: nem támogatott
 
| Méret          | vCPU | Memória (GiB) | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális hőmérséklet-tárolási sebesség (IOPS/MBps) | Nem gyorsítótárazott lemez maximális átviteli sebessége (IOPS/MBps) | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20000 | 

Az ls-sorozatú virtuális gépek maximális átviteli sebessége az összes csatlakoztatott lemez számával, méretétől és csíkozásával korlátozható. Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.

### <a name="gs-series"></a>GS sorozat 

ACU: 180 – 240 <sup>1</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> a GS SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható. Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.

<sup>3</sup> korlátozott méretű alapméret érhető el.

<br>

### <a name="g-series"></a>G sorozat

ACU: 180–240

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.
<br>
