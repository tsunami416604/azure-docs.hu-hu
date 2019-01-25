---
title: Állítsa be a Linux rendszerű virtuális gép PostgreSQL |} A Microsoft Docs
description: Ismerje meg, hogyan PostgreSQL telepítése és konfigurálása az Azure-ban Linux rendszerű virtuális gépen
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: e851f6f5433a832aa30a0d87a917b64d1bc721f2
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888277"
---
# <a name="install-and-configure-postgresql-on-azure"></a>A PostgreSQL telepítése és konfigurálása Azure-ban
PostgreSQL az Oracle- és DB2 hasonlít egy speciális nyílt forráskódú adatbázis. Teljes ACID megfelelőségi, megbízható tranzakciós feldolgozást, és többverziós egyidejűségi vezérlésre nagyvállalati szintű funkciókat tartalmaz. Támogatja a szabványok – például az ANSI SQL és az SQL/MED (beleértve a külső adatok burkolókat az Oracle, MySQL, mongodb-hez és sok más) is. Fontos a nagy mértékben bővíthetők, a JSON és a kulcs-érték-alapú alkalmazások több mint 12 eljárási nyelvet, a GIN és GiST indexek, a térbeli adatok támogatása és a több NoSQL-hez hasonló funkciók támogatása.

Ebből a cikkből megismerheti, hogyan a PostgreSQL telepítése és konfigurálása a Linux operációs rendszert futtató Azure virtuális gép lesz.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Install PostgreSQL
> [!NOTE]
> Ez az oktatóanyag elvégzéséhez a Linux operációs rendszert futtató Azure virtuális gép már rendelkeznie kell. Hozhat létre, és állítsa be a Linux rendszerű virtuális gép a folytatás előtt, tekintse meg a [Azure Linux VM-oktatóanyag](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Ebben az esetben 1999-as port használható a PostgreSQL-port.  

Csatlakozzon a PuTTY használatával létrehozott virtuális gép Linux. Ha első alkalommal használja az Azure Linux VM, lásd: [hogyan használja SSH használata Linuxon az Azure-ban](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) megtudhatja, hogyan használható a putty-t egy Linux rendszerű virtuális Géphez való csatlakozáshoz.

1. Futtassa a következő parancsot a legfelső szintű (rendszergazdai) váltani:
   
        # sudo su -
2. Néhány disztribúciók függőségeit, amelyek a PostgreSQL telepítése előtt telepítenie kell rendelkeznie. Ellenőrizze a disztribúció ebben a listában, és futtassa a megfelelő parancsot:
   
   * Red Hat base Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian alapszintű Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Töltse le a PostgreSQL a legfelső szintű könyvtárba, és ezután bontsa ki a csomagot:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    A fent látható egy példa. A részletes letöltési címet annak a [/pub/forrás / Index](https://ftp.postgresql.org/pub/source/).
4. A build indításához futtassa a következő parancsokat:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Ha szeretne létrehozni minden, amelyek létrehozhatók, beleértve a dokumentáció (HTML- és ember lapok) és a további modulok (contrib), futtassa a következő parancsot:
   
        # gmake install-world
   
    A következő megerősítő üzenetet kell kapnia:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configure PostgreSQL
1. (Nem kötelező) Hozzon létre egy szimbolikus hivatkozást, így rövidítve a PostgreSQL hivatkozást tartalmaz a verziószám:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Hozzon létre egy könyvtárat, az adatbázis számára:
   
        # mkdir -p /opt/pgsql_data
3. Nem legfelső szintű felhasználó létrehozása és módosítása az adott felhasználó profilját. Ezután váltson az új felhasználóhoz (nevű *postgres* ebben a példában):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Biztonsági okokból PostgreSQL inicializálása, elindítani vagy leállítani az adatbázis nem legfelső szintű felhasználó használja.
   > 
   > 
4. Szerkessze a *bash_profile* fájl az alábbi parancsok beírásával. Ezek a sorok végén megjelenik a *bash_profile* fájlt:
   
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
7. A PostgreSQL-verzió is keresheti:
   
        $ psql -V

8. Az adatbázis inicializálása:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    A következő kimenetnek kell megjelennie:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Set up PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Futtassa az alábbi parancsot:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Módosítsa a /etc/init.d/postgresql fájlban két változót. Az előtag a telepítési útvonalat a PostgreSQL értékre van állítva: **/opt/pgsql**. PGDATA értéke PostgreSQL adatokat tároló elérési útja: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Győződjön meg a végrehajtható fájl módosítása:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Ellenőrizze, hogy-e a PostgreSQL-végpont a:

    # netstat -tunlp|grep 1999

A következő kimenetnek kell megjelennie:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>A Postgres-adatbázishoz
A postgres-felhasználó ismét váltani:

    # su - postgres

A Postgres-adatbázis létrehozása:

    $ createdb events

Csatlakozzon az imént létrehozott események adatbázishoz:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Hozzon létre, és a egy Postgres tábla törlése
Most, hogy csatlakozott az adatbázishoz, akkor azt táblákat hozhat létre.

Például hozzon létre egy új példa Postgres tábla a következő paranccsal:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Most már beállított egy négy táblát a következő oszlopok neveit és korlátozások:

1. A "name" oszlopban rendelkezik lett korlátozzák a VARCHAR parancsot 20 karakter hosszú lehet.
2. A "food" oszlop jelzi az élelmiszer elem, amely minden egyes személy tartalomtérkép érhető el. VARCHAR korlátozza ezt a szöveget 30 karakter lehet.
3. A "megerősítve" oszlop rögzíti-e a személyt amelyeknél reagált a meghívásra, a piknik. Az elfogadható értékek: "Y" és "N".
4. A "dátum" oszlopban látható, ha azok regisztrált esemény. Postgres megköveteli, hogy a dátumok kell megírni, éééé-hh-nn.

A következő kell megjelennie, ha a tábla sikeresen létrejött:

![image](./media/postgresql-install/no4.png)

A táblázat szerkezetét ellenőrzéséhez a következő paranccsal:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adatok hozzáadása táblázathoz
Első lépésként adatokat beszúrni egy sort:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

A kimenetnek kell megjelennie:

![image](./media/postgresql-install/no6.png)

Néhány további személyek adhat hozzá, valamint a táblában. Néhány lehetőség, vagy létrehozhat saját:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Táblák megjelenítése
A következő paranccsal egy táblát jelenítsen meg:

    select * from potluck;

A kimenet a következő:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Egy tábla adatainak törlése
A következő parancs segítségével törölheti a táblákban tárolt adatokat:

    delete from potluck where name=’John’;

Ezzel törli a "János" sort található összes információt. A kimenet a következő:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Egy tábla adatainak frissítése
A következő parancsot használja egy tábla adatainak frissítéséhez. Ez egy Sandy megerősítette, hogy ő lesz ott, így saját RSVP "N", "Y" módosítjuk:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>További információ a PostgreSQL beolvasása
Most, hogy a PostgreSQL elérhető az Azure Linux VM telepítése befejeződött, élvezheti, használja az Azure-ban. PostgreSQL kapcsolatos további információkért látogasson el a [PostgreSQL webhely](http://www.postgresql.org/).

