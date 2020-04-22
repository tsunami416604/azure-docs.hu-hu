---
title: A PostgreSQL beállítása Linux os virtuális gépen
description: A PostgreSQL telepítése és konfigurálása Linux os virtuális gépen az Azure-ban
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759331"
---
# <a name="install-and-configure-postgresql-on-azure"></a>A PostgreSQL telepítése és konfigurálása az Azure-ban
A PostgreSQL az Oracle-hez és a DB2-hez hasonló, fejlett nyílt forráskódú adatbázis. Ez magában foglalja a vállalati használatra kész funkciók, mint például a teljes ACID megfelelés, megbízható tranzakciós feldolgozás, és a többverziós egyidejűség-szabályozás. Támogatja az OLYAN szabványokat is, mint az ANSI SQL és az SQL/MED (beleértve az Oracle, a MySQL, a MongoDB és még sokan mások külföldi adatburkolóit). Rendkívül bővíthető több mint 12 eljárási nyelv, GIN és GiST indexek, térbeli adattámogatás és több NoSQL-szerű funkció támogatásával a JSON vagy a kulcsérték-alapú alkalmazásokhoz.

Ebben a cikkben megtudhatja, hogyan telepítheti és konfigurálhatja a PostgreSQL-t egy Linux ot futtató Azure virtuális gépen.


## <a name="install-postgresql"></a>A PostgreSQL telepítése
> [!NOTE]
> Az oktatóanyag befejezéséhez már rendelkeznie kell linuxos Azure-alapú virtuális géppel. Linuxos virtuális gép létrehozásához és beállításához a folytatás előtt tekintse meg az [Azure Linux virtuális gép oktatóanyagát.](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

Ebben az esetben használja az 1999-es portot PostgreSQL portként.  

Csatlakozzon a PuTTY-n keresztül létrehozott Linux virtuális géphez. Ha ez az első alkalom, hogy egy Azure Linux virtuális gép használata, olvassa el az [SSH használata az Azure-ban,](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hogy megtudja, hogyan használhatja a PuTTY linuxos virtuális géphez való csatlakozást.

1. Futtassa a következő parancsot a gyökérre való váltáshoz (admin):
   
        # sudo su -
2. Egyes disztribúciók függőségekkel rendelkeznek, amelyeket telepítenie kell a PostgreSQL telepítése előtt. Ellenőrizze a kivezető tarolebben a listában, és futtassa a megfelelő parancsot:
   
   * Red Hat alap Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian alap Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Töltse le a PostgreSQL-t a gyökérkönyvtárba, majd csomagolja ki a csomagot:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    A fenti egy példa. A részletesebb letöltési címet a [/pub/source/ indexben](https://ftp.postgresql.org/pub/source/)találja.
4. A build elindításához futtassa az alábbi parancsokat:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Ha mindent fel szeretne építeni, beleértve a dokumentációt (HTML és man`contrib`oldalak) és a további modulokat ( ), futtassa inkább a következő parancsot:
   
        # gmake install-world
   
    A következő megerősítő üzenetet kell kapnia:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>A PostgreSQL konfigurálása
1. (Nem kötelező) Hozzon létre egy szimbolikus linket, hogy lerövidíti a PostgreSQL hivatkozásnem tartalmazza a verziószámot:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Könyvtár létrehozása az adatbázishoz:
   
        # mkdir -p /opt/pgsql_data
3. Hozzon létre egy nem legfelső szintű felhasználót, és módosítsa a felhasználó profilját. Ezután váltson erre az új felhasználóra (a példánkban *postgres-nek* nevezi):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Biztonsági okokból a PostgreSQL nem root felhasználót használ az adatbázis inicializálására, indítására vagy leállítására.
   > 
   > 
4. Az alábbi parancsok beírásával szerkeszti a *bash_profile* fájlt. Ezek a sorok a *bash_profile* fájl végére kerülnek:
   
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
6. A telepítés ellenőrzése a következő paranccsal:
   
        $ which psql
   
    Ha a telepítés sikeres, a következő válasz jelenik meg:
   
        /opt/pgsql/bin/psql
7. A PostgreSQL verziót is ellenőrizheti:
   
        $ psql -V

8. Az adatbázis inicializálása:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    A következő kimenetet kell kapnia:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>A PostgreSQL beállítása
<!--    [postgres@ test ~]$ exit -->

Futtassa az alábbi parancsot:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Módosítsa az /etc/init.d/postgresql fájl két változóját. Az előtag a PostgreSQL telepítési útvonalára van állítva: **/opt/pgsql**. A PGDATA a PostgreSQL adattárolási útvonalára van beállítva: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Módosítsa a fájlt, hogy végrehajtható legyen:

    # chmod +x /etc/init.d/postgresql

Indítsa el a PostgreSQL-t:

    # /etc/init.d/postgresql start

Ellenőrizze, hogy a PostgreSQL végpontja be van-e kapcsolva:

    # netstat -tunlp|grep 1999

A következő kimenetnek kell megjelennie:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Csatlakozás a Postgres adatbázishoz
Váltson ismét a postgres felhasználóra:

    # su - postgres

Postgres-adatbázis létrehozása:

    $ createdb events

Csatlakozás az imént létrehozott eseményadatbázishoz:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Postgres-tábla létrehozása és törlése
Most, hogy csatlakozott az adatbázishoz, táblákat hozhat létre benne.

Hozzon létre például egy új postgres-példa táblát a következő paranccsal:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Most egy négyoszlopos táblát állított be a következő oszlopnevekkel és korlátozásokkal:

1. A VARCHAR parancs a "név" oszlopot 20 karakternél hosszabbra korlátozta.
2. Az "élelmiszer" oszlop jelzi az élelmiszer-tétel, hogy minden egyes személy hozza. A VARCHAR korlátozza, hogy ez a szöveg 30 karakternél hosszabb legyen.
3. A "megerősített" oszlop rögzíti, hogy a személy rsvp'd a potluck. Az elfogadható értékek az "Y" és az "N".
4. A "dátum" oszlopban látható, hogy mikor regisztráltak az eseményre. Postgres megköveteli, hogy a dátumokat kell írni, mint yyyy-mm-dd.

Ha a tábla sikeresen létrejött, a következőket kell látnia:

![image](./media/postgresql-install/no4.png)

A táblaszerkezetet a következő paranccsal is ellenőrizheti:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adatok hozzáadása táblához
Először szúrjon be információt egy sorba:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

A következő kimenet jelenik meg:

![image](./media/postgresql-install/no6.png)

Az asztalhoz még több embert is hozzáadhat. Íme néhány lehetőség, vagy létrehozhat sajátot:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Táblázatok megjelenítése
Táblázat megjelenítéséhez használja a következő parancsot:

    select * from potluck;

A kimenet a következő:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Tábla adatainak törlése
A táblázatban lévő adatok törléséhez használja a következő parancsot:

    delete from potluck where name=’John’;

Ezzel törli a "János" sorban lévő összes információt. A kimenet a következő:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Tábla adatainak frissítése
A táblázat ban lévő adatok frissítéséhez használja a következő parancsot. Ehhez Sandy megerősítette, hogy részt vesznek, ezért az RSVP-t "N"-ről "Y"-ra változtatjuk:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>További információ a PostgreSQL-ről
Most, hogy befejezte a PostgreSQL telepítését egy Azure Linux os virtuális gépben, élvezheti az Azure-ban való használatát. Ha többet szeretne megtudni a PostgreSQL-ről, látogasson el a [PostgreSQL weboldalára.](https://www.postgresql.org/)

