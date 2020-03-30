---
title: Az Azure HPC cache adatok betöltése – manuális másolás
description: Cp-parancsok használata adatok áthelyezése blobtároló-tárolóba az Azure HPC-gyorsítótárban
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168482"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Az Azure HPC cache adatok betöltése – manuális másolási módszer

Ez a cikk részletes útmutatást ad az adatok manuális másolásához egy Blob storage-tárolóba az Azure HPC-gyorsítótárral való használatra. Ez használ többszálú párhuzamos műveleteket, hogy optimalizálja a másolási sebességet.

Ha többet szeretne megtudni az adatok Blob storage-ba való áthelyezéséről az Azure HPC-gyorsítótárszámára, olvassa el [az Adatok áthelyezése az Azure Blob storage-ba .](hpc-cache-ingest.md)

## <a name="simple-copy-example"></a>Példa egyszerű másolásra

Az ügyfélen manuálisan is létrehozhat többszálas másolatot, ha egyszerre több másolási parancsot futtat a háttérben előre meghatározott fájl- vagy elérésiutakkal szemben.

A Linux/UNIX ``cp`` parancs ``-p`` tartalmazza a tulajdonjog és a mtime metaadatok megőrzésére vonatkozó argumentumot. Ezt az argumentumot az alábbi parancsokhoz nem lehet hozzáadni. (Az argumentum hozzáadása növeli az ügyfél által a célfájlrendszerbe metaadatok módosítása céljából küldött fájlrendszer-hívások számát.)

Ez az egyszerű példa két fájlt másol párhuzamosan:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

A parancs kiadása után `jobs` a parancs azt mutatja, hogy két szál fut.

## <a name="copy-data-with-predictable-file-names"></a>Adatok másolása kiszámítható fájlnevekkel

Ha a fájlnevek kiszámíthatóak, a kifejezések segítségével párhuzamos másolási szálakat hozhat létre. 

Ha például a könyvtár 1000 olyan fájlt tartalmaz, `1000`amelyek számozása egymás után történik a rendszerhez `0001` képest, a következő kifejezések segítségével tíz párhuzamos szálat hozhat létre, amelyek mindegyike 100 fájlt másol:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Adatok másolása strukturálatlan fájlnevekkel

Ha a fájlelnevezési struktúra nem kiszámítható, a fájlokat könyvtárnevek szerint csoportosíthatja. 

Ez a példa teljes könyvtárakat ``cp`` gyűjt, amelyeket háttérfeladatként futtatandó parancsoknak küld:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

A fájlok összegyűjtését követően párhuzamos másolási parancsokat futtathat az alkönyvtárak és azok teljes tartalmának rekurzív másolásához:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Mikor kell csatlakoztatni pontokat hozzáadni?

Miután elegendő párhuzamos szál megy egy cél fájlrendszer csatlakoztatási pont, lesz egy pont, ahol több szál hozzáadása nem ad nagyobb átviteli. (Az átviteli forgalma t az adatok típusától függően fájlok/másodperc vagy bájt/másodperc ben mérik.) Vagy ami még rosszabb, a túl-threading néha okozhat átviteli terhelés romlása.  

Ha ez történik, ügyféloldali csatlakoztatási pontokat adhat hozzá más Azure HPC cache-csatlakoztatási címekhez, ugyanazzal a távoli fájlrendszer-csatlakoztatási útvonallal:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Az ügyféloldali csatlakoztatási pontok hozzáadásával további másolási parancsokat is elágazhat a további `/mnt/destination[1-3]` csatlakoztatási pontokhoz, további párhuzamosság érhető el.  

Ha például a fájlok nagyon nagyok, megadhatja a másolási parancsokat, hogy különböző célútvonalakat használjon, és további parancsokat küldjön ki párhuzamosan a másolatot végző ügyféllel.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

A fenti példában mindhárom célcsatlakoztatási pontot az ügyfélfájl másolási folyamatai célozzák meg.

## <a name="when-to-add-clients"></a>Mikor kell ügyfeleket hozzáadni?

Végül, ha elérte az ügyfél képességeit, további másolási szálak vagy további csatlakoztatási pontok hozzáadása nem eredményez további fájlokat/mp vagy bájt/sec növekedést. Ebben az esetben telepíthet egy másik ügyfelet ugyanazzal a csatlakoztatási ponttal, amely a saját fájlmásolási folyamatok készletét fogja futtatni. 

Példa:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Fájljegyzékek létrehozása

A fenti megközelítések (célonként több másolási szál, ügyfélenként több cél, hálózatonként több ügyfél) megismerése után fontolja meg ezt a javaslatot: Fájljegyzékek létrehozása, majd másolással való használata parancsokat több ügyfélen keresztül.

Ebben az esetben ``find`` a UNIX paranccsal fájlok vagy könyvtárak jegyzékfájljait hozza létre:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Az eredmény átirányítása fájlba:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ezután végighaladhat a jegyzékfájlon, bash parancsokkal megszámolhatja a fájlokat, és meghatározhatja az alkönyvtárak méretét:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Végül meg kell készítenie a tényleges fájlmásolási parancsokat az ügyfeleknek.  

Ha négy ügyféllel rendelkezik, használja ezt a parancsot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Ha öt ügyfele van, használja a következőt:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

És hatért... Szükség szerint extrapolálja.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

*N* eredményül kapott fájlokat kap, egyet-egyet minden N-ügyfélhez, amely rendelkezik a *N* `find` parancs kimenetének részeként kapott négyszintű könyvtárak elérési útneveivel. 

A másolási parancs létrehozásához használja az egyes fájlokat:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

A fenti kapsz *N* fájlokat, mindegyik egy másolat parancsot soronként, hogy lehet futtatni, mint egy BASH script az ügyfélen. 

A cél az, hogy több szálat futtatjon ezekből a parancsfájlokból egyidejűleg egy ügyfélenként párhuzamosan több ügyfélen.
