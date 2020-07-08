---
title: A PostgreSQL beállítása Linux rendszerű virtuális gépen
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a PostgreSQL-t egy Linux rendszerű virtuális gépen az Azure-ban
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759331"
---
# <a name="install-and-configure-postgresql-on-azure"></a>A PostgreSQL telepítése és konfigurálása az Azure-ban
A PostgreSQL egy fejlett, nyílt forráskódú adatbázis, amely az Oracle és a DB2 számára is hasonló. Olyan nagyvállalati használatra kész funkciókat tartalmaz, mint például a teljes sav megfelelősége, a megbízható tranzakciós feldolgozás és a többverziós Egyidejűség-vezérlés. Emellett olyan szabványokat is támogat, mint például az ANSI SQL és az SQL/MED (beleértve az Oracle, a MySQL, a MongoDB és számos más adatburkolót is). A szolgáltatás nagyszámú, több mint 12 eljárási nyelv, a GIN és a lényegi indexek, a térbeli adattámogatás, valamint a JSON-vagy kulcs-érték alapú alkalmazások több NoSQL funkciójának támogatásával bővíthető.

Ebből a cikkből megtudhatja, hogyan telepítheti és konfigurálhatja a PostgreSQL-t egy Linux rendszerű Azure-beli virtuális gépen.


## <a name="install-postgresql"></a>A PostgreSQL telepítése
> [!NOTE]
> Az oktatóanyag elvégzéséhez már rendelkeznie kell Linux rendszerű Azure-beli virtuális géppel. Linux rendszerű virtuális gép létrehozásához és beállításához a továbblépés előtt tekintse meg az [Azure Linux VM oktatóanyagát](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Ebben az esetben a 1999-as portot használja PostgreSQL-portként.  

Kapcsolódjon a PuTTY használatával létrehozott linuxos virtuális géphez. Ha első alkalommal használ Azure Linux rendszerű virtuális gépet, tekintse meg az [SSH és a Linux használata az Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) -ban című témakört, amelyből megtudhatja, hogyan használhatja a PuTTY-t Linux RENDSZERű virtuális gépekhez való kapcsolódáshoz.

1. Futtassa a következő parancsot a gyökérre való váltáshoz (rendszergazda):
   
        # sudo su -
2. Egyes eloszlások olyan függőségekkel rendelkeznek, amelyeket telepítenie kell a PostgreSQL telepítése előtt. Keresse meg a disztribúciót a listában, és futtassa a megfelelő parancsot:
   
   * Red Hat Base Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian Base Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Töltse le a PostgreSQL-t a gyökérkönyvtárba, majd bontsa ki a csomagot:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    A fenti példa. A részletesebb letöltési címeket a [/pub/Source/indexében](https://ftp.postgresql.org/pub/source/)találja.
4. A Build elindításához futtassa a következő parancsokat:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Ha mindent létre szeretne készíteni, beleértve a dokumentációt (HTML-és Man-lapokat) és további modulokat is ( `contrib` ), futtassa a következő parancsot:
   
        # gmake install-world
   
    A következő megerősítő üzenetet kell kapnia:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>A PostgreSQL konfigurálása
1. Választható Hozzon létre egy szimbolikus hivatkozást, amely lerövidíti a PostgreSQL-hivatkozást, hogy ne tartalmazza a verziószámot:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Hozzon létre egy könyvtárat az adatbázishoz:
   
        # mkdir -p /opt/pgsql_data
3. Hozzon létre egy nem gyökérszintű felhasználót, és módosítsa a felhasználó profilját. Ezután váltson erre az új felhasználóra (a példában a *postgres* néven látható):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Biztonsági okokból a PostgreSQL egy nem root felhasználót használ az adatbázis inicializálására, indítására vagy leállítására.
   > 
   > 
4. Szerkessze a *bash_profile* fájlt az alábbi parancsok beírásával. Ezek a sorok a *bash_profile* fájl végéhez lesznek hozzáadva:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. A *bash_profile* fájl végrehajtása:
   
        $ source .bash_profile
6. Ellenőrizze a telepítést a következő paranccsal:
   
        $ which psql
   
    Ha a telepítés sikeres, a következő választ fogja látni:
   
        /opt/pgsql/bin/psql
7. A PostgreSQL-verziót is megtekintheti:
   
        $ psql -V

8. Az adatbázis inicializálása:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    A következő kimenetnek kell megjelennie:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>A PostgreSQL beállítása
<!--    [postgres@ test ~]$ exit -->

Futtassa az alábbi parancsot:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Módosítsa két változót a/etc/init.d/PostgreSQL fájlban. Az előtag a PostgreSQL: **/opt/pgsql**telepítési útvonalára van beállítva. A PGDATA a PostgreSQL: **/opt/pgsql_data**adattároló elérési útjára van beállítva.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Módosítsa a fájlt úgy, hogy végrehajtható legyen:

    # chmod +x /etc/init.d/postgresql

A PostgreSQL elindítása:

    # /etc/init.d/postgresql start

Ellenőrizze, hogy a PostgreSQL végpontja be van-e kapcsolva:

    # netstat -tunlp|grep 1999

A következő kimenetnek kell megjelennie:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Kapcsolódás a postgres-adatbázishoz
Váltson újra a postgres-felhasználóra:

    # su - postgres

Hozzon létre egy postgres-adatbázist:

    $ createdb events

Kapcsolódjon az imént létrehozott Events adatbázishoz:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Postgres-tábla létrehozása és törlése
Most, hogy csatlakozott az adatbázishoz, létrehozhat táblákat.

Hozzon létre például egy új példa postgres táblázatot a következő parancs használatával:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Most már beállított egy négy oszlopos táblázatot a következő oszlopnevek és korlátozásokkal:

1. A "Name" oszlopot a VARCHAR parancs csak 20 karakter hosszú lehet.
2. Az "élelmiszer" oszlop azt az élelmiszeripari tételt jelzi, amelyet az egyes személyek el fognak juttatni. VARCHAR korlátozza ezt a szöveget, hogy 30 karakternél rövidebb legyen.
3. A "megerősített" oszlop rögzíti, hogy a személynek van-e RSVP-Potluck. Az elfogadható értékek: "Y" és "N".
4. A "date" oszlop azt mutatja be, hogy mikor jelentkezett be az eseményre. A postgres használatához a dátumoknak éééé-hh-nn formátumúnak kell lenniük.

Ha sikeresen létrejött a tábla, a következőnek kell megjelennie:

![image](./media/postgresql-install/no4.png)

A tábla szerkezetét a következő parancs használatával is megtekintheti:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Az adattábla hozzáadása
Először szúrjon be adatokat egy sorba:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

A következő kimenet jelenik meg:

![image](./media/postgresql-install/no6.png)

Több személyt is hozzáadhat a táblához. Itt talál néhány lehetőséget, vagy létrehozhat saját:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Táblák megjelenítése
A következő parancs használatával jelenítheti meg a táblázatot:

    select * from potluck;

A kimenet a következő:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Táblákban lévő adattörlés
A következő paranccsal törölhet egy táblában lévő adatoszlopokat:

    delete from potluck where name=’John’;

Ezzel törli a "János" sorban lévő összes információt. A kimenet a következő:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Tábla adatfrissítése
A következő parancs használatával frissítheti a táblákban lévő adatfájlokat. A Sandy megerősítette, hogy részt vesznek, ezért az "N" értékről "Y"-re módosítjuk az RSVP-t:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>További információ a PostgreSQL-ről
Most, hogy befejezte a PostgreSQL telepítését egy Azure Linux rendszerű virtuális gépen, használhatja azt az Azure-ban. Ha többet szeretne megtudni a PostgreSQL-ről, látogasson el a [PostgreSQL webhelyére](https://www.postgresql.org/).

