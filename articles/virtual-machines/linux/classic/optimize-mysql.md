---
title: Linux rendszeren a MySQL teljesítményének optimalizálása |} A Microsoft Docs
description: Ismerje meg, hogyan optimalizálható a MySQL egy Azure virtuális gépen (VM) Linux rendszerű futtató.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 0ba85e82824bc257869d9801f342bd6dbb0402d2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247449"
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Azure-beli Linuxos virtuális gépeken a MySQL teljesítményének optimalizálása
Nincsenek számos tényező befolyásolja, a MySQL teljesítményének az Azure-ban, mind a virtuális hardver kiválasztása és a szoftverfrissítési konfiguráció. Ez a cikk foglalkozik a storage, a rendszer és a konfigurációk optimalizálása teljesítményét.

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Linux rendszerű virtuális gép optimalizálása a Resource Manager modellel kapcsolatos további információkért lásd: [optimalizálhatja Azure-beli Linuxos virtuális gép](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Használja a RAID-beli virtuális gépen
Storage szolgáltatás a kulcsfontosságú tényező, amely hatással van az adatbázis teljesítményét a felhőalapú környezetekben. Képest egyetlen lemez, RAID keresztül egyidejűségi gyorsabb hozzáférést biztosíthat. További információkért lásd: [standard szintű RAID-szintek](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Lemez i/o-adatátviteli és i/o-válaszidő az Azure-ban RAID javítható. Labor a tesztek megjelenítése, hogy a lemez i/o-teljesítményt is meg kell duplázni és i/o-válaszidő mérsékelhető félig átlagosan RAID lemezek száma nő (a kettő, négy, négy és nyolc, stb.) Ha. Lásd: [függelék](#AppendixA) részleteiről.  

A MySQL teljesítményének mellett a lemez i/o, javítja a növelése a RAID-szinteket.  Lásd: [B függelék](#AppendixB) részleteiről.  

Emellett érdemes figyelembe venni az adatrészlet méretének. Általában egy nagyobb adattömbméret rendelkezik, kap alacsonyabb többletterhelést okoz, különösen a nagy írási műveletek. Azonban ha adatrészlet mérete túl nagy, előfordulhat, hogy adhat hozzá további erőforrásokra, amely meggátolja RAID kihasználásával. Az aktuális alapértelmezett mérete 512 KB, amely a legtöbb általános éles környezetben optimális bizonyítottan. Lásd: [C függelék:](#AppendixC) részleteiről.   

Hány lemezeket adhat hozzá a különböző virtuális géptípus számára korlátozva van. Ezek a korlátok részletesen ismertetett [virtuálisgép és felhőszolgáltatás-méretek az Azure-ban](https://msdn.microsoft.com/library/azure/dn197896.aspx). Bár választhat kevesebb lemezek RAID beállításához, kövesse az ebben a cikkben a RAID példában négy csatlakoztatott adatlemezekkel kell.  

Ez a cikk feltételezi, hogy már létrehozott egy Linux rendszerű virtuális gép és a MYSQL telepítése és konfigurálása. A bevezetés további információkért tekintse meg a MySQL telepítése Azure-ban.  

### <a name="set-up-raid-on-azure"></a>Állítsa be az Azure-ban RAID
A következő lépések bemutatják, hogyan hozhat létre az Azure-ban RAID az Azure portal használatával. Akkor is állíthat be RAID Windows PowerShell-parancsfájlok használatával.
Ebben a példában négy lemezek konfigurálunk RAID 0.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Virtuálisgép-adatlemez hozzáadása
Az Azure Portalon nyissa meg az irányítópultot, és válassza ki a virtuális gépet, amelyhez szeretné hozzáadni egy adatlemezt. Ebben a példában a virtuális gép mysqlnode1.  

<!--![Virtual machines][1]-->

Kattintson a **lemezek** majd **új csatolása**.

![A lemezt adja hozzá a virtuális gépek](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Hozzon létre egy új 500 GB-os lemezt. Győződjön meg arról, hogy **gazdagép gyorsítótár szabályozó** értékre van állítva **None**.  Ha elkészült, kattintson a **OK**.

![Üres lemez csatlakoztatása](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Ez egy üres lemez a virtuális gépet hozzáadja. Ismételje meg ezt a lépést három még többször, hogy rendelkezik a négy adatlemezek RAID.  

A virtuális gépen a hozzáadott meghajtók láthatja a kernel üzenet napló megtekintésével. Például az ubuntu rendszeren ez látható, használja a következő parancsot:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>A további lemezek RAID létrehozása
Az alábbi lépések bemutatják, hogyan [szoftveres RAID linuxon konfigurálása](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Ha a XFS fájlrendszert használ, RAID létrehozása után hajtsa végre az alábbi lépéseket.
>
>

A Debian, Ubuntu vagy Linux menta XFS telepítéséhez használja a következő parancsot:  

    apt-get -y install xfsprogs  

Az RHEL, Fedora és CentOS XFS telepítéséhez használja a következő parancsot:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Állítsa be egy új. tárolási elérési útja
A következő paranccsal állítsa be az új tároló elérési út:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Az eredeti adatok átmásolása az új tároló elérési útja
A következő parancsot használja az adatok másolása az új tároló elérési útja:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Engedélyek módosítása, a MySQL hozzáférhet (olvasási és írási) az adatlemez
A következő paranccsal módosítsa az engedélyeket:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>A lemez i/o-ütemezési algoritmus beállítása
Linux négy típusú algoritmusok ütemezés i/o valósít meg:  

* NOOP algoritmus (nincs művelet)
* Határidő algoritmus (határidő)
* Teljesen valós üzenetsor-kezelési algoritmus (CFQ)
* Költségvetés időszak algoritmus (Anticipatory)  

Kiválaszthatja, hogy a teljesítmény optimalizálása különböző i/o-ütemezők különböző forgatókönyvek. Teljesen elérésű környezet esetén nem áll a CFQ és a határidő algoritmusok a teljesítmény jelentős különbség. Azt javasoljuk, hogy a MySQL-adatbázis környezet stabilitását határidejét beállítani. Szekvenciális i/o rengeteg esetén CFQ csökkentheti a lemez i/o-teljesítményt.   

SSD és más eszközök esetében NOOP vagy határidő érheti el az alapértelmezett ütemező jobb teljesítményt.   

Előtt a kernel 2.5-ös az alapértelmezett ütemezési i/o-algoritmus a határidő. A kernel 2.6.18 kezdve CFQ vált, az alapértelmezett ütemezési i/o-algoritmus.  Adja meg ezt a beállítást a rendszermag rendszerindítási időben, vagy dinamikusan módosíthatja ezt a beállítást, ha a rendszer fut-e.  

Az alábbi példa bemutatja, hogyan ellenőrizze és állítsa be az alapértelmezett ütemező az NOOP algoritmus használatára, a Debian terjesztési család.  

### <a name="view-the-current-io-scheduler"></a>Az aktuális i/o-ütemezőt megtekintése
Futtassa a következő parancsot az ütemező megtekintése:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Az alábbi kimenet, amely azt jelzi, hogy a jelenlegi Feladatütemező jelenik meg:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Az aktuális eszköz (/ dev/sda), az i/o-ütemezési algoritmus módosítása
A következő parancsokat az aktuális eszköz módosítása:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Ez a /dev/sda önálló, nem lehet hasznos. Be kell állítani az összes adatlemezek ahol az adatbázis található.  
>
>

Az alábbi kimenet, jelezve, hogy grub.cfg újraépítve sikeresen és, hogy az alapértelmezett ütemező frissítve lett, hogy NOOP kell megjelennie:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

A Red Hat terjesztési termékcsalád szüksége lesz a következő parancsot:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Rendszer fájl műveleti beállítások konfigurálása
Egy ajánlott eljárás, hogy tiltsa le a *atime* használatnaplózási funkciót a fájlrendszeren. Atime a fájl utolsó hozzáférés időpontja. Minden alkalommal, amikor egy fájl érhető el, a fájlrendszerben történő küldés időbélyegzője legyen a naplóban rögzíti. Azonban ez az információ ritkán használják. Ha letiltja, ha már nincs szüksége, ami csökkenti az általános hozzáférési kihasználtsága (%).  

Atime naplózás letiltásához kell módosítani a fájl rendszer konfigurációs fájl /etc/ fstab, és adja hozzá a **noatime** lehetőséget.  

Például szerkesztése a vim /etc/fstab fájl hozzáadása a noatime az alábbi mintában látható módon:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Ezt követően csatlakoztassa újra a fájlrendszer, a következő paranccsal:  

    mount -o remount /RAID0

A módosított teszteredmény. Amikor módosítja a fájl tesztelése, a hozzáférés ideje nem frissül. Az alábbi példák bemutatják, hogy a kód néz módosítás előtti és utáni.

Előtte:        

![Kód elérésének módosítása előtt][5]

Utána:

![Kód elérésének módosítása után][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Kezeli a nagy feldolgozási maximális számának növelése
MySQL egy olyan magas egyidejűségi adatbázis. Egyidejű leírók száma alapértelmezés szerint akkor 1024 Linux rendszeren, amely nem mindig elegendő. A következő lépések használatával növelheti a maximális párhuzamos kezeli a rendszer a MySQL magas egyidejűségi támogatásához.

### <a name="modify-the-limitsconf-file"></a>A limits.conf fájl módosítása
Növeli a maximális engedélyezett egyidejű végzi, a /etc/security/limits.conf fájlban adja hozzá a következő négy sorokat. Vegye figyelembe, hogy a 65536 értékű a rendszer által támogatott maximális számát.   

    * helyreállítható nofile 65536 értékű
    * a 65536 értékű rögzített nofile
    * helyreállítható nproc 65536 értékű
    * a 65536 értékű rögzített nproc

### <a name="update-the-system-for-the-new-limits"></a>Frissítés a rendszer az új korlátok
A rendszer frissítéséhez futtassa az alábbi parancsokat:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Győződjön meg arról, hogy a rendszerindítás közben frissülnek a korlátok
A /etc/rc.local fájlban a következő indítási parancsok helyezi, így a rendszerindítás közben érvénybe léptetéséhez.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>MySQL-adatbázis optimalizálása
MySQL konfigurálása az Azure-ban, használhatja az azonos teljesítményének hangolása stratégia használhatja a helyszíni gépen.  

A fő i/o-optimalizálás szabályok a következők:   

* Növelje a gyorsítótár méretét.
* I/o-válaszidő csökkentése.  

MySQL-kiszolgáló beállításainak optimalizálása érdekében a my.cnf fájlt, amely az alapértelmezett konfigurációs fájl a kiszolgáló és az ügyfélszámítógépek frissítheti.  

A következő konfigurációs elemek a következők a MySQL teljesítményt érintő főbb tényezőket:  

* **innodb_buffer_pool_size**: A pufferkészletben a pufferelt adatok és az index tartalmazza. Ez általában a fizikai memória 70 %-os értékre.
* **innodb_log_file_size**: Ez az, hogy a visszaállítási napló méretét. Ismétlés naplók segítségével győződjön meg arról, hogy az írási műveletek gyors, megbízható és helyreállítható rendszerösszeomlás után. A beállított érték 512 MB, mivel ez elég hely a naplózás az írási műveletek.
* **max_connections**: néha alkalmazások ne zárja be kapcsolatok megfelelően. Nagyobb lehetővé teszik a kiszolgáló újraindításához idled kapcsolatok több időt. A kapcsolatok maximális száma: 10 000-et, de az ajánlott maximális 5000.
* **Innodb_file_per_table**: Ez a beállítás engedélyezi vagy letiltja a külön fájlban tárolhatja az táblák InnoDB képességét. Jelölje be a jelölőnégyzetet, győződjön meg arról, hogy hatékonyan alkalmazható számos speciális felügyeleti műveleteket. A teljesítmény szempontjából, a tábla terület átviteli fejlesszen és tekintetében felügyeleti teljesítményének optimalizálásához. Ez a beállítás az ajánlott beállítás be Kapcsolva.</br></br>
A MySQL 5.6-os az alapértelmezett beállítás be Kapcsolva, ezért semmilyen műveletet nem kell. Régebbi verziók esetében az alapértelmezett beállítás értéke OFF. A beállítás kell módosítani előtt az adatok betöltése, mert csak az újonnan létrehozott táblák érintett.
* **innodb_flush_log_at_trx_commit**: az alapértelmezett értéke 1, állítsa 0-ra a hatókörrel rendelkező ~ 2. Az alapértelmezett értéke a MySQL-adatbázis önálló leginkább megfelelő lehetőséget. 2. a beállítás lehetővé teszi, hogy a legtöbb adatok integritását, és a MySQL-fürt fő alkalmas. A 0 érték engedélyezi az adatvesztést, ami hatással lehet a megbízhatóság (bizonyos esetekben a jobb teljesítmény érdekében), és alkalmas a MySQL-fürt alárendelt.
* **Innodb_log_buffer_size**: A napló puffer lehetővé teszi, hogy a napló kiüríteni a lemezre, a tranzakció véglegesítése előtt nélkül futtathat tranzakciókat. Azonban ha nagyméretű bináris objektumok vagy szövegmezőt, a gyorsítótár gyorsan fognak használni, és akkor aktiválódik, a gyakori lemez i/o. Fontos, hogy jobban a pufferméret növelése, ha Innodb_log_waits állapot változó nem 0.
* **query_cache_size**: A legjobb lehetőség, hogy tiltsa le a kezdettől. Query_cache_size értéke 0 (Ez az alapértelmezett beállítást, a MySQL 5.6-os) és egyéb módszerek használatával a lekérdezések felgyorsításához.  

Lásd: [D függelék:](#AppendixD) előtt és után az optimalizálási teljesítményének összehasonlítását.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Kapcsolja be a MySQL a lassú lekérdezések naplójának a teljesítménybeli szűk keresztmetszetet elemzése
A MySQL lassú lekérdezések naplója segíthet a lassú lekérdezések azonosítása a MySQL-hez. Miután engedélyezte a MySQL lassú lekérdezések naplója, használhatja a MySQL-eszközökkel, mint a **mysqldumpslow** a teljesítménybeli szűk keresztmetszetek azonosításához.  

Alapértelmezés szerint ez nincs engedélyezve. A lassú lekérdezések naplója bekapcsolásával néhány Processzor-erőforrások igénybe vehet. Javasoljuk, hogy engedélyezze Ez ideiglenesen a teljesítmény szűk hibaelhárításhoz. A lassú lekérdezések naplója bekapcsolása:

1. Módosítsa a my.cnf fájlt a következő sorokat a teljes körű hozzáadásával:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Indítsa újra a MySQL-kiszolgálóhoz.

        service  mysql  restart

3. Ellenőrizze, hogy a beállítás használatával van lépnek érvénybe a **megjelenítése** parancsot.

![Lassú lekérdezések-log ON][7]   

![Lassú lekérdezések-naplója eredmények][8]

Ebben a példában láthatja, hogy a lassú lekérdezések funkció van kapcsolva. Ezután a **mysqldumpslow** eszköz segítségével határozza meg a teljesítmény szűk keresztmetszeteket és optimalizálhatja a teljesítményt, például indexek hozzáadását.

## <a name="appendices"></a>Mellékletek
Az alábbiakban a minta teszt teljesítményadatok előállított célzott laborkörnyezetben. A különböző teljesítmény-finomhangolási megközelítést nyújtanak adatok teljesítmény alakulását általános háttér. Az eredmények alapján különböző környezet vagy a termékverzió-verziók eltérő lehet.

### <a name="AppendixA"></a>A függelék  
**Lemez teljesítménye (IOPS) a RAID különböző szintjeit**

![Lemez iops-érték különböző RAID-szintek][9]

**Teszt parancsok**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Ebben a tesztben a munkaterhelés 64 szál elérni a felső határ RAID használ.
>
>

### <a name="AppendixB"></a>"B" függelék  
**MySQL teljesítményét (teljesítmény) összehasonlítás a különböző RAID-szintek**   
(XFS fájlrendszer)

![Különböző RAID-szintek a MySQL teljesítményének összehasonlítása][10]  
![Különböző RAID-szintek a MySQL teljesítményének összehasonlítása][11]

**Teszt parancsok**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**MySQL teljesítményét (OLTP) összehasonlítás a különböző RAID-szintek**  
![MySQL teljesítményét (OLTP) összehasonlítás a különböző RAID-szintek][12]

**Teszt parancsok**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>C függelék   
**Különböző adattömbök méretű lemezek teljesítményt (IOPS) összehasonlítása**  
(XFS fájlrendszer)

![][13]

**Teszt parancsok**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

A tesztelés végrehajtásához használt méretűek 30 GB és 1 GB, jelölik, a RAID 0 (4 lemez) XFS fájlrendszer.

### <a name="AppendixD"></a>D függelék  
**MySQL teljesítményét (teljesítmény) összehasonlítása előtt és után optimalizálása**  
(XFS fájlrendszer)

![MySQL teljesítményét (teljesítmény) összehasonlítása előtt és után optimalizálása][14]

**Teszt parancsok**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A konfigurációs beállítás alapértelmezett és optimalizálás a következőképpen történik:**

| Paraméterek | Alapértelmezett | Optimalizálás |
| --- | --- | --- |
| **innodb_buffer_pool_size** |None |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

További részletes [optimalizálási konfigurációs paramétereket](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), tekintse meg a [MySQL hivatalos utasítások](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Tesztkörnyezet**  

| Hardver | Részletek |
| --- | --- |
| CPU |AMD Opteron(tm) processzor 4171 HE / 4 mag |
| Memory (Memória) |14 GB |
| Lemez |10 GB/lemez |
| Operációs rendszer |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

