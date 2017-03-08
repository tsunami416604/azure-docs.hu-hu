


Az Azure-ban több standard méret közül választhat. A méretek némelyikével kapcsolatos megfontolások a következők:

* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb magonkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.
* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* Az F-sorozat a legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, amelynek az órajele akár 3,1 GHz-re is növelhető az Intel Turbo Boost Technology 2.0 alkalmazásával. Ez ugyanakkora teljesítményt jelent, mint a Dv2-sorozat virtuális gépei esetében.  Az alacsonyabb óránkénti listaáron elérhető F-sorozat képviseli a legjobb ár–teljesítmény arányt az Azure-portfólióban az egy magra jutó Azure-alapú számítási egységek (Azure Compute Unit, ACU) alapján. 
  
    Az F-sorozattal egy új elnevezési szabványt vezetünk be az Azure-alapú virtuális gépek méretére vonatkozóan. Az F-sorozat, valamint a később bevezetett virtuálisgép-méretek esetében a család betűjele után álló szám a processzormagok számát jelzi. A további funkciókat, például a prémium szintű optimalizált tárolás támogatását a processzormagok számát jelző szám után álló betűk jelzik majd. Ezt az elnevezési formátumot alkalmazzuk majd a továbbiakban kiadott virtuálisgép-méretek esetében, azonban a korábbiakban kiadott meglévő méretek neveit visszamenőleg nem módosítjuk.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.
* A DS-, a DSv2-, az Fs- és a GS-sorozat virtuális gépein használható a Premium Storage, amely nagy teljesítményű, kis késésű tárolást biztosít az I/O-igényes számítási feladatokhoz. Ezek a virtuális gépek SSD meghajtókon üzemeltetik a virtuálisgép-meghajtókat, és egy helyi SSD-gyorsítótárat is kínálnak. A Premium Storage szolgáltatás csak bizonyos régiókban érhető el. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket.
*   Az A- és az Av2-sorozat virtuális gépei különféle hardvertípusokon és processzorokon helyezhetőek üzembe. A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.
* Az A0 méret esetében a fizikai hardvernek az ideálisnál több előfizetést kell kezelnie. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

A virtuális gépek mérete befolyásolja az árképzést. A méret emellett hatással van a virtuális gép feldolgozási teljesítményére, valamint a memória- és tárhelykapacitására is. A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. Részletekért lásd [A Virtual Machines díjszabásának részletei](https://azure.microsoft.com/pricing/details/virtual-machines/) és [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) című cikket. 

Az alábbiak segíthetnek a megfelelő méret kiválasztásában:

* Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ. A méretek használatával kapcsolatos részletes információkért és szempontokért lásd [a H-sorozatú és a nagy számítási igényű A-sorozatú virtuális gépekkel kapcsolatos tudnivalókat](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ismertető cikket. 
* A Dv2-, a D- és a G-sorozat, valamint ezek DS/GS megfelelői gyorsabb processzort, jobb helyi lemezteljesítményt vagy több memóriát igényló alkalmazásokhoz ideálisak.  Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.
* Az F-sorozat virtuális gépei remek választásnak bizonyulnak az olyan számítási feladatokhoz, amelyekhez gyorsabb processzor szükséges, azonban kisebb a processzormagonkénti memória- vagy helyi SSD-igényük.  Az analitikai alkalmazások, játékkiszolgálók, webkiszolgálók vagy kötegelt feldolgozások számítási feladatai számára előnyös az F-sorozat használata.
* Az Azure-központokban lévő némelyik fizikai gazdagép nem képes futtatni a nagyobb virtuálisgép-méreteket, például az A5–A11 méreteket. Ennek okán a rendszer **A virtuális gép konfigurálása meghiúsult<machine name>** vagy **A virtuális gép létrehozása meghiúsult<machine name>** hibaüzenetet adhatja vissza, ha megkísérel átméretezni egy meglévő virtuális gépet; ha egy 2013. április 16. előtt létrehozott virtuális hálózatban kísérel meg új virtuális gépet létrehozni; vagy ha új virtuális gépet kísérel meg felvenni egy meglévő felhőszolgáltatásba. Az egyes üzembe helyezési forgatókönyvekben alkalmazható kerülő megoldásokért lásd a [Error: “Failed to configure virtual machine”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (Hiba: „A virtuális gép konfigurálása meghiúsult”) című témakört a támogatási fórumon.  
* Az előfizetés is korlátozhatja az egyes családokban üzembe helyezhető magok mennyiségét. A kvóták növelésével kapcsolatban vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
Az Azure-alapú számítási egység (Azure Compute Unit, ACU) fogalmát azért vezettük be, hogy lehetséges legyen összehasonlítani az egyes Azure-termékváltozatok számítási (CPU-) teljesítményét. Így könnyebben választhatja ki a teljesítményigényeinek leginkább megfelelő termékváltozatot.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet. 

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál.  Az egyes számítási terhelések eredményei ettől eltérhetnek. 
> 
> 

<br>

| Termékváltozat-család | ACU/mag |
| --- | --- |
| [A0](#a-series) |50 |
| [A1–A4](#a-series) |100 |
| [A5–A7](#a-series) |100 |
| [A1_v2–A8_v2](#av2-series) |100 |
| [A2m_v2–A8m_v2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1–D14](#d-series) |160 |
| [D1_v2–D15_v2](#dv2-series) |210 - 250* |
| [DS1–DS14](#ds-series) |160 |
| [DS1_v2–DS15_v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1–G5](#g-series) |180 - 240* |
| [GS1–GS5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |
| [L4s-L32s](#l-series) |180 - 240* |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt.  A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="size-tables"></a>Mérettáblázatok
Az alábbi táblázatokban a méretek és azok kapacitásai láthatóak.

* A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
* A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
* Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek.  Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva.  Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
* A maximális hálózati sávszélesség a virtuálisgép-típusonként kiosztott és hozzárendelt maximális összesített sávszélesség. A maximális sávszélesség iránymutatást nyújthat a megfelelő VM-típus kiválasztása során, hogy biztosítható legyen a megfelelő hálózati kapacitás rendelkezésre állása. A Low (Alacsony), Moderate (Közepes), High (Magas) és Very High (Nagyon magas) értékek közt váltva a teljesítmény ennek megfelelően nő. A tényleges hálózati teljesítmény számos tényezőtől függ, többek közt a hálózat és az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól.

## <a name="a-series"></a>A-sorozat
| Méret | Processzormagok | Memória: GiB | Helyi merevlemez: GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0.768 |20 |1 |1x500 |1/alacsony |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |1/közepes |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |1/közepes |
| Standard_A3 |4 |7 |285 |8 |8x500 |2/magas |
| Standard_A4 |8 |14 |605 |16 |16x500 |4/magas |
| Standard_A5 |2 |14 |135 |4 |4x500 |1/közepes |
| Standard_A6 |4 |28 |285 |8 |8x500 |2/magas |
| Standard_A7 |8 |56 |605 |16 |16x500 |4/magas |

<br>

## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok
A méretek használatával kapcsolatos információkért és szempontokért lásd a [H-sorozatú és a nagy számítási igényű A-sorozatú virtuális gépekkel kapcsolatos tudnivalókat](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Méret | Processzormagok | Memória: GiB | Helyi merevlemez: GiB | Adatlemezek max. száma | Adatlemezek max. teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2/magas |
| Standard_A9* |16 |112 |382 |16 |16x500 |4/nagyon magas |
| Standard_A10 |8 |56 |382 |16 |16x500 |2/magas |
| Standard_A11 |16 |112 |382 |16 |16x500 |4/nagyon magas |

*RDMA-kompatibilis

<br>

## <a name="av2-series"></a>Av2-sorozat

| Méret            | Processzormagok | Memória: GiB | Helyi SSD: GiB | Helyi lemez max. teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500                         | 1/közepes                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500                         | 2/közepes                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500                         | 4/magas                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500                       | 8/magas                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500                         | 2/közepes                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500                         | 4/magas                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500                       | 8/magas                     |

## <a name="d-series"></a>D-sorozat

| Méret         | Processzormagok | Memória: GiB | Helyi SSD: GiB | Helyi lemez max. teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 2 / 2x500                         | 1/közepes                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2/magas                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4/magas                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8/magas                     |
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2/magas                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4/magas                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8/magas                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8/nagyon magas                |
<br>

## <a name="dv2-series"></a>Dv2-sorozat


| Méret              | Processzormagok | Memória: GiB | Helyi SSD: GiB | Helyi lemez max. teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 2 / 2x500                         | 1/közepes                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2/magas                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4/magas                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8/magas                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8/rendkívül magas           |
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4x500                         | 2/magas                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8x500                         | 4/magas                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8/magas                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8/rendkívül magas           |
| Standard_D15_v2** | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 40 / 40x500                       | 8/rendkívül magas*          |

*Egyes régiókban a Standard_D15_v2 méret elérhető gyorsított hálózatkezeléssel is. Ennek használatával és rendelkezésre állásával kapcsolatos információkért lásd [a gyorsított hálózatkezelés az előzetes verzióban történő elérhetővé válását](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) és a [virtuális gép gyorsított hálózatkezelését](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md) ismertető cikket.

**A példány ügyfelenként külön dedikált hardveren üzemel.

<br>

## <a name="ds-series"></a>DS-sorozat*
| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és helyi lemezteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 32 |1/közepes |
| Standard_DS2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 64 |2/magas |
| Standard_DS3 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 128 |4/magas |
| Standard_DS4 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 256 |8/magas |
| Standard_DS11 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 64 |2/magas |
| Standard_DS12 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 128 |4/magas |
| Standard_DS13 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 256 |8/magas |
| Standard_DS14 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 512 |8/nagyon magas |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

*A DS-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket.

<br>

## <a name="dsv2-series"></a>DSv2-sorozat*
| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Max. gyorsítótárazott lemezteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 48 |1/közepes |
| Standard_DS2_v2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 96 |2/magas |
| Standard_DS3_v2 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 192 |4/magas |
| Standard_DS4_v2 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 384 |8/magas |
| Standard_DS5_v2 |16 |56 |112 |32 |64,000 / 512 (688) |51,200 / 768 |8/rendkívül magas |
| Standard_DS11_v2 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 96 |2/magas |
| Standard_DS12_v2 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 192 |4/magas |
| Standard_DS13_v2 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 384 |8/magas |
| Standard_DS14_v2 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 768 |8/rendkívül magas |
| Standard_DS15_v2*** |20 |140 |280 |40 |80,000 / 640 (720) |64,000 / 960 |8/rendkívül magas** |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

*A DSv2-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket.

**Egyes régiókban a Standard_DS15_v2 méret elérhető gyorsított hálózatkezeléssel is. Ennek használatával és rendelkezésre állásával kapcsolatos információkért lásd [a gyorsított hálózatkezelés az előzetes verzióban történő elérhetővé válását](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) és a [virtuális gép gyorsított hálózatkezelését](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md) ismertető cikket.

***A példány ügyfelenként külön dedikált hardveren üzemel.
<br>

## <a name="f-series"></a>F-sorozat

| Méret         | Processzormagok | Memória: GiB | Helyi SSD: GiB | Helyi lemez max. teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2x500                         | 1/közepes                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4x500                         | 2/magas                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8x500                         | 4/magas                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16x500                       | 8/magas                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32x500                       | 8/rendkívül magas           |
<br>

## <a name="fs-series"></a>Fs-sorozat*
| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és helyi lemezteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |1/közepes |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2/magas |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4/magas |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8/magas |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8/rendkívül magas |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

*Az Fs-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja.  Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket.

<br>

## <a name="g-series"></a>G-sorozat

| Méret         | Processzormagok | Memória: GiB | Helyi SSD: GiB | Helyi lemez max. teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 1/magas                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8x500                       | 2/magas                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4/nagyon magas                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8/rendkívül magas           |
| Standard_G5* | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8/rendkívül magas           |

*A példány ügyfelenként külön dedikált hardveren üzemel.
<br>

## <a name="gs-series"></a>GS-sorozat*
| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és helyi lemezteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10,000 / 100 (264) |5,000 / 125 |1/magas |
| Standard_GS2 |4 |56 |112 |8 |20,000 / 200 (528) |10,000 / 250 |2/magas |
| Standard_GS3 |8 |112 |224 |16 |40,000 / 400 (1,056) |20,000 / 500 |4/nagyon magas |
| Standard_GS4 |16 |224 |448 |32 |80,000 / 800 (2,112) |40,000 / 1,000 |8/rendkívül magas |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8/rendkívül magas |

MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt.

*A GS-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket. 

**A példány ügyfelenként külön dedikált hardveren üzemel.
<br>

## <a name="h-series"></a>H-sorozat
Az Azure H-sorozat virtuális gépei a nagy teljesítményű feldolgozásra képes virtuális gépek következő generációja, amelyek olyan magas szintű számítási igényekre lettek kialakítva, mint a molekuláris modellezés vagy a folyadékdinamikai számítások. Ezek a 8- és 16-magos virtuális gépek az Intel Haswell E5-2667 V3 processzor technológiára épülnek, valamint DDR4 memóriával és helyi SSD-alapú tárolóval rendelkeznek. 

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.

A méretek használatával kapcsolatos információkért és szempontokért lásd a [H-sorozatú és a nagy számítási igényű A-sorozatú virtuális gépekkel kapcsolatos tudnivalókat](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Hálózati adapterek max. száma/hálózati sávszélesség |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16x500 |2/magas |
| Standard_H16 |16 |112 |2000 |32 |32x500 |4/nagyon magas |
| Standard_H8m |8 |112 |1000 |16 |16x500 |2/magas |
| Standard_H16m |16 |224 |2000 |32 |32x500 |4/nagyon magas |
| Standard_H16r* |16 |112 |2000 |32 |32x500 |4/nagyon magas |
| Standard_H16mr* |16 |224 |2000 |32 |32x500 |4/nagyon magas |

*RDMA-kompatibilis

<br>


## <a name="ls-series"></a>Ls-sorozat* 

Az Ls-sorozat kis késleltetésű, helyi tárolást igénylő számítási feladatokhoz van optimalizálva. Ilyenek például az NoSQL-adatbázisok (pl. a Cassandra, a MongoDB, a Cloudera és a Redis). Az Ls-sorozat akár 32, az [Intel® Xeon® E5 v3 processzorcsaládba](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) tartozó processzormagot is biztosíthat. Ez ugyanakkora teljesítményt jelent, mint a G/GS-sorozat esetében, és processzormagonként 8 GiB memóriát biztosít.  

 
| Méret          | Processzormagok | Memória: GiB | Helyi SSD: GiB | Adatlemezek max. száma | Max. gyorsítótárazott lemezteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek max. száma/hálózati sávszélesség | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5,000 / 125                               | 2/magas       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA / NA (0)          | 10,000 / 250                              | 4/nagyon magas  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA / NA (0)          | 20,000 / 500                              | 8/rendkívül magas | 
| Standard_L32s** | 32   | 256  | 5,630 | 64             | NA / NA (0)          | 40,000 / 1,000                            | 8/rendkívül magas | 
 
MBps = 10^6 bájt/másodperc és GiB = 1024^3 bájt. 

*Az LS-sorozatba tartozó virtuális gépek lehetséges maximális lemezteljesítményét (IOPS vagy MBps) a csatolt lemezek mennyisége, mérete és szétosztottsága befolyásolhatja. Részletekért lásd a [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-alapú virtuális gépek számítási feladataihoz](../articles/storage/storage-premium-storage.md) című cikket. 

**A példány ügyfelenként külön dedikált hardveren üzemel.



## <a name="n-series"></a>N-sorozat
Az NC- és NV-méretek más néven GPU-kompatibilis példányokként is ismertek. Ezek olyan speciális virtuális gépek, amelyek az NVIDIA grafikus (GPU-) kártyáival rendelkeznek, és különféle forgatókönyvekre és használati esetekre vannak optimalizálva. Az NV-méretek OpenGL, DirectX és hasonló keretrendszereket használó távoli megjelenítési, streamelési, játék-, kódolási és VDI-forgatókönyvekhez lettek kialakítva és optimalizálva. Az NC-méretek inkább nagy számítási és hálózatigényű alkalmazásokra és algoritmusokra vannak optimalizálva, beleértve a CUDA- és OpenCL-alapú alkalmazásokat és szimulációkat. 


### <a name="nv-instances"></a>NV-példányok
A gyorsított asztali alkalmazások és virtuális asztalok esetében az NVIDIA által gyártott Tesla M60 GPU kártya és az NVIDIA GRID működteti az NV-példányokat, így az ügyfelek hatékonyan jeleníthetik meg adataikat vagy szimulációikat. Az ügyfelek a nagy grafikai igényű számítási feladataikat az NV-példányokon megjelenítve kiemelkedő grafikai teljesítményt érhetnek le, illetve futtathatnak különálló precíziós számítási feladatokat is, mint amilyen a konvertálás vagy a renderelés. A Tesla M60 két grafikus kártyát tartalmazó kialakítása 4096 CUDA-magot biztosít akár 36 egyidejű 1080p H.264 kódolású streammel. 


| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = fél M60 kártya.

**Támogatott operációs rendszerek**

* Windows Server 2016, Windows Server 2012 R2 – lásd [az N-sorozat illesztőinek Windows esetében történő beállítását](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md) ismertető cikket

### <a name="nc-instances"></a>NC-példányok
Az NC-példányok az NVIDIA által gyártott Tesla K80 kártyát használják. A felhasználók a CUDA kiaknázásával sokkal gyorsabban dolgozhatják fel az adatokat az energiafeltérképező alkalmazások, az ütközésszimulációk, a sugárkövetéses renderelés, a mély tanulás és egyebek terén. A Tesla K80 két grafikai kártyát tartalmazó kialakítása 4992 CUDA-magot biztosít, akár 2,91 Teraflop kétszeres pontosságú vagy 8,93 Teraflop egyszeres pontosságú teljesítménnyel.

| Méret | Processzormagok | Memória: GiB | Helyi SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = fél K80-kártya.

*RDMA-kompatibilis

**Támogatott operációs rendszerek**

* Windows Server 2016, Windows Server 2012 R2 – lásd [az N-sorozat illesztőinek Windows esetében történő beállítását](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md) ismertető cikket
* Ubuntu 16.04 LTS – lásd [az N-sorozat illesztőinek Linux rendszer esetében történő beállítását](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md) ismertető cikket

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Megjegyzések: Standard A0–A4, CLI és PowerShell használatával
A klasszikus üzemi modellben egyes virtuálisgép-méretek neve eltérő a CLI-ben és a PowerShellben:

* A Standard_A0 neve ExtraSmall 
* A Standard_A1 neve Small
* A Standard_A2 neve Medium
* A Standard_A3 neve Large
* A Standard_A4 neve ExtraLarge

## <a name="next-steps"></a>Következő lépések
* Ismerje meg [az Azure-előfizetések és -szolgáltatások korlátozásait, kvótáit és megkötéseit](../articles/azure-subscription-service-limits.md).
* További [tudnivalók a H-sorozatú és a nagy számítási igényű A-sorozatú virtuális gépekről](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a nagy teljesítményű feldolgozási (HPC) alkalmazásokhoz.

