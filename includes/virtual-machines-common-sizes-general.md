---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 4eeff0d89fa8a73b8f7f4b73fb5bfb85cda26184
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
Általános célú Virtuálisgép-méretek adja meg az elosztott terhelésű CPU-memória aránya. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

- Az A- és az Av2-sorozat virtuális gépei különféle hardvertípusokon és processzorokon helyezhetőek üzembe. A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.

- A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb vCPU-nkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.

- Dv3-sorozat, Dv2-sorozat, a Folytatás, az eredeti D-adatsorozat, nagyobb teljesítményű CPU funkciókat. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- Az alapszintű rétegméretek elsődlegesen a fejlesztési számítási feladatokhoz és olyan egyéb alkalmazásokhoz valók, amelyekhez nincs szükség terheléselosztásra, automatikus skálázásra vagy memóriaigényes virtuális gépekre.

## <a name="b-series"></a>B sorozat

A B sorozatnak burstable virtuális gépek ideálisak munkaterhelések esetén, amelyek nem kell a Processzor teljes teljesítményének folyamatosan, például a webkiszolgálók, a kis adatbázisok és a fejlesztési és tesztelési környezetben. Az ilyen terhelések általában burstable teljesítmény követelményekkel rendelkezik. A B sorozatnak teszi lehetővé ezen ügyfelek vásároljon egy egy ár lelkiismereti alapteljesítményének, amely lehetővé teszi, hogy a Virtuálisgép-példány kreditek kialakításához, amikor a virtuális gép kevesebb, mint a kiinduló teljesítményét használ-e a Virtuálisgép-méretet. A virtuális gép jóváírás keletkezett, amikor a virtuális Gépet a virtuális gép alapmennyiséget meghaladóan akár 100 %-a CPU használatát, ha az alkalmazás által igényelt, a nagyobb processzorteljesítmény is kapacitásnövelés.


| Méret             | vCPU  | Memória: GiB | Helyi SSD: GiB | Az alapszintű alapszintű teljesítmény | Banki kreditek / óra | Maximális banki kreditek | Adatlemezek max. száma | Helyi lemez teljesítmény max.: IOPS / MB/s | Maximális nem gyorsítótárazott lemez teljesítmény: IOPS / MB/s | Hálózati adapterek maximális száma |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>Dsv3-sorozat <sup>1</sup>

ACU: 160–190

A 2,4 GHz-es Intel Xeon® E5-2673 v3 alapuló Dsv3-sorozat méretek (Haswell) processzor- és a legújabb 2.3 GHz-es Intel XEON® E5-2673 v4 Intel Turbo program technológia 2.0-s és 3.5-ös GHz elérése és prémium szintű Storage (Broadwell) processzor. A Dsv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.


| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80,000 / 1200                              | 8 / 30,000                                               |

<sup>1</sup> Dsv3 sorozatú virtuális gép funkció Intel® Hyper-Threading technológia

## <a name="dv3-series-sup1sup"></a>Dv3-sorozat <sup>1</sup>

ACU: 160–190

A 2,4 GHz-es Intel Xeon® E5-2673 v3 alapuló Dv3-sorozat méretek (Haswell) processzor vagy 2.3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzor Intel Turbo program technológia 2.0-s és 3.5-ös GHz érhető el. A Dv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához Dsv3-méreteket vegyen igénybe. A Dsv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dv3-sorozatéval. 


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1,000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8,000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48000/750/375                                            | 8 / 16,000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96000/1000/500                                            | 8 / 30,000                             |

<sup>1</sup> Dv3 sorozatú virtuális gép funkció Intel® Hyper-Threading technológia

## <a name="dsv2-series"></a>DSv2-sorozat

ACU: 210–250

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64,000 / 512 (688) |51,200 / 768 |8 / 12000 |



## <a name="dv2-series"></a>Dv2-sorozat

ACU: 210–250

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3.5 |4 |50 |3000 / 46 / 23 |4 / 4x500 |2 / 750 |
| Standard_D2_v2 |2 |7 |8 |100 |6000 / 93 / 46 |8 / 8x500 |2 / 1500 |
| Standard_D3_v2 |4 |14 |16 |200 |12000 / 187 / 93 |16 / 16x500 |4 / 3000 |
| Standard_D4_v2 |8 |28 |32 |400 |24000 / 375 / 187  |32 / 32x500 |8 / 6000 |
| Standard_D5_v2 |16 |56 |64 |800 |48000 / 750 / 375 |64 / 64x500 |8 / 12000 |

<br>

## <a name="ds-series"></a>DS-sorozat

ACU: 160

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<br>

## <a name="d-series"></a>D-sorozat 

ACU: 160

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<br>


## <a name="av2-series"></a>Av2-sorozat

ACU: 100



| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

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

|Méret – Méret\név | vCPU |Memory (Memória)|Hálózati adapterek (max)|Ideiglenes lemez max. mérete |Legfeljebb adatlemez, egyenként 1023 GB)|Legfeljebb IOPS (300 lemezenként)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|


Vegye figyelembe, hogy a klasszikus virtuális gépek adatlemezek száma előfordulhat, hogy az Azure Resource Manager virtuális gépek adatlemezek száma kisebb.
