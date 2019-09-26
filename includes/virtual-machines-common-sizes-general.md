---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5d89feaa5225aea56af86aa7f70d3666994fdaec
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266891"
---
Az általános célú virtuálisgép-méretek kiegyensúlyozott processzor-memória arányt biztosítanak. Ideális választás tesztelési-fejlesztési feladatokhoz, kis és közepes méretű adatbázisokhoz, valamint kis és közepes adatforgalmú webkiszolgálókhoz. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számával, valamint az ebben a csoportosításban lévő méretek tárolási sebességével kapcsolatos információkat tartalmaz.

- A [DC sorozat](#dc-series) az Azure-beli virtuális gépek egyik családja, amely az adatok és a kód titkosságának és integritásának védelmét segíti a nyilvános felhőben történő feldolgozás során. A gépeket SGX technológiát használó 3,7 GHz-es Intel XEON E-2176G processzorok támogatják. Az Intel Turbo Boost technológiával a teljesítményük akár 4,7 GHz-ig is növelhető. A DC-példányokkal enklávéalapú alkalmazások hozhatók létre, amelyekkel a felhasználók használat közben is védelmet biztosíthatnak a kódnak és az adatoknak.

- A Av2 sorozatú virtuális gépek számos különböző hardvereszközön és processzoron is üzembe helyezhetők. Az A sorozatú virtuális gépek a belépő szintű számítási feladatokhoz – például fejlesztéshez és teszteléshez – igazodó CPU-teljesítmény- és memóriakonfigurációkkal rendelkeznek. A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.

  Ilyenek például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis-és közepes adatbázisok, a bizonyítási fogalmak és a kódok tárházai.

- A Dv2 sorozat, amely az eredeti D sorozatra épül, egy nagyobb teljesítményű CPU-és optimális CPU-memória-konfigurációval rendelkezik, ami a legtöbb éles számítási feladathoz megfelelő. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. Ez a legújabb generációs Intel Xeon® E5-2673 v3 2,4 GHz-es (Haswell) vagy E5-2673 v4 2,3 GHz-es (Broadwell) processzorok alapján, valamint az Intel Turbo Boost Technology 2,0 használatával akár 3,1 GHz-re is felmehet. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- A Dv3 sorozat a 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzort vagy a legújabb 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzort tartalmazza egy Hyper-threaded konfigurációban, ami jobb értéket biztosít a legtöbb általános célú számítási feladathoz.  A memória ki lett bontva (~ 3,5 GiB/vCPU – 4 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer alapszinten igazította ki, hogy az a feleznie-re legyen igazítva.  A Dv3 már nem rendelkezik a D/Dv2 családok nagy memóriabeli virtuálisgép-méretével, amelyeket az új Ev3 családba helyeztek át.

  Példa: a D sorozat használati esetei nagyvállalati szintű alkalmazások, a kapcsolódó adatbázisok, a memórián belüli gyorsítótárazás és az elemzések.

- A da-sorozat és a Dasv3 sorozat új méretek az AMD 2.35 GHz-es EPYC<sup>TM</sup> 7452V processzorát egy többszálas konfigurációban, amely akár 256 GB L3-os gyorsítótárat is felhasznál, amely 8 GB-ot tesz elérhetővé az cél munkaterhelések. A da-sorozat és a Dasv3-sorozat ugyanazokkal a memória-és lemez-konfigurációkkal rendelkezik, mint a D & Dsv3 sorozat.
  
## <a name="b-series"></a>B sorozat

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

A B sorozatú, feltört virtuális gépek ideálisak olyan számítási feladatokhoz, amelyeknek nincs szükségük a CPU teljes teljesítményére, például webkiszolgálók, kisméretű adatbázisok, fejlesztési és tesztelési környezetek. Ezek a számítási feladatok általában feltört teljesítménnyel kapcsolatos követelményekkel rendelkeznek. A B sorozat biztosítja, hogy ezek az ügyfelek a virtuálisgép-méretet olyan tudatos alapszintű teljesítmény mellett vásárolják meg, amely lehetővé teszi, hogy a virtuálisgép-példány krediteket hozzon létre, amikor a virtuális gép az alapteljesítménynél kevesebbet használ. Ha a virtuális gép felhalmozott Kredittel rendelkezik, a virtuális gép a CPU alapkonfigurációja felett akár 100%-ot is megadhat, ha az alkalmazás a nagyobb CPU-teljesítményt igényli.

Ilyenek például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kisméretű adatbázisok, a mikro-szolgáltatások, a rendszer-előállítók kiszolgálói, a Build-kiszolgálók.


| Size             | vCPU  | Memória: GiB | Ideiglenes tárterület (SSD) GiB | A VM alap CPU-teljesítménye | A VM maximális CPU-teljesítménye | Kezdeti kreditek | Elkülönített kreditek óránként | Elkülönített kreditek max. száma | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek maximális száma |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640/100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> a B1ls csak Linux rendszeren támogatott

## <a name="dsv3-series-sup1sup"></a>Dsv3 – <sup>1</sup> . sorozat

ACU: 160-190

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

A Dsv3-sorozat méretei az 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron vagy a legújabb 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzoron alapulnak, amely az Intel Turbo Boost Technology 2,0 használatával elérheti a 3,5 GHz-es és a Premium Storage-t is. A Dsv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.


| Size             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> a Dsv3 SOROZATú virtuális gépek funkciójának Intel® Hyper-Threading technológiája

## <a name="dasv3-series-preview"></a>Dasv3 sorozat (előzetes verzió)

Prémium szintű Storage: Támogatott

Premium Storage gyorsítótárazás: Támogatott

A Dasv3-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely növelheti a 3.35 GHz-es Fmax, és prémium szintű tárolást is használhat. A Dasv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

[Kattintson ide az előzetes verzióra való feliratkozáshoz](http://aka.ms/azureamdpreview).

| Size | vCPU | Memória: GiB | Ideiglenes tároló (SSD): GiB |
|---|---|---|---|
| Standard_D2as_v3  | 2  | 8   | 16  |
| Standard_D4as_v3  | 4  | 16  | 32  |
| Standard_D8as_v3  | 8  | 32  | 64  |
| Standard_D16as_v3 | 16 | 64  | 128 |
| Standard_D32as_v3 | 32 | 128 | 256 |
| Standard_D48as_v3 | 48 | 192 | 384 |
| Standard_D64as_v3 | 64 | 256 | 512 |

## <a name="dv3-series-sup1sup"></a>Dv3 – <sup>1</sup> . sorozat

ACU: 160-190

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

A Dv3-sorozat méretei az 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron vagy 2,3 GHz-es Intel XEON® E5-2673 v4 (Broadwell) processzoron alapulnak, amely az Intel Turbo Boost Technology 2,0 használatával elérheti a 3,5 GHz-es technológiát. A Dv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához Dsv3-méreteket vegyen igénybe. A Dsv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dv3-sorozatéval. 


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard d32 v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> a Dv3 SOROZATú virtuális gépek funkciójának Intel® Hyper-Threading technológiája

## <a name="dav3-series-preview"></a>Dav3 sorozat (előzetes verzió)

Prémium szintű Storage: Nem támogatott

Premium Storage gyorsítótárazás: Nem támogatott

A Dav3-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely a 3.35 GHz-es kibővített Fmax érhető el. A Dav3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A Premium Storage-lemezek használatához használja a Dasv3-méreteket. A Dasv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dav3 sorozattal.

[Kattintson ide az előzetes verzióra való feliratkozáshoz](http://aka.ms/azureamdpreview).

| Size | vCPU | Memória: GiB | Ideiglenes tároló (SSD): GiB |
|---|---|---|---|
| Standard_D2a_v3  | 2  | 8   | 50   |
| Standard_D4a_v3  | 4  | 16  | 100  |
| Standard_D8a_v3  | 8  | 32  | 200  |
| Standard_D16a_v3 | 16 | 64  | 400  |
| Standard_D32a_v3 | 32 | 128 | 800  |
| Standard_D48a_v3 | 48 | 192 | 1200 |
| Standard_D64a_v3 | 64 | 256 | 1600 |

## <a name="dsv2-series"></a>DSv2-sorozat

ACU: 210-250

Prémium szintű Storage:  Támogatott

Premium Storage gyorsítótárazás:  Támogatott

| Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32 (43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400/96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-sorozat

ACU: 210-250

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott

| Méret           | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Adatlemezek max. száma | Átviteli sebesség IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-sorozat

ACU: 100

Prémium szintű Storage:  Nem támogatott

Premium Storage gyorsítótárazás:  Nem támogatott


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage-átviteli sebesség: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

## <a name="dc-series"></a>DC sorozat

Prémium szintű Storage: Támogatott

Premium Storage gyorsítótárazás: Támogatott



| Size          | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
