---
title: Adatok áthelyezése az Avere vFXT for Azure-ba
description: Adatok hozzáadása új tárolókötethez az Azure-hoz való Avere vFXT-vel való használatra
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153718"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Adatok áthelyezése a vFXT-fürtbe – Párhuzamos adatbetöltés

Miután létrehozott egy új vFXT-fürtöt, az első feladat az azure-beli új tárolókötetre való áthelyezése lehet. Ha azonban az adatok áthelyezésének szokásos módja egyszerű másolási parancsot ad ki az egyik ügyféltől, akkor valószínűleg lassú másolási teljesítményt fog látni. Az egyszálas másolás nem jó megoldás az Avere vFXT fürt háttértárolójára történő másoláshoz.

Mivel az Avere vFXT for Azure cluster egy méretezhető többügyféles gyorsítótár, a leggyorsabb és leghatékonyabb módja az adatok másolásának, több ügyféllel. Ez a módszer párhuzamosítja a fájlok és objektumok betöltését.

![Többügyféles, többszálas adatmozgást bemutató diagram: A bal felső sarokban a helyszíni hardveres tárolás ikonja több nyíllal rendelkezik. A nyilak négy ügyfélgépre mutatnak. Minden ügyfélgépről három nyíl mutat az Avere vFXT felé. Az Avere vFXT-ből több nyíl mutat a Blob storage-ra.](media/avere-vfxt-parallel-ingest.png)

Az ``cp`` ``copy`` egyik tárolórendszerből a másikba történő adatátvitelre gyakran használt vagy parancsok egyszálas folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt vesz fel – ami a fürt erőforrásainak pazarlása.

Ez a cikk ismerteti a többügyféles, többszálas fájlmásoló rendszer létrehozásának stratégiáit az Avere vFXT-fürtbe való áthelyezéséhez. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek felhasználhatók a több ügyfél és egyszerű másolási parancsok használatával történő hatékony adatmásoláshoz.

Azt is elmagyarázza, néhány segédprogramok, amelyek segíthetnek. A ``msrsync`` segédprogram segítségével részlegesen automatizálható az adatkészlet eksztázisokra osztásának folyamata és a parancsok használata. ``rsync`` A ``parallelcp`` parancsfájl egy másik segédprogram, amely beolvassa a forráskönyvtárat, és automatikusan kiadja a másolási parancsokat. Emellett az ``rsync`` eszköz két fázisban is használható, hogy gyorsabb másolatot biztosítson, amely továbbra is biztosítja az adatok konzisztenciáját.

Kattintson a hivatkozásra, ha egy szakaszra szeretne ugrani:

* [Példa kézi másolási példa](#manual-copy-example) - Alapos magyarázat másolási parancsok használatával
* [Példa kétfázisú rsync](#use-a-two-phase-rsync-process)
* [Részben automatizált (msrsync) példa](#use-the-msrsync-utility)
* [Példa párhuzamos másolásra](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Adatbetöltési virtuálisgép-sablon

Egy Erőforrás-kezelő sablon érhető el a GitHubon, hogy automatikusan hozzon létre egy virtuális gép a cikkben említett párhuzamos adatbetöltési eszközökkel.

![a blobstorage, a hardveres tároló és az Azure-fájlforrásokból származó több nyíl. A nyilak egy "adatbetöltési vm"-re mutatnak, és onnan több nyíl mutat az Avere vFXT-re](media/avere-vfxt-ingestor-vm.png)

Az adatbetöltő virtuális gép egy oktatóanyag része, ahol az újonnan létrehozott virtuális gép csatlakoztatja az Avere vFXT-fürtöt, és letölti a rendszerindítási parancsfájlt a fürtből. Olvassa [el Bootstrap egy adatbetöltő virtuális gép](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) a részletekért.

## <a name="strategic-planning"></a>Stratégiai tervezés

Amikor stratégiát tervez az adatok párhuzamos másolására, meg kell értenie a fájlméret, a fájlszám és a könyvtármélység kompromisszumait.

* Ha a fájlok kicsik, az érdeklődési mutató a fájlok másodpercenként.
* Ha a fájlok nagyok (10MiBi vagy nagyobb), az érdeklődési mutató bájt/másodperc.

Minden másolási folyamat áteresztőhet tesz le, és fájlokat továbbít, ami a másolási parancs hosszának időzítésével, valamint a fájlméret és a fájlszám tényezőjével mérhető. Az árak mérésének magyarázata nem tartozik a dokumentum hatálya alá, de fontos megérteni, hogy kis vagy nagy méretű fájlokkal fog-e foglalkozni.

## <a name="manual-copy-example"></a>Példa kézi másolásra

Az ügyfélen manuálisan is létrehozhat többszálas másolatot, ha egyszerre több másolási parancsot futtat a háttérben előre meghatározott fájl- vagy elérésiutakkal szemben.

A Linux/UNIX ``cp`` parancs ``-p`` tartalmazza a tulajdonjog és a mtime metaadatok megőrzésére vonatkozó argumentumot. Ezt az argumentumot az alábbi parancsokhoz nem lehet hozzáadni. (Az argumentum hozzáadása növeli az ügyfél által a célfájlrendszerbe metaadatok módosítása céljából küldött fájlrendszeri hívások számát.)

Ez az egyszerű példa két fájlt másol párhuzamosan:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

A parancs kiadása után `jobs` a parancs azt mutatja, hogy két szál fut.

### <a name="predictable-filename-structure"></a>Kiszámítható fájlnév-struktúra

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

### <a name="unknown-filename-structure"></a>Ismeretlen fájlnév-struktúra

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

### <a name="when-to-add-mount-points"></a>Mikor kell csatlakoztatni pontokat hozzáadni?

Miután elegendő párhuzamos szál megy egy cél fájlrendszer csatlakoztatási pont, lesz egy pont, ahol több szál hozzáadása nem ad nagyobb átviteli. (Az átviteli forgalma t az adatok típusától függően fájlok/másodperc vagy bájt/másodperc ben mérik.) Vagy ami még rosszabb, a túl-threading néha okozhat átviteli terhelés romlása.

Ebben az esetben ügyféloldali csatlakoztatási pontokat adhat más vFXT-fürt IP-címeihez, ugyanazzal a távoli fájlrendszer-csatlakoztatási útvonallal:

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

### <a name="when-to-add-clients"></a>Mikor kell ügyfeleket hozzáadni?

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

### <a name="create-file-manifests"></a>Fájljegyzékek létrehozása

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
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

## <a name="use-a-two-phase-rsync-process"></a>Kétfázisú rsync-folyamat használata

A ``rsync`` standard segédprogram nem működik megfelelően a felhőalapú tárhely feltöltéséhez az Avere vFXT for Azure rendszeren keresztül, mivel nagyszámú fájllétrehozási és átnevezési műveletet hoz létre az adatok integritásának biztosítása érdekében. Azonban nyugodtan használhatja ``--inplace`` a ``rsync`` lehetőséget, hogy kihagyja a gondosabb másolási eljárást, ha követi, hogy a második futtatás, amely ellenőrzi a fájl integritását.

A ``rsync`` szabványos másolási művelet ideiglenes fájlt hoz létre, és kitölti azt adatokkal. Ha az adatátvitel sikeresen befejeződik, az ideiglenes fájl átnevezése az eredeti fájlnévre. Ez a módszer akkor is garantálja a konzisztenciát, ha a fájlok másolás közben érhetők el. De ez a módszer több írási műveletet generál, ami lelassítja a fájlok mozgását a gyorsítótáron keresztül.

A ``--inplace`` beállítás az új fájlt közvetlenül a végső helyére írja. A fájlok nem garantáltan konzisztensek az átvitel során, de ez nem fontos, ha később egy tárolórendszert használ.

A ``rsync`` második művelet az első művelet konzisztencia-ellenőrzéseként szolgál. Mivel a fájlok másolása már megtörtént, a második fázis egy gyors vizsgálat, amely biztosítja, hogy a célhelyen lévő fájlok megegyeznek a forráson lévő fájlokkal. Ha a fájlok nem egyeznek, a rendszer újramásolja őket.

Mindkét fázist egy paranccsal együtt is kiadhatja:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Ez a módszer egy egyszerű és időhatékony módszer az adatkészletek számára a belső címtárkezelő által kezelhető fájlok számáig. (Ez általában 200 millió fájl egy 3 csomópontos fürthöz, 500 millió fájl egy hatcsomópontos fürthöz és így tovább.)

## <a name="use-the-msrsync-utility"></a>Az msrsync segédprogram használata

Az ``msrsync`` eszköz az adatok átmozgatására is használható az Avere-fürt háttérmagos fájlkezelőjéhez. Ez az eszköz több párhuzamos ``rsync`` folyamat futtatásával optimalizálja a sávszélesség-használatot. A GitHubról érhető <https://github.com/jbd/msrsync>el a.

``msrsync``a forráskönyvtárat külön "gyűjtőkre" bontja, majd minden egyes gyűjtőben futtatja az egyes ``rsync`` folyamatokat.

A négymagos virtuális gép használatával végzett előzetes tesztelés 64 folyamat használata esetén mutatta a legjobb hatékonyságot. Ezzel ``msrsync`` a ``-p`` beállítással 64-re állíthatja a folyamatok számát.

Az ``--inplace`` argumentumot parancsokkal ``msrsync`` is használhatja. Ha ezt a beállítást használja, fontolja meg egy második parancs futtatását (mint [az rsync,](#use-a-two-phase-rsync-process)a fent leírt) az adatok integritásának biztosítása érdekében.

``msrsync``csak helyi kötetekbe és helyi kötetekről tud írni. A forrásnak és a célnak elérhetőnek kell lennie a fürt virtuális hálózatának helyi csatlakoztatásaként.

``msrsync`` Az Azure felhőkötetek Avere-fürttel való feltöltéséhez kövesse az alábbi utasításokat:

1. Telepítés ``msrsync`` és előfeltételei (rsync és Python 2.6 vagy újabb)
1. Határozza meg a másolandó fájlok és könyvtárak teljes számát.

   Használja például az Avere segédprogramot ``prime.py`` argumentumokkal ```prime.py --directory /path/to/some/directory``` <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(url letöltésével érhető el).

   Ha nem ``prime.py``használja a programot, a GNU ``find`` eszközzel az alábbiak szerint számíthatja ki az elemek számát:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása 64-re az elemek folyamatonkénti számának meghatározásához. Ezzel a ``-f`` számmal állíthatja be a gyűjtők méretét a parancs futtatásakor.

1. Fájlok ``msrsync`` másolására a parancs kiadása:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ha ``--inplace``használja, adjon hozzá egy második végrehajtást anélkül, hogy ellenőrizheti volna, hogy az adatok megfelelően vannak-e másolva:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például 11 000 fájlt helyezhet át 64 folyamatban a /test/source-repository könyvtárból a /mnt/vfxt/repository-ba:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>A párhuzamos másolási parancsfájl használata

A ``parallelcp`` parancsfájl is hasznos lehet az adatok áthelyezése a vFXT-fürt háttér-tároló.

Az alábbi szkript hozzáadja `parallelcp`a végrehajtható . (Ez a szkript ubuntuhoz készült; ha egy ``parallel`` másik disztribúciót használ, külön kell telepíteni.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Példa párhuzamos másolásra

Ez a példa a párhuzamos ``glibc`` másolási parancsfájlt használja az Avere-fürtből származó forrásfájlok használatával történő fordításhoz.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

A forrásfájlok az Avere fürt csatlakoztatási pontján, az objektumfájlok pedig a helyi merevlemezen tárolódnak.

Ez a parancsfájl a fenti párhuzamos másolási parancsfájlt használja. A ``-j`` beállítás a ``parallelcp`` ``make`` párhuzamosítással és a párhuzamosság megszerzésével használható.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
