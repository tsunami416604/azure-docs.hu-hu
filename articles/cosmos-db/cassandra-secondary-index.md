---
title: Indexelés az Azure Cosmos DB Cassandra API-fiókjában
description: Ismerje meg, hogyan működik a másodlagos indexelés az Azure Azure Cosmos DB Cassandra API-fiókban.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758026"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Másodlagos indexelés az Azure Cosmos DB Cassandra API-ban

A Cassandra API az Azure Cosmos DB-ben az alapul szolgáló indexelő infrastruktúrát használja a platformban rejlő indexelési erőssége. Azonban az alapvető SQL API-val ellentétben az Azure Cosmos DB Cassandra API alapértelmezés szerint nem indexeli az összes attribútumot. Ehelyett támogatja a másodlagos indexelés t, hogy hozzon létre egy indexet bizonyos attribútumok, amely ugyanúgy viselkedik, mint az Apache Cassandra.  

Általában nem tanácsos szűrőlekérdezéseket végrehajtani a nem particionált oszlopokon. Explicit módon kell használnia az ALLOW FILTERING szintaxist, ami olyan műveletet eredményez, amely nem teljesít jól. Az Azure Cosmos DB futtathatja az ilyen lekérdezések alacsony számosságú attribútumok, mert a partíciók között az eredmények lekéréséhez.

Nem ajánlott indexet létrehozni egy gyakran frissített oszlopban. A tábla definiálásakor célszerű indexet létrehozni. Ez biztosítja, hogy az adatok és az indexek konzisztens állapotban legyenek. Abban az esetben, ha új indexet hoz létre a meglévő adatokhoz, jelenleg nem követheti nyomon a tábla indexállapotának változását. Ha nyomon kell követnie a művelet előrehaladását, támogatási jegyen keresztül kell kérnie a folyamat [módosítását.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> A másodlagos index nem támogatott a következő objektumokon:
> - adattípusok, például fagyasztott gyűjteménytípusok, tizedes- és változattípusok.
> - Statikus oszlopok
> - Fürtözési kulcsok

## <a name="indexing-example"></a>Példa indexelésre

Először hozzon létre egy mintakulcstant és táblázatot a következő parancsok futtatásával a CQL rendszerhéj parancssorában:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Ezután szúrjon be mintafelhasználói adatokat a következő parancsokkal:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Ha megpróbálja végrehajtani a következő utasítást, akkor egy olyan `ALLOW FILTERING`hibába ütközik, amely a következők et kéri: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Bár a Cassandra API támogatja az ALLOW SZŰRÉS, az előző szakaszban említettek szerint, ez nem ajánlott. Ehelyett létre kell hoznia egy indexet a következő példában látható módon:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Miután létrehozott egy indexet a "vezetéknév" mezőben, most már sikeresen futtathatja az előző lekérdezést. Cassandra API-val az Azure Cosmos DB,nem kell megadnia egy index nevét. A program a `tablename_columnname_idx` formátumot mutató alapértelmezett indexet használja. Például ` t1_lastname_idx` az előző tábla indexneve.

## <a name="dropping-the-index"></a>Az index eldobása 
Tudnia kell, hogy mi az index neve, hogy dobja el az indexet. Futtassa a `desc schema` parancsot, hogy megkapja a tábla leírását. A parancs kimenete tartalmazza az index `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`nevét a formátumban. Ezután az index nevével eldobhatja az indexet az alábbi példában látható módon:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan működik az [automatikus indexelés](index-overview.md) az Azure Cosmos DB-ben
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra funkciók](cassandra-support.md)
