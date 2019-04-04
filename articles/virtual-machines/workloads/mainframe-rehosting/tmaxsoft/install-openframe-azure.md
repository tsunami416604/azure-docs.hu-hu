---
title: TmaxSoft OpenFrame telepítése Azure-beli virtuális gépeken
description: Áthelyezési IBM z/OS nagyszámítógépes feladatait TmaxSoft OpenFrame környezet Azure-beli virtuális gépeken (VM-EK) használatával.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 6b109f347ee7a917b57acfc56ab4418755295bc5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896374"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame telepítése Azure-ban

Ismerje meg, hogyan fejlesztési, bemutatók, tesztelési vagy éles üzemi környezetekhez alkalmasak az Azure-ban OpenFrame környezet beállításával. Ez az oktatóanyag végigvezeti az egyes lépések.

OpenFrame magában foglalja a több összetevőt, amely a nagyszámítógépes emulációs környezet létrehozása az Azure-ban. Ha például OpenFrame online szolgáltatások, cserélje le a nagyszámítógépes közbenső például IBM vásárlói adatokat vezérlő rendszer (CICS), és OpenFrame Batch, a TJES összetevővel váltja fel az IBM Nagyszámítógépek feladat bejegyzés alrendszer (JES).

Minden olyan relációs adatbázis, beleértve az Oracle Database, a Microsoft SQL Server, az IBM DB2-höz és a MySQL OpenFrame működik. A telepítést OpenFrame TmaxSoft Tibero relációs adatbázist használja. OpenFrame és a Linux operációs rendszeren futtassa Tibero. Ebben az oktatóanyagban CentOS 7.3, telepíti, bár egyéb támogatott Linux-disztribúciók is használhatja. A OpenFrame-kiszolgáló és az Tibero adatbázis egy virtuális gépen (VM) vannak telepítve.

Az oktatóanyag végigvezeti Önt a OpenFrame csomag összetevőinek telepítését. Néhány külön kell telepíteni.

Fő OpenFrame összetevők:

- Szükséges telepítési csomagokat.
- Tibero adatbázis.
- Nyissa meg adatbázis-kapcsolat (ODBC) OpenFrame alkalmazások használják a Tibero adatbázissal való kommunikációhoz.
- OpenFrame alap, a közbenső szoftver, amely a teljes rendszer felügyeli.
- A megoldás, amely kiváltja a nagyszámítógépes batch rendszerek OpenFrame kötegben.
- TACF, a szolgáltatás a modul, amely szabályozza a rendszerek és az erőforrások felhasználói hozzáférését.
- ProSort, rendezési eszközt a kötegelt tranzakciókat.
- OFCOBOL, fordító, amely értelmezi a nagyszámítógépes COBOL programok.
- OFASM, fordító, amely értelmezi a nagyszámítógépes assembler programok.
- OpenFrame kiszolgáló típusú C (OSC), a megoldás, amely a nagyszámítógépes közbenső és IBM CICS váltja fel.
- A Java Enterprise felhasználói megoldás (JEUS), egy webalkalmazás-kiszolgáló, amely a Java Enterprise Edition 6 minősítéssel.
- OFGW, a OpenFrame átjáró összetevője, amely egy 3270 figyelő.
- OFManager, egy megoldás, amely a webes környezet OpenFrame a művelet és a felügyeleti funkciókat biztosít.

Egyéb szükséges OpenFrame összetevők:

- OSI, a megoldás, amely a nagyszámítógépes közbenső szoftveres és a tartományvezérlő IMS váltja fel.
- TJES, a megoldás, amely a nagyszámítógépes JES környezetet biztosít.
- OFTSAM, a megoldás, amely lehetővé teszi a nyílt rendszerben használható SAM (V) fájlokat.
- OFHiDB, a megoldás, amely a nagyszámítógépes lecseréli a IMS DB.
- OFPLI, fordító, amely értelmezi a nagyszámítógépes a PL / I programokat.
- PROTRIEVE, olyan megoldás, amely végrehajtja a CA-Easytrieve nagyszámítógépes nyelvét.
- OFMiner, olyan megoldás, amely elemzi az Nagyszámítógépek eszközöket, és ezután áttelepíti őket az Azure-bA.

## <a name="architecture"></a>Architektúra

Az alábbi ábra az ebben az oktatóanyagban telepítette OpenFrame 7.0 architektúra összetevői áttekintést nyújt:

![OpenFrame összetevők](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Az Azure rendszerre vonatkozó követelmények

Az alábbi táblázat felsorolja az Azure-ban a telepítési követelményeinek.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Követelmény</th><th>Leírás</th></tr>
</thead>
<tbody>
<tr><td>Az Azure-ban támogatott Linux-disztribúciók
</td>
<td>
Linux x86 2.6-os (32 bites, 64 bites)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardver
</td>
<td>Mag: 2 (legalább)<br/>
Memória: 4 GB (minimum)<br/>
Lapozófájl: 1 GB (legalább)<br/>
Merevlemez: 100 GB (legalább)<br/>
</td>
</tr>
<tr><td>Windows-felhasználók számára nem kötelező szoftverek
</td>
<td>Putty-kapcsolaton keresztül: Ebben az útmutatóban használt virtuális gép funkciók konfigurálása<br/>
WinSCP: Egy népszerű SFTP-ügyfél és az FTP-ügyfél használata<br/>
For Windows eclipse-ben: Egy fejlesztői platform TmaxSoft által támogatott<br/>
(A Microsoft Visual Studio nem támogatott jelenleg)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Előfeltételek

Tervezze meg a kiadások néhány napig az összes szükséges szoftver össze, és végezze el a manuális folyamatokat.

Az első lépések előtt tegye a következőket:

- A OpenFrame telepítési adathordozó beszerzése TmaxSoft. Ha Ön már TmaxSoft ügyfél, a képviselőjétől TmaxSoft licencelt másolatot. Ellenkező esetben a rendszer próbaverzióját az kérelem [TmaxSoft](http://www.tmaxsoft.com/contact/).

- E-mailek küldése a OpenFrame dokumentáció kérni <support@tmaxsoft.com>.

- Ha még nem rendelkezik egy Azure-előfizetés beszerzése. Ezenkívül létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

- Választható. Site-to-site VPN-alagúton vagy korlátozza a hozzáférést a feljogosított felhasználók a szervezet az Azure virtuális Gépen a jumpbox beállítása. Ez a lépés nem kötelező, de az ajánlott eljárás.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Azure-beli virtuális gép OpenFrame és Tibero beállítva

Beállíthatja a OpenFrame-környezet különböző üzembe helyezési minták, de az alábbi eljárás bemutatja a OpenFrame-kiszolgáló és a egy virtuális gépen a Tibero adatbázis üzembe helyezése. Nagyobb környezetekben, és jelentős számítási feladatokhoz ajánlott eljárás, hogy az adatbázis a saját virtuális Gépet, a jobb teljesítmény érdekében külön-külön telepíti-e.

**Virtuális gép létrehozása**

1. Nyissa meg az Azure Portalra a <http://portal.azure.com> , és jelentkezzen be a fiókjába.

2. Kattintson a **virtuális gépek**.

    ![Az Azure portal Resource list](media/vm-01.png)

3. Kattintson a **Hozzáadás** parancsra.

    ![A beállítás hozzáadása az Azure Portalon](media/vm-02.png)

4. Jobb oldalán **operációs rendszerek**, kattintson a **további**.

     ![További lehetőség az Azure Portalon](media/vm-03.png)

5. Kattintson a **CentOS-alapú 7.3** pontosan, kövesse az ebben az útmutatóban, vagy választhat egy másik támogatott Linux-disztribúció.

     ![Operációs rendszer beállításait, az Azure Portalon](media/vm-04.png)

6. Az a **alapjai** adja meg a beállításokat, **neve**, **felhasználónév**, **hitelesítési típus**, **előfizetés** (Használatalapú a fizetés AWS stílusa), és **erőforráscsoport** (használjon egy meglévőt, vagy hozzon létre egy TmaxSoft).

7. Ha végzett (beleértve a nyilvános/titkos kulcspárt a **hitelesítési típus**), kattintson a **küldés**.

> [!NOTE]
> Ha egy nyilvános SSH-kulcs, a használatával **hitelesítési típus**, tekintse meg a lépéseket a nyilvános/titkos kulcspár létrehozásához a következő szakaszban, majd folytathatja a lépéseket.

### <a name="generate-a-publicprivate-key-pair"></a>Egy nyilvános/titkos kulcspár létrehozása

Ha egy Windows operációs rendszert használ, egy nyilvános/titkos kulcspár létrehozásához PuTTYgen kell.

A nyilvános kulcs szabadon megoszthatók, de a titkos kulcs teljes egészében titkos kell tartani, és a egy másik fél soha nem lehet megosztva. A kulcsok létrehozása, után kell beilleszteni a **nyilvános SSH-kulcs** be a konfiguráció – érvényben, feltölti a Linux rendszerű virtuális gép. Tárolt belül engedélyezett\_belül a kulcsok a \~a felhasználói fiók kezdőkönyvtár /.ssh könyvtár. A Linux rendszerű virtuális gép majd tudja majd felismerni és a kapcsolat ellenőrzése után adja meg a társított **titkos SSH-kulcs** (esetünkben putty-t) SSH-ügyfél a.

Ha új egyéni felhasználók számára a virtuális gép hozzáférés megadását: 

- Minden egyes új személy a saját nyilvános/titkos kulcsok PuTTYgen használatával hoz létre.
- Egyéni felhasználók számára a saját titkos kulcsok külön tárolja, és a nyilvánoskulcs-adatokat küldeni a rendszergazdának, a virtuális gép.
- A rendszergazda a nyilvános kulcs tartalmát illeszti be a \~/.ssh/authorized\_kulcsok fájlja.
- Az új egyéni a PuTTY-n keresztül kapcsolódik.

**Egy nyilvános/titkos kulcspár létrehozása**

1.  Töltse le a PuTTYgen <https://www.putty.org/> , és telepítse az alapértelmezett beállítások használatával.

2.  Nyissa meg a PuTTYgen, keresse meg a PuTTY telepítési könyvtár c:\\Program Files\\putty-kapcsolaton keresztül.

    ![PuTTY felülete](media/puttygen-01.png)

3.  Kattintson a **készítése**.

    ![PuTTY Key Generator párbeszédpanel](media/puttygen-02.png)

4.  Generáció, után mentse a nyilvános kulcs és a titkos kulcsot. Illessze be a nyilvános kulcsot a tartalmát a **nyilvános SSH-kulcs** szakaszában a **hozzon létre virtuális gépet \> alapjai** (6. és 7 az előző szakaszban látható) panelen.

    ![PuTTY Key Generator párbeszédpanel](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Virtuális gép funkciók konfigurálása

1. Az Azure Portalon az a **méret kiválasztása** panelen válassza ki a kívánt Linux rendszerű gép hardver beállításokat. A *minimális* Tibero és a OpenFrame telepítésének követelményei 2 processzorra és 4 GB RAM-MAL telepítési példában látható módon:

    ![Hozzon létre virtuálisgép - alapjai](media/create-vm-01.png)

2. Kattintson a **3 beállítások** és az alapértelmezett beállításokat használja a választható funkciók konfigurálása.
3. Tekintse át a fizetési adatait.

    ![Hozzon létre virtuálisgép - beszerzési](media/create-vm-02.png)

4. Küldje el a beállításokat. Az Azure elkezdi a virtuális gép üzembe helyezése. Ez a folyamat általában néhány percet vesz igénybe.

5. A virtuális gép telepítésekor a hozzá tartozó irányítópult látható, a konfiguráció során megadott beállításokat. Jegyezze fel a **nyilvános IP-cím**.

    ![Azure-irányítópulton Tmax](media/create-vm-03.png)

6. Nyissa meg a PuTTY eszközt.

7. A **állomásnév**, írja be a felhasználónevét és a nyilvános IP-cím, másolja. Ha például **felhasználónév\@publicip**.

    ![PuTTY konfiguráció párbeszédpanel](media/putty-01.png)

8. Az a **kategória** kattintson **kapcsolat \> SSH \> Auth**. Az elérési útját adja meg a **titkos kulcs** fájlt.

    ![PuTTY konfiguráció párbeszédpanel](media/putty-02.png)

9. Kattintson a **nyílt** a PuTTY ablak megnyitása. Ha ez sikeres, csatlakozik az új CentOS virtuális gép az Azure-ban.

10. Jelentkezzen be gyökér szintű felhasználó, írja be a következőt **sudo bash**.

    ![Legfelső szintű felhasználói bejelentkezési parancssori ablakban](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>A környezet és a csomagok beállítása

Most, hogy a virtuális gép létrejött, és be van jelentkezve, néhány beállítás lépésekkel, és a szükséges előtelepítési csomagok telepítéséhez.

1. A név hozzárendelése **ofdemo** vi használatával a hosts fájl szerkesztése a helyi IP-címre (`vi /etc/hosts`). Feltéve, hogy az IP 192.168.96.148 ofdemo, ez az a váltás előtt:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Ez a váltás után:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Felhasználók és csoportok létrehozása:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. A felhasználó oframe7 jelszavának módosítása:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Frissítse a kernel paramétereket /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Frissítse a kernel paraméterek dinamikusan újraindítás nélkül:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Kérje le a szükséges csomagokat: Győződjön meg arról, hogy a kiszolgáló kapcsolódik az internethez, töltse le a következő csomagokat, és telepítse őket:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > A ncurses csomag telepítése után a következő szimbolikus hivatkozás létrehozása:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Java RPM telepítési, esetén tegye a következőket:

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

## <a name="install-the-tibero-database"></a>A Tibero adatbázisának telepítése

Tibero OpenFrame környezetben számos kulcsfontosságú funkciót biztosít az Azure-ban:

- Tibero OpenFrame belső adattárként szolgál különféle rendszerfunkciók.
- VSAM fájlok, KSDS RRDS és ESDS, beleértve a Tibero adatbázis belső szolgáltatást használják adattárolásra.
- Tibero TACF adattár tárolja.
- A OpenFrame katalógusadatokat Tibero van tárolva.
- A Tibero adatbázis helyett az IBM DB2-höz, az alkalmazások adatainak tárolására használható.

**Tibero telepítése**

1. Ellenőrizze, hogy megtalálható-e a Tibero bináris telepítőfájllal, és tekintse át a verziószámot.
2. Másolja a Tibero szoftver Tibero felhasználói fiók (oframe). Példa:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Nyissa meg a .bash\_profil vi (`vi .bash_profile`), és illessze be a következőket:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. A bash-profilt, a parancssorba írja be a végrehajtásához:

    ```
    source .bash_profile
    ```

5. Hozza létre a tip-fájlt (konfigurációs fájlt az Tibero), majd nyissa meg a vi. Példa:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Módosítsa \$TB\_HOME/client/config/tbdsn.tbr, és csak 127.0.0.1 inkább oflocalhost látható módon:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Az adatbázis létrehozásához. A következő eredmény jelenik meg:

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

8. Újrahasznosítást Tibero, először állítsa le használatával a `tbdown` parancsot. Példa:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Most indítsa el az Tibero `tbboot`. Példa:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Hozzon létre egy táblaterületre, nyissa meg az adatbázis SYS felhasználói (sys/tmax), majd hozza létre a szükséges táblaterületen. keresse az alapértelmezett mennyiségi és TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Most írja be a következő SQL-parancsokat:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Rendszerindítási Tibero, és győződjön meg arról, hogy futnak-e a Tibero folyamatok:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Kimenet:

![Tibero kimenet](media/tibero-01.png)

## <a name="install-odbc"></a>Telepítse az ODBC

OpenFrame az alkalmazásokat az Tibero adatbázissal, a nyílt forráskódú unixodbc szoftverre projekt által biztosított az ODBC-API-val folytatott kommunikációhoz.

ODBC telepítése:

1. Győződjön meg arról, hogy a unixodbc szoftverre-2.3.4.tar.gz installer fájl jelen-e, vagy használja a `wget unixODBC-2.3.4.tar.gz` parancsot. Példa:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Csomagolja ki a bináris fájlt. Példa:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Lépjen abba a könyvtárba unixodbc szoftverre-2.3.4, és a Makefile létrehozása a ellenőrzési adatai alapján. Példa:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Alapértelmezés szerint unixodbc szoftverre van telepítve usr végrehajtásához, ezért `--prefix` helyének módosításához értéket továbbítja. Hasonlóképpen, konfigurációs fájlokat az/etc alapértelmezés szerint települnek, így `--sysconfdir` átadja a kívánt helyre értékét.

4. Hajtsa végre a Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. A végrehajtható fájlt másolja a program könyvtárban fordítása után. Példa:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. A bash-profil szerkesztése vi használatával (`vi ~/.bash_profile`), és adja hozzá a következő:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. A alkalmazni az ODBC. Ennek megfelelően módosítsa a következő fájlokat. Példa:

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

8. Hozzon létre szimbolikus hivatkozást, és a Tibero adatbázis-kapcsolat ellenőrzése:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

A következő kimenet jelenik meg:

![Az SQL ODBC-kimenetet megjelenítő csatlakoztatva](media/odbc-01.png)

## <a name="install-openframe-base"></a>Telepítési OpenFrame

Az alapszintű application server telepítve van az egyes szolgáltatásokat használó OpenFrame kezelése az Azure-ban, beleértve a kiszolgálói folyamatokat kezeli a tranzakciót a rendszer előtt.

**A telepítési OpenFrame**

1. Ellenőrizze, hogy a Tibero telepítése sikeres volt, akkor ellenőrizze, hogy a következő OpenFrame\_Base7\_0\_Linux\_x86\_64. bin installer fájl- és base.properties konfigurációs fájlban találhatók.

2. A bash-profil frissítése a következő Tibero-specifikus információkkal:

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

3. Hajtsa végre a bash-profilhoz:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Győződjön meg arról, hogy a Tibero folyamatok futnak. Példa:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Alapszintű](media/base-01.png)

     > [!IMPORTANT]
     > Ellenőrizze, hogy a telepítés előtt Tibero megkezdése.

5. A licenc létrehozása [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) és OSC licenceket a OpenFrame alap, a Batch, a TACF HELYEZNI, a megfelelő mappát:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Töltse le a OpenFrame alap bináris és base.properties fájlokat:

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

7. A telepítő a base.properties fájllal hajtható végre. Példa:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Amikor végzett, a telepítés befejezése üzenetet kötelező tulajdonságlapján látható.

8. Ellenőrizze a OpenFrame alap directory szerkezet használatával a `ls -ltr` parancsot. Példa:

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

9. Indítsa el a OpenFrame alapja:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot parancs kimenete](media/base-02.png)

10. Ellenőrizze, hogy a folyamat állapotának készen áll a tmadmin paranccsal si-e. RDY jelenik meg a **állapot** oszlop az egyes folyamatok:

     ![tmadmin parancs kimenete](media/base-03.png)

11. Állítsa le a OpenFrame alapja:

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

## <a name="install-openframe-batch"></a>OpenFrame köteg telepítése

OpenFrame Batch nagyszámítógépes batch környezetek szimuláló több összetevőből áll, és az Azure-ban kötegelt feladatok futtatására szolgál.

**A Batch telepítése**

1. Győződjön meg arról, hogy az alapul szolgáló telepítés sikeres volt, akkor ellenőrizze, hogy a OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64. bin telepítőfájllal, és konfigurációs fájl Batch.Properties válnak elérhetővé:

2. A parancssorba írja be a `vi batch.properties` vi használatával batch.properties fájl szerkesztése.

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

4. A batch-telepítő, a parancssorba írja be a végrehajtásához:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. A telepítés befejeződése után indítsa el a telepített OpenFrame ügyfélcsomagok beírásával `tmboot` parancsot a parancssorba.

    ![tmboot kimenet](media/tmboot-01.png)

6. Típus `tmadmin` parancsot a parancssorba a OpenFrame folyamat ellenőrzéséhez.

    ![Tmax rendszergazdai képernyős](media/tmadmin-01.png)

7. Hajtsa végre a következő parancsokat:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Használja a `tmdown` indítsa el, és állítsa le a Batch parancsot:

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

## <a name="install-tacf"></a>TACF telepítése

TACF Manager egy OpenFrame szolgáltatás modul, amely szabályozza a rendszerek és RACF biztonsági erőforrásai a felhasználói hozzáférést.

**TACF telepítése**

1. Ellenőrizze, hogy a OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64. bin installer fájl- és tacf.properties konfigurációs fájlban találhatók.
2. Győződjön meg arról, hogy a kötegelt telepítés sikeres volt, majd nyissa meg a tacf.properties fájlt vi használatával (`vi tacf.properties`).
3. Módosítsa a TACF paramétereket:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Miután befejezte a TACF telepítő, TACF környezeti változók vonatkoznak. A parancssorba írja be a következőt:

     ```
     source \~/.bash\_profile
     ```

5. Hajtsa végre a TACF telepítő. A parancssorba írja be a következőt:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Kimenet az alábbihoz hasonlóan jelenik meg:

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

6. A parancssorba írja be a `tmboot` OpenFrame újraindítására. Kimenet az alábbihoz hasonlóan jelenik meg:

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

7. Ellenőrizze, hogy a folyamat állapota Kész használatával `tmadmin` a a `si` parancsot. Példa:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Az a **állapot** oszlop, RDY jelenik meg:

    ![Az Állapot oszlopban RDY](media/tmboot-02.png)

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

9. A kiszolgáló leállítása használatával a `tmdown` parancsot. Kimenet az alábbihoz hasonlóan jelenik meg:

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

## <a name="install-prosort"></a>ProSort telepítése

ProSort rendezésre használni a kötegelt tranzakciókat a data egy segédprogram.

**ProSort telepítése**

1. Győződjön meg arról, hogy a kötegelt telepítés sikeres volt, és ellenőrizze, hogy a **prosort bin-prosort\_2sp3-linux64-2123-opt.tar.gz** installer fájl jelen.

2. A telepítő a Tulajdonságok fájllal hajtható végre. A parancssorba írja be a következőt:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Helyezze át a prosort könyvtár az otthoni régiója. A parancssorba írja be a következőt:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Létrehoz egy licencet, és másolja a licencfájl van. Példa:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Nyissa meg a bash.profile vi (`vi .bash_profile`), és frissítse a következőképpen:

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

6. Hajtsa végre a bash profil parancsot a parancssorba írja be: ` . .bash_profile`

7. A konfigurációs fájl létrehozása. Példa:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Hozzon létre a szimbolikus hivatkozást. Példa:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. ProSort telepítésének ellenőrzéséhez futtassa a `prosort -h` parancsot. Példa:

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

## <a name="install-ofcobol"></a>OFCOBOL telepítése

OFCOBOL, amely értelmezi a nagyszámítógépes COBOL programok OpenFrame nyelvű fordítón belülre. 

**OFCOBOL telepítése**

1. Győződjön meg arról, hogy a Batch/Online telepítése sikeres volt, akkor ellenőrizze, hogy a OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin installer fájl jelen.

2. Hajtsa végre a OFCOBOL telepítő parancsot a parancssorba írja be:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Olvassa el a licencszerződést, és nyomja le az Enter billentyűt.

4. Fogadja el a licencszerződést. A telepítés befejeződése után a következő jelenik meg:

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

5. Nyissa meg a bash-profil vi (`vi .bash_profile`), és ellenőrizze, hogy frissül a OFCOBOL változók.
6. Hajtsa végre a bash-profilhoz. A parancssorba írja be a következőt:

     ```
      source ~/.bash_profile
     ```

7. A telepített mappába másolja a OFCOBOL licenc. Példa:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Nyissa meg a OpenFrame tjclrun.conf konfigurációs fájlt, és nyissa meg a vi. Példa:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   A módosítás SYSLIB szakaszt a következő:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   A módosítás után a következő a SYSLIB szakaszban:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Tekintse át a OpenFrame\_COBOL\_InstallLog.log vi fájlt, és győződjön meg arról, hogy nincsenek-e hibák. Példa:
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
10. Használja a `ofcob --version ` parancsot, és tekintse át a verziószámot a telepítés ellenőrzése. Példa:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Indítsa újra a OpenFrame használatával a `tmdown/tmboot` parancsot.

## <a name="install-ofasm"></a>OFASM telepítése

OFASM, amely értelmezi a nagyszámítógépes assembler programok OpenFrame nyelvű fordítón belülre.

**OFASM telepítése**

1. Győződjön meg arról, hogy a Batch/Online telepítése sikeres volt, akkor ellenőrizze, hogy a OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin installer fájl jelen.

2. Hajtsa végre a telepítőt. Példa:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Olvassa el a licencszerződést, és nyomja le az Enter billentyűt.
4. Fogadja el a licencszerződést.
5. Ellenőrizze, hogy a bash-profilhoz OFASM változók frissül. Példa:

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

6. Nyissa meg a OpenFrame tjclrun.conf konfigurációs fájl a vi, és módosítsa az alábbiak szerint:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Íme a [SYSLIB] szakaszban *előtt* módosítása:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Íme a [SYSLIB] szakaszban *után* módosítása:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Nyissa meg a OpenFrame\_ASM\_InstallLog.log vi fájlt, és győződjön meg arról, hogy nincsenek-e hibák. Példa:

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

8. Indítsa újra a OpenFrame alapján a következő parancsok egyikét:

     ```
     tmdown / tmboot
     ```

     – vagy –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC telepítése

OSC a OpenFrame környezet, IBM, amely támogatja a nagy sebességű OLTP-tranzakciók és egyéb felügyeleti funkciókkal CICS hasonló.

**OSC telepítése**

1. Győződjön meg arról, hogy az alapul szolgáló telepítés sikeres volt, akkor ellenőrizze, hogy a OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64. bin installer-fájl és a osc.properties konfigurációs fájl jelen.
2. Szerkessze a osc.properties fájlban a következő paraméterekkel:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Hajtsa végre a telepítő használatával a tulajdonságok fájl látható módon:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Amikor végzett, a "Telepítés befejeződött" üzenet jelenik meg.

4. Győződjön meg arról, hogy a bash-profilhoz OSC változók frissül.
5. Tekintse át a OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log fájlt. A következőhöz hasonló eredményt kell kapnia:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Nyissa meg a ofsys.seq konfigurációs fájl a vi használatával. Példa:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Az a \#alap és \#BATCH-szakaszok, szerkesztheti a paraméterek látható módon.

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

9. Elindításához, és állítsa le a OSC, inicializálni a CICS régió, a megosztott memória beírásával `osctdlinit OSCOIVP1` parancsot a parancssorba.

10. Futtatás `oscboot` OSC indításához. Kimenet az alábbihoz hasonlóan jelenik meg:

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

11. Győződjön meg arról, hogy készen áll-e a folyamat állapotát, használja a `tmadmin` si parancsot. Összes folyamat kell megjelenítenie a RDY a **állapot** oszlop.

    ![Folyamatok RDY megjelenítése](media/tmadmin-02.png)

12. OSC állítsa le, használja a `oscdown` parancsot.

## <a name="install-jeus"></a>JEUS telepítése

JEUS (Java vállalati felhasználó megoldás), a OpenFrame webalkalmazás-kiszolgáló a bemutató réteget biztosít.

Mielőtt telepítené a JEUS, telepítse az Apache Ant csomagot, amely biztosítja a kódtárakat és parancssori eszközök JEUS telepítéséhez szükséges.

**Az Apache Ant telepítése**

1. Letöltési Ant bináris használatával a `wget` parancsot. Példa:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Használja a `tar` segédprogramot, bontsa ki a bináris fájlt, és helyezze át a megfelelő helyére. Példa:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. A hatékonyság érdekében hozzon létre szimbolikus csatolást:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Nyissa meg a bash-profil vi (`vi .bash_profile`), és frissítse a következő változókkal:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  A alkalmazni a módosított környezeti változót. Példa:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS telepítése**

1. A telepítő használatával bontsa ki a `tar` segédprogramot. Példa:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Hozzon létre egy **jeus** mappa (`mkdir jeus7`) és a bináris fájlt csomagolja ki.
3. Módosítsa a **telepítő** könyvtár (vagy a saját környezetének JEUS paraméter használata). Példa:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Hajtsa végre `ant clean-all` a build végrehajtása előtt. Kimenet az alábbihoz hasonlóan jelenik meg:

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

5.  Készítsen biztonsági másolatot a tartomány-config-template.properties fájlt. Példa:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Nyissa meg a tartomány-config-template.properties fájl a vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Változás `jeus.password=jeusadmin nodename=Tmaxsoft` , `jeus.password=tmax1234 nodename=ofdemo`

8. Hajtsa végre a `ant install` parancsot JEUS felépítéséhez.
9.  Frissítse a .bash\_profilfájl látható módon a JEUS változókkal:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Hajtsa végre a bash-profilhoz. Példa:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Választható*. Hozzon létre egyszerűen, leállás és rendszerindító JEUS összetevők alias:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Ellenőrizze a telepítés, indítsa el a tartományi felügyeleti kiszolgáló látható módon:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Ellenőrizze a webalkalmazás bejelentkezési a szintaxis használatával:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Ha például <http://192.168.92.133:9736/webadmin/login.> a bejelentkezési képernyőn jelenik meg:
    
     ![JEUS WebAdmin bejelentkezési képernyő](media/jeus-01.png)

     > [!NOTE]
     > Problémákat tapasztal a port-biztonságát, nyissa meg a portot 9736 vagy a tűzfal letiltása (`systemctl stop firewall`).

14. Az állomásnév a kiszolgáló1 módosításához kattintson **zárolása sz & Erkessze**, majd kattintson a **kiszolgáló1**. A kiszolgáló ablakban módosítsa az állomásnevet az alábbiak szerint:

    1.  Változás **csomópontnév** való **ofdemo**.
    2.  Kattintson a **OK** az ablak jobb oldalán.
    3.  Kattintson a **módosítások alkalmazása** a bal alsó és leírást az ablak, írja be az *eszköznév módosítása*.

    ![JEUS WebAdmin screen](media/jeus-02.png)

15. Ellenőrizze, hogy a konfiguráció sikeres, a megerősítő képernyőn.

    ![jeus_domain kiszolgáló képernyő](media/jeus-03.png)

16. Indítsa el a felügyelt kiszolgáló folyamat "kiszolgáló1" a következő paranccsal:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW telepítése

OFGW a OpenFrame átjáró, amely támogatja a 3270 terminálemulátorral és OSI alap közötti kommunikációt, és a munkamenetek között a terminálemulátorral és OSI kezeli.

**OFGW telepítése**

1. Győződjön meg arról, hogy JEUS telepítése sikeres volt, akkor ellenőrizze, hogy a OFGW7\_0\_1\_Generic.bin installer fájl jelen.
2. Hajtsa végre a telepítőt. Példa:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. A következő helyeken használja a megfelelő utasításokat:
     -   JEUS kezdőkönyvtára
     -   JEUS Domain Name
     -   JEUS Server Name
     -   Tibero illesztőprogram
     -   Csomópont-azonosító Tmax ofdemo

4. Fogadja el a többi alapértelmezett beállítást, majd nyomja le az Enter billentyűt, hogy a telepítőből való kilépéshez.

5. Győződjön meg arról, hogy a várt módon működik-e OFGW URL-címe:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     A következő képernyő jelenik meg:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager telepítése

OFManager biztosít műveletet és felügyeleti funkciók OpenFrame a webes környezetben.

**OFManager telepítése**

1. Ellenőrizze, hogy a OFManager7\_Generic.bin installer fájl jelen.
2. Hajtsa végre a telepítőt. Példa:

     ```
     OFManager7_Generic.bin
     ```

3.  Nyomja le az Enter billentyűt a folytatáshoz, majd fogadja el a licencszerződést.
4.  Válassza ki a telepítési mappát.
5.  Fogadja el az alapértelmezett beállításokat.
6.  Válassza ki a Tibero-adatbázisként.
7.  Nyomja le az Enter billentyűt, hogy a telepítőből való kilépéshez.
8.  Győződjön meg arról, hogy a várt módon működik-e OFManager URL-címe:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

A kezdőképernyő jelenik meg:

![Tmax OpenFrame Manager bejelentkezési képernyő](media/ofmanager-01.png)

Ezzel befejezte a OpenFrame összetevők telepítése.

## <a name="next-steps"></a>További lépések

A nagyszámítógépes migráció használatát fontolgatja, ha segítséget nyújtanak a növekvő partneri ökoszisztémánkban érhető el. Egy partneri megoldás kiválasztásával kapcsolatban részletes útmutatásért tekintse meg a [Platform korszerűsítése Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Bevezetés az Azure használatába](https://docs.microsoft.com/azure/)
-   [Host Integration Server (HIS) dokumentációja](https://docs.microsoft.com/host-integration-server/)
-   [Az Azure virtuális adatközpont Lift-and-Shift-útmutató](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
