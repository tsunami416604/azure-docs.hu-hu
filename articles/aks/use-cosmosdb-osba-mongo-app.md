---
title: Integrálható meglévő MongoDB-alkalmazások Azure Cosmos DB API a mongodb-hez és az Open Service Broker for Azure (OSBA)
description: Ebből a cikkből elsajátíthatja egy meglévő Java- és MongoDB-alkalmazás integrálása az Azure Cosmos DB API a mongodb-hez Open Service Broker for Azure (OSBA) használatával.
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: A cosmos DB, nyissa meg a Service Brokert, az Open Service Broker for Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991982"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrálható meglévő MongoDB-alkalmazások Azure Cosmos DB API a mongodb-hez és az Open Service Broker for Azure (OSBA)

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Átviteli protokoll kompatibilitási többek között a mongodb-hez, több nosql-alapú API-kkal is tartalmazza. A Cosmos DB mongodb API Cosmos DB használatával a meglévő MongoDB-alkalmazás az alkalmazás-adatbázis illesztőprogramok, illetve implementációjára módosítása nélkül teszi lehetővé. Egy Cosmos DB szolgáltatást az Azure Open Service Broker használatával is kiépíthetők.

Ebben a cikkben egy meglévő Java-alkalmazás egy MongoDB-adatbázist használó igénybe vehet, és frissítse úgy, hogy használja az Open Service Broker használatával az Azure Cosmos DB-hez.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, tegye a következőket:
    
* Rendelkezik egy [Azure Kubernetes Service-fürt](kubernetes-walkthrough.md) létrehozott.
* Rendelkezik [Open Service Broker for Azure telepíteni és konfigurálni az AKS-fürt](integrate-azure.md). 
* Rendelkezik a [szolgáltatáskatalógus CLI](https://svc-cat.io/docs/install/) telepített és konfigurált futtatásához `svcat` parancsokat.
* Rendelkezik egy meglévő [MongoDB](https://www.mongodb.com/) adatbázis. Például lehet futó mongodb-hez a [fejlesztői gépen](https://docs.mongodb.com/manual/administration/install-community/) vagy egy [Azure virtuális gép](../virtual-machines/linux/install-mongodb.md).
* Csatlakozik, és a MongoDB-adatbázis lekérdezéséhez például módon a [mongo shell](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Az alkalmazáskód letöltése
    
Ebben a cikkben fogja használni a [zene mintaalkalmazás spring Cloud Foundry a](https://github.com/cloudfoundry-samples/spring-music) egy MongoDB-adatbázist használó alkalmazás bemutatása.
    
Klónozza az alkalmazást a Githubról, és lépjen abba a könyvtárba:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Az alkalmazás használatához a MongoDB-adatbázis előkészítése

A spring zene mintaalkalmazás több adatforrást biztosít. Ez a cikk konfigurálja úgy, hogy egy meglévő MongoDB-adatbázist használja. 

Adja hozzá a YAML következő végéhez *src/main/resources/application.yml*. A Hozzáadás nevű profilt hoz létre *mongodb* , és konfigurál egy URI-t és az adatbázis nevét. Cserélje le az URI-t a meglévő MongoDB-adatbázis a kapcsolati adatokat. Az URI-t, amely tartalmazza a felhasználónevet és jelszót, közvetlenül ehhez a fájlhoz, akkor hozzáadni a **csak fejlesztésekhez** és **soha nem lehet hozzáadni a verziókövetés**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Ha az alkalmazás elindításához, és mondja el, hogy használja a *mongodb* profil, csatlakozik a MongoDB-adatbázis, és segítségével az alkalmazások adatainak tárolására.

Az alkalmazás létrehozásához:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Indítsa el az alkalmazást, és mondja el, hogy használja a *mongodb* profil:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigáljon a `http://localhost:8080` a böngészőben.

![Spring Music alkalmazás alapértelmezett adatokkal.](media/music-app.png)

Figyelje meg, hogy az alkalmazás egyes feltöltődtek [alapértelmezett adatok](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Dolgozzon vele törlésével néhány meglévő albumokat és néhány újakat hozna létre.

Ellenőrizheti az alkalmazás van, csatlakoztatása és lekérdezése a MongoDB-adatbázis használatával. a *musicdb* adatbázis:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

Az előző példában a [mongo shell](https://docs.mongodb.com/manual/mongo/) a MongoDB-adatbázishoz, és lekérdezést. A változtatások megmaradnak az alkalmazás leállítása, újraindítása, és lépjen vissza a böngészőben, is ellenőrizheti. Figyelje meg, hogy a módosítások vannak-e továbbra is.


## <a name="create-a-cosmos-db-database"></a>Hozzon létre egy Cosmos DB-adatbázisban

Az Open Service Broker használatával Azure Cosmos DB-adatbázis létrehozásához használja a `svcat provision` parancsot:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Az előző parancs kiosztja az erőforráscsoporthoz tartozik, az Azure Cosmos DB-hez *MyResourceGroup* a a *eastus* régióban. További információ a *resourceGroup*, *hely*, és más Azure-ra vonatkozó JSON-paraméter elérhető a [Cosmos DB modul dokumentációjában](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Annak ellenőrzéséhez, hogy az adatbázis kiépítése befejeződött, használja a `svcat get instance` parancsot:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Az adatbázis készen mikor lásd *készen* alatt *állapot*.

Az adatbázis kiépítése befejezését követően kell kötni a metaadatait, egy [Kubernetes titkos](https://kubernetes.io/docs/concepts/configuration/secret/). Más alkalmazások ezután hozzáférhetnek az adatokat, miután hozzá lett kötve a titkos kulcs. Az adatbázis metaadatai kötése egy titkos kulcsot, használja a `svcat bind` parancsot:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Az alkalmazás a Cosmos DB-adatbázis használata

A Cosmos DB-adatbázis használata az alkalmazás, az URI-t szeretne csatlakozni, ismernie kell. Ezek az információk lekéréséhez használja a `kubectl get secret` parancsot:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Az előző parancs lekérdezi a *musicdb* titkos kulcsot, és csak az URI-t jeleníti meg. Titkos kódok base64 formátumban tárolódnak, így a fenti paranccsal is dekódol.

Az URI-ját a Cosmos DB-adatbázist használ, frissítse *src/main/resources/application.yml* vele:

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

Frissítése az URI-t, amely tartalmazza a felhasználónevet és jelszót, közvetlenül a fájl már a **csak fejlesztésekhez** és **soha nem lehet hozzáadni a verziókövetés**.

Építse újra, és a Cosmos DB-adatbázis használatának megkezdéséhez az alkalmazás elindításához:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Figyelje meg, hogy az alkalmazás továbbra is használja a *mongodb* profil és a egy URI-t, amely a következővel kezdődik: *mongodb: / /* a Cosmos DB-adatbázishoz való csatlakozáshoz. A [Azure Cosmos DB MongoDB API-](../cosmos-db/mongodb-introduction.md) a kompatibilitást biztosít. Lehetővé teszi az alkalmazás továbbra is működnek, mintha egy MongoDB-adatbázist használ, de a Cosmos DB ténylegesen használ.

Navigáljon a `http://localhost:8080` a böngészőben. Figyelje meg, hogy a program visszaállította az alapértelmezett adatokat. Dolgozzon vele törlésével néhány meglévő albumokat és néhány újakat hozna létre. Ellenőrizheti, hogy az alkalmazás leállítása, újraindítása, és lépjen vissza a böngészőben, a módosítások megmaradnak. Figyelje meg, hogy a módosítások vannak-e továbbra is. A módosítások megmaradnak a létrehozott Open Service Broker for Azure Cosmos DB-hez.


## <a name="run-your-application-on-your-aks-cluster"></a>Futtassa az alkalmazást a az AKS-fürt

Használhat [Azure fejlesztési tárolóhelyek](../dev-spaces/azure-dev-spaces.md) való az alkalmazás az AKS-fürt üzembe helyezése. Az Azure fejlesztői, szóközök köszönhetően Ön hozza létre az összetevők, például a docker-fájlok és a Helm-diagramok, és üzembe helyezése és alkalmazás futtatása az aks-ben.

Az AKS-fürt Azure fejlesztési tárolóhelyek engedélyezése:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Az Azure fejlesztési tárolóhelyek eszközkészlet használatával készítse elő az alkalmazás futtatásához az aks-ben:

```cmd
azds prep --public
```

Ez a parancs létrehoz összetevők, beleértve egy *diagramok /* mappát, amely a Helm-diagramot, a projekt gyökerében. Ez a parancs nem hozható létre egy *Dockerfile* adott projekthez, létre kell hoznia azt.

Hozzon létre egy fájlt a projekt neve gyökérmappájában *Dockerfile* ezzel a tartalommal:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Emellett frissítenie a *configurations.develop.build* tulajdonság *azds.yaml* való *hamis*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Emellett frissítenie kell a *containerPort* attribútumot *8080-as* a *charts/spring-music/templates/deployment.yaml*:

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

Az aks-ben az alkalmazás telepítéséhez:

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

Keresse meg az URL-cím jelenik meg a naplókat. Használja az előző példában *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Ellenőrizze, hogy megjelenik az alkalmazás mellett a módosításokat.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti a Cosmos DB API a mongodb-hez készült MongoDB-vel egy meglévő alkalmazás frissítése. Ez a cikk is bemutatta, hogyan lehet kiépíteni egy Cosmos DB szolgáltatást az Open Service Broker használatával az Azure-hoz és az alkalmazást az Azure fejlesztési tárolóhelyek az aks üzembe helyezése.

További információ a Cosmos DB, az Open Service Broker for Azure és az Azure fejlesztési szóközöket, lásd:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Az Open Service Broker for Azure](https://osba.sh)
* [Fejlesztés a fejlesztési tárolóhelyek használatával](../dev-spaces/azure-dev-spaces.md)
