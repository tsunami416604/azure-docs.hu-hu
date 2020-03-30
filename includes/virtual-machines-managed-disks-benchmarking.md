---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673366"
---
*A gyorsítótár bemelegítése*  
A Csak olvasási állomásgyorsítótárazásával rendelkező lemez magasabb IOPS-t tud adni, mint a lemezkorlát. Ahhoz, hogy ezt a maximális olvasási teljesítményt a gazdagyorsítótárból lehessze, először be kell melegítenie a lemez gyorsítótárát. Ez biztosítja, hogy a read IOs, hogy a benchmarking eszköz fog vezetni cachereads kötet, ténylegesen eléri a gyorsítótárat, és nem a lemez közvetlenül. A gyorsítótár-lekérések további IOPS-t eredményeznek az egyetlen gyorsítótárban engedélyezett lemezről.

> [!IMPORTANT]
> A teljesítményértékelés futtatása előtt be kell melegítenie a gyorsítótárat, minden alkalommal, amikor a virtuális gép újraindul.

## <a name="tools"></a>Eszközök

### <a name="iometer"></a>Iometer

[Töltse le az Iometer eszközt](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a virtuális gépről.

#### <a name="test-file"></a>Tesztfájl

Az Iometer egy tesztfájlt használ, amely azon a köteten van tárolva, amelyen a teljesítményértékelési tesztet futtatja. Ez a meghajtók olvasások és írások ezen a tesztfájlon a lemez IOPS és átviteli. Az Iometer létrehozza ezt a tesztfájlt, ha nem adott meg ilyenfájlt. Hozzon létre egy 200 GB-os tesztfájlt iobw.tst néven a CacheReads és a NoCacheWrites köteteken.

#### <a name="access-specifications"></a>Hozzáférési előírások

A specifikációk, a kérelem i/o-mérete, az olvasás/írás % százaléka, a véletlenszerű/szekvenciális % az Iometer "Hozzáférési specifikációk" lapján vannak konfigurálva. Hozzon létre egy hozzáférési specifikációt az alábbiakban ismertetett forgatókönyvekhez. Hozza létre a hozzáférési specifikációk és a "Mentés"\_a megfelelő\_nevet, mint a - RandomWrites 8K, RandomReads 8K. Válassza ki a megfelelő specifikációt a tesztforgatókönyv futtatásakor.

A maximális írási IOPS-forgatókönyv hozzáférési specifikációinak egyik példája az alábbiakban látható:  
    ![Példa a maximális írási IOPS hozzáférési specifikációira](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maximális IOPS-vizsgálati specifikációk

A maximális IPP-k bemutatásához használjon kisebb kérelemméretet. Használja a 8K kérelem méretét, és hozzon létre specifikációkat véletlenszerű írások és olvasások.

| Hozzáférési specifikáció | A kérelem mérete | Véletlenszerű % | Olvasás % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 E |100 |0 |
| RandomReads\_8K |8 E |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maximális átviteli teszt specifikációk

A maximális átviteli teljesítmény bemutatásához használjon nagyobb kérelemméretet. Használja a 64 K kérelem méretét, és hozzon létre specifikációkvéletlenszerű írások és olvasások.

| Hozzáférési specifikáció | A kérelem mérete | Véletlenszerű % | Olvasás % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 Ezer |100 |0 |
| RandomReads\_64K |64 Ezer |100 |100 |

#### <a name="run-the-iometer-test"></a>Az Iometer-teszt futtatása

Hajtsa végre az alábbi lépéseket a gyorsítótár bemelegítéséhez

1. Hozzon létre két hozzáférési specifikációt az alábbi értékekkel,

   | Név | A kérelem mérete | Véletlenszerű % | Olvasás % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Futtassa az Iometer-tesztet a gyorsítótár lemezinicializálásához a következő paraméterekkel. A célkötethez három munkavégző szálat és 128-as várólista-mélységet használjon. Állítsa a "Futási idő" időtartamát a teszt 2 óra a "Teszt beállítása" lapon.

   | Forgatókönyv | Célmennyiség | Név | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótárlemez inicializálása |Gyorsítótárolvasások |RandomWrites\_1MB |2 óra |
1. Futtassa az Iometer tesztet a gyorsítótár-lemez következő paraméterekkel való bemelegítéséhez. A célkötethez három munkavégző szálat és 128-as várólista-mélységet használjon. Állítsa a "Futási idő" időtartamát a teszt 2 óra a "Teszt beállítása" lapon.

   | Forgatókönyv | Célmennyiség | Név | Időtartam |
   | --- | --- | --- | --- |
   | Bemelegedési gyorsítótárlemez |Gyorsítótárolvasások |RandomReads\_1MB |2 óra |

A gyorsítótár-lemez bemelegedése után folytassa az alább felsorolt tesztforgatókönyvekkel. Az Iometer-teszt futtatásához **használjon** legalább három munkavégző szálat minden célkötethez. Minden munkaszálhoz válassza ki a célkötetet, állítsa be a várólista mélységét, és válassza ki az alábbi táblázatban látható mentett tesztspecifikációk egyikét a megfelelő tesztforgatókönyv futtatásához. A táblázat is mutatja a várt eredményeket az IOPS és az átviteli eredmények a tesztek futtatásakor. Minden esetben egy kis I/O-mérete 8 KB és a magas várólista mélysége 128.

| Tesztforgatókönyv | Célmennyiség | Név | Eredmény |
| --- | --- | --- | --- |
| Legfeljebb IOPS olvasása |Gyorsítótárolvasások |RandomWrites\_8K |50 000 IOPS |
| Legfeljebb IOPS írása |NoCacheWrites |RandomReads\_8K |64 000 IOPS |
| Legfeljebb Kombinált IOPS |Gyorsítótárolvasások |RandomWrites\_8K |100 000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Legfeljebb Mb/mp olvasása |Gyorsítótárolvasások |RandomWrites\_64K |524 MB/mp |
| Legfeljebb Írás MB/mp |NoCacheWrites |RandomReads\_64K |524 MB/mp |
| Kombinált MB/mp |Gyorsítótárolvasások |RandomWrites\_64K |1000 MB/mp |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Az alábbiakban az Iometer teszteredményeinek képernyőképei taszanak le a kombinált IOPS- és átviteli forgatókönyvekhez.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinált olvasási és írási maximális IOPS

![Kombinált olvasásés írás maximális IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinált olvasási és írási maximális átviteli teljesítmény

![Kombinált olvasások és írások maximális átviteli](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

Fio egy népszerű eszköz a linuxos virtuális gépek tárolásának teljesítményértékelésére. Rugalmasan választhatja ki a különböző I/O-méreteket, szekvenciális vagy véletlenszerű olvasásokat és írásokat. Munkaszálakat vagy folyamatokat hoz létre a megadott I/O-műveletek végrehajtásához. Megadhatja, hogy az egyes munkaszálaknak milyen típusú I/O-műveleteket kell végrehajtaniuk a feladatfájlok használatával. Az alábbi példákban bemutatott forgatókönyvenként egy feladatfájlt hoztunk létre. Módosíthatja a specifikációk ezekben a feladatfájlokban a prémium szintű storage-on futó különböző számítási feladatok teljesítményének. A példákban egy **Ubuntut**futtató szabványos DS 14 VM-et használunk. Használja ugyanazt a beállítást, amelyet a Teljesítménypontozás szakasz elején ismertetett, és a teljesítményteszt-tesztek futtatása előtt melegítse be a gyorsítótárat.

Mielőtt elkezdené, [töltse le a FIO-t,](https://github.com/axboe/fio) és telepítse a virtuális gépre.

Futtassa a következő parancsot az Ubuntuhoz,

```
apt-get install fio
```

Négy munkaszálat használunk az írási műveletek vezetéséhez, és négy munkaszálat az olvasási műveletek lemezen történő vezetéséhez. Az írási dolgozók forgalmat generálnak a "nocache" köteten, amely 10 lemezt állít be "Nincs" gyorsítótárral. Az olvasási dolgozók forgalmat generálnak a "readcache" köteten, amelynek egyik lemeze "Csak olvasás" lett.

#### <a name="maximum-write-iops"></a>Írási IOPS maximális

Hozza létre a feladatfájlt a következő specifikációkkal a maximális írási IOPS leértékeléshez. Nevezd el "fiowrite.ini"-nek.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Vegye figyelembe az alábbi kulcsfontosságú dolgokat, amelyek összhangban vannak az előző szakaszokban tárgyalt tervezési irányelvekkel. Ezek a specifikációk elengedhetetlenek a maximális IOPS  

* A magas sormélység 256.  
* Egy kis blokk mérete 8 KB.  
* Több szál, amelyek véletlenszerű írásokat hajtanak végre.

Futtassa a következő parancsot a FIO teszt 30 másodpercre való  

```
sudo fio --runtime 30 fiowrite.ini
```

Ateszt futása közben láthatja a virtuális gép és a prémium szintű lemezek által kézbesített írási IOPS számát. Amint az alábbi mintában látható, a DS14 virtuális gép a maximális 50 000 IOPS-írási IOPS-korlátot biztosítja.  
    ![Az írási IOPS virtuális gépek és a prémium szintű lemezek száma](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximális olvasási IOPS

Hozza létre a feladatfájlt a következő specifikációkkal a maximális olvasási IOPS leolvasásához. Nevezd el "fioread.ini"-nek.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Vegye figyelembe az alábbi kulcsfontosságú dolgokat, amelyek összhangban vannak az előző szakaszokban tárgyalt tervezési irányelvekkel. Ezek a specifikációk elengedhetetlenek a maximális IOPS

* A magas sormélység 256.  
* Egy kis blokk mérete 8 KB.  
* Több szál, amelyek véletlenszerű írásokat hajtanak végre.

Futtassa a következő parancsot a FIO teszt 30 másodpercre való

```
sudo fio --runtime 30 fioread.ini
```

Ateszt futása közben láthatja a virtuális gép és a prémium szintű lemezek által kézbesített olvasási IOPS számát. Amint az alábbi mintában látható, a DS14 virtuális gép több mint 64 000 olvasási IOPS-t szállít. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximális olvasási és írási IOPS

Hozza létre a feladatfájlt a következő specifikációkkal, hogy maximális kombinált olvasási és írási IOPS.Create the job file with following specifications to get maximum combined Read and Write IOPS. Nevezd el "fioreadwrite.ini"-nek.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Vegye figyelembe az alábbi kulcsfontosságú dolgokat, amelyek összhangban vannak az előző szakaszokban tárgyalt tervezési irányelvekkel. Ezek a specifikációk elengedhetetlenek a maximális IOPS

* A magas sormélység 128.  
* Egy kis blokk mérete 4 KB.  
* Több szál, amelyek véletlenszerű olvasást és írást végeznek.

Futtassa a következő parancsot a FIO teszt 30 másodpercre való

```
sudo fio --runtime 30 fioreadwrite.ini
```

Ateszt futása közben láthatja a virtuális gép és a prémium szintű lemezek által szállított kombinált olvasási és írási IOPS-t. Amint az alábbi mintában látható, a DS14 virtuális gép több mint 100 000 kombinált olvasási és írási IOPS-t szállít. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    ![Kombinált olvasási és írási IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximális kombinált átviteli teljesítmény

A maximális kombinált olvasási és írási átviteli teljesítmény eléréséhez használjon nagyobb blokkméretet és nagy várólistamélységet több írási és írási műveletet végző szállal. 64 KB-os blokkméretet és 128-as várólista-mélységet használhat.
