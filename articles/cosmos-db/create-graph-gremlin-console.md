---
title: "Oktatóanyag az Azure Cosmos DB használatához: Elemek létrehozása, lekérdezése és gráfbejárás az Apache TinkerPops Gremlin-konzolban | Microsoft Docs"
description: "Az Azure Cosmos DB bevezetője csúcsok, élek és lekérdezések létrehozásához az Azure Cosmos DB Graph API-val"
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: quickstart
ms.date: 07/27/2017
ms.author: denlee
ms.openlocfilehash: 9755446d2c01313db9fd80b4f2a7f46f8bec500c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Elemek létrehozása, lekérdezése és a gráfok bejárása a Gremlin konzolban

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a rövid útmutató bemutatja egy Azure Cosmos DB-fiók, egy adatbázis és egy gráf (tároló) Azure Portal segítségével való létrehozását, majd az [Apache TinkerPop](http://tinkerpop.apache.org) [Gremlin-konzoljának](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) használatát a Graph API (előzetes verzió) adataival végzett munkához. Ebben az oktatóanyagban éleket és csúcspontokat hoz létre és kérdez le, csúcsponttulajdonságokat frissít, csúcspontokat kérdez le, bejárja a gráfot és elvet csúcspontokat.

![Azure Cosmos DB az Apache Gremlin-konzolból](./media/create-graph-gremlin-console/gremlin-console.png)

A Gremlin konzol egy Groovy/Java-alapú program, mely Linux, Mac és Windows rendszereken futtatható. A konzol az [Apache TinkerPop webhelyről](http://tinkerpop.apache.org/downloads.html) tölthető le.

## <a name="prerequisites"></a>Előfeltételek

A bevezető során az Azure Cosmos DB-fiók létrehozásához Azure-előfizetés szükséges.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Emellett a [Gremlin-konzolnak](http://tinkerpop.apache.org/) telepítve kell lennie. A 3.2.5-ös vagy újabb verziót használja.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Gráf hozzáadása

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Csatlakozás az alkalmazásszolgáltatáshoz
1. A Gremlin konzol elindítása előtt hozza létre vagy módosítsa a remote-secure.yaml konfigurációs fájlt az `apache-tinkerpop-gremlin-console-3.2.5/conf` könyvtárban.
2. Adja meg a *host* (gazdagép), *Port*, *username* (felhasználónév), *password* (jelszó), *ConnectionPool* (kapcsolatkészlet), és *serializer* (szerializáló) beállításokat:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    gazdagépek|[***.graphs.azure.com]|Lásd az alábbi képernyőképet. Ez a Gremlin URI értéke szögletes zárójelben az Azure Portal Áttekintés oldalán a :443 / végződés nélkül.<br><br>Ez az érték a Kulcsok lapról is lekérhető az URI értékkel a https:// eltávolításával, a dokumentumok gráfokká alakításával és a :443/ végződés eltávolításával.
    port|443|Állítsa 443 értékre.
    felhasználónév|*Az Ön felhasználóneve*|A `/dbs/<db>/colls/<coll>` űrlap erőforrása, ahol a `<db>` az adatbázis neve és a `<coll>` a gyűjtemény neve.
    jelszó|*Az Ön elsődleges kulcsa*| Lásd az alábbiakban a második képernyőképet. Ez az Ön elsődleges kulcsa, amelyet az Azure Portal Kulcsok oldalának Elsődleges Kulcs mezőjéből kérdezhet le. Az érték másolásához használja a mező bal oldalán lévő Másolás gombot.
    kapcsolatkészlet|{enableSsl: true}|A kapcsolatkészletre vonatkozó beállítás az SSL-hez.
    szerializáló|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Állítsa be ezt az értéket, és törölje a `\n` sortöréseket az érték beillesztésekor.

    A gazdagépek értékéhez másolja a **Gremlin URI** értéket az **Áttekintés** lapról: ![Az Azure Portal Áttekintés lapján található Gremlin URI érték megtekintése és másolása](./media/create-graph-gremlin-console/gremlin-uri.png)

    A jelszó értékéhez másolja az **Elsődleges kulcs** értékét a **Kulcsok** lapról: ![Az Azure Portal Kulcsok lapján található elsődleges kulcs megtekintése és másolása](./media/create-graph-gremlin-console/keys.png)

A remote-secure.yaml fájlnak így kell kinéznie:

```
hosts: [your_database_server.graphs.azure.com]
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

3. A terminálban futtassa a `bin/gremlin.bat` vagy a `bin/gremlin.sh` parancsot a [Gremlin-konzol](http://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/) elindításához.
4. A terminálban futtassa a `:remote connect tinkerpop.server conf/remote-secure.yaml` parancsot az alkalmazásszolgáltatáshoz való csatlakozáshoz.

    > [!TIP]
    > Ha a rendszer `No appenders could be found for logger` hibát ad vissza, győződjön meg arról, hogy frissítette a szerializáló értékét a remote-secure.yaml fájlban a 2. lépésben leírtak szerint. 

Remek! Most, hogy befejeztük a beállítást, futtassunk néhány konzolparancsot!

Próbáljon ki egy egyszerű count() parancsot. Írja be a következőket a konzolon, amikor a rendszer kéri:
```
:> g.V().count()
```

> [!TIP]
> Észrevette a `g.V().count()` szöveg előtti `:>` előtagot? 
>
> Ez része a parancsnak, amit be kell írnia. Ez fontos a Gremlin-konzol Azure Cosmos DB-vel való használatánál.  
>
> A `:>` előtag kihagyása arra utasítja a konzolt, hogy helyileg, gyakran egy, a memóriában tárolt gráfon hajtsa végre a parancsot.
> A `:>` előtag alkalmazása távoli parancs végrehajtására utasítja a konzolt ebben az esetben a Cosmos DB-n (a localhost emulátoron vagy egy > Azure-példányon).


## <a name="create-vertices-and-edges"></a>Csúcsok és élek létrehozása

Először hozzon létre öt darab, egy-egy személyt jelölő csúcsot *Thomas*, *Mary Kay*, *Robin*, *Ben*, és *Jack* néven.

Bemenet (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

Kimenet:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Bemenet (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

Kimenet:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Bemenet (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

Kimenet:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Bemenet (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

Kimenet:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Bemenet (Jack):

```
:> g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

Kimenet:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
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

Bemenet (a Jack csúcs elvetése):

```
:> g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Gráf adatainak törlése

Végezetül töröljük az adatbázisból az összes csúcsot és élet.

Bemenet:

```
:> g.E().drop()
:> g.V().drop()
```

Gratulálunk! Az Azure Cosmos DB: Graph API-oktatóanyag végére ért.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:  

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

A rövid útmutatóból megtudhatta, hogyan hozhat létre Azure Cosmos DB-fiókot, miként készíthet gráfokat az Adatkezelő használatával, és hogyan hozhat létre csúcsokat és éleket, valamint hogy hogyan járhat be gráfokat a Gremlin-konzollal. Az útmutató információira támaszkodva összetett lekérdezéseket hozhat létre és hatékony gráfbejárási logikákat helyezhet üzembe a Gremlin használatával. 

> [!div class="nextstepaction"]
> [Lekérdezés a Gremlin használatával](tutorial-query-graph.md)
