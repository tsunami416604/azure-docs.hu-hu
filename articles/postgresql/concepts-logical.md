---
title: Logikai dekódolás - Azure Database for PostgreSQL - Single Server
description: A cikk logikai dekódolást és wal2jsont ír le a módosítási adatok rögzítéséhez a PostgreSQL Azure Database for PostgreSQL – Single Server szolgáltatásban
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522146"
---
# <a name="logical-decoding"></a>Logikai dekódolás
 
[A PostgreSQL logikai dekódolása](https://www.postgresql.org/docs/current/logicaldecoding.html) lehetővé teszi az adatváltozások streamelését külső fogyasztóknak. A logikai dekódolást népszerűen használják az eseménystreameléshez és az adatrögzítési forgatókönyvek módosításához.

Logikai dekódolás használ kimeneti plugin átalakítani Postgres írási előre log (WAL) egy olvasható formátumban. Az Azure Database for PostgreSQL két kimeneti beépülő modult biztosít: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) és [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> A logikai dekódolás nyilvános előzetes verzióban érhető el az Azure Database for PostgreSQL – Single Server rendszeren.


## <a name="set-up-your-server"></a>A kiszolgáló beállítása
A logikai dekódolás használatának megkezdéséhez engedélyezze, hogy a kiszolgáló mentse és streamelje a WAL-t. 

1. Állítsa az azure.replication_support az Azure CLI `logical` használatára. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Ha olvasási replikákat használ, `logical` az azure.replication_support készlete lehetővé teszi a replikák futtatását is. Ha nem használja a logikai dekódolást, módosítsa a beállítást vissza a ikonra. `replica` 


2. A módosítások alkalmazásához indítsa újra a kiszolgálót.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Logikai dekódolás indítása

A logikai dekódolás streamelési protokollon vagy SQL-felületen keresztül használható fel. Mindkét módszer [replikációs bővítőhelyeket](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)használ. A tárolóhely egyetlen adatbázis módosításainak adatfolyamát jelöli.

A replikációs bővítőhely használatához a Postgres replikációs jogosultságai szükségesek. Jelenleg a replikációs jogosultság csak a kiszolgáló rendszergazdai felhasználója számára érhető el. 

### <a name="streaming-protocol"></a>Streamelési protokoll
A streamelési protokoll használatával gyakran előnyösebb a módosítások használata. Létrehozhatja saját fogyasztói / csatlakozóját, vagy használhat olyan eszközt, mint [a Debezium](https://debezium.io/). 

Látogasson el a wal2json [dokumentációegy példa segítségével a streaming protokoll pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-felület
Az alábbi példában az SQL felületet használjuk a wal2json beépülő modullal.
 
1. Hozzon létre egy bővítőhelyet.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-parancsok kiadása. Példa:
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

   A kimenet így fog kinézni:
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

4. Dobja el a nyílást, ha már befejezte a használatát.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Felügyeleti bővítőhelyek

Figyelnie kell a logikai dekódolást. A nem használt replikációs helyeket el kell dobni. A nyerőgépek mindaddig megtartják a Postgres WAL naplókat és a vonatkozó rendszerkatalógusokat, amíg a módosításokat el nem olvassa a fogyasztó. Ha a fogyasztó meghibásodik, vagy nincs megfelelően konfigurálva, a fel nem tárolt naplók felhalmozódnak, és töltse ki a tárolót. Emellett a fel nem tárolt naplók növelik a tranzakcióazonosító-körbefuttatás kockázatát. Mindkét helyzet miatt a kiszolgáló elérhetetlenné válhat. Ezért fontos, hogy a logikai replikációs tárolóhelyek folyamatosan felhasználásra kerüljenek. Ha a logikai replikációs tárolóhelyet már nem használja, azonnal dobja el.

A pg_replication_slots nézet "aktív" oszlopa jelzi, hogy van-e fogyasztó csatlakozik-e egy tárolóhelyhez.
```SQL
SELECT * FROM pg_replication_slots;
```

[Riasztások beállítása](howto-alert-on-metric.md) a *használt storage-ban,* és a replikák metriák maximális *késése,* hogy értesítse, ha az értékek a normál küszöbértékek túlnőnek. 

> [!IMPORTANT]
> A nem használt replikációs helyeket el kell dobnia. Ennek elmulasztása a kiszolgáló elérhetetlensedéséhez vezethet.

## <a name="how-to-drop-a-slot"></a>Hogyan kell dobni egy slot
Ha nem használja fel aktívan a replikációs tárolóhelyet, dobja el azt.

Sql használatával hívott `test_slot` replikációs tárolóhely eldobása:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Ha nem használja a logikai dekódolást, `off`módosítsa az azure.replication_support vissza a vagy a alkalmazásra. `replica` A WAL részletek `logical` által megőrzött több részletes, és le kell tiltani, ha a logikai dekódolás nincs használatban. 

 
## <a name="next-steps"></a>További lépések

* A [logikai dekódolásról a Postgres dokumentációjában olvashat bővebben.](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)
* Ha kérdése van a logikus dekódolással kapcsolatban, forduljon [csapatunkhoz.](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)
* További információ az [olvasott replikákról.](concepts-read-replicas.md)

