---
title: Meglévő MongoDB-alkalmazás integrálása az Azure Cosmos DB API-val a MongoDB-hoz és az Open Service Broker for Azure (OSBA) szolgáltatáshoz
description: Ebben a cikkben megtudhatja, hogyan integrálhat egy meglévő Java és MongoDB alkalmazást az Azure Cosmos DB API-val a MongoDB-hoz az Open Service Broker for Azure (OSBA) használatával.
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247919"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Meglévő MongoDB-alkalmazás integrálása az Azure Cosmos DB API-val a MongoDB-hoz és az Open Service Broker for Azure (OSBA) szolgáltatáshoz

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Azt is előírja, vezetékes protokoll kompatibilitás több NoSQL API-k, beleértve a MongoDB. A Cosmos DB API mongoDB lehetővé teszi, hogy a Cosmos DB a meglévő MongoDB alkalmazás anélkül, hogy az alkalmazás adatbázis-illesztőprogramok vagy megvalósítása módosítása nélkül. Cosmos DB-szolgáltatást is üzembe helyezhet az Open Service Broker for Azure használatával.

Ebben a cikkben egy meglévő Java-alkalmazást, amely egy MongoDB-adatbázist használ, és frissíti azt egy Cosmos DB-adatbázis használatával az Open Service Broker for Azure használatával.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt a következőket kell tennie:
    
* Egy [Azure Kubernetes-szolgáltatás fürtjének](kubernetes-walkthrough.md) létrehozása.
* Telepítette [és konfigurálta az Open Service Broker for Azure szolgáltatást az AKS-fürtön.](integrate-azure.md) 
* A [service catalog CLI](https://svc-cat.io/docs/install/) telepítése `svcat` és konfigurálása a parancsok futtatásához.
* Van egy meglévő [MongoDB](https://www.mongodb.com/) adatbázis. Például előfordulhat, hogy a MongoDB a [fejlesztői gépen](https://docs.mongodb.com/manual/administration/install-community/) vagy egy [Azure-beli virtuális gépen](../virtual-machines/linux/install-mongodb.md)fut.
* A MongoDB adatbázishoz, például a [mongo shellhez](https://docs.mongodb.com/manual/mongo/)való csatlakozás és lekérdezés módja.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése
    
Ebben a cikkben a [Cloud Foundry tavaszi zenei mintaalkalmazásával](https://github.com/cloudfoundry-samples/spring-music) mutathatja be a MongoDB-adatbázist használó alkalmazást.
    
Klónozza az alkalmazást a GitHubról, és navigáljon a könyvtárába:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Az alkalmazás előkészítése a MongoDB adatbázis használatára

A tavaszi zenei minta alkalmazás számos lehetőséget kínál az adatforrások számára. Ebben a cikkben úgy konfigurálhatja, hogy egy meglévő MongoDB-adatbázist használjon. 

Adja hozzá a YAML követően a végén *src/main/resources/application.yml*. Ez a kiegészítés létrehoz egy *mongodb* nevű profilt, és konfigurálja az URI-t és az adatbázis nevét. Cserélje le az URI-t a meglévő MongoDB-adatbázishoz való kapcsolódási adatokkal. A felhasználónevet és jelszót tartalmazó URI hozzáadása közvetlenül ehhez a fájlhoz **csak fejlesztési célokra szolgál,** és **soha nem szabad hozzáadni a verziókövetéshez.**

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Amikor elindítja az alkalmazást, és azt mondja, hogy használja a *mongodb* profilt, csatlakozik a MongoDB adatbázishoz, és használja az alkalmazás adatainak tárolására.

Az alkalmazás létrehozása:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Indítsa el az alkalmazást, és mondja meg neki, hogy használja a *mongodb* profilt:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Keresse `http://localhost:8080` meg a böngészőben.

![Spring Music alkalmazás alapértelmezett adatokkal](media/music-app.png)

Figyelje meg, hogy az alkalmazás néhány [alapértelmezett adatot](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)töltött fel. Lépjen kapcsolatba vele néhány meglévő album törlésével és néhány új album létrehozásával.

Ellenőrizheti, hogy az alkalmazás a MongoDB-adatbázist használja-e, ha csatlakozik hozzá, és lekérdezi a *musicdb* adatbázist:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Az előző példa a [mongo rendszerhéj](https://docs.mongodb.com/manual/mongo/) segítségével csatlakozik a MongoDB adatbázishoz, és lekérdezi azt. Ellenőrizheti azt is, hogy a módosítások megmaradnak-e az alkalmazás leállításával, újraindításával és a böngészőben való visszanavigálással. Figyelje meg, hogy a módosítások még mindig ott vannak.


## <a name="create-a-cosmos-db-database"></a>Cosmos DB-adatbázis létrehozása

Ha Cosmos DB-adatbázist szeretne létrehozni az `svcat provision` Azure-ban az Open Service Broker használatával, használja a következő parancsot:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Az előző parancs egy Cosmos DB-adatbázist foglal az Azure-ban az *eastus* régióban a *MyResourceGroup* erőforráscsoportban. Az *erőforráscsoportról*, *a helyről*és más Azure-specifikus JSON-paraméterekről a [Cosmos DB modul referenciadokumentációjában](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)talál további információt.

Az adatbázis kiépítési befejezésének ellenőrzéséhez használja a `svcat get instance` következő parancsot:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Az adatbázis készen áll, amikor a *Készenlét* az *ÁLLAPOT*csoportban látható.

Miután az adatbázis kiépített, a metaadatokat egy [Kubernetes-titokhoz kell kötnie.](https://kubernetes.io/docs/concepts/configuration/secret/) Ezután más alkalmazások is hozzáférhetnek ezekhez az adatokhoz, miután titkos adathoz kötötték őket. Ha az adatbázis metaadatait titkos fájlhoz `svcat bind` szeretné kötni, használja a következő parancsot:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>A Cosmos DB adatbázis használata az alkalmazással

A Cosmos DB-adatbázis használata az alkalmazással, ismernie kell az URI-hoz csatlakozni. Az információ leéséhez `kubectl get secret` használja a következő parancsot:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Az előző parancs leveszi a musicdb titkos *kulcsot,* és csak az URI-t jeleníti meg. A titkos kulcsok base64 formátumban vannak tárolva, így az előző parancs is dekódolja azt.

A Cosmos DB adatbázis URI-jának használatával frissítse *az src/main/resources/application.yml* fájlt a használatához:

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

A felhasználónevet és jelszót tartalmazó URI frissítése közvetlenül ehhez a fájlhoz **csak fejlesztési célokra szolgál,** és **soha nem szabad hozzáadni a verziókövetéshez.**

Építse újra és indítsa el az alkalmazást a Cosmos DB-adatbázis használatának megkezdéséhez:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Figyelje meg, hogy az alkalmazás továbbra is a *mongodb-profilt* és egy URI-t használ, amely *mongodb://* kezdődik a Cosmos DB-adatbázishoz való csatlakozáshoz. Az [Azure Cosmos DB API a MongoDB](../cosmos-db/mongodb-introduction.md) biztosítja ezt a kompatibilitást. Lehetővé teszi, hogy az alkalmazás továbbra is úgy működjön, mintha egy MongoDB adatbázist használna, de valójában cosmos DB-t használ.

Keresse `http://localhost:8080` meg a böngészőben. Figyelje meg, hogy az alapértelmezett adatok visszalettek állítva. Lépjen kapcsolatba vele néhány meglévő album törlésével és néhány új album létrehozásával. Ellenőrizheti, hogy a módosítások megmaradnak-e az alkalmazás leállításával, újraindításával és a böngészőben való visszanavigálással. Figyelje meg, hogy a módosítások még mindig ott vannak. A módosítások megmaradnak a Cosmos DB az Azure-hoz nyílt szolgáltatásközvetítő használatával létrehozott.


## <a name="run-your-application-on-your-aks-cluster"></a>Az alkalmazás futtatása Az AKS-fürtön

[Az Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) segítségével telepítheti az alkalmazást az AKS-fürtre. Az Azure Dev Spaces segítségével összetevőket hozhat létre, például a Docker-fájlok és a Helm-diagramokat, és üzembe helyezhet és futtathat egy alkalmazást az AKS-ben.

Az Azure dev spaces engedélyezése az AKS-fürtben:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Az Azure Dev Spaces eszközhasználatával előkészítheti az alkalmazást az AKS-ben való futtatásra:

```cmd
azds prep --public
```

Ez a parancs számos műtárgyat hoz létre, beleértve a *diagramokat / mappát,* amely a Helm-diagram, a projekt gyökere. Ez a parancs nem tud *Docker-fájlt* létrehozni ehhez a projekthez, ezért létre kell hoznia.

Hozzon létre egy *dockerfile* nevű fájlt a projekt gyökerében ezzel a tartalommal:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Ezenkívül frissítenie kell a *configurations.develop.build tulajdonságot* az *azds.yaml-ben* *hamis*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Azt is meg kell frissíteni a *containerPort* attribútum *8080* *a térképek / tavaszi zene / sablonok / deployment.yaml*:

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

Az alkalmazás aKS-re való üzembe helyezése:

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

Keresse meg a naplókban megjelenő URL-címet. Az előző példában a *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* használatát kell használnia. 

Ellenőrizze, hogy látja-e az alkalmazást a módosításokkal együtt.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan frissítheti a meglévő alkalmazások at mongoDB a Cosmos DB API a MongoDB használatával. Ez a cikk azt is ismerteti, hogyan lehet egy Cosmos DB szolgáltatás kiépítése az Open Service Broker for Azure használatával, és az alkalmazás üzembe helyezése az AKS-be az Azure Dev Spaces használatával.

A Cosmos DB,open service broker for Azure és az Azure Dev Spaces szolgáltatásról a következő témakörökben talál további információt:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Fejlesztés a Dev Spaces használatával](../dev-spaces/azure-dev-spaces.md)
