---
title: A TmaxSoft OpenFrame telepítése Azure virtuális gépekre
description: Az IBM z/OS nagyszámítógépes számítási feladatainak újraüzemeltetése TmaxSoft OpenFrame környezet használatával azure-beli virtuális gépeken.Rehost your IBM z/OS mainframe workloads using TmaxSoft OpenFrame environment on Azure Virtual Machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436044"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>A TmaxSoft OpenFrame telepítése az Azure-ra

Ismerje meg, hogyan állíthat be egy OpenFrame-környezetet az Azure-ban fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok számára. Ez az oktatóanyag végigvezeti az egyes lépésein.

Az OpenFrame több összetevőt is tartalmaz, amelyek az Azure-beli nagyszámítógépes emulációs környezetet hozzák létre. Az OpenFrame online szolgáltatásai például a nagyszámítógépes köztes szoftvereket, például az IBM Customer Information Control System (CICS) rendszert, az OpenFrame Batch pedig a TJES összetevőt, az IBM nagyszámítógép feladatbeviteli alrendszerét (JES) helyettesítik.

Az OpenFrame minden relációs adatbázissal működik, beleértve az Oracle Database, a Microsoft SQL Server, az IBM Db2 és a MySQL adatbázist. Az OpenFrame ezen telepítése a TmaxSoft Tibero relációs adatbázist használja. Mind az OpenFrame, mind a Tibero Linux operációs rendszeren fut. Ez az oktatóanyag telepíti a CentOS 7.3-at, bár más támogatott Linux disztribúciók is használhatók. Az OpenFrame alkalmazáskiszolgáló és a Tibero-adatbázis egy virtuális gépen (VM) van telepítve.

Az oktatóanyag végighalad az OpenFrame-csomag összetevőinek telepítésén. Néhányat külön kell telepíteni.

Fő OpenFrame-összetevők:

- Szükséges telepítőcsomagok.
- Tibero adatbázis.
- Az Open Database Connectivity (ODBC) az OpenFrame alkalmazásai a Tibero adatbázissal való kommunikációra szolgálnak.
- OpenFrame Base, a köztes szoftver, amely kezeli a teljes rendszert.
- OpenFrame Batch, a megoldás, amely felváltja a nagyszámítógép kötegelt rendszerek.
- TACF, egy szolgáltatás modul, amely szabályozza a felhasználók hozzáférését a rendszerekhez és erőforrásokhoz.
- ProSort, a kötegelt tranzakciók rendezési eszköze.
- OFCOBOL, egy fordító, amely értelmezi a mainframe COBOL programjait.
- OFASM, egy fordító, amely értelmezi a mainframe assembler programokat.
- OpenFrame Server Type C (OSC ), a nagyszámítógép köztes szoftverét és az IBM CICS-t felváltó megoldás.
- Java Enterprise User Solution (JEUS), egy java enterprise edition 6 minősítéssel rendelkező webalkalmazás-kiszolgáló.
- OFGW, az OpenFrame átjáró összetevő, amely egy 3270 figyelő.
- OFManager, egy olyan megoldás, amely az OpenFrame működési és felügyeleti funkcióit biztosítja a webes környezetben.

Egyéb szükséges OpenFrame-összetevők:

- OSI, a megoldás, amely felváltja a mainframe middleware és IMS DC.
- TJES, a megoldás, amely a mainframe JES környezetben.
- OFTSAM, a megoldás, amely lehetővé teszi a (V)SAM fájlokat kell használni a nyílt rendszerben.
- OFHiDB, a megoldás, amely felváltja a mainframe IMS DB.
- OFPLI, egy fordító, amely értelmezi a mainframe PL / I programokat.
- PROTRIEVE, egy megoldás, amely végrehajtja a mainframe nyelv CA-Easytrieve.
- OFMiner, egy megoldás, amely elemzi a nagyszámítógépek eszközeit, majd áttelepíti azokat az Azure-ba.

## <a name="architecture"></a>Architektúra

Az alábbi ábra áttekintést nyújt az oktatóanyagban telepített OpenFrame 7.0 építészeti összetevőkről:

![OpenFrame-összetevők](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Az Azure rendszerkövetelményei

Az alábbi táblázat az Azure-beli telepítés követelményeit sorolja fel.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Követelmény</th><th>Leírás</th></tr>
</thead>
<tbody>
<tr><td>Támogatott Linux-disztribúciók az Azure-ban
</td>
<td>
Linux x86 2.6 (32 bites, 64 bites)<br/>
Vörös kalap 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardver
</td>
<td>Magok: 2 (minimum)<br/>
Memória: 4 GB (minimum)<br/>
Csereterület: 1 GB (minimum)<br/>
Merevlemez: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Választható szoftver Windows-felhasználók számára
</td>
<td>PuTTY: Ebben az útmutatóban a virtuális gép funkcióinak konfigurálására használt<br/>
WinSCP: Népszerű SFTP-ügyfél és FTP-ügyfél, amelyet<br/>
Eclipse for Windows: A TmaxSoft által támogatott fejlesztői platform<br/>
(A Microsoft Visual Studio jelenleg nem támogatott)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Előfeltételek

Tervezze meg a kiadások néhány napot, hogy össze az összes szükséges szoftvert, és töltse ki az összes kézi folyamatokat.

Az első lépések előtt tegye a következőket:

- Az OpenFrame telepítési adathordozója a TmaxSoft-tól szerezhető be. Ha Ön már létező TmaxSoft ügyfél, forduljon a TmaxSoft képviselőjéhez egy licencelt példányért. Ellenkező esetben kérje a próbaverziót a [TmaxSoft-tól.](https://www.tmaxsoft.com/contact/)

- Kérje az OpenFrame dokumentációját <support@tmaxsoft.com>e-mailben a rendszernek.

- Szerezzen be egy Azure-előfizetést, ha még nem rendelkezik ilyen-előfizetéssel. A kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) is.

- Választható. Állítson be egy helyek közötti VPN-alagutat vagy egy ugródobozt, amely korlátozza az Azure virtuális géphez való hozzáférést a szervezet engedélyezett felhasználói számára. Ez a lépés nem szükséges, de ajánlott eljárás.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Virtuális gép beállítása az Azure-ban openframe-hez és Tibero-hoz

Az OpenFrame-környezet et különböző telepítési minták használatával állíthatja be, de az alábbi eljárás bemutatja, hogyan telepítheti az OpenFrame alkalmazáskiszolgálót és a Tibero-adatbázist egy virtuális gépre. Nagyobb környezetekben és a jelentős számítási feladatok, ajánlott eljárás az, hogy az adatbázist külön-külön a saját virtuális gép a jobb teljesítmény érdekében.

**Virtuális gép létrehozása**

1. Nyissa meg az <https://portal.azure.com> Azure Portalon, és jelentkezzen be a fiókjába.

2. Kattintson a **Virtuális gépek** lehetőségre.

    ![Erőforráslista az Azure Portalon](media/vm-01.png)

3. Kattintson a **Hozzáadás** gombra.

    ![Hozzáadási lehetőség az Azure Portalon](media/vm-02.png)

4. Az operációs **rendszerek**től jobbra kattintson az **Egyebek**gombra.

     ![További lehetőség az Azure Portalon](media/vm-03.png)

5. Kattintson **a CentOS-alapú 7.3-ra,** hogy pontosan kövesse ezt a bejárást, vagy választhat egy másik támogatott Linux disztribúciót.

     ![Az operációs rendszer beállításai az Azure Portalon](media/vm-04.png)

6. Az **Alapok** beállításai ban adja meg a **Nevet**, **a Felhasználónevet**, **a Hitelesítés típusát**, az **Előfizetést** (használatalapú fizetés az AWS fizetési stílus) és az **Erőforrás csoportot** (használjon egy meglévőt, vagy hozzon létre egy TmaxSoft csoportot).

7. Ha elkészült (beleértve a hitelesítés **típushoz**szükséges nyilvános/titkos kulcspárt is), kattintson a **Küldés**gombra.

> [!NOTE]
> Ha egy SSH nyilvános kulcsot használ a **hitelesítés típusához,** tekintse meg a következő szakaszban a nyilvános/titkos kulcspár létrehozásához szükséges lépéseket, majd itt folytassa a lépéseket.

### <a name="generate-a-publicprivate-key-pair"></a>Nyilvános/titkos kulcspár létrehozása

Ha Windows operációs rendszert használ, a PuTTYgen szükséges a nyilvános/titkos kulcspár létrehozásához.

A nyilvános kulcs szabadon megosztható, de a személyes kulcsot teljesen titokban kell tartani, és soha nem szabad megosztani egy másik féllel. A kulcsok létrehozása után be kell illesztenie az **SSH nyilvános kulcsot** a konfigurációba – valójában feltölti azt a Linux virtuális gépre. A felhasználói fiók kezdőkönyvtárának\_ \~/.ssh könyvtárában tárolt engedélyezett kulcsok találhatók. A Linux virtuális gép ezután képes felismerni és érvényesíteni a kapcsolatot, miután megadja a kapcsolódó **SSH titkos kulcsot** az SSH-ügyfélben (a mi esetünkben a PuTTY).

Amikor új személyeknek hozzáférést biztosít a virtuális géphez: 

- Minden új személy generál saját nyilvános / privát kulcsok segítségével PuTTYgen.
- Magánszemélyek külön-külön tárolják saját személyes kulcsaikat, és elküldik a nyilvános kulcs adatait a virtuális gép rendszergazdájának.
- A rendszergazda beilleszti a nyilvános \~kulcs tartalmát a /.ssh/authorized\_keys fájlba.
- Az új egyén a PuTTY-n keresztül csatlakozik.

**Nyilvános/titkos kulcspár létrehozása**

1.  Töltse le puttygen-tól, <https://www.putty.org/> és telepítse az összes alapértelmezett beállítással.

2.  A PuTTYgen megnyitásához keresse meg a PuTTY telepítési könyvtárat a C:\\Program Files\\PuTTY-ban.

    ![PuTTY interfész](media/puttygen-01.png)

3.  Kattintson a **Létrehozás** lehetőségre.

    ![PuTTY billentyűgenerátor párbeszédpanel](media/puttygen-02.png)

4.  Generáció után mentse a nyilvános kulcsot és a személyes kulcsot is. Illessze be a nyilvános kulcs tartalmát a **Virtuálisgép \> alapjainak létrehozása** ablaktábla **SSH nyilvános kulcsszakaszában** (az előző szakasz 6. és 7. lépésében látható).

    ![PuTTY billentyűgenerátor párbeszédpanel](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Virtuálisgép-szolgáltatások konfigurálása

1. Az Azure Portalon a **Méret kiválasztása** panelen válassza ki a linuxos gép hardverbeállításait. A Tibero és az OpenFrame telepítésének *minimális* követelményei 2 PROCESSZOR és 4 GB RAM, amint az a telepítés ben látható:

    ![Virtuális gép létrehozása - Alapok](media/create-vm-01.png)

2. Kattintson **a 3 Beállítások gombra,** és az alapértelmezett beállításokkal konfigurálja a választható szolgáltatásokat.
3. Tekintse át a fizetési adatait.

    ![Virtuális gép létrehozása - Vásárlás](media/create-vm-02.png)

4. Küldje el a beállításokat. Az Azure megkezdi a virtuális gép üzembe helyezését. Ez a folyamat általában néhány percet vesz igénybe.

5. A virtuális gép üzembe helyezésekor megjelenik az irányítópult, amely a konfiguráció során kiválasztott összes beállítást megjeleníti. Jegyezze fel a **nyilvános IP-címet.**

    ![tmax az Azure irányítópultján](media/create-vm-03.png)

6. Nyissa meg a PuTTY eszközt.

7. Az **Állomásnév**mezőbe írja be a felhasználónevét és a másolt nyilvános IP-címet. **Például,\@felhasználónév publicip**.

    ![PuTTY-konfiguráció párbeszédpanel](media/putty-01.png)

8. A **Kategória mezőben** kattintson a ** \> Connection SSH \> Auth**elemre. Adja meg a **személyes kulcsfájl** elérési útját.

    ![PuTTY-konfiguráció párbeszédpanel](media/putty-02.png)

9. Kattintson **a Megnyitás** gombra a PuTTY ablak elindításához. Ha sikeres, csatlakozik az Azure-on futó új CentOS virtuális géphez.

10. A root felhasználóként való bejelentkezéshez írja be a sudo bash ( **sudo bash**.

    ![Gyökérfelhasználó bejelentkezés a parancsablakban](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>A környezet és a csomagok beállítása

Most, hogy a virtuális gép létrejött, és be van jelentkezve, végre kell hajtania néhány telepítési lépést, és telepítenie kell a szükséges előtelepítési csomagokat.

1. Rendelje hozzá a **demo** nevét a helyi IP-címhez a`vi /etc/hosts`gazdafájl ( szerkesztéséhez vi használatával . Feltételezve, hogy az IP 192.168.96.148 ofdemo, ez a változás előtt:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Ez a változás után:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Csoportok és felhasználók létrehozása:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Változtassa meg a jelszót a felhasználórame7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Frissítse a kernel paramétereit az /etc/sysctl.conf fájlban:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Frissítse a kernel paramétereit dinamikusan újraindítás nélkül:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. A szükséges csomagok beszerezhetése: Győződjön meg arról, hogy a kiszolgáló csatlakozik az internethez, töltse le a következő csomagokat, majd telepítse őket:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio között
     - ncurses

          > [!NOTE]
          > Az ncurses csomag telepítése után hozza létre a következő szimbolikus hivatkozásokat:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - Gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - itrace (nyomkövetés)
     - Gdb

7. Java RPM-telepítés esetén tegye a következőket:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>A Tibero adatbázis telepítése

A Tibero az Azure OpenFrame környezetében számos kulcsfontosságú funkciót biztosít:

- A Tibero az OpenFrame belső adattára a különböző rendszerfunkciókhoz.
- VSAM fájlok, beleértve a KSDS, RRDS, és ESDS, használja a Tibero adatbázis belső adattárolás.
- A TACF adattár tiberoban tárolódik.
- Az OpenFrame katalógus információi tiberoban tárolódnak.
- A Tibero adatbázis az IBM Db2 helyettesítésére használható az alkalmazásadatok tárolására.

**A Tibero telepítése**

1. Ellenőrizze, hogy a Tibero bináris telepítőfájl jelen van-e, és tekintse át a verziószámot.
2. Másolja a Tibero szoftvert a Tibero felhasználói fiókjába (oframe). Példa:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Nyissa meg\_a .bash profilt a vi (`vi .bash_profile`) színben, és illessze be a következőt:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. A bash profil végrehajtásához a parancssortípusra:

    ```
    source .bash_profile
    ```

5. Hozza létre a tippfájlt (a Tibero konfigurációs fájlját), majd nyissa meg vi. Példa:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Módosítsa \$a\_TB HOME/client/config/tbdsn.tbr fájlt, és helyezze a 127.0.0.1-et a localhost helyett az alábbi módon:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Hozza létre az adatbázist. A következő eredmény jelenik meg:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. A Tibero újrahasznosításához először `tbdown` állítsa le a parancsot. Példa:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Most boot Tibero segítségével `tbboot`. Példa:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Táblaterület létrehozásához érje el az adatbázist a SYS-felhasználó (sys/tmax) használatával, majd hozza létre a szükséges táblaterületet az alapértelmezett kötethez és a TACF-hez:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Most írja be a következő SQL parancsokat:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Boot Tibero és ellenőrizze, hogy a Tibero folyamatok futnak:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Kimenet:

![Tibero kimenet](media/tibero-01.png)

## <a name="install-odbc"></a>AZ ODBC telepítése

Az OpenFrame alkalmazásai a nyílt forráskódú unixODBC projekt által biztosított ODBC API használatával kommunikálnak a Tibero adatbázissal.

Az ODBC telepítése:

1. Ellenőrizze, hogy a unixODBC-2.3.4.tar.gz telepítőfájl jelen `wget unixODBC-2.3.4.tar.gz` van-e, vagy használja a parancsot. Példa:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Csomagolja ki a bináris. Példa:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Nyissa meg a unixODBC-2.3.4 könyvtárat, és hozza létre a Makefile fájlt az ellenőrző gép adatai alapján. Példa:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Alapértelmezés szerint a unixODBC a /usr `--prefix` /local kapcsolóban van telepítve, ezért átad egy értéket a hely módosításához. Hasonlóképpen, konfigurációs fájlok telepítése /etc `--sysconfdir` alapértelmezés szerint, így átadja az értéket a kívánt helyre.

4. Makefile végrehajtása:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. A fájl másolása a programkönyvtárba a fordítás után. Példa:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Használja vi szerkeszti a`vi ~/.bash_profile`bash profilt ( ) és adja hozzá a következőket:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Alkalmazza az ODBC-t. Ennek megfelelően szerkesztsd a következő fájlokat. Példa:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Hozzon létre egy szimbolikus hivatkozást, és érvényesítse a Tibero adatbázis-kapcsolatot:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

A következő kimenet jelenik meg:

![ODBC kimenet, amely az SQL-hez csatlakoztatva van](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame base telepítése

Az alapalkalmazás-kiszolgáló telepítése az OpenFrame által a rendszer Azure-beli kezeléséhez használt egyes szolgáltatások előtt történik, beleértve a tranzakciókezelő kiszolgáló folyamatait is.

**Az OpenFrame Base telepítése**

1. Győződjön meg arról, hogy a Tibero\_telepítése\_sikeres\_\_volt,\_majd ellenőrizze, hogy a következő OpenFrame Base7 0 Linux x86 64.bin telepítő fájl és base.properties konfigurációs fájl található-e.

2. Frissítse a bash profilt a következő Tibero-specifikus információkkal:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. A bash profil végrehajtása:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Győződjön meg arról, hogy a Tibero folyamatok futnak. Példa:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Alap](media/base-01.png)

     > [!IMPORTANT]
     > Győződjön meg róla, hogy a telepítés előtt elindítja a Tibero-t.

5. Licenc létrehozása [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) és az OpenFrame Base, Batch, TACF és OSC licencek létrehozása a megfelelő mappában:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Töltse le az OpenFrame Base bináris és base.properties fájljait:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. A telepítő végrehajtása a base.properties fájl használatával. Példa:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Ha elkészült, a telepítés befejezése üzenet diplayed.

8. Ellenőrizze az OpenFrame alapkönyvtárstruktúráját a `ls -ltr` paranccsal. Példa:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. OpenFrame alapindítása:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot parancs kimenete](media/base-02.png)

10. Ellenőrizze, hogy a folyamat állapota készen áll-e a tmadmin paranccsal a si-ben. Az RDY az egyes folyamatok **állapotoszlopában** jelenik meg:

     ![tmadmin parancs kimenete](media/base-03.png)

11. OpenFrame base leállítása:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame Batch telepítése

Az OpenFrame Batch több összetevőből áll, amelyek nagyszámítógépes kötegelt környezeteket szimulálnak, és kötegelt feladatok futtatására szolgálnak az Azure-ban.

**A Batch telepítése**

1. Győződjön meg arról, hogy az alaptelepítés\_sikeres\_volt, majd ellenőrizze, hogy az OpenFrame\_Batch7 0 Fix2\_\_MVS Linux\_x86\_64.bin telepítő fájl és a batch.properties konfigurációs fájl jelen van-e:

2. A parancssorba `vi batch.properties` írja be a batch.properties fájl szerkesztését az vi használatával.

3. Módosítsa a paramétereket a következőképpen:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. A kötegtelepítő végrehajtásához írja be a parancssorba a következőt:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Amikor a telepítés befejeződött, indítsa el a `tmboot` telepített OpenFrame-csomagokat a parancssorba való beírással.

    ![tmboot kimenet](media/tmboot-01.png)

6. Írja `tmadmin` be a parancssorba az OpenFrame folyamat ellenőrzéséhez.

    ![Tmax Admin képernyő](media/tmadmin-01.png)

7. Hajtsa végre a következő parancsokat:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. A `tmdown` parancs segítségével indítsa el és állítsa le a Batch programot:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>A TACF telepítése

A TACF Manager egy OpenFrame szolgáltatásmodul, amely a RACF biztonságán keresztül szabályozza a felhasználók hozzáférését a rendszerekhez és erőforrásokhoz.

**A TACF telepítése**

1. Ellenőrizze, hogy\_az OpenFrame\_\_Tacf7 0 Fix2\_Linux\_x86\_64.bin telepítőfájl és a tacf.properties konfigurációs fájl jelen van-e.
2. Győződjön meg arról, hogy a Batch telepítése sikeres volt,`vi tacf.properties`majd használja a vi parancsot a tacf.properties fájl ( ) megnyitásához.
3. Módosítsa a TACF paramétereit:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. A TACF telepítő befejezése után alkalmazza a TACF környezeti változókat. A parancssorba írja be a következőt:

     ```
     source \~/.bash\_profile
     ```

5. A TACF telepítő végrehajtása. A parancssorba írja be a következőt:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     A kimenet valahogy így néz ki:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. A parancssorba `tmboot` írja be az OpenFrame újraindítását. A kimenet valahogy így néz ki:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Ellenőrizze, hogy a folyamat `tmadmin` állapota `si` készen áll-e a parancsban. Példa:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Az **állapotoszlopban** megjelenik az RDY:

    ![RDY az állapotoszlopban](media/tmboot-02.png)

8. Hajtsa végre a következő parancsokat:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Állítsa le a `tmdown` kiszolgálót a paranccsal. A kimenet valahogy így néz ki:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>A ProSort telepítése

A ProSort egy kötegelt tranzakciókban használt segédprogram az adatok rendezésére.

**A ProSort telepítése**

1. Ellenőrizze, hogy a Batch telepítése sikeres volt-e, majd ellenőrizze, hogy a **\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz** telepítőfájl jelen van-e.

2. A telepítő végrehajtása a tulajdonságfájl használatával. A parancssorba írja be a következőt:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Helyezze át a kiválogat könyvtárat az otthoni helyre. A parancssorba írja be a következőt:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Hozzon létre egy licencalkönyvtárat, és másolja ide a licencfájlt. Példa:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Nyissa meg a bash.profile-t a vi (`vi .bash_profile`) -ban, és frissítse a következőképpen:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. A bash profil végrehajtásához írja be a parancssorba a következőt:`. .bash_profile`

7. Hozza létre a konfigurációs fájlt. Példa:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Hozza létre a szimbolikus hivatkozást. Példa:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Ellenőrizze a ProSort telepítését `prosort -h` a parancs végrehajtásával. Példa:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Az OFCOBOL telepítése

Az OFCOBOL az OpenFrame fordító, amely a nagyszámítógép COBOL programjait értelmezi. 

**Az OFCOBOL telepítése**

1. Ellenőrizze, hogy a Batch/Online telepítése sikeres volt-e, majd ellenőrizze,\_hogy\_az OpenFrame\_COBOL3\_0\_40\_Linux x86 64.bin telepítőfájl jelen van-e.

2. Az OFCOBOL telepítő végrehajtásához írja be a parancssorba a következőt:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Olvassa el a licencszerződést, és a folytatáshoz nyomja le az Enter billentyűt.

4. Fogadja el a licencszerződést. Amikor a telepítés befejeződött, a következők jelennek meg:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Nyissa meg a bash`vi .bash_profile`profilt vi ( ) nyelven, és ellenőrizze, hogy frissült-e OFCOBOL változókkal.
6. Hajtsa végre a bash profilt. A parancssorba írja be a következőt:

     ```
      source ~/.bash_profile
     ```

7. Másolja az OFCOBOL licencet a telepített mappába. Példa:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Nyissa meg az OpenFrame tjclrun.conf konfigurációs fájlt, és nyissa meg vi. Példa:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Itt a SYSLIB szakasz a változás előtt:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Itt a SYSLIB rész a változás után:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Tekintse át\_az\_OpenFrame COBOL InstallLog.log fájlt a vi fájlban, és ellenőrizze, hogy nincsenek-e hibák. Példa:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Használja `ofcob --version` a parancsot, és tekintse át a verziószámot a telepítés ellenőrzéséhez. Példa:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Indítsa újra az `tmdown/tmboot` OpenFrame-et a paranccsal.

## <a name="install-ofasm"></a>AZ OFASM telepítése

Az OFASM az OpenFrame fordító, amely a nagyszámítógép összeszerelő programjait értelmezi.

**Az OFASM telepítése**

1. Ellenőrizze, hogy a Batch/Online telepítése sikeres volt-e, majd\_\_ellenőrizze, hogy az OpenFrame\_ASM3\_0 Linux x86\_64.bin telepítőfájl jelen van-e.

2. A telepítő végrehajtása. Példa:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Olvassa el a licencszerződést, és a folytatáshoz nyomja le az Enter billentyűt.
4. Fogadja el a licencszerződést.
5. Ellenőrizze, hogy a bash profil frissült OFASM változókkal. Példa:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Nyissa meg az OpenFrame tjclrun.conf konfigurációs fájlt vi-ban, és az alábbiak szerint szerkesztse:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Itt van a [SYSLIB] szakasz a változás *előtt:*

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Itt van a [SYSLIB] szakasz a változás *után:*

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Nyissa meg\_az\_OpenFrame ASM InstallLog.log fájlt a vi fájlban, és ellenőrizze, hogy nincsenek-e hibák. Példa:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Indítsa újra az OpenFrame-et az alábbi parancsok egyikének kiadásával:

     ```
     tmdown / tmboot
     ```

     —vagy—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Operációs rendszerc telepítése

Az OSC az IBM CICS-hez hasonló OpenFrame környezet, amely támogatja a nagy sebességű OLTP tranzakciókat és más felügyeleti funkciókat.

**Az OSC telepítése**

1. Ellenőrizze, hogy az alaptelepítés sikeres volt-e, majd ellenőrizze,\_hogy\_az OpenFrame\_OSC7\_0\_Fix2\_Linux x86 64.bin telepítőfájl és az osc.properties konfigurációs fájl jelen van-e.
2. Az osc.properties fájlban a következő paramétereket szerkeszti:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. A telepítő végrehajtása a tulajdonságfájl használatával az alábbi módon:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Ha elkészült, megjelenik a "Telepítés befejezve" üzenet.

4. Ellenőrizze, hogy a bash profil frissített-e az OSC változókkal.
5. Tekintse át\_az\_OpenFrame\_OSC7 0 Fix2\_InstallLog.log fájlt. A következőhöz hasonló eredményt kell kapnia:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Az ofsys.seq konfigurációs fájl vi használatával nyissa meg a programot. Példa:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Az \#ALAP \#és a BATCH szakaszban az ábrán látható módon szerkeszti a paramétereket.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Másolja a licencfájlt. Példa:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Az OSC elindításához és leállításához inicializálja a `osctdlinit OSCOIVP1` CICS-régió megosztott memóriáját a parancssorba való beírással.

10. Futtassa `oscboot` az OSC indításához. A kimenet valahogy így néz ki:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Annak ellenőrzéséhez, hogy a folyamat `tmadmin` állapota készen áll-e, használja a si parancsát. Az összes folyamatnak meg kell **status** jelenítenie az RDY-t az állapotoszlopban.

    ![RdY-t megjelenítő folyamatok](media/tmadmin-02.png)

12. Az OSC leállítása `oscdown` a paranccsal.

## <a name="install-jeus"></a>A JEUS telepítése

A JEUS (Java Enterprise User Solution) biztosítja az OpenFrame webalkalmazás-kiszolgáló megjelenítési rétegét.

A JEUS telepítése előtt telepítse az Apache Ant csomagot, amely a JEUS telepítéséhez szükséges könyvtárakat és parancssori eszközöket biztosítja.

**Az Apache Ant telepítése**

1. Letöltés Ant bináris `wget` a parancsot. Példa:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. A `tar` segédprogram segítségével bontsa ki a bináris fájlt, és helyezze át a megfelelő helyre. Példa:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. A hatékonyság érdekében hozzon létre egy szimbolikus linket:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Nyissa meg a bash`vi .bash_profile`profilt vi ( ), és frissítse azt a következő változók:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Alkalmazza a módosított környezeti változót. Példa:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**A JEUS telepítése**

1. Bontsa ki `tar` a telepítőt a segédprogrammal. Példa:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Hozzon létre egy`mkdir jeus7` **jeus** mappát ( ) és csomagolja ki a bináris.
3. Váltson a **telepítési** könyvtárra (vagy használja a JEUS paramétert a saját környezetéhez). Példa:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. A `ant clean-all` build végrehajtása előtt hajtsa végre. A kimenet valahogy így néz ki:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Készítsen biztonsági másolatot a domain-config-template.properties fájlról. Példa:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Nyissa meg a domain-config-template.properties fájlt a vi.vi.-ban:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Váltás `jeus.password=jeusadmin nodename=Tmaxsoft` a következőre:`jeus.password=tmax1234 nodename=ofdemo`

8. A `ant install` JEUS létrehozásához hajtsa végre a parancsot.
9.  Frissítse a\_.bash profilfájlt a JEUS változókkal az ábrán látható módon:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Hajtsa végre a bash profilt. Példa:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Nem kötelező*. Hozzon létre egy aliast a JEUS-összetevők egyszerű leállításához és rendszerindításához:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. A telepítés ellenőrzéséhez indítsa el a tartományi felügyeleti kiszolgálót az alábbi módon:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ellenőrizze a webes bejelentkezés tasztással:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Megjelenik <http://192.168.92.133:9736/webadmin/login.> például a bejelentkezési képernyő:
    
     ![JEUS WebAdmin bejelentkezési képernyő](media/jeus-01.png)

     > [!NOTE]
     > Ha bármilyen problémát tapasztal a portbiztonsággal kapcsolatban, nyissa`systemctl stop firewall`meg a 9736-os portot, vagy tiltsa le a tűzfalat ( ).

14. A kiszolgáló1 állomásnevének módosításához kattintson **a & szerkesztés zárolása parancsra,** majd a **kiszolgáló1**parancsra. A Kiszolgáló ablakban módosítsa az állomásnevet az alábbiak szerint:

    1.  Módosítsa **a Nodename nevet** **ofdemo**névre.
    2.  Kattintson az ablak jobb oldalán az **OK** gombra.
    3.  Kattintson a **Módosítások alkalmazása** gombra az ablak bal alsó részén, és a leírásmezőbe írja be az *Állomásnév módosítása parancsot*.

    ![JEUS WebAdmin képernyő](media/jeus-02.png)

15. Ellenőrizze, hogy a konfiguráció sikeres-e a megerősítő képernyőn.

    ![jeus_domain kiszolgáló képernyője](media/jeus-03.png)

16. Indítsa el a "server1" felügyelt kiszolgálófolyamatot a következő paranccsal:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Az OFGW telepítése

OFGW Az OpenFrame átjáró, amely támogatja a kommunikációt a 3270 terminál emulátor és az OSI bázis között, és kezeli a terminál emulátor és az OSI közötti munkameneteket.

**Az OFGW telepítése**

1. Ellenőrizze, hogy a JEUS telepítése sikeresen megtörtént-e, majd ellenőrizze, hogy az OFGW7\_0\_1\_Generic.bin telepítőfájl jelen van-e.
2. A telepítő végrehajtása. Példa:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. A megfelelő kérésekhez használja a következő helyeket:
     -   JEUS Kezdőkönyvtár
     -   JEUS domain név
     -   JEUS-kiszolgáló neve
     -   Tibero vezető
     -   Tmax csomópontazonosítóofdemo

4. Fogadja el a többi alapértelmezést, majd nyomja le az Enter billentyűt a telepítőből való kilépéshez.

5. Ellenőrizze, hogy az OFGW URL-címe a várt módon működik-e:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     A következő képernyő jelenik meg:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Az OFManager telepítése

Az OFManager működési és kezelési funkciókat biztosít az OpenFrame számára a webes környezetben.

**Az OFManager telepítése**

1. Ellenőrizze, hogy az\_OFManager7 Generic.bin telepítőfájl jelen van-e.
2. A telepítő végrehajtása. Példa:

     ```
     OFManager7_Generic.bin
     ```

3.  A folytatáshoz nyomja le az Enter billentyűt, majd fogadja el a licencszerződést.
4.  Válassza ki a telepítési mappát.
5.  Fogadja el az alapértelmezett beállításokat.
6.  Válassza a Tibero adatbázist.
7.  Nyomja le az Enter billentyűt a telepítőből való kilépéshez.
8.  Ellenőrizze, hogy az OFManager URL-címe a várt módon működik-e:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Megjelenik a kezdőképernyő:

![Tmax OpenFrame Manager bejelentkezési képernyő](media/ofmanager-01.png)

Ezzel befejeződött az OpenFrame-összetevők telepítése.

## <a name="next-steps"></a>További lépések

Ha nagyszámítógépes migrációt fontolgat, bővülő partneri ökoszisztémánk segítséget nyújt Önnek. A partneri megoldás kiválasztásával kapcsolatos részletes útmutatásért tekintse meg a [Platform Modernizációs Szövetséget.](https://datamigration.microsoft.com/)

-   [Bevezetés az Azure használatába](https://docs.microsoft.com/azure/)
-   [A gazdarendszer integrációs kiszolgálójának (HIS) dokumentációja](https://docs.microsoft.com/host-integration-server/)
-   [Útmutató az Azure virtuális adatközpontok lift-and-shift útmutató](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
