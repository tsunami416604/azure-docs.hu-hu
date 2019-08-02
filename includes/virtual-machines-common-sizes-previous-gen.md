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
ms.openlocfilehash: cfffc29a467a89416964564b9c55a73cbf77377d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601337"
---
Ez a szakasz a virtuálisgép-méretek előző generációinak adatait tartalmazza. Ezek a méretek továbbra is használhatók, de újabb generációk is elérhetők. 

## <a name="f-series"></a>F-sorozat

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

ACU: 210 – 250

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS – <sup>1</sup> . sorozat

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

ACU: 210 – 250

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

| Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400/96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000/512 (192) |51200 / 768 |8 / 12000 |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

<sup>1</sup> az FS SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható.  Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Ls-sorozat

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

Az ls-sorozat nem támogatja a helyi gyorsítótár létrehozását, hogy növelje a tartós adatlemezek által elérhető IOPS. A helyi lemez nagy átviteli sebessége és IOPS révén az ls sorozatú virtuális gépek ideálisak az olyan NoSQL-tárolók számára, mint például az Apache Cassandra és a MongoDB, amelyek több virtuális gépen replikálják az adatmegőrzést, így az egyetlen virtuális gép meghibásodása esetén is kitartható.

ACU: 180-240

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Nem támogatott
 
| Méret          | vCPU | Memória (GiB) | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális hőmérséklet-tárolási sebesség (IOPS/MBps) | Nem gyorsítótárazott lemez maximális átviteli sebessége (IOPS/MBps) | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000 / 200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| &nbsp;<sup>1</sup> . Standard_L32s | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000/1000     | 8 / 20000 | 

Az ls-sorozatú virtuális gépek maximális átviteli sebessége az összes csatlakoztatott lemez számával, méretétől és csíkozásával korlátozható. Részletekért lásd: [a nagy teljesítmény kialakítása](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.

## <a name="nvv2-series-preview"></a>NVv2 sorozat (előzetes verzió)

**Újabb méretre vonatkozó javaslat**: [NVv3 sorozat (előzetes verzió)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

A NVv2 sorozatú virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és az NVIDIA Grid Technology működteti az Intel Broadwell CPU-val. Ezek a virtuális gépek a GPU-gyorsított grafikus alkalmazásokat és virtuális asztalokat célozzák meg, ahol az ügyfelek szeretnék megjeleníteni az adatokat, szimulálni az eredményeket a megtekintésre, a CAD-re való munkavégzésre vagy a tartalmak megjelenítésére és továbbítására. Ezenkívül egyszeres pontosságú számítási feladatok is futtathatók az ilyen gépeken, például kódolás és renderelés. A NVv2 Virtual Machines támogatja a Premium Storaget, és a korábbi NV-sorozattal összehasonlítva kétszer a rendszermemóriát (RAM) is elérheti.  

A NVv2-példányok minden GPU-je tartalmaz egy RÁCSos licencet. Ez a licenc lehetővé teszi, hogy az NV-példányokat virtuális munkaállomásként használja egyetlen felhasználó számára, vagy 25 egyidejű felhasználó csatlakozhat a virtuális GÉPHEZ egy virtuális alkalmazási forgatókönyv esetén.

| Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások | 
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
