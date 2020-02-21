---
title: Azure Linux VM-méretek – előző generációk | Microsoft Docs
description: Felsorolja az Azure-beli linuxos virtuális gépekhez elérhető méretek korábbi generációit. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: cd45d38759bcf41307ba42d68d504266719579d2
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493422"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>A virtuális gépek méreteinek korábbi generációi

Ez a szakasz a virtuálisgép-méretek előző generációinak adatait tartalmazza. Ezek a méretek továbbra is használhatók, de újabb generációk is elérhetők.

## <a name="f-series"></a>F-sorozat

Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  

Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a vCPU-nkénti memória- vagy ideiglenes tárterületigényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.

ACU: 210–250

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS – <sup>1</sup> . sorozat

Az Fs-sorozat az F-sorozat összes előnyét biztosítja a prémium szintű tárterület mellett.

ACU: 210–250

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

<sup>1</sup> az FS SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható.  Részletekért lásd: a [Windows](windows/premium-storage-performance.md) vagy [Linux](linux/premium-storage-performance.md)nagy teljesítményének tervezése.  

## <a name="nvv2-series"></a>NVv2 sorozat

**Újabb méretre vonatkozó javaslat**: [NVv3 sorozat](nvv3-series.md)

A NVv2 sorozatú virtuális gépeket az [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és az NVIDIA Grid Technology működteti az Intel Broadwell CPU-val. Ezek a virtuális gépek a GPU-gyorsított grafikus alkalmazásokat és virtuális asztalokat célozzák meg, ahol az ügyfelek szeretnék megjeleníteni az adatokat, szimulálni az eredményeket a megtekintésre, a CAD-re való munkavégzésre vagy a tartalmak megjelenítésére és továbbítására. Emellett ezek a virtuális gépek egyetlen pontosságú számítási feladatot futtathatnak, például a kódolást és a renderelést. A NVv2 Virtual Machines támogatja a Premium Storaget, és a korábbi NV-sorozattal összehasonlítva kétszer a rendszermemóriát (RAM) is elérheti.  

A NVv2-példányok minden GPU-je tartalmaz egy RÁCSos licencet. Ez a licenc lehetővé teszi, hogy az NV-példányokat virtuális munkaállomásként használja egyetlen felhasználó számára, vagy 25 egyidejű felhasználó csatlakozhat a virtuális GÉPHEZ egy virtuális alkalmazási forgatókönyv esetén.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>A virtuális gépek méreteinek régebbi generációi

Ez a szakasz a virtuális gépek méreteinek régebbi generációit ismerteti. Ezek a méretek továbbra is támogatottak, de nem kapnak további kapacitást. Az általánosan elérhető újabb vagy alternatív méretek. A [Linux rendszerű virtuális gépek méretei az Azure-ban](linux/sizes.md) , az igényeinek leginkább megfelelő virtuálisgép-méretek kiválasztásához szükségesek.  

A Windows rendszerű virtuális gépek átméretezésével kapcsolatos további információkért lásd: [linuxos virtuális gép](linux/change-vm-size.md)átméretezése.  

<br>

### <a name="basic-a"></a>Alapszintű A  

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](av2-series.md)

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

| Méret – Méret\név | vCPU | Memory (Memória)|Hálózati adapterek (max)| Ideiglenes lemez max. mérete | Legfeljebb adatlemezek (1023 GB)| Legfeljebb IOPS (300 lemezenként) |
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

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](av2-series.md)

ACU: 50–100

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> az a0 méret a fizikai hardveren felül van előfizetve. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok  

**Újabb méretre vonatkozó javaslat**: [Av2 sorozat](av2-series.md)

ACU: 225

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ.  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (HDD) GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek maximális száma|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> Az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a FDR InfiniBand hálózata teszi lehetővé, amely rendkívül alacsony késést és nagy sávszélességet biztosít.  

<br>

### <a name="d-series"></a>D-sorozat  

**Újabb méretre vonatkozó javaslat**: [Dv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D sorozat – optimalizált memória  

**Újabb méretre vonatkozó javaslat**: [Dv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-sorozat  

**Újabb méretre vonatkozó javaslat**: [Dsv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-sorozat – optimalizált memória  

**Újabb méretre vonatkozó javaslat**: [Dsv3 sorozat](dv3-dsv3-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> a DS-SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható.  Részletekért lásd: a [Windows](windows/premium-storage-performance.md) vagy [Linux](linux/premium-storage-performance.md)nagy teljesítményének tervezése.
<sup>2</sup> a VM-család a következő CPU-k egyikén futhat: 2,2 GHz-es intel Xeon® E5-2660 v2, 2,4 GHz-es intel Xeon® E5-2673 v3 (Haswell) vagy 2,3 GHz-es intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-sorozat

Az Ls-sorozat akár 32 virtuális processzort (vCPU) is biztosíthat az [Intel® Xeon® E5 v3 processzorcsalád](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) használatával. Az Ls-sorozat ugyanakkora teljesítményt kínál, mint a G/GS-sorozat esetében, és vCPU-nként 8 GiB memóriát biztosít.

Az ls-sorozat nem támogatja a helyi gyorsítótár létrehozását, hogy növelje a tartós adatlemezek által elérhető IOPS. A helyi lemez nagy átviteli sebessége és IOPS révén az ls sorozatú virtuális gépek ideálisak az olyan NoSQL-tárolók számára, mint például az Apache Cassandra és a MongoDB, amelyek több virtuális gépen replikálják az adatmegőrzést, így az egyetlen virtuális gép meghibásodása esetén is kitartható.

ACU: 180–240

Premium Storage: támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória (GiB) | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális hőmérséklet-tárolási sebesség (IOPS/MBps) | Nem gyorsítótárazott lemez maximális átviteli sebessége (IOPS/MBps) | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Az ls-sorozatú virtuális gépek maximális átviteli sebessége az összes csatlakoztatott lemez számával, méretétől és csíkozásával korlátozható. Részletekért lásd: a [Windows](windows/premium-storage-performance.md) vagy [Linux](linux/premium-storage-performance.md)nagy teljesítményének tervezése.

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.

### <a name="gs-series"></a>GS sorozat

ACU: 180 – 240 <sup>1</sup>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> a GS SOROZATú virtuális gépek maximális átviteli sebessége (IOPS vagy Mbps) a csatlakoztatott lemez (ek) számával, méretétől és csíkozásával korlátozható. Részletekért lásd: a [Windows](windows/premium-storage-performance.md) vagy [Linux](linux/premium-storage-performance.md)nagy teljesítményének tervezése.

<sup>2</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.

<sup>3</sup> korlátozott méretű alapméret érhető el.

<br>

### <a name="g-series"></a>G-sorozat

ACU: 180–240

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> példány az egyetlen ügyfél számára dedikált hardveren van elkülönítve.
<br>

## <a name="other-sizes"></a>Egyéb méretek

* [Általános célú](sizes-general.md)
* [Számításra optimalizált](sizes-compute.md)
* [Memóriaoptimalizált](sizes-memory.md)
* [Tárolásra optimalizált](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Nagy teljesítményű számítás](sizes-hpc.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.