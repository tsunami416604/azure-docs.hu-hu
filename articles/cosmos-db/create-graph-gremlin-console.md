---
title: "Oktatóanyag az Azure Cosmos DB-hez: Elemek létrehozása, lekérdezése és a gráfok bejárása a Gremlin konzolban | Microsoft Docs"
description: "Az Azure Cosmos DB bevezetője csúcsok, élek és lekérdezések létrehozásához az Azure Cosmos DB Graph API-val"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fb27ba1a70959ba92fbd021e9e42438081000e45
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Elemek létrehozása, lekérdezése és a gráfok bejárása a Gremlin konzolban

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, adatbázist és gráfot (tárolót), majd hogyan dolgozhat a Graph API (előzetes verzió) alkalmazás adataival a [Gremlin-konzolban](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Az oktatóanyagból megtudhatja, hogyan hozhat létre és kérdezhet le csúcsokat és éleket, hogyan frissítheti a csúcsok tulajdonságait, hogyan járhatja be a gráfokat és vetheti el csúcsokat.

![Azure Cosmos DB az Apache Gremlin-konzolból](./media/create-graph-gremlin-console/gremlin-console.png)

A Gremlin konzol egy Groovy/Java-alapú program, mely Linux, Mac és Windows rendszereken futtatható. A konzol az [Apache TinkerPop webhelyről](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip) tölthető le.

## <a name="prerequisites"></a>Előfeltételek

A bevezető során az Azure Cosmos DB-fiók létrehozásához Azure-előfizetés szükséges.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Emellett a [Gremlin-konzolnak](http://tinkerpop.apache.org/) telepítve kell lennie. A program 3.2.4-es vagy újabb verzióját használja.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a id="ConnectAppService"></a>Csatlakozás az alkalmazásszolgáltatáshoz
1. A Gremlin-konzol elindítása előtt hozza létre vagy módosítsa a *remote-secure.yaml* konfigurációs fájlt az *apache-tinkerpop-gremlin-console-3.2.4/conf* könyvtárban.
2. Adja meg a *host* (gazdagép), *Port*, *username* (felhasználónév), *password* (jelszó), *ConnectionPool* (kapcsolatkészlet), és *serializer* (szerializáló) beállításokat:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Hosts|***.graphs.azure.com|A gráfszolgáltatás URI-címe, amely az Azure Portalról kérhető le
    Port|443|Állítsa be a 443-as portot.
    Felhasználónév|*Az Ön felhasználóneve*|`/dbs/<db>/colls/<coll>` formátumú erőforrás
    Jelszó|*Az Ön elsődleges főkulcsa*|Az Ön elsődleges főkulcsa az Azure Cosmos DB rendszerhez
    ConnectionPool|{enableSsl: true}|Kapcsolatkészletének beállítása SSL használatához
    Serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Ezt az értéket adja meg.

3. A terminálban futtassa a *bin/gremlin.bat* vagy a *bin/gremlin.sh* fájlt a [Gremlin-konzol](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/) elindításához.
4. A terminálban futtassa a *:remote connect tinkerpop.server conf/remote-secure.yam* fájlt az alkalmazásszolgáltatóhoz való kapcsolódáshoz.

Remek! Most, hogy befejeztük a beállítást, futtassunk néhány konzolparancsot!

## <a name="create-vertices-and-edges"></a>Csúcsok és élek létrehozása

Először is hozzunk létre négy személyt jelölő csúcsot *Thomas*, *Mary Kay*, *Robin* és *Ben* néven.

Bemenet (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Kimenet:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
Bemenet (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

Kimenet:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

Bemenet (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

Kimenet:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

Bemenet (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

Kimenet:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

Ezután adjunk meg éleket. Ezek a személyek közötti kapcsolatokat jelölik.

Bemenet (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Kimenet:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Bemenet (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Kimenet:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Bemenet (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Kimenet:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Csúcs frissítése

Most frissítsük a *Thomas* csúcspontot új életkor (*45*) megadásával.

Bemenet:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Kimenet:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Gráf lekérdezése

Futtassunk néhány lekérdezést a gráfon.

Első lépésként futtassunk egy lekérdezést olyan szűrővel, amely csak a 40 évesnél idősebb személyeket adja vissza.

Bemenet (szűrőlekérdezés):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Kimenet:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Most jelenítsük meg a 40 évesnél idősebb személyek utónevét.

Bemenet (szűrő- és kivetítési lekérdezés):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Kimenet:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>A gráf bejárása

A gráf bejárásával most kérdezzük le Thomas összes barátját.

Bemenet (Thomas barátai):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Kimenet: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Ezután folytassuk a csúcsok következő szintjével. A gráf bejárásával kérdezzük le Thomas barátainak minden barátját.

Bemenet (Thomas barátainak barátai):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Kimenet:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Csúcs elvetése

Töröljünk egy csúcsot a gráfadatbázisból.

Bemenet (a Robin csúcs elvetése):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>Gráf adatainak törlése

Végezetül töröljük az adatbázisból az összes csúcsot és élet.

Bemenet:

```
:> g.V().drop()
```

Gratulálunk! Az Azure Cosmos DB: Graph API-oktatóanyag végére ért.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást:  

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

A rövid útmutatóból megtudhatta, hogyan hozhat létre Azure Cosmos DB-fiókot, miként készíthet gráfokat az Adatkezelő használatával, és hogyan hozhat létre csúcsokat és éleket, valamint hogy hogyan járhat be gráfokat a Gremlin-konzollal. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)

