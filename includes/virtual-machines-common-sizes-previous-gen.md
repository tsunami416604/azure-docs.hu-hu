---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/22/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 4b4dcb16adf207b775bd8411848d9e5d69c397a9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669798"
---
Ez a cikk bemutatja, előző generációs virtuális gépek méretét. Ezek mérete továbbra is használható, de nincsenek elérhető újabb generációt.


## <a name="ds-series"></a>DS-sorozat

ACU: 160

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<br>

## <a name="ds-series---memory-optimized"></a>DS-sorozat - memóriaoptimalizált

ACU: 160 <sup>1</sup>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> a maximális átviteli sebesség (IOPS vagy MB/s) lehetséges DS több virtuális gép korlátozhatja a száma, mérete és a csatlakoztatott lemezek az csíkozást.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.



## <a name="d-series"></a>D-sorozat 

ACU: 160

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<br>

## <a name="d-series---memory-optimized"></a>D sorozat - memóriaoptimalizált

ACU: 160

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<br>

## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok

ACU: 225

Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  Ez a cikk ismerteti a Vcpu, adatlemezek, és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>MPI-alkalmazások esetében dedikált RDMA háttér hálózathoz FDR InfiniBand hálózati, rendszerrel ultra min késésű és nagy sávszélesség engedélyezte.

<br>

## <a name="a-series"></a>A-sorozat

ACU: 50–100

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |
<br>

<sup>1</sup> a A0 mérete túlzott előfizetett a fizikai hardveren. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4, CLI és PowerShell használatával
A klasszikus üzemi modellben egyes virtuálisgép-méretek neve eltérő a CLI-ben és a PowerShellben:

* A Standard_A0 neve ExtraSmall 
* A Standard_A1 neve Small
* A Standard_A2 neve Medium
* A Standard_A3 neve Large
* A Standard_A4 neve ExtraLarge

## <a name="basic-a"></a>Alapszintű A

Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

|Méret – Méret\név | vCPU |Memory (Memória)|Hálózati adapterek (max)|Ideiglenes lemez max. mérete |Legfeljebb az adatlemezek (1023 GB-os minden)|Legfeljebb IOPS (300 lemezenként)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|
 


