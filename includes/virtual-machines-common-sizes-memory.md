---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: dba5a094ce4dfd55efd892c268e903d89fff90a9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269595"
---
A memóriaoptimalizált VM-méretek ajánlat egy nagy memória-CPU arányú, amelyek kiváló relációs adatbázis-kiszolgálók, közepes vagy nagyméretű gyorsítótárak és memórián belüli elemzés. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

* A M-sorozat kínál a legmagasabb vCPU száma (legfeljebb 128 Vcpu) és a legnagyobb memória (akár 3.8 TiB) a virtuális gép a felhőben.  Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.

* Dv2-sorozat, G-sorozat, és a DSv2/GS megfelelők esetében, amelyek az alkalmazások, amelyeknek gyorsabb Vcpu, ideiglenes tárolási jobb teljesítmény érdekében igény, vagy magasabb memória iránti igények kielégítése érdekében ideális.  Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.


* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációját alapul 2,4 GHz-es Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) vagy E5-2673 v4 2.3 GHz (Broadwell) processzort, és az Intel Turbo program technológia 2.0 folytathatja legfeljebb 3.1-es GHz-es. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

* A Ev3-sorozat szolgáltatások E5-2673 v4 2.3 GHz (Broadwell) processzor többszálú konfiguráció esetén a legtöbb általános célú munkaterhelések jobb értékajánlatához biztosítása, és a Ev3 üzembe igazítását az általános célú virtuális gépek a legtöbb más felhők.  Memória (a 7 GiB/vCPU való 8 GiB/vCPU) ki lett terjesztve, amíg a lemez-és hálózati korrigálták való megfelelés érdekében a Hyper-Threading technológia való átálláskor / core alapon.  A Ev3, a D/Dv2 családtagjai felső memóriaterület Virtuálisgép-méretek legfeljebb végezze.

* Az Azure Compute kínál a virtuális gép méretét, amely a adott hardverekhez típusra, és egyetlen ügyfél számára kijelölt elszigetelt vannak.  A virtuális gép használata esetén például a megfelelőségi és szabályozási követelmények elemek munkaterhelések nagyfokú elkülönítési más felhasználóktól igénylő munkaterhelések legmegfelelőbb értékek.  Az ügyfelek is beállíthatja a további fel az erőforrások ezek elszigetelt virtuális gépek használatával [beágyazott virtuális gépek az Azure támogatási](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).  Ellenőrizze a virtuális gép családok alatt a virtuális gép elkülönített lehetőségek táblák.

## <a name="esv3-series"></a>Esv3-sorozat 

ACU: 160-190 <sup>1</sup>

Az ESv3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával, valamint prémium szintű tárolás használatára is képes. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.


| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |


<sup>1</sup> Esv3 sorozatú virtuális gép funkció Intel® Hiperszálkezelési technológiája.

<sup>2</sup> használható core méret korlátozott.

<sup>3</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.


## <a name="ev3-series"></a>Ev3-sorozat 

ACU: 160-190 <sup>1</sup>

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
| Standard_E64i_v3&nbsp;<sup>2</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |

<sup>1</sup> Ev3 sorozatú virtuális gép funkció Intel® Hiperszálkezelési technológiája.

<sup>2</sup> használható core méret korlátozott. 


## <a name="m-series"></a>M sorozat 

ACU: 160-180 <sup>1</sup>

| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10 000 vagy 100 (793)  | 5000 / 125 | 4 / 2000 |
| M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20 000 / 200 (1,587) | 10,000 / 250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M32ls | 32 | 256    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M64s  | 64 | 1,024   | a 2048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
|Standard_M64ls  | 64 | 512    | a 2048 | 64 | 80,000 / 800 (6,348) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1,792 | a 2048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 160,00          |
| Standard_M128s&nbsp;<sup>2,&nbsp;3</sup> | 128  | a 2048        | 4096  | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3&nbsp;4</sup> | 128  | 3,892  | 4096 | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80,000 / 800 (1,228) | 40 000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80,000 / 800 (1,228) | 40 000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | a 2048 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80,000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80,000 / 2000 | 8 / 32000 |



<sup>1</sup> M-sorozat VM szolgáltatás Intel® Hyper-Threading technológia

<sup>2</sup> 64-nél több vCPU szükséges a támogatott vendég operációs rendszer: Windows Server 2016, a Ubuntu 16.04 LTS, a SLES 12 SP2, és a Red Hat Enterprise Linux, a CentOS 7.3 vagy a LIS 4.2.1 az Oracle Linux 7.3.

<sup>3</sup> használható core méret korlátozott.

<sup>4</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.
<br>

## <a name="gs-series"></a>GS sorozat 

ACU: 180-240 <sup>1</sup>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> a maximális átviteli sebesség (IOPS vagy MB/s) lehetséges GS több virtuális gép korlátozhatja a száma, mérete és a csatlakoztatott lemezek az csíkozást. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

<sup>2</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.

<sup>3</sup> használható core méret korlátozott.

<br>

## <a name="g-series"></a>G-sorozat

ACU: 180–240

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard szintű, G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.
<br>


## <a name="dsv2-series"></a>DSv2-sorozat 

ACU: 210-250 <sup>1</sup>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> a maximális átviteli sebesség (IOPS vagy MB/s) lehetséges DSv2 több virtuális gép korlátozhatja a száma, mérete és a csatlakoztatott lemezek az csíkozást.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.

<sup>2</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver.

<sup>3</sup> használható core méret korlátozott.

<sup>4</sup> 25000 Mbps gyorsított hálózattal.

<br>

## <a name="dv2-series"></a>Dv2-sorozat

ACU: 210–250

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> példány el különítve az egyetlen ügyfél számára dedikált hardver. 

<sup>2</sup> 25000 Mbps gyorsított hálózattal.



<br>



