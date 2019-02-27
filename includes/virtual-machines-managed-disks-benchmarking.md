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
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890994"
---
*A gyorsítótár Bemelegedés*  
A lemezt a csak olvasható állomás-gyorsítótárazás is biztosítanak a lemez korlátnál magasabb iops-t. A maximális olvasási teljesítmény eléréséhez a gazdagép-gyorsítótárból, először meg kell ízelítőt kapott a gyorsítótár a lemezről. Ez biztosítja, hogy az, hogy a teljesítménymérési eszköz CacheReads köteten vonják olvasási IOs ténylegesen eléri a gyorsítótár, és nem a lemezt közvetlenül. Az egyetlen gyorsítótár további IOPS gyorsítótár-találatok eredményez engedélyezve van a lemez.

> [!IMPORTANT]
> A gyorsítótár futtatása, a teljesítmény mérésére, minden alkalommal, amikor a virtuális gép újraindítása előtt kell ízelítőt.

## <a name="tools"></a>Eszközök

### <a name="iometer"></a>Iometer

[Töltse le a Iometer eszközt](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a virtuális gépen.

#### <a name="test-file"></a>Fájl tesztelése

Iometer egy tesztfájlt, amely a köteten, amelyen a teljesítménymérési teszt futtatásakor használ. Ez olvasási meghajtók, és a fájl tesztelése és mérhető, a lemez írási IOPS és átviteli sebesség. Iometer a tesztfájlt hoz létre, ha nincs megadva, az egyik. Hozzon létre egy 200 GB-os tesztfájlt iobw.tst nevű CacheReads és NoCacheWrites kötetekre.

#### <a name="access-specifications"></a>Access-leírások

A specifikációk kérés i/o-mérete, % olvasási/írási, % véletlenszerű/soros konfigurált Iometer "Access specifikációi" lapján. Hozzon létre egy hozzáférési leírása az alábbiakban leírt forgatókönyveket. Hozzon létre hozzáférés előírásoknak és "Mentés" megfelelő nevezze hasonlóan – RandomWrites\_8K RandomReads\_8 K. Válassza ki a megfelelő specifikáció, ha a vizsgálati eset futtatása.

Hozzáférés specifikációk maximális írási IOPS-forgatókönyvhöz példa az alábbi  
    ![Hozzáférés specifikációk írási iops-t – példa](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maximális IOPS specifikációk tesztelése

Maximális IOPs mutatja be, használja a kérést kisebb méretet. Használja a 8K kérés méretét, és hozzon létre a véletlenszerű írások és olvasások specifikációk.

| Hozzáférési leírása | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maximális átviteli sebesség tesztelése specifikációk

Maximális átviteli sebesség bemutatása érdekében használja a kérelem nagyobb méretű. 64 K kérés mérete használja, és a véletlenszerű írások és olvasások leírások létrehozásához.

| Hozzáférési leírása | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
| --- | --- | --- | --- |
| RandomWrites\_64 K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>A Iometer teszt futtatása

Hajtsa végre a gyorsítótár ízelítőt az alábbi lépéseket

1. Hozzon létre két hozzáférési specifikációk alább látható értékekkel

   | Name (Név) | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Futtassa a következő paraméterekkel gyorsítótárlemez inicializálása Iometer tesztet. Három munkaszálak használata a célkötet és a egy várólistamélység 128. A teszt "Futási idő" időtartamának beállítása: 2 óra "Beállítás tesztelése" lapon.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótárlemez inicializálása |CacheReads |RandomWrites\_1 MB |2 óra |
1. Futtassa a következő paraméterekkel gyorsítótárlemez Bemelegedés Iometer tesztet. Három munkaszálak használata a célkötet és a egy várólistamélység 128. A teszt "Futási idő" időtartamának beállítása: 2 óra "Beállítás tesztelése" lapon.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Meleg Gyorsítótárlemez mentése |CacheReads |RandomReads\_1 MB |2 óra |

Után gyorsítótárlemez bemelegíteni van, folytassa az alább felsorolt tesztcélú forgatókönyveket. A Iometer teszt futtatásához használja legalább három feldolgozó szálak **egyes** mennyiségi cél. Az egyes munkavégző szál válassza ki a célkötet, várólistamélységének beállítása, és válasszon a mentett teszt előírások, ahogyan az az alábbi táblázat a megfelelő vizsgálati eset futtatása. Ezek a tesztek futtatása a táblázatban is látható várt eredmény IOPS és átviteli sebességet. Minden esetben egy kis méretű i/o 8 KB-os és a egy 128 magas várólistamélység szolgál.

| Tesztkörnyezet | Célkötet | Name (Név) | Eredmény |
| --- | --- | --- | --- |
| Legfeljebb Olvasási iops-érték |CacheReads |RandomWrites\_8K |50 000 IOPS-ÉRTÉK |
| Legfeljebb Az írási IOPS |NoCacheWrites |RandomReads\_8K |AKÁR 64 000 IOPS-ÉRTÉK |
| Legfeljebb Kombinált IOPS |CacheReads |RandomWrites\_8K |100 000 IOPS-ÉRTÉK |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Legfeljebb Read MB/sec |CacheReads |RandomWrites\_64 K |524 MB/mp |
| Legfeljebb MB/mp/írás |NoCacheWrites |RandomReads\_64K |524 MB/mp |
| Kombinált MB/mp |CacheReads |RandomWrites\_64 K |1000 MB/mp |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Az alábbiakban a Iometer pillanatképeiért teszteredmények kombinált IOPS és átviteli sebesség forgatókönyvekhez.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Kombinált olvasási és írási maximális iops-érték

![Egyesített írási és olvasási maximális iops-érték](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombinált olvasási és írási maximális átviteli sebesség

![Egyesített írási és olvasási maximális átviteli sebesség](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO egy olyan népszerű eszköz a teljesítményteszt storage a Linuxos virtuális gépeken. Azt a rugalmasságot, jelölje be a különböző IO méretű, egymást követő vagy véletlenszerű olvasást, és írja. Ez indít a munkaszálak vagy folyamatok a megadott i/o-műveletek végrehajtásához. Megadhatja az egyes munkavégző szál feladat fájlok használatával kell végrehajtania i/o-műveletek típusa. Létrehoztunk egy feladat-fájlt az alábbi példákban szemléltetett forgatókönyv szerint. A feladat fájlokban becslésére a Premium Storage futó különböző számítási feladatok specifikációit módosíthatja. Standard DS 14 virtuális gép futó használjuk a példákban **Ubuntu**. Telepítőjével az ugyanazon a Benchmarking szakasz elején leírt és a meleg fel a gyorsítótárat a teljesítménymérési tesztek futtatása előtt.

Mielőtt elkezdené, [FIO letöltése](https://github.com/axboe/fio) , és telepítse a virtuális gépen.

Futtassa a következő parancsot az Ubuntu rendszeren

```
apt-get install fio
```

Írási műveletek a négy munkaszálak és négy munkaszálak használunk a lemezeken környezetbarát olvasási művelet. Az írási feldolgozók biztosítják a forgalmat a "nocache" köteten lévő, az értéke "Nincs" cache 10 lemezzel rendelkezik. Az olvasási feldolgozók biztosítják a forgalmat a "readcache" köteten, amely rendelkezik egy lemezt a gyorsítótár beállítása "Csak olvasható".

#### <a name="maximum-write-iops"></a>Maximális írási iops-érték

Hozzon létre következő előírásoknak írási IOPS maximális beolvasni a feladat-fájlt. Nevezze el "fiowrite.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó  

* Egy 256 magas várólista mélységét.  
* Kis blokkméret 8 KB-os.  
* Véletlenszerű írások végrehajtása több szálon.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson  

```
sudo fio --runtime 30 fiowrite.ini
```

A vizsgálat fut, amíg Ön száma írási iops-t a virtuális gép és a prémium szintű lemezek kézbesítéséhez. Az alábbi példában látható módon a DS14 VM elkötelezett az írási iops-érték 50 000 IOPS-korlátját.  
    ![Az írási IOPS-VM és a prémium szintű lemezek gyártanak száma](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maximális iops-érték olvasása

Hozzon létre a feladat-fájlt az alábbi előírások beolvasni a maximális olvasási iops-t. Nevezze el "fioread.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó

* Egy 256 magas várólista mélységét.  
* Kis blokkméret 8 KB-os.  
* Véletlenszerű írások végrehajtása több szálon.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson

```
sudo fio --runtime 30 fioread.ini
```

A vizsgálat fut, amíg Ön száma olvasási iops-t a virtuális gép és a prémium szintű lemezek kézbesítéséhez. Ahogy az alábbi példában is látható, a DS14 VM elkötelezett olvasási több mint 64 000 IOPS. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maximális olvasási és írási iops-érték

A feladat-fájlt létrehozni a következő beolvasni a maximális előírások együttesen olvasási és írási iops-t. Nevezze el "fioreadwrite.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó

* Magas várólista mélysége 128.  
* Kis blokkméret 4 KB-os.  
* Több szál végrehajtása véletlenszerű olvassa be és ír.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson

```
sudo fio --runtime 30 fioreadwrite.ini
```

Bár a vizsgálat fut, tekintsük meg összesített olvasási és írási iops-t a virtuális gép és prémium szintű lemezek kézbesítéséhez. Ahogy az alábbi példában is látható, a DS14 VM elkötelezett kombinált több mint 100 000 olvasási és írási iops-érték. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![Kombinált olvasási és írási iops-érték](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maximális összesített átviteli sebesség

Beolvasni a maximális kombinált olvasási és írási teljesítményt, a nagyobb blokkméret és nagy várólistamélységének használata az olvasások és írások végrehajtása több szálon. A 64 KB-os blokkméret és 128 várólistamélység is használhatja.
