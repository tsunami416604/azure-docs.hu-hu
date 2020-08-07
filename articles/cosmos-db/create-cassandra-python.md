---
title: 'Gyors útmutató: Cassandra API a Pythonnal – Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogyan használható az Azure Cosmos DB Apache Cassandra API profilalkalmazások létrehozására a Python használatával.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2521a579538b272ac4990c3d390fb1aa6f2e58a0
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876529"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Gyors útmutató: Cassandra-alkalmazás létrehozása a Python SDK-val és a Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Cassandra API-fiókot hoz létre, és a GitHubról származó Cassandra Python-alkalmazást használ egy Cassandra-adatbázis és-tároló létrehozásához. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül.
- [Python 2.7.14 + vagy 3.4 +](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python-illesztőprogram az Apache Cassandra](https://github.com/datastax/python-driver)-hoz.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy Cassandra API alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy parancssort. Hozzon létre egy `git-samples` nevű mappát. Ezután zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan hozza létre a kód az adatbázis erőforrásait, tekintse át a következő kódrészleteket. A kódrészletek mind a *pyquickstart.py* fájlból származnak. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra. 

* A felhasználónév és a jelszó értékei a kapcsolati sztring oldalán állíthatók be az Azure Portalon. A `path\to\cert` egy X509-tanúsítvány elérési útját tartalmazza. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* A `cluster` inicializálása contactPoint adataival történik. A contactPoint az Azure Portalról kérhető le.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* A `cluster` csatlakozik az Azure Cosmos DB Cassandra API-hoz.

    ```python
    session = cluster.connect()
    ```

* A rendszer létrehoz egy új kulcsteret.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Létrejön egy új tábla.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* A rendszer beszúrja a kulcs/érték entitásokat.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Lekérdezés az összes kulcsérték lekéréséhez.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Lekérdezés a kulcs-érték lekéréséhez.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. A kapcsolati sztring lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kapcsolatok karakterlánc**lehetőséget. 

1. A :::image type="icon" source="./media/create-cassandra-python/copy.png"::: képernyő jobb oldalán található gomb használatával másolja a legfelső értéket, a kapcsolódási pontot. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Hozzáférési felhasználónév, jelszó és kapcsolódási pont megtekintése és másolása az Azure Portal kapcsolati sztring paneljén":::

1. Nyissa meg a *config.py* fájlt. 

1. Illessze be a CONTACT POINT értéket a portálról a `<FILLME>` helyére a 10. sorban.

    A 10. sornak ekkor a következőképp kell kinéznie: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. Másolja a USERNAME értéket a portálról, és illessze be a `<FILLME>` helyére a 6. sorban.

    A 6. sornak ekkor a következőképp kell kinéznie: 

    `'username': 'cosmos-db-quickstart',`
    
1. Másolja a PASSWORD értéket a portálról, és illessze be a `<FILLME>` helyére a 8. sorban.

    A 8. sornak ekkor a következőképp kell kinéznie:

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. Mentse a *config.py* fájlt.
    
## <a name="use-the-x509-certificate"></a>Az X509-tanúsítvány használata

1. Töltse le a Baltimore CyberTrust főtanúsítványát helyileg innen: [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt) . Nevezze át a fájlt a *. cer*kiterjesztésű fájl használatával.

   A tanúsítvány sorozatszáma `02:00:00:b9`, az SHA1 ujjlenyomata pedig `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Nyissa meg a *pyquickstart.py* , és módosítsa a `path\to\cert` pontot az új tanúsítványra.

3. Mentse a *pyquickstart.py*.

## <a name="run-the-python-app"></a>A Python-alkalmazás futtatása

1. A cd paranccsal a git terminálon váltson az `azure-cosmos-db-cassandra-python-getting-started` mappára. 

2. Futtassa az alábbi parancsokat a szükséges modulok telepítéséhez:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Futtassa a következő parancsot a Python-alkalmazás elindításához:

    ```
    python pyquickstart.py
    ```

3. Ellenőrizze az eredményt a parancssorban.

    Nyomja le a CTRL + C billentyűkombinációt a program végrehajtásának leállításához és a konzol ablak bezárásához. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="A kimenet megtekintése és ellenőrzése":::
    
4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="Adatok megtekintése az Adatkezelőben":::

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB fiókot a Cassandra API, és hogyan futtathat egy Cassandra Python-alkalmazást, amely létrehoz egy Cassandra-adatbázist és-tárolót. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)

