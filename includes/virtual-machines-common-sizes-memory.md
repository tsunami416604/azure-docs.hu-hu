---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 9e145bc3a6824100409a0f6215152cdf70ec6777
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501241"
---
Az optimalizált Virtuálisgép-méretek ajánlat egy magas memória – Processzor aránya, amelyek ideális választás relációs adatbázis-kiszolgálókhoz, közepes és nagy gyorsítótárakhoz és memóriabeli elemzésekhez. Ez a cikk ismerteti a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélességet az ennél a csoportosításnál méreteire vonatkoztatva számát. 

* Az Mv2-sorozat a legnagyobb vCPU-számot (legfeljebb 208 Vcpu) és a legnagyobb mértékű memóriát (legfeljebb 5.7 TiB) a felhőben lévő összes virtuális gép. Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.
 
* Az M-sorozat a nagy vCPU-számot (legfeljebb 128 Vcpu) és a egy nagy mennyiségű (akár 3,8 TiB) memóriát biztosít. Emellett ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek nagy vcpu-és nagy mennyiségű memóriát.

* A Dv2-sorozat és a G-sorozat, a DSv2 és GS igényló alkalmazásokhoz ideálisak, amelyek a kereslet-gyorsabb Vcpu, jobb ideiglenes tárteljesítményt vagy több memóriát. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. -Alapú, a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz vagy E5-2673 v4 (Broadwell) 2,3 GHz processzor, és az Intel Turbo Boost Technology 2.0-akár 3,1 GHz-es. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

* Az Ev3-sorozat funkciók E5-2673 v4 (Broadwell) 2,3 GHz processzor egy hyper-threaded konfigurációban, így a legtöbb általános célú számítási feladatok esetében jobb értékajánlat, és az Ev3 üzembe igazítását az általános célú virtuális gépek a legtöbb más felhőkben.  Memória (a 7, 8 GiB/vCPU GiB/vCPU) ki lett terjesztve, amíg a lemez és a hálózati korlátok hozzá lett igazítva, helyezze át a Hyper-Threading technológia igazodva magonként alapon.  Az Ev3 a követés, akár a D/Dv2 családok magas memóriahasználat Virtuálisgép-méretek.

* Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy meghatározott hardvertípusban vannak elkülönítve, és egyetlen ügyfél számára vannak fenntartva.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz megfelelőek, amelyeket magas szintű izolációval szükséges elkülöníteni más ügyfelek számítási feladataitól, beleértve olyan elemeket is mint a megfelelőség vagy a jogszabályi előírások betartása.  Ügyfelek is kiválaszthatják a ezek elkülönített virtuálisgép-erőforrások vertikálisan tovább particionálhatja a [beágyazott virtuális gépek Azure-támogatás](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Tekintse át a táblákat, az elkülönített Virtuálisgép-beállítások az alábbi virtuálisgép-családot.

## <a name="esv3-series"></a>Az Esv3-adatsorok 

ACU: 160-190 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

Az ESv3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával, valamint prémium szintű tárolás használatára is képes. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.


| Size             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Esv3 sorozatú virtuális gépek Intel® Hiperszálkezelési technológiát funkciót.

<sup>2</sup> core elérhető méretek korlátozott.

<sup>3</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.


## <a name="ev3-series"></a>Ev3-sorozat 

ACU: 160 - 190 <sup>1</sup>

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

Az Ev3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához ESv3-méreteket vegyen igénybe. Az Esv3-méretek díjszabása és számlázási mérőszámai megegyeznek az Ev3-sorozatéval. 


| Size            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3-sorozat virtuális gép a Intel® Hiperszálkezelési technológiát funkció.

<sup>2</sup> core elérhető méretek korlátozott.

<sup>3</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.


## <a name="mv2-series"></a>Mv2 sorozat

Prémium szintű Storage: Támogatott

Prémium szintű Storage gyorsítótárazást: Támogatott

Írásgyorsító: [Támogatott](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

A Mv2 sorozat funkciók nagy teljesítményű, kis késésű, közvetlenül hozzá van rendelve helyi NVMe-tároló egy hyper-threaded Intel® Xeon® Platinum 8180 M 2,5 GHz-es (Skylake) processzor verziójával futó az összes core alap gyakoriságát, 2,5 GHz-es és a egy 3,8 GHz-es maximális turbo gyakoriságát. Összes Mv2 sorozat virtuálisgép-méretek is támogatja a standard és prémium szintű, perzisztens lemezek használatát. Mv2 sorozat példányai memóriahasználatra optimalizált virtuális gépek mérete, így páratlan számítási teljesítménnyel támogatja a nagy memóriabeli adatbázisok és a egy magas memória – Processzor arány, amely ideális választás relációs adatbázis-kiszolgálókhoz, nagy gyorsítótárakhoz és memóriabeli számítási feladatok elemzés. 

|Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |

Mv2-sorozatú virtuális gépek Intel® Hiperszálkezelési technológiát funkció  

<sup>1</sup> ezek nagy virtuális gépeknek szüksége van, a támogatott vendég operációs rendszerek egyikét: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> Mv2-sorozat virtuális gépei csak a 2. generációs. Linux használata, a következő részben keresse meg és válassza ki a SUSE Linux-rendszerképen.

#### <a name="find-a-suse-image"></a>A SUSE-lemezkép keresése

Egy megfelelő SUSE Linux-rendszerképek kiválasztása az Azure Portalon: 

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** 
1. Keressen "SUSE SAP" 
1. Az SLES for SAP 2. generációs rendszerképek érhetők el, vagy használatalapú fizetéses, vagy saját előfizetésével (saját) használata. A keresési eredmények között bontsa ki a kívánt lemezkép kategóriát:

    * SUSE Linux Enterprise Server (SLES) SAP-
    * SUSE Linux Enterprise Server (SLES) SAP (saját)
    
1. Kompatibilis a Mv2-sorozat a SUSE-rendszerképek van fűzve előtagként a név `GEN2:`. Az alábbi SUSE lemezképek Mv2-sorozat virtuális gépei számára érhetők el:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 az SAP-alkalmazások
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 az SAP-alkalmazások
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 (saját) SAP-alkalmazások
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 az SAP-alkalmazások (saját)

#### <a name="select-a-suse-image-via-azure-cli"></a>Válassza ki a SUSE-lemezkép az Azure CLI-n keresztül

Mv2-sorozat virtuális gépei a jelenleg elérhető SLES for SAP rendszerkép listájának megtekintéséhez használja a következő [ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) parancsot:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

A parancs jelenít meg a jelenleg elérhető Generation 2 virtuális gépek elérhető SUSE Mv2-sorozat virtuális gépei számára. 

Példa a kimenetre:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M sorozat 

ACU: 160-180 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

Írásgyorsító:  [Támogatott](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4000 |
| Standard m32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard m32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard m64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard m64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard m 128 s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard m 128 MS&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> M-sorozatú virtuális gépek szolgáltatás Intel® Hiperszálkezelési technológiát

<sup>2</sup> több mint 64 vCPU esetében a támogatott vendég operációs rendszerek egyikét: A Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 és Red Hat Enterprise Linux, CentOS 7.3 vagy Oracle Linux 7.3 + lis 4.2.1.

<sup>3</sup> core elérhető méretek korlátozott.

<sup>4</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 sorozat 11. 15.

ACU: 210 - 250 <sup>1</sup>

Prémium szintű Storage:  Támogatott

Prémium szintű Storage gyorsítótárazást:  Támogatott

| Size | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> a maximális lemezteljesítményét (IOPS vagy MBps) DSv2-sorozatba virtuális gép is lehet a száma, mérete és szétosztottsága befolyásolhatja a csatolt lemezek mennyisége.  További információkért lásd: [magas teljesítmény-központú tervezés](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.  
<sup>3</sup> core elérhető méretek korlátozott.  
<sup>4</sup> 25000 Mbps gyorsított hálózatkezeléssel. 

<br>

## <a name="dv2-series-11-15"></a>A Dv2 sorozat 11. 15.

ACU: 210 - 250

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / olvasási MBps / írási MBps | Adatlemezek max. száma / átviteli sebesség: IO | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> példány elkülönítve egyetlen ügyfél számára dedikált hardveren üzemel.  
<sup>2</sup> 25000 Mbps gyorsított hálózatkezeléssel. 
