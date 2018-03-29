---
title: Állítsa be a Linux virtuális gép PostgreSQL |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja PostgreSQL egy Linux virtuális gép az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: iainfou
ms.openlocfilehash: 7741f861c5697da1e453c0d613b4b762511cf555
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="install-and-configure-postgresql-on-azure"></a>A PostgreSQL telepítése és konfigurálása Azure-ban
PostgreSQL szerepel egy speciális nyílt forráskódú adatbázis Oracle és DB2 hasonló. Ez magában foglalja a vállalati szolgáltatások, mint a teljes ACID megfelelőségi megbízható tranzakciós feldolgozást és több verzió egyidejűség-vezérlési. Például az ANSI SQL és az SQL/MED (beleértve a külső adatokat burkolók Oracle, MySQL, MongoDB és sok más) szabványokat is támogatja. Széles körben bővíthető támogatja a több mint 12 eljárási nyelveket, GIN és GiST indexek, térbeli adatokat, és több NoSQL-hasonló szolgáltatásokat JSON vagy kulcs-érték-alapú alkalmazások is.

Ön ebből a cikkből megtudhatja, hogyan telepítéséhez és konfigurálásához PostgreSQL Linux operációs rendszert futtató Azure virtuális géphez.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Install PostgreSQL
> [!NOTE]
> Az oktatóanyag teljesítéséhez Linux operációs rendszert futtató Azure virtuális gép már kell rendelkeznie. Hozzon létre, és a folytatás előtt Linux virtuális gépet, a [Azure Linux virtuális gép oktatóanyag](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Ebben az esetben használja a port 1999 a PostgreSQL-portjaként működik.  

A PuTTY használatával létrehozott virtuális gép Linux csatlakozni. Ha az első alkalommal szeretne megtudni az Azure Linux virtuális gép, lásd: [hogyan használja SSH Linux Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a PuTTY használata a Linux virtuális gépek csatlakozni.

1. A következő paranccsal váltson át a legfelső szintű (rendszergazda):
   
        # sudo su -
2. Néhány terjesztéseket táblává PostgreSQL telepítése előtt telepítenie kell. Keressen a distro ezen a listán, és futtassa a megfelelő parancsot:
   
   * Red Hat alap Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian alap Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Töltse le a PostgreSQL a legfelső szintű könyvtárba, és ezután bontsa ki a csomagot:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    A fent látható egy példa. A részletes letöltési címet megtalálhatja a [/pub/forrás / Index](https://ftp.postgresql.org/pub/source/).
4. A build indításához futtassa az alábbi parancsokat:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Ha szeretne létrehozni minden, ami építhetők, beleértve a dokumentáció (HTML- és man lapok) és a kiegészítő modulok (contrib), futtassa a következő parancsot:
   
        # gmake install-world
   
    A következő megerősítő üzenetet kell kapnia:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configure PostgreSQL
1. (Választható) Hozzon létre egy szimbolikus hivatkozást, hogy csökkentse a PostgreSQL hivatkozást tartalmaz a verziószám:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Hozzon létre egy könyvtárat, az adatbázis számára:
   
        # mkdir -p /opt/pgsql_data
3. Nem legfelső szintű felhasználó létrehozásához és módosításához, a felhasználó. Ezt az új felhasználót, majd átváltása (nevű *postgres* ebben a példában):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Biztonsági okokból PostgreSQL inicializálása, indítsa el, vagy állítsa le az adatbázis nem rendszergazda felhasználó használja.
   > 
   > 
4. Szerkessze a *bash_profile* fájlt az alábbi parancsok beírásával. Ezek a sorok nem kerülnek be az végén a *bash_profile* fájlt:
   
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
5. Hajtsa végre a *bash_profile* fájlt:
   
        $ source .bash_profile
6. A telepítés ellenőrzése a következő paranccsal:
   
        $ which psql
   
    Ha a telepítés sikeres, a következő válasz jelenik meg:
   
        /opt/pgsql/bin/psql
7. Ellenőrizheti a PostgreSQL-verzió:
   
        $ psql -V
8. Az adatbázis inicializálása:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    A következő kimenetet kell kapnia:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL beállítása
<!--    [postgres@ test ~]$ exit -->

Futtassa az alábbi parancsot:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

A /etc/init.d/postgresql fájlban két változók módosításához. Az előtag PostgreSQL telepítési elérési útjának beállítása: **/opt/pgsql**. Az adatok tárolási elérési útja PostgreSQL PGDATA van beállítva: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Abba, hogy a végrehajtható fájl módosítása:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Annak ellenőrzése, hogy a végpont esetén PostgreSQL meg:

    # netstat -tunlp|grep 1999

A következő kimenetet kell megjelennie:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Csatlakozás a Postgres adatbázishoz
A postgres felhasználó ismét váltani:

    # su - postgres

Postgres-adatbázis létrehozása:

    $ createdb events

Csatlakoztassa az újonnan létrehozott események adatbázishoz:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Hozzon létre és Postgres tábla törlése
Most, hogy az adatbázis csatlakozott, a táblák azt is létrehozhat.

Például hozzon létre új példa Postgres tábla a következő paranccsal:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Most már készen négy oszlop táblázatot a következő oszlopneveket és korlátozások:

1. A "name" oszlop lett korlátozott a VARCHAR paranccsal 20 karakter hosszú lehet.
2. A "étele" oszlopban látható, hogy az egyes be fogja hozni étele elemet. VARCHAR korlátozza ezt a szöveget 30 karakter lehet.
3. A "megerősített" oszlop rögzíti, hogy a személy amelyeknél reagált a meghívásra a piknik. Az elfogadható értékek: "Y", "N".
4. A "date" oszlopban látható, ha azok iratkozott fel a következő eseményhez. Postgres megköveteli, hogy a dátum, éééé-hh-nn. írható

A következő kell megjelennie, ha a tábla létrehozása sikeresen megtörtént:

![image](./media/postgresql-install/no4.png)

A táblázat szerkezetét ellenőrzéséhez a következő parancsot:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adatok hozzáadása a táblához
Információk először beszúrni egy sort:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

A kimenetnek kell megjelennie:

![image](./media/postgresql-install/no6.png)

Néhány több ember is hozzáadhat. Néhány lehetőség, vagy létrehozhat saját:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Táblák megjelenítése
A következő paranccsal egy táblázat megjelenítése:

    select * from potluck;

A kimenete:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>A tábla adatainak törlése
A következő parancs segítségével törölheti az adatok a táblázatban:

    delete from potluck where name=’John’;

Ezzel törli a "John" sor összes információt. A kimenete:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>A tábla adatainak frissítése
A következő paranccsal egy tábla adatainak frissítése. Ez egy Sandy megerősítette, hogy ő lesz ott, így azt fogja módosítani a saját RSVP "N" a "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>További információ a PostgreSQL beolvasása
Most, hogy PostgreSQL egy Azure Linux virtuális gép telepítése befejeződött, élvezheti használja azt az Azure-ban. PostgreSQL kapcsolatos további információkért látogasson el a [PostgreSQL webhely](http://www.postgresql.org/).

