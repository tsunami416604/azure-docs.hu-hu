---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67673366"
---
*A gyorsítótár bemelegítése*  
A írásvédett gazdagép gyorsítótárazásával rendelkező lemez magasabb IOPS tud adni, mint a lemez korlátja. A maximális olvasási teljesítmény eléréséhez a gazdagép-gyorsítótárból először be kell melegíteni a lemez gyorsítótárát. Ezzel biztosíthatja, hogy a teljesítményértékelési eszköz által a CacheReads-köteten lévő olvasási IOs legyen, valójában a gyorsítótárba kerül, nem pedig közvetlenül a lemezre. A gyorsítótárbeli találatok további IOPS eredményeznek az egyetlen gyorsítótárral kompatibilis lemezről.

> [!IMPORTANT]
> A teljesítményteszt futtatása előtt be kell melegíteni a gyorsítótárat, minden alkalommal, amikor a virtuális gép újraindul.

## <a name="tools"></a>Eszközök

### <a name="iometer"></a>Iometer

[Töltse le a Iometer eszközt](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a virtuális gépen.

#### <a name="test-file"></a>Fájl tesztelése

A Iometer egy olyan tesztoldalt használ, amely azon a köteten található, amelyen a teljesítményteszt-tesztet futtatja. Ez a teszt fájl olvasását és írását hajtja a lemez IOPS és átviteli sebességének méréséhez. Ha nem adott meg ilyet, a Iometer létrehozza ezt a tesztoldalt. Hozzon létre egy iobw. TST nevű 200 GB-os tesztelési fájlt a CacheReads és a NoCacheWrites köteteken.

#### <a name="access-specifications"></a>Hozzáférési specifikációk

A specifikációk, a kérések i/o-mérete, a% Read/Write, a% Random/szekvenciális a Iometer hozzáférési specifikációk lapján konfigurálhatók. Hozzon létre egy hozzáférési specifikációt az alább ismertetett forgatókönyvekhez. Hozza létre a hozzáférési specifikációkat és a mentést egy megfelelő névvel, például: – RandomWrites \_ 8K, RandomReads \_ 8K. A tesztelési forgatókönyv futtatásakor válassza ki a megfelelő specifikációt.

Alább látható egy példa a maximális írási IOPS forgatókönyvhöz tartozó hozzáférési specifikációra.  
    ![Példa a maximális írási IOPS hozzáférési előírásaira](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>IOPS-tesztelési előírások maximális száma

A maximális IOPs megjelenítéséhez használja a kisebb kérések méretét. A 8K-kérelmek méretének használata és a véletlenszerű írások és olvasások előírásainak létrehozása.

| Hozzáférési specifikáció | Kérelem mérete | Véletlenszerű | Olvasni |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8 E |100 |0 |
| RandomReads \_ 8K |8 E |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maximális teljesítmény-tesztelési előírások

A maximális átviteli sebesség kimutatása érdekében használjon nagyobb méretű kéréseket. 64 K kérések méretének használata, valamint a véletlenszerű írások és olvasások előírásainak létrehozása.

| Hozzáférési specifikáció | Kérelem mérete | Véletlenszerű | Olvasni |
| --- | --- | --- | --- |
| RandomWrites \_ 64 kb |64 K |100 |0 |
| RandomReads \_ 64 kb |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>A Iometer teszt futtatása

Hajtsa végre az alábbi lépéseket a gyorsítótár bemelegítéséhez

1. Hozzon létre két hozzáférési specifikációt az alább látható értékekkel,

   | Name | Kérelem mérete | Véletlenszerű | Olvasni |
   | --- | --- | --- | --- |
   | RandomWrites \_ 1mb |1 MB |100 |0 |
   | RandomReads \_ 1mb |1 MB |100 |100 |
1. Futtassa a Iometer tesztet a gyorsítótár-lemez inicializálásához a következő paraméterekkel. Használjon három munkavégző szálat a célként megadott kötethez, valamint egy 128-es üzenetsor-mélységet. Állítsa a teszt "futtatási idő" időtartamát 2 órára a "teszt beállítása" lapon.

   | Forgatókönyv | Cél kötete | Name | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótár lemezének inicializálása |CacheReads |RandomWrites \_ 1mb |2 óra |
1. Futtassa a Iometer tesztet a gyorsítótár-lemez bemelegítéséhez a következő paraméterekkel. Használjon három munkavégző szálat a célként megadott kötethez, valamint egy 128-es üzenetsor-mélységet. Állítsa a teszt "futtatási idő" időtartamát 2 órára a "teszt beállítása" lapon.

   | Forgatókönyv | Cél kötete | Name | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótár-lemez bemelegítése |CacheReads |RandomReads \_ 1mb |2 óra |

A gyorsítótár-lemez bemelegítése után folytassa az alább felsorolt tesztelési forgatókönyvekkel. A Iometer teszt futtatásához legalább három munkavégző szálat kell használnia **minden egyes** cél kötethez. Minden munkavégző szál esetében válassza ki a cél kötetet, állítsa be a várólista mélységét, és válassza ki a mentett tesztelési specifikációk egyikét az alábbi táblázatban látható módon a megfelelő tesztelési forgatókönyv futtatásához. Ezen tesztek futtatásakor a táblázat a IOPS és az átviteli sebesség várható eredményeit is megjeleníti. Az összes forgatókönyv esetében a rendszer a 8 KB-os kis i/o-méretet és a 128-es magas üzenetsor-mélységet használja.

| Tesztelési forgatókönyv | Cél kötete | Name | Eredmény |
| --- | --- | --- | --- |
| Legfeljebb IOPS olvasása |CacheReads |RandomWrites \_ 8K |50 000 IOPS |
| Legfeljebb IOPS írása |NoCacheWrites |RandomReads \_ 8K |64 000 IOPS |
| Legfeljebb Kombinált IOPS |CacheReads |RandomWrites \_ 8K |100 000 IOPS |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Legfeljebb Olvasási sebesség (MB/s) |CacheReads |RandomWrites \_ 64 kb |524 MB/s |
| Legfeljebb Írási sebesség (MB/s) |NoCacheWrites |RandomReads \_ 64 kb |524 MB/s |
| Összesített MB/s |CacheReads |RandomWrites \_ 64 kb |1000 MB/s |
| NoCacheWrites |RandomReads \_ 64 kb | &nbsp; | &nbsp; |

Az alábbi Képernyőképek a Iometer-teszt eredményeiből állnak a kombinált IOPS és az átviteli sebességi forgatókönyvek esetében.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinált olvasások és írások maximális IOPS

![Kombinált olvasások és írások maximális IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinált olvasások és írások maximális átviteli sebessége

![Kombinált olvasások és írások maximális átviteli sebessége](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

A FIO egy népszerű eszköz, amely a Linux rendszerű virtuális gépeken használható. Rugalmasan választhatja ki a különböző IO-méreteket, a szekvenciális vagy véletlenszerű olvasási és írási műveleteket. Feldolgozói szálakat vagy folyamatokat indít el a megadott I/O-műveletek végrehajtásához. Megadhatja az egyes munkaszálakhoz tartozó I/O-műveletek típusát. Egy, az alábbi példákban bemutatott feladattípust hoztunk létre. A feladathoz tartozó fájlok specifikációi a Premium Storageon futó különböző számítási feladatok teljesítménytesztéhez módosíthatók. A példákban egy **Ubuntut**futtató standard DS 14 virtuális gépet használunk. Használja ugyanazt a telepítőt, amely a teljesítményértékelési szakasz elején szerepel, és a teljesítménytesztek futtatása előtt bemelegíti a gyorsítótárat.

Mielőtt elkezdené, [töltse le a Fio](https://github.com/axboe/fio) , és telepítse a virtuális gépre.

Futtassa az alábbi parancsot az Ubuntuhoz,

```
apt-get install fio
```

Négy munkaszálat használunk az írási műveletekhez és négy munkaszálhoz a lemezek olvasási műveleteinek elvégzéséhez. Az írási feldolgozók a "nincs" értékre beállított gyorsítótárral rendelkező 10 lemezről vezetik a forgalmat. Az olvasási feldolgozók a "readcache" köteten vezetik a forgalmat, amely egy olyan lemezzel rendelkezik, amelynek a gyorsítótára "ReadOnly" értékre van állítva.

#### <a name="maximum-write-iops"></a>Maximális írási IOPS

Hozza létre a fájlt a következő specifikációkkal a maximális írási IOPS lekéréséhez. Nevezze el "fiowrite.ini".

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

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,  

* 256 magas üzenetsor-mélysége.  
* A kis blokk mérete 8 KB.  
* Több szál véletlenszerű írásokat végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.  

```
sudo fio --runtime 30 fiowrite.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek írási IOPS számát. Ahogy az alábbi példában is látható, a DS14 virtuális gép a maximális írási IOPS korlátját adja meg 50 000 IOPS.  
    ![Az írási IOPS virtuális gép és a prémium szintű lemezek száma](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Olvasási IOPS maximális száma

Hozza létre a fájlt a következő specifikációkkal a maximális olvasási IOPS lekéréséhez. Nevezze el "fioread.ini".

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

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,

* 256 magas üzenetsor-mélysége.  
* A kis blokk mérete 8 KB.  
* Több szál véletlenszerű írásokat végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.

```
sudo fio --runtime 30 fioread.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek olvasási IOPS számát. Ahogy az alábbi példában is látható, a DS14 virtuális gép több mint 64 000 olvasási IOPS. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Olvasási és írási IOPS maximális száma

Hozza létre a fájlt a következő specifikációkkal a kombinált olvasási és írási IOPS maximális számának lekéréséhez. Nevezze el "fioreadwrite.ini".

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

Vegye figyelembe, hogy kövesse az előző szakaszokban tárgyalt tervezési irányelvekkel összhangban lévő legfontosabb dolgokat. Ezek a specifikációk elengedhetetlenek a maximális IOPS meghajtásához,

* 128 magas üzenetsor-mélysége.  
* A kis blokk mérete 4 KB.  
* Több szál véletlenszerű olvasási és írási műveleteket végez.

Futtassa a következő parancsot a FIO-teszt 30 másodpercig való kikapcsolásához.

```
sudo fio --runtime 30 fioreadwrite.ini
```

A teszt futtatása közben megtekintheti a virtuális gép és a prémium szintű lemezek együttes olvasási és írási IOPS számát. Ahogy az alábbi példában is látható, a DS14 virtuális gép több mint 100 000 kombinált olvasási és írási IOPS biztosít. Ez a lemez és a gyorsítótár teljesítményének kombinációja.  
    ![Kombinált olvasási és írási IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Kombinált átviteli sebesség maximális száma

A kombinált olvasási és írási sebesség maximális mennyiségének lekéréséhez használjon nagyobb blokk-méretet és nagy várólista-mélységet több, olvasást és írást végző szál használatával. A blokk mérete 64 KB, a várólista mélysége pedig 128.
