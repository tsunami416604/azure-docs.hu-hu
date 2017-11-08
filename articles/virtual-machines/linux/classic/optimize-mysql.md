---
title: "Linux MySQL teljesítményének optimalizálásához |} Microsoft Docs"
description: "Ismerje meg, hogyan optimalizálható a Linux operációs rendszert futtató Azure virtuális géphez (VM) futtató MySQL."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 7e7582a31cb3e74fd8c3cd0dd54961392d9c53bb
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Az Azure Linux virtuális gépeken futó MySQL teljesítményének optimalizálása
Nincsenek számos tényező befolyásolja az Azure, mind a virtuális hardver kiválasztása és szoftverkonfigurációt MySQL teljesítményére. Ez a cikk foglalkozik, a tárolás, a rendszer és a Helyadatbázis-konfigurációk keresztül optimalizálás teljesítményét.

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager](../../../resource-manager-deployment-model.md) és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. További információ a Linux virtuális gép optimalizálás a Resource Manager modellt: [optimalizálhatja a Linux virtuális Gépet az Azure-on](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Egy Azure virtuális gépen RAID használata
Tároló, a kulcsfontosságú tényező, amely befolyásolja az adatbázis teljesítményének felhőalapú környezetben. Képest egyetlen lemez, RAID keresztül párhuzamossági gyorsabb hozzáférést biztosíthat. További információkért lásd: [szabványos RAID-szintek](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Lemezes i/o-átviteli és az Azure-ban i/o-válaszidő RAID javítható ki. Labor tesztek megjelenítése, hogy a lemez i/o-átviteli meg kell duplázni és i/o-válaszidő csökkenthető félig átlagosan RAID lemezek száma nő (a két négy, négy és nyolc, stb.) Ha. Lásd: [függelék](#AppendixA) részleteiről.  

Lemezes i/o, mellett MySQL teljesítmény javítja, abban az esetben, amikor növeli a RAID szintjét.  Lásd: [B függelék](#AppendixB) részleteiről.  

Érdemes figyelembe venni az adatrészlet méretének is. Általában még nagyobb adattömbméretet, nyílik meg alsó terhelés, különösen a nagy írási műveleteket. Azonban ha adatrészlet mérete túl nagy, adhat, amely megakadályozza, hogy kihasználja a RAID többletterhelést. A jelenlegi alapértelmezett méret 512 KB, ami a legáltalánosabb éles környezetek optimális bizonyult. Lásd: [C függelék](#AppendixC) részleteiről.   

Nincsenek korlátozások is hozzáadhat más virtuális gépek típusainak hány lemezeken. Ezek a korlátozások részletes leírást talál [virtuális gép és felhő mérete](http://msdn.microsoft.com/library/azure/dn197896.aspx). Kövesse az ebben a cikkben RAID példa négy csatolt adatlemezek szüksége lesz, bár esetén dönthet úgy állítsa be a RAID kevesebb lemezt.  

Ez a cikk azt feltételezi, hogy már létrehozott egy Linux virtuális gép és a MYSQL telepítette és konfigurálta. A bevezetés további információkért lásd: MySQL telepítése az Azure-on.  

### <a name="set-up-raid-on-azure"></a>Az Azure-on RAID beállítása
A következő lépések bemutatják, hogyan Azure RAID létrehozása az Azure-portál használatával. Is állíthatja be RAID Windows PowerShell-parancsfájlok használatával.
Ebben a példában azt konfigurálja RAID 0 négy lemezzel.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Adatlemez hozzáadása a virtuális gép
Az Azure portálon az irányítópult megnyitásához, és válassza ki a kívánt adatok lemezt szeretne felvenni a virtuális gépet. Ebben a példában a virtuális gép mysqlnode1.  

<!--![Virtual machines][1]-->

Kattintson a **lemezek** majd **új csatolása**.

![Lemez hozzáadása a virtuális gépek](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Hozzon létre egy új 500 GB lemezterület. Győződjön meg arról, hogy **állomás gyorsítótár preferencia** értéke **nincs**.  Amikor végzett, kattintson a **OK**.

![Üres lemez csatolása](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Ez hozzáadja egy üres lemez a virtuális gép. Ismételje meg ezt a három még többször, hogy négy adatlemezek a RAID.  

A hozzáadott meghajtó van a virtuális gép a kernel üzenet napló megnézzük látható. Például ez az Ubuntu, használja a következő parancsot:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>A további lemezek RAID létrehozása
A következő lépések bemutatják, hogyan [szoftveres RAID Linux konfigurálása](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Ha XFS fájlrendszert használ, RAID létrehozása után hajtható végre az alábbi lépéseket.
>
>

Debian, Ubuntu vagy Linux menta XFS telepítéséhez használja a következő parancsot:  

    apt-get -y install xfsprogs  

A Fedora, a CentOS, vagy az RHEL XFS telepítéséhez használja a következő parancsot:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Állítson be egy új. tárolási elérési útja
Az alábbi parancs segítségével állítson be egy új. tárolási elérési útja:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Az eredeti adatok másolása az új tárolási elérési útja
A következő paranccsal adatok másolása az új tárolási elérési útja:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Módosítsa az engedélyeket, MySQL hozzáférhet (olvasási és írási) az adatok lemezre
A következő paranccsal módosítsa az engedélyeket:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>A lemez i/o-ütemezési algoritmus beállítása
Linux megvalósítja négy különböző ütemezése algoritmusok i/o:  

* NOOP algoritmus (nincs művelet)
* Határidő algoritmus (határidő)
* Teljesen valós Üzenetsor-kezelés algoritmus (CFQ)
* Keret időszak algoritmus (Anticipatory)  

A teljesítmény optimalizálása különböző i/o-bejegyzéstípusait különböző forgatókönyvek alapján választhatja ki. Teljesen elérésű környezetben nincs jelentős különbség a teljesítmény CFQ és a határidő algoritmusok között. Azt javasoljuk, hogy beállította a MySQL-adatbázis környezet stabilitását határideje. Ha nagy mennyiségű szekvenciális i/o, CFQ csökkentheti a lemezek i/o műveleteinek teljesítménye.   

Az SSD és egyéb eszközökről NOOP vagy határidő érhető el az alapértelmezett ütemező jobb teljesítményt.   

A kernel 2.5, mielőtt az alapértelmezett ütemezés i/o-algoritmus határidő. A kernel 2.6.18 verziótól kezdődően CFQ vált az alapértelmezett ütemezés i/o-algoritmus.  Adja meg ezt a beállítást, kernel rendszerindítás közben, vagy dinamikusan módosíthatja ezt a beállítást, ha a rendszer.  

A következő példa bemutatja, hogyan ellenőrizze, és állítsa be az alapértelmezett ütemező a Debian terjesztési termékcsalád NOOP algoritmus.  

### <a name="view-the-current-io-scheduler"></a>Az aktuális i/o-ütemező megtekintése
A következő parancsot az ütemező megtekintése:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Kimeneti, amely megadja, hogy a jelenlegi Feladatütemező következő jelenik meg:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Az aktuális eszköz (/ dev/sda) ütemezési i/o-algoritmus módosítása
A következő parancsokat az aktuális eszköz módosításához:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Beállítása ez /dev/sda csak akkor nem hasznos. Kell beállítani az összes adat lemez ahol az adatbázis található.  
>
>

A következő kimeneti, jelezve, hogy grub.cfg sikeresen újraépítették, és, hogy az alapértelmezett ütemező környezetébe NOOP kell megjelennie:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

A Red Hat terjesztési termékcsalád szükséges csak a következő parancsot:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Fájl műveletek rendszerbeállításainak konfigurálására
Egy bevált gyakorlat az, hogy tiltsa le a *atime* funkciót a fájlrendszeren. Atime az utolsó hozzáférés időpontjának. Amikor egy fájl érhető el, a fájlrendszer a Timestamp típusú rögzíti a naplóban. Ezek az információk azonban igen ritkán alkalmazzák. Ha letiltja, ha nincs szüksége, amely csökkenti a teljes lemez hozzáférés idejét.  

Atime naplózás letiltásához kell módosítani a fájl rendszer konfigurációs fájl /etc/ fstab, és adja hozzá a **noatime** lehetőséget.  

Például szerkesztése a vim /etc/fstab fájl hozzáadása a noatime a következő mintában látható módon:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Ezután csatlakoztassa újra a fájlrendszer, a következő paranccsal:  

    mount -o remount /RAID0

A módosított teszteredmény. Ha módosítja a fájl tesztelése, a hozzáférés időpontja nem frissül. Az alábbi példák bemutatják, mi a kódot a következőképpen néz módosítás előtti és utáni.

Előtte:        

![Code access módosítás előtt][5]

Utána:

![Code access módosítás után][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Kezeli a nagy egyidejű maximális számának növelése
MySQL egy olyan nagy feldolgozási adatbázis. Az alapértelmezett száma párhuzamos leírók érték 1024 Linux, amely nem mindig elegendő. Az alábbi lépések segítségével növelheti a maximális párhuzamos kezeli a rendszer támogatja a magas egyidejűségi beállítása pedig MySQL.

### <a name="modify-the-limitsconf-file"></a>Módosítsa a limits.conf fájlt.
Növeli a maximális megengedett egyidejű kezeli, vegye fel a következő négy sorokat a /etc/security/limits.conf fájlban. Vegye figyelembe, hogy a 65536 értékű a rendszer által támogatott maximális számát.   

    * a 65536 értékű enyhe nofile
    * a 65536 értékű rögzített nofile
    * a 65536 értékű enyhe nproc
    * a 65536 értékű rögzített nproc

### <a name="update-the-system-for-the-new-limits"></a>A rendszer az új korlátok frissítésére
A rendszer frissítéséhez futtassa a következő parancsokat:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Győződjön meg arról, hogy a korlátok rendszerindítás frissítése
Helyezze el a következő indítási parancsok a /etc/rc.local fájl, a rendszerindítás érvénybe léptetéséhez.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>MySQL-adatbázis optimalizálása
Adja meg a MySQL az Azure-on, használhatja a helyszíni gépen azonos teljesítményének hangolása stratégia is használhat.  

A fő i/o-optimalizálás szabályok a következők:   

* Növelje a gyorsítótár méretét.
* I/o-válaszidő csökkentése.  

MySQL-kiszolgáló beállításainak optimalizálása érdekében frissítheti a my.cnf fájlt, amely az alapértelmezett konfigurációs fájl a kiszolgáló és az ügyfélszámítógépek számára.  

A következő konfigurációs elemek olyan a fő MySQL teljesítményét befolyásoló tényezők:  

* **innodb_buffer_pool_size**: A pufferkészlet pufferelt adatok és az index tartalmazza. Ez általában 70 %-a fizikai memória van beállítva.
* **innodb_log_file_size**: visszaállítási napló mérete. Ismét: naplók segítségével győződjön meg arról, hogy az írási műveletek gyors, megbízható és helyreállítható rendszerösszeomlás után. Ez 512 MB-ra, így elég hely az írási műveletek naplózása van beállítva.
* **max_connections**: néha alkalmazások ne zárja be kapcsolatok megfelelően. Nagyobb értéket ad a kiszolgáló több időt idled kapcsolatok újrahasznosítása. Kapcsolatok maximális száma 10 000, de a javasolt legfeljebb 5000.
* **Innodb_file_per_table**: Ez a beállítás engedélyezi vagy letiltja a táblák külön fájlban tárolhatja InnoDB képességét. Jelölje be a jelölőnégyzetet annak érdekében, hogy számos speciális felügyeleti műveletek hatékonyan alkalmazható. A teljesítmény szempontjából ez felgyorsíthatja az a tábla terület átviteli és tekintetében felügyeleti teljesítményének optimalizálásához. Ez a beállítás az ajánlott beállítás be Kapcsolva.</br></br>
A MySQL 5.6 az alapértelmezett beállítás be Kapcsolva, nincs teendője. Régebbi verziók esetében az alapértelmezett beállítás értéke OFF. A úgy kell módosítani, mielőtt adatok betöltése, mert az csak az újonnan létrehozott táblák is érint.
* **innodb_flush_log_at_trx_commit**: az alapértelmezett értéke 1, 0 értékre állítva hatókörű ~ 2. Az alapértelmezett érték a legmegfelelőbb beállítás a különálló MySQL-adatbázis. 2 lehetővé teszi, hogy a legtöbb adatintegritást és MySQL-fürt főkiszolgálójának alkalmas. A 0 érték engedélyezi adatvesztés, ami hatással lehet a megbízhatóság (egyes esetekben nagyobb teljesítményű), és az alárendelt MySQL-fürt alkalmas.
* **Innodb_log_buffer_size**: A napló puffer lehetővé teszi, hogy a tranzakciók anélkül, hogy a napló kiürítése lemezre, a tranzakciók véglegesítése előtt futtatásához. Azonban ha nagy bináris objektum vagy szövegmezőt, a gyorsítótár gyorsan fognak használni, és gyakori lemez i/o indul. Fontos, hogy jobban a pufferméret növelése, ha Innodb_log_waits állapot változó nem 0.
* **query_cache_size**: A legjobb lehetőség le kell tiltani a kezdettől. Query_cache_size értéke 0 (Ez a MySQL 5.6 az alapbeállítás), és más módszerekkel lekérdezések felgyorsítása érdekében.  

Lásd: [D függelék](#AppendixD) előtt és után az optimalizálási teljesítmény összehasonlítása.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>A MySQL lassú lekérdezés naplóban elemzése a teljesítménybeli szűk keresztmetszetek bekapcsolása
A MySQL lassú lekérdezés napló segítségével azonosíthatja a lassú lekérdezések a MySQL. Miután engedélyezte a MySQL lassú lekérdezés napló, használhatja a MySQL-eszközök például **mysqldumpslow** a teljesítménybeli szűk keresztmetszetek azonosításához.  

Alapértelmezés szerint ez nincs engedélyezve. A lassú lekérdezés napló bekapcsolása, előfordulhat, hogy néhány Processzor-erőforrások felhasználását. Javasoljuk, hogy engedélyezze a ideiglenesen szűk keresztmetszetek hibaelhárításhoz. A lassú lekérdezés napló bekapcsolása:

1. Módosítsa a my.cnf fájlt adja hozzá a következő sorokat a befejezési:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Indítsa újra a MySQL-kiszolgálót.

        service  mysql  restart

3. Ellenőrizze, hogy a beállítás használatával tart hatása a **megjelenítése** parancsot.

![Lassú lekérdezés-log ON][7]   

![Lassú lekérdezés-napló eredmények][8]

Ebben a példában láthatja, hogy a lassú lekérdezés szolgáltatás be lett kapcsolva. Ezután a **mysqldumpslow** eszköz a szűk keresztmetszetek, és mint indexek hozzáadása a teljesítmény optimalizálása.

## <a name="appendices"></a>Mellékletek
Az alábbiakban minta teszt teljesítményadatok előállított célzott laborkörnyezetben. Hangolási módszerek különböző teljesítménnyel biztosítják a teljesítmény adatok trend általános. Az eredmények a környezet vagy a termék különböző verziói függően változhat.

### <a name="AppendixA"></a>A függelék  
**Lemez teljesítménye (IOPS) a különböző RAID-szintek**

![Különböző RAID-szintek IOPS lemez][9]

**Teszt parancsok**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> A szinkronizálás számítási Ez a vizsgálat elérni a RAID felső korlátja 64 szál használja.
>
>

### <a name="AppendixB"></a>B függelék  
**Különböző RAID-szintek MySQL (teljesítmény) teljesítmény összehasonlítása**   
(XFS fájlrendszer)

![Különböző RAID-szintek MySQL teljesítmény összehasonlítása][10]  
![Különböző RAID-szintek MySQL teljesítmény összehasonlítása][11]

**Teszt parancsok**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Különböző RAID-szintek MySQL (OLTP) teljesítmény összehasonlítása**  
![Különböző RAID-szintek MySQL (OLTP) teljesítmény összehasonlítása][12]

**Teszt parancsok**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>C függelék   
**A különböző adatrészlet mérete a lemez teljesítménye (IOPS) összehasonlítását**  
(XFS fájlrendszer)

![][13]

**Teszt parancsok**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

A fájl a teszteléshez használt értékek 30 és 1 GB-os, illetve, XFS fájlrendszer a RAID 0 (4 lemezek).

### <a name="AppendixD"></a>D függelék:  
**MySQL teljesítmény (teljesítmény) összehasonlítás előtt és után optimalizálása**  
(XFS fájlrendszer)

![MySQL teljesítmény (teljesítmény) összehasonlítás előtt és után optimalizálása][14]

**Teszt parancsok**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A konfigurációs beállítás az alapértelmezett és optimalizálás a következőképpen történik:**

| Paraméterek | Alapértelmezett | Optimalizálás |
| --- | --- | --- |
| **innodb_buffer_pool_size** |None |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

További részletes [optimalizálási konfigurációs paraméterek](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), tekintse meg a [MySQL hivatalos utasításokat](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Tesztkörnyezet**  

| Hardver | Részletek |
| --- | --- |
| CPU |AMD Opteron(tm) processzor 4171 Helykiszolgálójához / 4 magos |
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

