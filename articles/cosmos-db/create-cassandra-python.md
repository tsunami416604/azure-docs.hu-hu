---
title: 'Gyors útmutató: Cassandra API a Pythonnal – Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogyan használható az Azure Cosmos DB Apache Cassandra API profilalkalmazások létrehozására a Python használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77134688"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Rövid útmutató: Cassandra-alkalmazás létrehozása Python SDK-val és Azure Cosmos DB-vel

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Ebben a rövid útmutatóban hozzon létre egy Azure Cosmos DB Cassandra API-fiókot, és a GitHubról klónozott Cassandra Python-alkalmazás használatával hozzon létre egy Cassandra-adatbázist és -tárolót. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül.
- [Python 2.7.14+ vagy 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python driver apache cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

A dokumentum-adatbázis létrehozásához először létre kell hoznia egy Cassandra-fiókot az Azure Cosmos DB segítségével.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most klónozzunk egy Cassandra API-alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

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

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza a **Kapcsolati karakterlánc**lehetőséget. 

    Válassza a ![a képernyő jobb oldalán található Másolás gombot](./media/create-cassandra-python/copy.png) használatával (a képernyő jobb oldalán) másolja a vágólapra a felső, CONTACT POINT (Kapcsolódási pont) értéket.

    ![Hozzáférési felhasználónév, jelszó és kapcsolódási pont megtekintése és másolása az Azure Portal kapcsolati sztring paneljén](./media/create-cassandra-python/keys.png)

2. Nyissa meg a *config.py* fájlt. 

3. Illessze be a CONTACT POINT értéket a portálról a `<FILLME>` helyére a 10. sorban.

    A 10. sornak ekkor a következőképp kell kinéznie: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Másolja a USERNAME értéket a portálról, és illessze be a `<FILLME>` helyére a 6. sorban.

    A 6. sornak ekkor a következőképp kell kinéznie: 

    `'username': 'cosmos-db-quickstart',`
    
5. Másolja a PASSWORD értéket a portálról, és illessze be a `<FILLME>` helyére a 8. sorban.

    A 8. sornak ekkor a következőképp kell kinéznie:

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Mentse a *config.py* fájlt.
    
## <a name="use-the-x509-certificate"></a>Az X509-tanúsítvány használata

1. Töltse le a Baltimore CyberTrust [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)root tanúsítványt helyileg a ból. Nevezze át a fájlt a *.cer*kiterjesztéssel.

   A tanúsítvány sorozatszáma `02:00:00:b9`, az SHA1 ujjlenyomata pedig `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Nyissa *meg pyquickstart.py,* és módosítsa az `path\to\cert` új tanúsítványra mutatást.

3. A *pyquickstart.py*mentése .

## <a name="run-the-python-app"></a>A Python-alkalmazás futtatása

1. A cd paranccsal a git terminálon váltson az `azure-cosmos-db-cassandra-python-getting-started` mappára. 

2. Futtassa az alábbi parancsokat a szükséges modulok telepítéséhez:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. A Python-alkalmazás elindításához futtassa a következő parancsot:

    ```
    python pyquickstart.py
    ```

3. Ellenőrizze az eredményt a parancssorban.

    Nyomja le a CTRL+C billentyűkombinációt a program végrehajtásának leállításához és a konzolablak bezárásához. 

    ![A kimenet megtekintése és ellenőrzése](./media/create-cassandra-python/output.png)
    
4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    ![Adatok megtekintése az Adatkezelőben](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot cassandra API-val, és futtathatja a Cassandra Python-alkalmazást, amely cassandra-adatbázist és -tárolót hoz létre. Most már importálhat további adatokat az Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)

