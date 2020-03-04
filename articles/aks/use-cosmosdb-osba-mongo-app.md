---
title: Meglévő MongoDB-alkalmazás integrálása Azure Cosmos DB API-val a MongoDB és a nyílt Service Broker for Azure (OSBA) használatával
description: Ebből a cikkből megtudhatja, hogyan integrálhat egy meglévő Java-és MongoDB-alkalmazást a MongoDB Azure Cosmos DB API-val az Azure-hoz készült nyílt Service Broker használatával (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, nyílt Service Broker, nyílt Service Broker az Azure-hoz
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247919"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Meglévő MongoDB-alkalmazás integrálása Azure Cosmos DB API-val a MongoDB és a nyílt Service Broker for Azure (OSBA) használatával

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Emellett több NoSQL API-t is biztosít, többek között a MongoDB. A MongoDB-hez készült Cosmos DB API lehetővé teszi a meglévő MongoDB-alkalmazással való Cosmos DB használatát anélkül, hogy módosítani kellene az alkalmazás adatbázis-illesztőprogramjait vagy megvalósítását. Cosmos DB szolgáltatást az Azure Open Service Broker használatával is kiépítheti.

Ebben a cikkben egy meglévő Java-alkalmazást fog használni, amely egy MongoDB-adatbázist használ, és frissíti azt egy Cosmos DB-adatbázis használatára az Azure nyílt Service Broker használatával.

## <a name="prerequisites"></a>Előfeltételek

A folytatáshoz a következőket kell tennie:
    
* Hozzon létre egy [Azure Kubernetes Service-fürtöt](kubernetes-walkthrough.md) .
* [Nyissa meg a Service Broker az Azure-ban telepítve és konfigurálva az AK-fürtön](integrate-azure.md). 
* Telepítse és konfigurálja `svcat` parancsok futtatására szolgáló [parancssori](https://svc-cat.io/docs/install/) felületet.
* Meglévő [MongoDB](https://www.mongodb.com/) -adatbázissal rendelkezik. Előfordulhat például, hogy a MongoDB fut a [fejlesztői gépen](https://docs.mongodb.com/manual/administration/install-community/) vagy egy Azure-beli [virtuális](../virtual-machines/linux/install-mongodb.md)gépen.
* A MongoDB-adatbázis, például a [Mongo-rendszerhéj](https://docs.mongodb.com/manual/mongo/)összekapcsolásának és lekérdezésének módja.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése
    
Ebben a cikkben a [Spring Music Sample alkalmazásból Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) egy MongoDB-adatbázist használó alkalmazást mutat be.
    
Az alkalmazás klónozása a GitHubról, majd a címtárba való navigálása:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Az alkalmazás előkészítése a MongoDB-adatbázis használatára

A Spring Music minta alkalmazás számos lehetőséget kínál az adatforrásokhoz. Ebben a cikkben egy meglévő MongoDB-adatbázis használatára konfigurálja. 

Adja hozzá a következő YAML a *src/Main/Resources/Application. YML*végéhez. Ez a Hozzáadás létrehoz egy *mongodb* nevű profilt, és BEÁLLÍTJA az URI és az adatbázis nevét. Cserélje le az URI-t a meglévő MongoDB-adatbázishoz tartozó kapcsolódási adatokra. A felhasználónevet és jelszót tartalmazó URI-azonosító hozzáadásával közvetlenül ehhez a fájlhoz **csak fejlesztési célú használatra van használatban** , és **soha nem adható hozzá a Version Control**szolgáltatáshoz.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Amikor elindítja az alkalmazást, és azt mondja el, hogy használja a *mongodb* -profilt, a mongodb-adatbázishoz csatlakozik, és az alkalmazás adatai tárolására használja.

Az alkalmazás létrehozása:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Indítsa el az alkalmazást, és mondja el, hogy használja a *mongodb* -profilt:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigáljon `http://localhost:8080` a böngészőben.

![Spring Music-alkalmazás alapértelmezett adattal](media/music-app.png)

Figyelje meg, hogy az alkalmazás néhány [alapértelmezett adattal](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)lett feltöltve. Egy pár meglévő album törlésével és néhány új létrehozásával dolgozhat a szolgáltatással.

Az alkalmazás a MongoDB-adatbázis használatával ellenőrizhető, hogy csatlakozik hozzá, és lekérdezi a *musicdb* -adatbázist:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Az előző példa a [Mongo-rendszerhéj](https://docs.mongodb.com/manual/mongo/) használatával csatlakozik a MongoDB-adatbázishoz, és lekérdezi azt. Azt is ellenőrizheti, hogy a módosítások megmaradnak-e az alkalmazás leállításával, újraindításával és a böngészőben való újbóli navigálással. Figyelje meg, hogy az elvégzett módosítások még mindig vannak.


## <a name="create-a-cosmos-db-database"></a>Cosmos DB-adatbázis létrehozása

Ha Cosmos DB adatbázist szeretne létrehozni az Azure-ban az Open Service Broker használatával, használja a `svcat provision` parancsot:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Az előző parancs egy Cosmos DB adatbázist helyez üzembe az Azure-ban a *eastus* régióban található erőforráscsoport- *MyResourceGroup* . A *resourceGroup*, a *hely*és az egyéb Azure-specifikus JSON-paraméterekkel kapcsolatos további információk a [Cosmos db modul dokumentációjában](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)találhatók.

Annak ellenőrzéséhez, hogy az adatbázis befejeződött-e, használja a `svcat get instance` parancsot:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Az adatbázis készen áll, amikor a *kész* *állapotot*látja.

Miután az adatbázis üzembe helyezése befejeződött, a metaadatokat egy [Kubernetes-titokhoz](https://kubernetes.io/docs/concepts/configuration/secret/)kell kötni. Más alkalmazások ezután hozzáférhetnek az adott adathoz, miután egy titkos kulcshoz kötötték. Ha egy titkos kulcshoz szeretné kötni az adatbázis metaadatait, használja a `svcat bind` parancsot:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Az Cosmos DB adatbázis használata az alkalmazással

Ha az Cosmos DB adatbázist szeretné használni az alkalmazással, ismernie kell az URI-t, hogy csatlakozhasson hozzá. Ezen információk beszerzéséhez használja a `kubectl get secret` parancsot:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Az előző parancs lekéri a titkos *musicdb* , és csak az URI-t jeleníti meg. A titkok Base64 formátumban vannak tárolva, így az előző parancs is dekódolja.

Használja az Cosmos DB adatbázis URI-JÁT, az *src/Main/Resources/Application. YML* frissítést a használatára:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Ha a felhasználónevet és jelszót tartalmazó URI-t frissíti, közvetlenül ehhez a fájlhoz **csak fejlesztési célú használatra van felhasználva** , és **soha nem adható hozzá a Version Control**szolgáltatáshoz.

Építse újra az alkalmazást, és kezdje el használni az Cosmos DB-adatbázis használatát:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Figyelje meg, hogy az alkalmazás továbbra is a *mongodb* -profilt és egy olyan URI-t használ, amely a Cosmos db-adatbázishoz való kapcsolódáshoz a *mongodb://* - A [MongoDB Azure Cosmos db API](../cosmos-db/mongodb-introduction.md) biztosítja ezt a kompatibilitást. Lehetővé teszi, hogy az alkalmazás továbbra is működőképes legyen, mintha MongoDB-adatbázist használ, de valójában Cosmos DBt használ.

Navigáljon `http://localhost:8080` a böngészőben. Figyelje meg, hogy az alapértelmezett értékek vissza lettek állítva. Egy pár meglévő album törlésével és néhány új létrehozásával dolgozhat a szolgáltatással. Ellenőrizheti, hogy a módosítások megmaradnak-e az alkalmazás leállításával, újraindításával és a böngészőben való visszalépéssel. Figyelje meg, hogy az elvégzett módosítások még mindig vannak. A változtatások megmaradnak az Azure nyílt Service Broker használatával létrehozott Cosmos DB.


## <a name="run-your-application-on-your-aks-cluster"></a>Az alkalmazás futtatása az AK-fürtön

Az [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) használatával üzembe helyezheti az alkalmazást az AK-fürtön. Az Azure dev Spaces lehetővé teszi összetevők, például Dockerfiles és Helm diagramok létrehozását, valamint alkalmazás üzembe helyezését és futtatását az AK-ban.

Az Azure dev-helyek engedélyezése az AK-fürtben:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Az Azure dev Spaces Tool használatával készítse elő az alkalmazást az AK-ban való futtatásra:

```cmd
azds prep --public
```

Ez a parancs számos összetevőt generál, például egy *diagramot/* mappát, amely a Helm diagram, a projekt gyökerében. Ez a parancs nem tud *Docker* létrehozni ehhez az adott projekthez, ezért létre kell hoznia azt.

Hozzon létre egy fájlt a *Docker* nevű projekt gyökerében a következő tartalommal:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Emellett frissítenie kell a *konfigurációkat. fejlessze. Build* tulajdonságot a *azds. YAML* *hamis*értékre:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Emellett frissítenie kell a *containerPort* attribútumot *8080* -re a *diagramok/Spring-Music/sablonok/üzembe helyezés. YAML*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Az alkalmazás üzembe helyezése az AK-ban:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navigáljon a naplókban megjelenő URL-címhez. Az előző példában a *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/t* kellene használnia. 

Ellenőrizze, hogy megjelenik-e az alkalmazás a módosítások mellett.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan lehet frissíteni egy meglévő alkalmazást a MongoDB használatával a MongoDB Cosmos DB API-ját használva. Ez a cikk azt is ismerteti, hogyan lehet Cosmos DB szolgáltatást kiépíteni az Azure-hoz készült nyílt Service Broker használatával, és hogy az alkalmazás üzembe helyezése az AK-ban az Azure dev Spaces

További információ a Cosmos DBről, az Azure-beli Open Service Broker és az Azure dev Spaces szolgáltatásról:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Azure-Service Broker megnyitása](https://osba.sh)
* [Fejlesztés a fejlesztői Spaces-szel](../dev-spaces/azure-dev-spaces.md)
