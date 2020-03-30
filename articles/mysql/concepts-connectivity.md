---
title: Átmeneti kapcsolódási hibák – Azure Database for MySQL
description: Ismerje meg, hogyan kezelhetők az átmeneti kapcsolódási hibák, és hogyan csatlakozhat hatékonyan az Azure Database for MySQL-hez.
keywords: mysql kapcsolat,kapcsolati karakterlánc,kapcsolódási problémák,átmeneti hiba,csatlakozási hiba,csatlakozás hatékony
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c5c7e485cc9cb03757b8a981cef92d79b81c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537176"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Átmeneti hibák kezelése és hatékony csatlakozás a MySQL-hez készült Azure Database-hez

Ez a cikk ismerteti, hogyan oldhatja meg az átmeneti hibákat, és hatékonyan csatlakozhat az Azure Database for MySQL-hez.

## <a name="transient-errors"></a>Átmeneti hibák

Egy átmeneti hiba, más néven átmeneti hiba, egy hiba, amely megoldja magát. Ezek a hibák általában az adatbázis-kiszolgálóval való kapcsolatként jelennek meg. A kiszolgálóval létesített új kapcsolatok sem nyithatók meg. Átmeneti hibák fordulhatnak elő például hardver- vagy hálózati hiba esetén. Egy másik ok lehet egy új verziója a PaaS szolgáltatás, amely folyamatban van kivezetve. A legtöbb ilyen eseményt a rendszer kevesebb mint 60 másodperc alatt automatikusan enyhíti. A felhőben lévő alkalmazások tervezésére és fejlesztésére vonatkozó ajánlott eljárás az, ha átmeneti hibákra számíthat. Tegyük fel, hogy bármikor bármely összetevőben előfordulhatnak, és hogy megfelelő logikával rendelkeznek az ilyen helyzetek kezeléséhez.

## <a name="handling-transient-errors"></a>Átmeneti hibák kezelése

Az átmeneti hibákat újrapróbálkozási logikával kell kezelni. Figyelembe veendő helyzetek:

* Hiba történik a kapcsolat megnyitásakor
* A kiszolgáló oldalon megszakad az az idlés kapcsolat. Amikor megpróbál kiadni egy parancsot, az nem hajtható végre
* A rendszer elejt egy aktív kapcsolatot, amely jelenleg egy parancsot hajt végre.

Az első és a második esetben meglehetősen egyenesen kezelni. Próbálja meg újra megnyitni a kapcsolatot. Ha sikeres, a rendszer enyhítette az átmeneti hibát. Az Azure Database for MySQL ismét használhatja. Javasoljuk, hogy várjon, mielőtt újra próbálkozna a kapcsolattal. Visszalépés, ha a kezdeti újrapróbálkozások sikertelenek lesznek. Így a rendszer minden rendelkezésre álló erőforrást felhasználhat a hibahelyzet leküzdésére. Egy jó minta, hogy kövesse a következő:

* Várjon 5 másodpercet az első újrapróbálkozás előtt.
* Minden következő újrapróbálkozás esetén a várakozás exponenciálisan, legfeljebb 60 másodpercig növekszik.
* Állítsa be az újrapróbálkozások maximális számát, amelynek során az alkalmazás a művelet sikertelennek tekinti.

Ha egy aktív tranzakcióval való kapcsolat sikertelen, nehezebb a helyreállítás megfelelő kezelése. Két eset van: Ha a tranzakció csak olvasható jellegű volt, biztonságos újra megnyitni a kapcsolatot, és újra megpróbálni a tranzakciót. Ha azonban a tranzakció is írt az adatbázisba, meg kell állapítania, hogy a tranzakció vissza lett-e állítva, vagy sikeres volt-e az átmeneti hiba bekövetkezése előtt. Ebben az esetben előfordulhat, hogy egyszerűen nem kapta meg a véglegesítési nyugtát az adatbázis-kiszolgálótól.

Ennek egyik módja az, hogy létrehoz egy egyedi azonosítót az ügyfélen, amely az összes újrapróbálkozáshoz használatos. Ezt az egyedi azonosítót a tranzakció részeként adja át a kiszolgálónak, és egy egyedi megkötéssel rendelkező oszlopban tárolja. Így biztonságosan újra próbálkozhat a tranzakcióval. Sikeres lesz, ha az előző tranzakció vissza lett állítva, és az ügyfél által létrehozott egyedi azonosító még nem létezik a rendszerben. Sikertelen lesz a duplikált kulcs megsértése, ha az egyedi azonosítót korábban tárolták, mert az előző tranzakció sikeresen befejeződött.

Amikor a program harmadik féltől származó köztes szoftveren keresztül kommunikál az Azure Database for MySQL szolgáltatással, kérdezze meg a szállítótól, hogy a köztes szoftver tartalmaz-e átmeneti hibák újrapróbálkozási logikáját.

Győződjön meg róla, hogy tesztelje a logika újrapróbálkozását. Például próbálja meg végrehajtani a kódot, miközben az Azure Database for MySQL-kiszolgáló számítási erőforrásainak fel- és leskálázása közben. Az alkalmazásnak gond nélkül kezelnie kell a művelet során észlelt rövid állásidőt.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Csatlakozás hatékonyan a MySQL Azure Database-hez

Az adatbázis-kapcsolatok korlátozott erőforrást jelentenek, így a mySQL-hez való hozzáféréshez a kapcsolatkészletezés hatékony használata optimalizálja a teljesítményt. Az alábbi szakasz bemutatja, hogyan használhatja a kapcsolatkészletezésvagy az állandó kapcsolatok hatékonyabb eléréséhez az Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Adatbázisok elérése kapcsolatkészletezés használatával (ajánlott)

Az adatbázis-kapcsolatok kezelése jelentős hatással lehet az alkalmazás egészének teljesítményére. Az alkalmazás teljesítményének optimalizálása érdekében a cél a kapcsolatok létrehozásának és a kulcskódelérési utak kapcsolatok létrehozásának ideje csökkentésének csökkentése kell, hogy legyen. Erősen javasoljuk, hogy adatbázis-kapcsolat készletezése vagy állandó kapcsolatok segítségével csatlakozzon az Azure Database for MySQL. Az adatbázis-kapcsolatkészletezés kezeli az adatbázis-kapcsolatok létrehozását, kezelését és elosztását. Amikor egy program adatbázis-kapcsolatot kér, az új kapcsolat létrehozása helyett a meglévő tétlen adatbázis-kapcsolatok lefoglalását helyezi előtérbe. Miután a program befejezte az adatbázis-kapcsolat használatát, a kapcsolat helyreáll a további használatra való felkészülés során, nem pedig egyszerűen bezárva.

A jobb illusztráció érdekében ez a cikk [egy mintakódot](./sample-scripts-java-connection-pooling.md) tartalmaz, amely példaként a JAVA-t használja. További információ: [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> A kiszolgáló időtúllépési mechanizmust konfigurál az erőforrások felszabadításához egy ideje tétlen állapotban lévő kapcsolat bezárásához. Ügyeljen arra, hogy hozzanak létre az ellenőrzési rendszer hatékonyságának biztosítása érdekében a tartós kapcsolatok, amikor használja őket. További információ: [Verification systems on the client side to ensure the effectiveness of persistent connections](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Adatbázisok elérése állandó kapcsolatok használatával (ajánlott)

Az állandó kapcsolatok fogalma hasonló a kapcsolatkészletezéséhez. A rövid kapcsolatok állandó kapcsolatokkal való lecserélése csak a kód kisebb módosításait igényli, de számos tipikus alkalmazási forgatókönyvben jelentős hatással van a teljesítmény javítására.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Adatbázisok elérése rövid kapcsolattal rendelkező várakozási és újrapróbálkozási mechanizmus sal

Ha erőforrás-korlátozásokkal rendelkezik, javasoljuk, hogy adatbázis-készletezést vagy állandó kapcsolatokat használjon az adatbázisok eléréséhez. Ha az alkalmazás rövid kapcsolatokat használ, és csatlakozási hibákat tapasztal, amikor megközelíti az egyidejű kapcsolatok számának felső határát, megpróbálhatja a várakozást, és újrapróbálkozhat a mechanizmussal. Beállíthatja a megfelelő várakozási időt, rövidebb várakozási idővel az első kísérlet után. Ezt követően többször is megvárhatja az eseményeket.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Ellenőrzési mechanizmusok konfigurálása az ügyfeleknél az állandó kapcsolatok hatékonyságának megerősítésére

A kiszolgáló időtúllépési mechanizmust konfigurál az erőforrások felszabadításához egy ideje tétlen állapotban lévő kapcsolat bezárásához. Amikor az ügyfél ismét hozzáfér az adatbázishoz, az egyenértékű az ügyfél és a kiszolgáló közötti új kapcsolatkérés létrehozásával. A kapcsolatok hatékonyságának biztosítása érdekében a használatuk során konfiguráljon egy ellenőrzési mechanizmust az ügyfélen. Ahogy az alábbi példában is látható, a Tomcat JDBC kapcsolatkészletezés segítségével konfigurálhatja ezt az ellenőrzési mechanizmust.

A TestOnBorrow paraméter beállításával, amikor új kérelem van, a kapcsolatkészlet automatikusan ellenőrzi a rendelkezésre álló üresjárati kapcsolatok hatékonyságát. Ha egy ilyen kapcsolat hatékony, a közvetlenül visszaadott egyébként kapcsolatkészlet visszavonja a kapcsolatot. A kapcsolatkészlet ezután létrehoz egy új hatékony kapcsolatot, és visszaadja azt. Ez a folyamat biztosítja az adatbázis hatékony elérését. 

A konkrét beállításokkal kapcsolatos információkért tekintse meg a [JDBC-kapcsolatkészlet hivatalos bevezető dokumentumát.](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes) Elsősorban a következő három paramétert kell beállítania: TestOnBorrow (igaz értékre), ValidationQuery (SELECT 1 értékre), és ValidationQueryTimeout (1-re). A konkrét mintakód az alábbiakban látható:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>További lépések

* [Az Azure Database for MySQL csatlakoztatási hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
