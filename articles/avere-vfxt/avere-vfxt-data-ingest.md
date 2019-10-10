---
title: Az Azure-ba irányuló avere-vFXT áthelyezése
description: Az Azure-hoz készült avere-vFXT való használatra szolgáló új tárolási kötethez való adatfelvétel módja
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: f4696d9e2d45e99089c9a723024067bf3b2aabcc
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255441"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Adatáthelyezés a vFXT-fürtbe – párhuzamos adatfeldolgozás 

Miután létrehozott egy új vFXT-fürtöt, az első feladat lehet az adatok áthelyezése az új tárolási kötetre. Ha azonban az adatok áthelyezésének szokásos módja egy egyszerű másolási parancs kiadása az egyik ügyfélről, valószínűleg lassú másolási teljesítményt fog látni. Az egyszálas másolás nem jó választás az adatoknak a avere vFXT-fürt háttér-tárolójába való másolásához.

Mivel a avere vFXT-fürt méretezhető, több ügyfélre kiterjedő gyorsítótár, a leggyorsabb és leghatékonyabb módszer az adatmásolásra több ügyféllel. Ezzel a technikával parallelizes a fájlok és objektumok betöltését.

![Több ügyfélből álló, többszálas adatáthelyezést ábrázoló diagram: a bal felső sarokban a helyszíni hardveres tárterület ikonja több nyílból származik. A nyilak négy ügyfélszámítógépre mutatnak. Az egyes ügyfélgépekről három nyíl mutat a avere vFXT felé. A avere vFXT több nyíl mutat a blob Storage-ra.](media/avere-vfxt-parallel-ingest.png) 

A ``cp`` vagy ``copy`` parancs, amely az adatok egyik tárolási rendszerből egy másikba való átvitelére használatos, egyszálas folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt tölt be – ez a fürt erőforrásainak hulladéka.

Ez a cikk a több ügyfelet tartalmazó, többszálas fájlmásolási rendszer létrehozására szolgáló stratégiákat ismerteti az adatoknak a avere vFXT-fürtbe való áthelyezéséhez. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek segítségével több ügyfél és egyszerű másolási parancs használatával hatékony Adatmásolást lehet használni.

Emellett ismerteti azokat a segédprogramokat is, amelyek segíthetnek. A ``msrsync`` segédprogram használatával részben automatizálható az adathalmazok gyűjtővé való osztása és az rsync-parancsok használata. A ``parallelcp`` szkript egy másik segédprogram, amely beolvassa a forrás könyvtárat, és automatikusan kiadja a másolási parancsokat.  

Kattintson a hivatkozásra a szakaszra való ugráshoz:

* [Manuális másolási példa](#manual-copy-example) – alapos magyarázat a másolási parancsok használatával
* [Részlegesen automatizált (msrsync) példa](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Példa párhuzamos másolásra](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Adatfeldolgozó virtuálisgép-sablon

A GitHubon egy Resource Manager-sablon áll rendelkezésre, amely automatikusan létrehoz egy virtuális gépet a jelen cikkben említett párhuzamos adatfeldolgozási eszközökkel. 

![a blob Storage, a hardver Storage és az Azure file sources használatával egyszerre több nyilat ábrázoló diagram. A nyilak egy "betöltési virtuális gépre" és onnan több nyílra mutatnak a avere vFXT](media/avere-vfxt-ingestor-vm.png)

Az adatfeldolgozó virtuális gép egy olyan oktatóanyag része, ahol az újonnan létrehozott virtuális gép csatlakoztatja a avere vFXT-fürtöt, és letölti a rendszerindítási parancsfájlt a fürtből. A részletekért olvassa el a rendszerindító [adatfeldolgozó virtuális gép](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) című cikkét.

## <a name="strategic-planning"></a>Stratégiai tervezés

Amikor párhuzamosan másolja az adatmásolási stratégiát, ismernie kell a fájlok mérete, a fájlok száma és a könyvtár mélysége közötti kompromisszumokat.

* Ha a fájlok kicsik, a kamat a fájlok másodpercenkénti száma.
* Ha a fájlok nagy méretűek (10MiBi vagy nagyobbak), a kamat mérőszáma másodpercenként bájt.

Minden másolási folyamathoz tartozik egy átviteli sebesség és egy fájl – átvitt sebesség, amely a másolási parancs hosszának időzítésével és a fájlméret és a fájlok számának megadásával mérhető. A díjszabás mértékének megállapítása kívül esik a jelen dokumentum hatókörén, de fontos tisztában lennie azzal, hogy a kis-és nagyméretű fájlokat is érdemes-e kezelni.

## <a name="manual-copy-example"></a>Példa manuális másolásra 

Manuálisan is létrehozhat többszálas másolatot egy ügyfélen, ha több másolási parancsot futtat egyszerre a háttérben a fájlok vagy elérési utak előre definiált készletei között.

A Linux/UNIX ``cp`` parancs ``-p`` argumentumot tartalmaz a tulajdonosi és a mtime-metaadatok megőrzése érdekében. Az argumentum hozzáadása az alábbi parancsokhoz nem kötelező. (Az argumentum hozzáadása növeli az ügyfél és a cél fájlrendszer közötti, a metaadatok módosítására irányuló hívások számát.)

Ez az egyszerű példa két fájlt másol át párhuzamosan:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

A parancs kiadása után a `jobs` parancs azt jeleníti meg, hogy két szál fut.

### <a name="predictable-filename-structure"></a>Kiszámítható fájlnév struktúra 

Ha a fájlnevek kiszámíthatók, a kifejezések használatával párhuzamos másolási szálakat hozhat létre. 

Ha például a könyvtár 1000-es fájlokat tartalmaz, amelyek egymás után, `0001` értékről `1000`-re vannak számozva, a következő kifejezésekkel hozhat létre tíz párhuzamos szálat, amelyek mindegyike 100-fájlok másolására használható:

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

Ha a fájl-elnevezési struktúra nem kiszámítható, a fájlokat a címtár neve alapján csoportosíthatja. 

Ez a példa teljes könyvtárat gyűjt a ``cp`` parancsok futtatásához háttérbeli feladatokként:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

A fájlok gyűjtése után párhuzamos másolási parancsok futtatásával rekurzív módon másolhatja az alkönyvtárakat és azok tartalmát:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Csatlakoztatási pontok hozzáadása

Ha elegendő párhuzamos szálat használ egy adott cél fájlrendszer csatlakoztatási pontján, akkor a több szál hozzáadására szolgáló pont nem ad nagyobb átviteli sebességet. (Az adatátviteli sebességet a rendszer a megadott adattípustól függően a fájl/másodperc vagy a bájt/másodperc értékben méri.) Vagy ami még rosszabb, az átviteli sebesség romlása időnként okozhatja a teljesítményt.  

Ebben az esetben az ügyféloldali csatlakoztatási pontokat más vFXT-fürt IP-címeihez is hozzáadhatja ugyanazzal a távoli fájlrendszer-csatlakozási útvonallal:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Az ügyféloldali csatlakoztatási pontok hozzáadásával további másolási parancsokat is kikapcsolhat a további @no__t – 0 csatlakoztatási pontokhoz, így további párhuzamosságot lehet elérni.  

Ha például a fájlok nagyon nagy méretűek, a másolási parancsokat definiálhatja a különböző elérési utak használatára, és a másolást végző ügyféltől párhuzamosan több parancsot is küldhet.

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

A fenti példában mindhárom cél csatlakoztatási pontra az ügyfél fájlmásolási folyamata irányul.

### <a name="when-to-add-clients"></a>Mikor kell ügyfeleket felvenni

Végül, ha elérte az ügyfél képességeit, további másolási szálak vagy további csatlakoztatási pontok hozzáadása nem eredményez további fájlokat/mp-t vagy bájt/mp-t. Ebben az esetben egy másik ügyfelet is telepíthet ugyanazon a csatlakoztatási ponttal, amely a fájlmásolás folyamatainak saját készletét fogja futtatni. 

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

### <a name="create-file-manifests"></a>Fájl-jegyzékfájlok létrehozása

Miután megértette a fenti megközelítéseket (több másolási szál/cél, több ügyfél, hálózaton keresztül elérhető forrásoldali fájlrendszer), vegye figyelembe ezt a javaslatot: fájl-jegyzékfájlok létrehozása, majd a másolattal való használata több ügyfélen belüli parancsok.

Ez a forgatókönyv a UNIX ``find`` parancs használatával hozza létre a fájlok vagy könyvtárak jegyzékét:

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

Az eredmény átirányítása fájlba: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ezután megismételheti a jegyzékfájlt, ha BASH-parancsokat használ a fájlok számlálásához és az alkönyvtárak méretének meghatározásához:

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

Végül meg kell adnia a tényleges fájlmásolás-parancsokat az ügyfeleknek.  

Ha négy ügyféllel rendelkezik, használja a következő parancsot:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Ha öt ügyfele van, használja az alábbihoz hasonlót:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

És hat.... Kikövetkeztetés igény szerint.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

A rendszer *n* eredményül kapott fájlokat fog kapni, amelyek mindegyike *n* -ügyfélhez tartozik, és az elérési utak nevei a `find` parancs kimenetének részeként kapott szint-négy könyvtárra vonatkoznak. 

Az egyes fájlok használatával hozza létre a másolási parancsot:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

A fentiekben *N* fájl jelenik meg, amelyek mindegyike soronként egy másolási paranccsal fut, amely bash-parancsfájlként is futtatható az ügyfélen. 

A cél a parancsfájlok több szálának párhuzamos futtatása egyszerre több ügyfélen egyidejűleg.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>A msrsync segédprogram használata a Felhőbeli kötetek feltöltéséhez

A ``msrsync`` eszköz segítségével áthelyezheti az adatátvitelt a avere-fürthöz tartozó backend Core filerbe. Ez az eszköz úgy lett kialakítva, hogy optimalizálja a sávszélesség-használatot több párhuzamos @no__t – 0 folyamat futtatásával. A GitHubról https://github.com/jbd/msrsync címen érhető el.

@no__t – 0 a forrás könyvtárat külön "gyűjtőre" bontja, majd az egyes gyűjtők ``rsync`` folyamatait futtatja.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A 64-ig terjedő folyamatok számának beállításához használja a ``-p`` ``msrsync`` kapcsolót.

Vegye figyelembe, hogy a ``msrsync`` csak helyi kötetek és azok között tud írni. A forrásnak és a célhelynek elérhetőnek kell lennie helyi csatlakoztatásként a fürt virtuális hálózatában.

Az Azure-beli Felhőbeli kötetek avere-fürtökkel való feltöltéséhez kövesse az alábbi utasításokat:

1. A msrsync és az Előfeltételek telepítése (rsync és Python 2,6 vagy újabb)
1. A másolandó fájlok és könyvtárak teljes számának meghatározása.

   Használja például a ``prime.py`` avere segédprogramot, ```prime.py --directory /path/to/some/directory``` argumentummal (az URL-cím letöltése https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py).

   Ha nem a ``prime.py`` értéket használja, kiszámíthatja a GNU ``find`` eszközzel rendelkező elemek számát a következőképpen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása a 64 alapján az elemek számának megállapítása folyamatban. Ezt a számot a ``-f`` kapcsolóval használva állíthatja be a gyűjtők méretét a parancs futtatásakor.

1. Adja ki a msrsync parancsot a fájlok másolásához:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például úgy lett kialakítva, hogy 11 000-es fájlokat helyezzen át a 64 folyamatokban a/test/Source-repository-ről a/mnt/vfxt/repository-re:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>A párhuzamos másolási parancsfájl használata

A ``parallelcp`` szkript akkor is hasznos lehet, ha az vFXT-fürt háttérbeli tárolójában szeretné áthelyezni az adatátvitelt. 

Az alábbi szkript hozzáadja a végrehajtható @no__t – 0 értéket. (Ezt a szkriptet Ubuntu-re tervezték, ha más disztribúciót használ, külön kell telepítenie a ``parallel``-ot.)

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

Ez a példa a párhuzamos másolási parancsfájlt használja a ``glibc`` fordításához a avere-fürtből származó forrásfájlok használatával. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

A forrásfájlok a avere-fürt csatlakoztatási pontján tárolódnak, és az objektum fájljai a helyi merevlemezen vannak tárolva.

Ez a szkript a fenti párhuzamos másolási parancsfájlt használja. A ``-j`` kapcsoló a ``parallelcp`` és a ``make`` értéket használja a párhuzamos megszerzéséhez.

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

