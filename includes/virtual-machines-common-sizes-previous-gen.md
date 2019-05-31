---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 464c7bcb510a2f6ab80fb11d722c241ec51a1b16
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391322"
---
Ez a szakasz előző generációs szoftvereknél jobban virtuálisgép-méretek információkat biztosít. Ezeket a méreteket továbbra is használható, de nincsenek elérhető újabb generáció esetében. 

## <a name="f-series"></a>F-sorozat

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

ACU: 210 - 250

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS sorozatú <sup>1</sup>

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

ACU: 210 - 250

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) használhatóságát egy Fs sorozatú virtuális gépek előfordulhat, hogy a a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége.  További információkért lásd: [magas teljesítmény-központú tervezés](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Ls-sorozat

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

Az Ls-sorozat nem támogatja a helyi gyorsítótárral a növeli az iops-érték elérhető tartós adatlemezek létrehozását. A magas teljesítmény és a helyi lemez IOPS teszi NoSQL-tárolókat, mint például az Apache Cassandra- és MongoDB replikálhatja adatait, így több virtuális gép folyamataik megőrzésére a meghiúsulása esetén egyetlen virtuális Gépet, amely ideális Ls-sorozat virtuális gépei.

ACU: 180-240

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott
 
| Méret          | vCPU | Memória (GiB) | Ideiglenes tárhely (GB) | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye (IOPS vagy MBps) | Maximális lemezteljesítmény (IOPS vagy MBps) | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000 / 250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20000 | 

Az Ls-sorozat virtuális gépei lehetséges maximális lemezteljesítményét lehet, hogy korlátozzák a száma, mérete és szétosztottsága csatlakoztatott lemezeket. További információkért lásd: [magas teljesítmény-központú tervezés](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

## <a name="nvv2-series-preview"></a>NVv2 sorozat (előzetes verzió)

**Újabb mérete javaslat**: [NVv3 sorozat (előzetes verzió)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

A NVv2-sorozat virtuális gépei által kezelt [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu-k és az NVIDIA GRID technológia és az Intel Broadwell processzorokat. Ezek a virtuális gépek célozzák meg GPU gyorsított grafikai alkalmazásnál, és virtuális asztalok, ahol az ügyfelek szeretnék megjelenítheti az adatokat, szimulálása eredmények megtekintése érdekében CAD, vagy a renderelési és a stream tartalmát. Ezenkívül egyszeres pontosságú számítási feladatok is futtathatók az ilyen gépeken, például kódolás és renderelés. NVv2 virtuális gépek Premium Storage támogatja, és kapható kétszer a rendszer memória (RAM), az NV-sorozat elődjéhez képest.  

Minden egyes GPU NVv2 esetekben rács licencet tartalmaz. Ez a licenc rugalmasságot biztosít az NV-példány használata virtuális munkaállomás, egy-egy felhasználóhoz, vagy 25 párhuzamos felhasználó csatlakozhat a virtuális gép virtuális alkalmazás esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU-memóriával: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomásait | A virtuális alkalmazások | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4, CLI és PowerShell használatával

A klasszikus üzemi modellben egyes virtuálisgép-méretek neve eltérő a CLI-ben és a PowerShellben:

* A Standard_A0 neve ExtraSmall
* A Standard_A1 neve Small
* A Standard_A2 neve Medium
* A Standard_A3 neve Large
* A Standard_A4 neve ExtraLarge
