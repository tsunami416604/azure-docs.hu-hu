---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: c2ef24692f9166fdca7eb19f648aee9ed3b0514f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40026213"
---
Az optimalizált Virtuálisgép-méretek ajánlat egy magas memória – Processzor aránya, amelyek ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélességet az ennél a csoportosításnál méreteire vonatkoztatva számát. 

* Az M-sorozat a legnagyobb vCPU-számot (legfeljebb 128 Vcpu) és a legnagyobb mértékű memóriát (akár 3,8 TiB) a felhőben lévő összes virtuális gép kínál.  Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.

* A Dv2-sorozat és a G-sorozat, a DSv2 és GS igényló alkalmazásokhoz ideálisak, amelyek a kereslet-gyorsabb Vcpu, jobb ideiglenes tárteljesítményt vagy több memóriát.  Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.


* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. -Alapú, a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz vagy E5-2673 v4 (Broadwell) 2,3 GHz processzor, és az Intel Turbo Boost Technology 2.0-akár 3,1 GHz-es. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

* Az Ev3-sorozat funkciók E5-2673 v4 (Broadwell) 2,3 GHz processzor egy hyper-threaded konfigurációban, így a legtöbb általános célú számítási feladatok esetében jobb értékajánlat, és az Ev3 üzembe igazítását az általános célú virtuális gépek a legtöbb más felhőkben.  Memória (a 7, 8 GiB/vCPU GiB/vCPU) ki lett terjesztve, amíg a lemez és a hálózati korlátok hozzá lett igazítva, helyezze át a Hyper-Threading technológia igazodva magonként alapon.  Az Ev3 a követés, akár a D/Dv2 családok magas memóriahasználat Virtuálisgép-méretek.

* Azure-beli számítási kínál a virtuális gép mérete, amely egy adott hardverekhez típusát, és egyetlen ügyfél számára fenntartott elkülönített vannak.  Ezek a virtuálisgép-méretek legalkalmasabbak elemeket, például a megfelelőségi és szabályozási követelmények járó számítási feladatok elkülönítése más felhasználóktól magas fokú megkövetelő számítási feladatokhoz.  Ügyfelek is kiválaszthatják a ezek elkülönített virtuálisgép-erőforrások vertikálisan tovább particionálhatja a [beágyazott virtuális gépek Azure-támogatás](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).  Tekintse át a táblákat, az elkülönített Virtuálisgép-beállítások az alábbi virtuálisgép-családot.

## <a name="esv3-series"></a>Az Esv3-adatsorok 

ACU: 160-190 <sup>1</sup>

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Az ESv3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával, valamint prémium szintű tárolás használatára is képes. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.


| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |


<sup>1</sup> Esv3 sorozatú virtuális gépek Intel® Hiperszálkezelési technológiát funkciót.

<sup>2</sup> core elérhető méretek korlátozott.

<sup>3</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.


## <a name="ev3-series"></a>Ev3-sorozat 

ACU: 160-190 <sup>1</sup>

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

Az Ev3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához ESv3-méreteket vegyen igénybe. Az Esv3-méretek díjszabása és számlázási mérőszámai megegyeznek az Ev3-sorozatéval. 


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8,000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |
| Standard e64i v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |

<sup>1</sup> Ev3-sorozat virtuális gép a Intel® Hiperszálkezelési technológiát funkció.

<sup>2</sup> core elérhető méretek korlátozott.

<sup>3</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.


## <a name="m-series"></a>M sorozat 

ACU: 160-180 <sup>1</sup>

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

Írásgyorsító: [támogatott](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard m8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10 000 vagy 100 (793)  | 5000 / 125 | 4 / 2000 |
| Standard m16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20 000 / 200-as (1,587) | 10,000 / 250 | 8 / 4000 |
| Standard m32ts | 32 | 192    | 1,024 | 32 | 40 000 / 400-as (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard m32ls | 32 | 256    | 1,024 | 32 | 40 000 / 400-as (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard m32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40 000 / 400-as (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard m64s  | 64 | 1,024   | a 2048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard m64ls  | 64 | 512    | a 2048 | 64 | 80,000 / 800 (6,348) | 40,000 / 1,000 | 8 / 16,000 |
| Standard m 64 MS&nbsp;<sup>3</sup>  | 64   | 1,792 | a 2048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard m 128 s&nbsp;<sup>2,&nbsp;3</sup> | 128  | a 2048        | 4096  | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard m 128 MS&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3,892  | 4096 | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80,000 / 800 (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80,000 / 800 (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M128&nbsp;<sup>2  | 128 | a 2048 | 14,336 | 64 | 250 000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250 000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32000 |



<sup>1</sup> M-sorozatú virtuális gépek szolgáltatás Intel® Hiperszálkezelési technológiát

<sup>2</sup> több mint 64 vCPU esetében a támogatott vendég operációs rendszerek egyikét: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 és Red Hat Enterprise Linux, CentOS 7.3 vagy Oracle Linux 7.3 + lis 4.2.1.

<sup>3</sup> core elérhető méretek korlátozott.

<sup>4</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.
<br>

## <a name="gs-series"></a>GS sorozat 

ACU: 180-240 <sup>1</sup>

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Például a Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) és a GS sorozatú virtuális gépek előfordulhat, hogy lehet a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

<sup>2</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

<sup>3</sup> core elérhető méretek korlátozott.

<br>

## <a name="g-series"></a>G-sorozat

ACU: 180–240

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 sorozat 11. 15.

ACU: 210 – 250 <sup>1</sup>

A Premium Storage: támogatott

Prémium szintű Storage gyorsítótárazási: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) DSv2-sorozatba virtuális gép is lehet a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.

<sup>2</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.

<sup>3</sup> core elérhető méretek korlátozott.

<sup>4</sup> 25000 Mbps gyorsított hálózatkezeléssel.

<br>

## <a name="dv2-series-11-15"></a>A Dv2 sorozat 11. 15.

ACU: 210–250

A Premium Storage: Nem támogatott.

Prémium szintű Storage gyorsítótárazási: Nem támogatott

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel. 

<sup>2</sup> 25000 Mbps gyorsított hálózatkezeléssel.



<br>



