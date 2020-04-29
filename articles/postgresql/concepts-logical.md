---
title: Logikai dekódolás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Leírja a logikai dekódolást és az adatváltozások rögzítésének wal2json Azure Database for PostgreSQL – egyetlen kiszolgálón
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522146"
---
# <a name="logical-decoding"></a>Logikai dekódolás
 
[A PostgreSQL-ben a logikai dekódolás](https://www.postgresql.org/docs/current/logicaldecoding.html) lehetővé teszi, hogy az adatváltozásokat továbbítsa a külső felhasználók számára. A logikai dekódolást az esemény-adatfolyamok és az adatváltozás-rögzítési forgatókönyvek esetében népszerűen használják.

A logikai dekódolás egy kimeneti beépülő modullal alakítja át a postgres írási előtt álló naplóját (WAL) olvasható formátumba. Azure Database for PostgreSQL két kimeneti beépülő modult biztosít: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) és [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> A logikai dekódolás nyilvános előzetes verzióban érhető el Azure Database for PostgreSQL – egyetlen kiszolgálón.


## <a name="set-up-your-server"></a>A kiszolgáló beállítása
A logikai dekódolás használatának megkezdéséhez engedélyezze a kiszolgálónak a WAL mentését és továbbítását. 

1. Az Azure. replication_support beállítása `logical` az Azure CLI használatára. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Ha olvasási replikákat használ, az Azure. replication_support úgy van `logical` beállítva, hogy a replikákat is futtasson. Ha leállítja a logikai dekódolást, állítsa vissza a beállítást `replica`a következőre:. 


2. A módosítások alkalmazásához indítsa újra a kiszolgálót.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Logikai dekódolás elindítása

A logikai dekódolás a Streaming Protocol vagy az SQL Interface használatával is felhasználható. Mindkét módszer [replikációs tárolóhelyeket](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)használ. A tárolóhelyek egyetlen adatbázis változásainak egy streamjét jelölik.

A replikációs tárolóhelyek használatához a postgres replikálási jogosultságai szükségesek. Jelenleg a replikációs jogosultság csak a kiszolgáló rendszergazda felhasználója számára érhető el. 

### <a name="streaming-protocol"></a>Streaming Protocol
A változások a Streaming Protocol használatával való felhasználása gyakran előnyösebb. Létrehozhat saját fogyasztót vagy összekötőt, vagy használhat olyan eszközt, mint például a [Debezium](https://debezium.io/). 

Tekintse meg a wal2json dokumentációját, amely [a streaming protocol pg_recvlogical használatával történő használatát szemlélteti](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-felület
Az alábbi példában az SQL-felületet használjuk a wal2json beépülő modullal.
 
1. Hozzon létre egy tárolóhelyet.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-parancsok kiadása. Például:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Használja fel a módosításokat.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   A kimenet a következőképpen fog kinézni:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Ha elkészült, dobja el a tárolóhelyet.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Figyelési tárolóhelyek

Figyelnie kell a logikai dekódolást. A fel nem használt replikációs tárolóhelyeket el kell dobni. A bővítőhelyek megtartják, hogy postgres a WAL-naplókat és a kapcsolódó rendszerkatalógusokat, amíg a felhasználó nem olvasta be a módosításokat. Ha a fogyasztó meghibásodik, vagy nincs megfelelően konfigurálva, a nem felhasználható naplók felhalmoznak és kitöltik a tárhelyet. A nem felhasználatlan naplók is növelhetik a tranzakció-azonosító wraparound kockázatát. Mindkét esetben előfordulhat, hogy a kiszolgáló elérhetetlenné válik. Ezért fontos, hogy a logikai replikációs tárolóhelyek folyamatosan legyenek felhasználva. Ha már nincs használatban logikai replikációs tárolóhely, azonnal dobja el.

A pg_replication_slots nézet "aktív" oszlopa jelzi, hogy van-e egy fogyasztó csatlakoztatva egy tárolóhelyhez.
```SQL
SELECT * FROM pg_replication_slots;
```

[Riasztások beállítása](howto-alert-on-metric.md) a *felhasznált tárterületen* és a replika-metrikák *maximális késése* , hogy értesítést kapjon, ha az értékek megnövekednek a korábbi normál küszöbértékek között. 

> [!IMPORTANT]
> A nem használt replikációs tárolóhelyeket el kell dobnia. Ha ezt elmulasztja, akkor a kiszolgáló nem lesz elérhető.

## <a name="how-to-drop-a-slot"></a>Tárolóhely eldobása
Ha nem aktívan használ egy replikációs tárolóhelyet, dobja el.

Az SQL használatával meghívott `test_slot` replikációs tárolóhely eldobása:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Ha leállítja a logikai dekódolást, módosítsa az Azure-t `replica` . `off`replication_support vissza a következőre: vagy. A megőrzött `logical` Wal-részletek részletesebbek, és le kell tiltani, ha nincs használatban a logikai dekódolás. 

 
## <a name="next-steps"></a>További lépések

* A [logikai dekódolással kapcsolatos további információkért](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)látogasson el a postgres dokumentációba.
* Ha a logikai dekódolással kapcsolatban kérdése van, akkor elérheti a [csapatot](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) .
* További információ az [olvasási replikáról](concepts-read-replicas.md).

