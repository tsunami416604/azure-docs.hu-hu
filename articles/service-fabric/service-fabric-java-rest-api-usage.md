---
title: Az Azure Service Fabric Java ügyfél API-k |} A Microsoft Docs
description: Hozzon létre, és a Service Fabric Java-ügyfél API-k használata a Service Fabric ügyfél REST API-specifikációnak használatával
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 116defb43126932c1a9ce0e7a9d588e731abff78
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182030"
---
# <a name="azure-service-fabric-java-client-apis"></a>Az Azure Service Fabric Java ügyfél API-k

A Service Fabric-ügyfél API-k lehetővé teszi, hogy üzembe helyezése és kezelése a mikroszolgáltatás-alapú alkalmazásokat és tárolókat egy Service Fabric-fürtön az Azure-on alapuló, a helyszínen, a helyi fejlesztői gépen, vagy más felhőben. Ez a cikk azt ismerteti, hogyan hozhat létre, illetve a Service Fabric Java-ügyfél API-k fölött a Service Fabric-ügyfél REST API-k használata

## <a name="generate-the-client-code-using-autorest"></a>Az AutoRest használatával Ügyfélkód generálása

[AutoRest](https://github.com/Azure/autorest) egy eszköz, amely létrehozza a klienskódtárak RESTful webes szolgáltatásokhoz való hozzáférését. AutoRest bemenete egy szabvány, amely ismerteti a REST API OpenAPI-specifikáció formátumban. [A Service Fabric ügyfél REST API-k](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) hajtsa végre a specifikációnak.

Kövesse az alábbi lépéseket az AutoRest eszközzel Service Fabric Java-Ügyfélkód generálása.

1. A node.js és az NPM telepítése a gépre

    Ha Linux majd használ:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Ha Mac OS X majd használ:
    ```bash
    brew install node
    ```

2. Telepítse az NPM segítségével AutoRest.
    ```bash
    npm install -g autorest
    ```

3. Elágazást, mind a Klónozás [azure – rest-api-specifikáció](https://github.com/Azure/azure-rest-api-specs) -tárház a helyi számítógépen, majd lépjen a klónozott helyét, a gépen a terminálból.


4. Nyissa meg a helyet a klónozott tárház a fentiekben említett.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Ha nem a fürt verziója 6.0. * Keresse meg a megfelelő könyvtárra a stabil mappában.
    >   

5. Futtassa a következő autorest parancsot a java-Ügyfélkód generálásához.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Alább látható egy példa, autorest használatának bemutatásához.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   A következő parancsot a veszi ``servicefabric.json`` specifikáció bemeneti fájlt, és a java ügyféloldali kódot állít elő ``java-rest-api-     code`` mappába helyezi a kódot, és ``servicefabricrest`` névtér. Ez a lépés után két mappát találjuk ``models``, ``implementation`` és a két fájlt ``ServiceFabricClientAPIs.java`` és ``package-info.java`` létrehozott a ``java-rest-api-code`` mappa.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Közé tartozik, és a generált ügyfél használja a projektben

1. Adja hozzá a generált kódot megfelelő projektbe a Finderből. Azt javasoljuk, hogy hozzon létre egy könyvtárat a generált kóddal, és ez a tár tartalmazza a projekt.
2. Ha egy könyvtárat hoz létre, akkor tartalmazza a következő függőséget a szalagtár-projektben. Ha az egy másik megközelítés hozzáadhatja a függőségi megfelelően.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Például, ha a buildelési rendszert, többek között az alábbiak a Maven használata a ``pom.xml`` fájlt:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Hozzon létre egy RestClient, a következő kód használatával:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Az ügyfél objektum használja, és szükség szerint a megfelelő hívásokat. Íme néhány példa, amely ügyfélobjektummal használatát mutatja be. Feltételezzük, hogy az alkalmazáscsomag készített, és feltölti a lemezképtároló használata előtt az alábbi API-k.
    * Alkalmazás üzembe helyezése
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Alkalmazás létrehozása

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>A generált kódot ismertetése
Minden API-ja található megvalósítási négy túlterheléssel. Ha vannak a választható paraméterek: ezután találjuk négy további változata, beleértve a választható paraméterek. Például érdemes lehet az API-t ``removeReplica``.
 1. **nyilvános void removeReplica (karakterlánc nodeName, UUID partitionId, karakterlánc replicaId, logikai forceRemove, hosszú időkorlátja)**
    * Ez az a szinkron változó a removeReplica API-hívás
 2. **nyilvános ServiceFuture<Void> removeReplicaAsync (karakterlánc-nodeName, UUID partitionId, replicaId karakterláncot, logikai forceRemove, hosszú időtúllépés, végső ServiceCallback<Void> serviceCallback)**
    * Ez az API-hívás változat használható, ha használja a jövőbeli alapján aszinkron programozás és visszahívások használni kívánt
 3. **nyilvános rendszernek megfigyelhetőnek<Void> removeReplicaAsync (karakterlánc nodeName, UUID partitionId, karakterlánc replicaId)**
    * Ez az API-hívás változat használható, ha a reaktív jellegű aszinkron programozás használni kívánt
 4. **nyilvános rendszernek megfigyelhetőnek < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (karakterlánc nodeName, UUID partitionId, karakterlánc replicaId)**
    * Ez mérettel bővült, API-hívás is használható, ha azt szeretné, használhatja a reaktív jellegű aszinkron programozás és foglalkozik a nyers rest-válasz

## <a name="next-steps"></a>További lépések
* Ismerje meg [Service Fabric – REST API-k](https://docs.microsoft.com/rest/api/servicefabric/)

