---
title: Adatok áthelyezése a Avere vFXT az Azure-hoz
description: Adatok hozzáadása egy új storage kötetre való használatra, a Avere vFXT az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a3d6cb745c782d2a7166208f2a8dd1202a330b15
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050489"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>A vFXT fürthöz - párhuzamos adatokat helyez át adatokat képes feldolgozni. 

Miután létrehozott egy új vFXT fürtöt, az első feladat lehet áthelyezni az új tárolási kötet se mají data. Azonban a szokásos módszer, amely adatokat helyez egy egyszerű másolás parancsot egy ügyféltől ad ki, ha valószínűleg látni fogja a lassú másolási teljesítmény. Másolás egyszálas lehetőség nem egy jó céljából a Avere vFXT fürt háttérrendszer Storage.

Mivel a Avere vFXT fürt több ügyfél gyorsítótárának méretezhető, több ügyfél van a leggyorsabb és leghatékonyabb módszert az adatok másolásához. Ezzel a technikával a fájlok és objektumok Adatbetöltési parallelizes.

![Bemutató ábra. Ez több ügyfél, több szálon futó adatáthelyezés: Bal felső részén egy ikont a helyszíni hardver tárolási rendelkezik több nyilak származó azt. A nyilak az ügyfélgépek négy mutasson. Az egyes ügyfél gépéről három nyilak a Avere vFXT felé mutat. A Avere vFXT a Blob storage több nyíl mutat.](media/avere-vfxt-parallel-ingest.png) 

A ``cp`` vagy ``copy`` használatával az adatok átvitele egy tárolórendszer között gyakran használt parancsok a következők egyszálas folyamatokat, amelyek egyszerre csak egy fájl másolása. Ez azt jelenti, hogy a fájlkiszolgáló van csak egy fájlt tölt egyszerre – amely a fürt erőforrásainak feleslegesen használna.

Ez a cikk ismerteti a rendszer adatok áthelyezése az Avere vFXT fürtre másolása több ügyfél, több szálon futó fájl létrehozásával kapcsolatos olyan stratégiák. Fájl adatátviteli fogalmakat és döntési pontokat is használható, hatékony adatokat másolni a több ügyfél- és egyszerű másolási parancsai használatával ismerteti.

Azt is bemutatja néhány segédprogram, amely segíthet. A ``msrsync`` segédprogram segítségével részlegesen értékkel való osztásának adatkészlet gyűjtők be és rsync parancsok automatizálni. A ``parallelcp`` parancsfájl egy másik eszköz, amely beolvassa a forráskönyvtár, és problémák automatikus másolása parancsokat.  

Kattintson a hivatkozásra kattintva szakaszra ugorhat:

* [Manuális másolással példa](#manual-copy-example) – másolási parancsai használatával egy alapos magyarázata
* [Részlegesen automatikus (msrsync) Példa](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Párhuzamos másolási példa](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Adatok módon eredményesen dolgozható Virtuálisgép-sablon

Resource Manager-sablont a GitHub automatikusan létrehozza a virtuális gép és a párhuzamos adatfeldolgozási eszközök ebben a cikkben említett érhető el. 

![több nyilak minden egyes blob storage-ba, hardver storage és Azure-beli adatforrások kapcsolatait bemutató diagram. A nyilak pont "adatok módon eredményesen dolgozható VM", és itt több nyilak a Avere vFXT mutasson.](media/avere-vfxt-ingestor-vm.png)

Az adatok módon eredményesen dolgozható virtuális gép egy oktatóanyag, ahol az újonnan létrehozott virtuális gép a Avere vFXT fürthöz csatlakoztatja, és a rendszer-indításkori parancsfájl tölt le a fürt része. Olvasási [elindíthat egy adatok módon eredményesen dolgozható VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) részleteiről.

## <a name="strategic-planning"></a>Stratégiai tervezése

Egy stratégia az adatok másolása a párhuzamos létrehozását, tisztában kell lennie a kompromisszumot kínál a fájl mérete, a fájlok száma és a directory mélysége.

* Fájlok kicsik, a fontos mérőszám értéke esetén a fájlok száma másodpercenként.
* Ha a fájlok nagyok (10MiBi vagy nagyobb), a fontos mérőszám értéke bájt / másodpercben.

Minden egyes másolási folyamat rendelkezik, az átviteli sebesség és a egy átvitt fájlok aránya, amelyek túllépik a Másolás parancs hossza és faktoring a méretét és a fájlok száma alapján mérhető. Elmagyarázza, hogyan mérheti a díjszabás a jelen dokumentum hatókörén kívüli, de fontos tudni, hogy, fog kell foglalkoznak, kis vagy nagy fájlok.

## <a name="manual-copy-example"></a>Manuális másolással példa 

Manuálisan létrehozhat több szálon futó másolatot az ügyfélen egynél több Másolás parancs futtatásával egyszerre beépített beállításkészletet fájlok vagy elérési utak elleni a háttérben.

A Linux-/ UNIX ``cp`` parancs tartalmazza a argumentum ``-p`` tulajdonjoga és mtime metaadatainak megőrzése érdekében. Ad hozzá az alábbi parancsok ezt az argumentumot nem kötelező. (Az argumentum hozzáadásával növeli a cél-fájlrendszer, a metaadatok módosítása az ügyfél által küldött fájlrendszer-hívások számát.)

Ebben az egyszerű példában két fájlt másolja a párhuzamos:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Ez a parancs kiadása után a `jobs` parancs jelennek meg, hogy két szálak futnak.

### <a name="predictable-filename-structure"></a>Kiszámítható filename struktúra 

Ha a fájlnevek kiszámítható, kifejezések használatával párhuzamos másolási szál létrehozása. 

Például, ha a címtár sorszámozottak az 1000 fájlokat tartalmaz `0001` való `1000`, az alábbi kifejezések használatával, hogy minden 100 fájlok másolása tíz párhuzamos szálak létrehozása:

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

### <a name="unknown-filename-structure"></a>Ismeretlen filename struktúra

Ha a fájlelnevezési struktúra nem kiszámítható, fájlok könyvtárának nevében szerint csoportosíthatók. 

Ebben a példában gyűjt küldendő teljes könyvtárak ``cp`` háttérfeladatokat futtató parancsok:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

A fájlok összegyűjtése, után futtathatja a párhuzamos másolási parancsokat a rekurzív módon másolja az alkönyvtárak és a tartalom mindegyikét:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Mikor érdemes hozzáadni a csatlakoztatási pontok

Miután elegendő párhuzamos szálak videóanyagai egyetlen fájlrendszer csatlakoztatási pont ellen is, a pont, ahol több szál hozzáadásával nem biztosít további átviteli sebesség lesz. (Teljesítményt méri a fájlok másodpercenként vagy bájt/s, az adatok típusától függően.) Vagy rosszabb esetben túlterhelt threading néha okozhat egy átviteli teljesítménycsökkenést.  

Ha ez történik, adhat hozzá ügyféloldali csatlakoztatási pontokat más vFXT fürt IP-címek, az azonos távoli fájlrendszer csatlakoztatási elérési út alapján:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Ügyféloldali csatlakoztatási pontok lehetővé teszi, hogy először ki további másolási parancsai, a további ágaztatnia hozzáadása `/mnt/destination[1-3]` csatlakozási pontok eléréséhez további párhuzamosságát.  

Például ha a fájlok nagyon nagy méretű, megadhat különböző cél elérési utakat, a Másolás-parancsok további parancsokat a Másolás elvégzése az ügyfél párhuzamosan küldésére.

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

A fenti példában összes három cél csatlakoztatási pont az ügyfél fájl másolási folyamat alatt vonatkozik.

### <a name="when-to-add-clients"></a>Mikor érdemes hozzáadni az ügyfelek számára

Végül, ha elérte az ügyfél-képességeket, több példányt szál hozzáadásával további csatlakoztatási pontok nem eredményez, minden további fájlokat/mp vagy vagy bájt/mp növeli. Ebben az esetben ugyanazokat a csatlakoztatási pontoknak, amelyeket a saját csoportok fájl másolása folyamat fut egy másik ügyfél telepítheti. 

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

### <a name="create-file-manifests"></a>Jegyzékek fájl létrehozása

Ismertetése után a megközelítések a fenti (több másolási-szálak száma a cél, több célhelyre ügyfelenként, több ügyfelek száma a forrás hálózati fájlrendszer), vegye figyelembe a javaslat: Hozhat létre a fájladatokat, majd azokat a másolási parancsai több ügyfélre.

Ez a forgatókönyv használ a UNIX ``find`` paranccsal hozza létre a jegyzékek található fájlok vagy könyvtárak:

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

Ez az eredmény átirányítása egy fájlt: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ezután Ön is iterálódnak a jegyzékfájlban, BASH-parancsok használatával a fájlok és alkönyvtárak méretét határozza meg:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
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

Végül meg kell formázhatják az ügyfélszámítógépeken a fájl tényleges másolási parancsai.  

Ha négy ügyfelek rendelkezik, használja ezt a parancsot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Ha öt ügyfelet, valami ilyesmit használja:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

És hat... Extrapolálja igény szerint.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Kap *N* eredményül kapott fájlok, így az egyes a *N* ügyfelek, a negyedik szintű könyvtárak kimenete részeként kapott az elérési út nevét is tartalmazza, a `find` parancsot. 

Minden egyes fájl használatával hozhat létre a Másolás parancsot:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

A fenti kap *N* fájlok, minden egyes másolása paranccsal minden sorában, az ügyfélen, a BASH-szkript futtatható. 

A cél, hogy több ügyfél egyidejű ügyfelenként parancsfájl több szál párhuzamos futtatása.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>A msrsync segédprogram használatával töltse fel a felhő-kötetek

A ``msrsync`` is eszköz adatok áthelyezése a Avere fürt a háttérrendszer core filer használható. Ez az eszköz az célja, hogy a sávszélesség-használat optimalizálása több párhuzamos futtatásával ``rsync`` folyamatokat. A githubról elérhető https://github.com/jbd/msrsync.

``msrsync`` működésképtelenné válik a forráskönyvtár a "buckets" külön történő mentése, és futtat az egyes ``rsync`` az egyes gyűjtők folyamatait.

Előzetes tesztelése egy 4 magos virtuális gép használatával bemutatta a hatékonyság növelése, 64 folyamatok használata esetén. Használja a ``msrsync`` beállítás ``-p`` , a folyamatok számát szeretné 64.

Vegye figyelembe, hogy ``msrsync`` csak írhat, és a helyi kötetek. A forrás- és a fürt virtuális hálózatban lévő helyi csatlakoztatása elérhetőnek kell lennie.

Msrsync használatával töltse fel az Azure-felhő kötet-Avere fürthöz, kövesse az alábbi utasításokat:

1. Telepítse a msrsync és az Előfeltételek (rsync és Python 2.6-os vagy újabb)
1. Határozza meg, hogy a fájlok és könyvtárak másolandó teljes száma.

   Ha például a Avere segédprogrammal ``prime.py`` argumentumokkal ```prime.py --directory /path/to/some/directory``` (töltse le az URL-cím elérhető https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py).

   Ha nem használ ``prime.py``, kiszámíthatja a Gnu az elemek száma ``find`` eszközt az alábbiak szerint:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Azon elemek száma nullával való osztás 64 folyamatonként elemek számának meghatározásához. Használja ezt a számot a ``-f`` állíthatja be a gyűjtőket méretétől, a parancs futtatásakor.

1. Adja ki a fájlok másolása a msrsync parancsot:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Például ez a parancs célja, hogy a 64 folyamatok 11 000 fájlok áthelyezése /test/source-repository /mnt/vfxt/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>A párhuzamos másolási szkripttel

A ``parallelcp`` parancsfájlt is hasznos lehet az adatok áthelyezése a vFXT fürttároló háttérrendszer. 

Az alábbi parancsfájlt felveszi a végrehajtható fájl `parallelcp`. (Ez a szkript Ubuntu készült; Ha egy másik terjesztési használja, telepítenie kell ``parallel`` külön-külön.)

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

### <a name="parallel-copy-example"></a>Párhuzamos másolási példa

Ebben a példában a párhuzamos másolási parancsfájl összeállításához ``glibc`` Avere-fürtből származó forrásfájlok használatához. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

A forrásfájlok a Avere fürt csatlakoztatási pont tárolja, és az objektum fájlokat a helyi merevlemezen tárolja.

Ez a szkript a fenti parancsfájl párhuzamos másolása. A beállítás ``-j`` együtt használják ``parallelcp`` és ``make`` ezerszer eléréséhez.

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

