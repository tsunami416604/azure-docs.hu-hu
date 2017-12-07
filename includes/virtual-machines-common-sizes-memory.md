
A memóriaoptimalizált VM-méretek ajánlat egy nagy memória-CPU arányú, amelyek kiváló relációs adatbázis-kiszolgálók, közepes vagy nagyméretű gyorsítótárak és memórián belüli elemzés. Ez a cikk tájékoztatást ad azokról a Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség minden méretéhez ennél a csoportosításnál száma. 

* Az M-sorozat biztosítja a legnagyobb vCPU-számot (legfeljebb 128 vCPU) és a legtöbb memóriát (legfeljebb 2,0 TiB) a felhőben lévő összes virtuális gép közül.  Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.

* A Dv2-, a D- és a G-sorozat, valamint ezek DS/GS megfelelői gyorsabb virtuális processzort (vCPU), jobb ideiglenes tárteljesítményt vagy több memóriát igényló alkalmazásokhoz ideálisak.  Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb memória–vCPU arányt, valamint az ideiglenes tároláshoz SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.

* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.


## <a name="esv3-series"></a>Esv3-sorozat *

ACU: 160–190

Az ESv3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával, valamint prémium szintű tárolás használatára is képes. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.


| Méret             | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3  | 2      | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2/közepes                                   |
| Standard_E4s_v3  | 4      | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2/közepes                                   |
| Standard_E8s_v3  | 8      | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4/magas                                       |
| Standard_E16s_v3 | 16     | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8/magas                                       |
| Standard_E32s_v3 | 32     | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8/rendkívül magas                             |
| Standard_E64s_v3 | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8/rendkívül magas                             |
* Esv3-sorozat VM funkció Intel® Hyper-Threading technológia


## <a name="ev3-series"></a>Ev3-sorozat *

ACU: 160–190 

Az Ev3-sorozat példányai a 2,3 GHz-es Intel XEON ® E5-2673 v4 (Broadwell) processzoron alapulnak, amelynek az órajele akár 3,5 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Az Ev3-sorozat példányai ideálisak a memóriaigényes vállalati alkalmazásokhoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához ESv3-méreteket vegyen igénybe. Az Esv3-méretek díjszabása és számlázási mérőszámai megegyeznek az Ev3-sorozatéval. 


| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2/közepes                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2/közepes                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4/magas                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8/magas                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8/rendkívül magas           |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8/rendkívül magas           |
* Ev3-sorozat VM funkció Intel® Hyper-Threading technológia

## <a name="m-series"></a>M-sorozat*

ACU: 160–180

| Méret            | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M64s  | 64   | 1024        | 2048           | 64             | 80,000 / 800 (6348)       | 40,000 / 1,000                            | 8 / 16000          |
| Standard_M64ms  | 64   | 1792        | 2048           | 64             | 80,000 / 800 (6348)       | 40,000 / 1,000                            | 8 / 16000          |
| Standard_M128s** | 128  | 2048        | 4096           | 64             | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 25000          |


*Az M-sorozatú virtuális gépek Intel® hiperszálkezelési technológiát használnak

**Több mint 64 vCPU esetében a következő támogatott vendég operációs rendszerek egyikére van szükség: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 és Red Hat Enterprise Linux vagy CentOS 7.3 + LIS 4.2.1 

<br>

## <a name="gs-series"></a>GS-sorozat*

ACU: 180–240

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4 |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 6000 - 16000 &#8224; |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

*A GS-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket. 

**A példány ügyfelenként külön dedikált hardveren üzemel.


<br>

## <a name="g-series"></a>G-sorozat

ACU: 180–240

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 6000 - 16000 &#8224;          |
| Standard_G5* | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

*A példány ügyfelenként külön dedikált hardveren üzemel.
<br>


## <a name="dsv2-series"></a>DSv2-sorozat*

ACU: 210–250

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2 / 1500 |
| Standard_DS12_v2 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4 / 3000 |
| Standard_DS13_v2 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8 / 6000 |
| Standard_DS14_v2 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 6000 - 12000 &#8224; |
| Standard_DS15_v2** |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 20000***

*A DSv2-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.

**A példány egy elkülönített csomópont, amely garantálja, hogy az Ön virtuális gépe az egyetlen virtuális gép a Intel Haswell csomópontunkon.

***25000 Mbps gyorsított hálózatkezeléssel.

<br>

## <a name="dv2-series"></a>Dv2-sorozat

ACU: 210–250

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 6000 - 12000 &#8224;          |
| Standard_D15_v2* | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 20000** |

*A példány egy elkülönített csomópont, amely garantálja, hogy az Ön virtuális gépe az egyetlen virtuális gép a Intel Haswell csomópontunkon.

**25000 Mbps gyorsított hálózatkezeléssel.

<br>

## <a name="ds-series"></a>DS-sorozat*

ACU: 160

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 6000 - 8000 &#8224; |

*A DS-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) című cikket.


## <a name="d-series"></a>D-sorozat

ACU: 160

| Méret         | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | A maximális hálózati adapterek / várható a hálózati sávszélesség (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 6000 - 8000 &#8224;                |

<br>

