---
title: Az Azure Service Fabric Java ügyfél API-k |} Microsoft Docs
description: Hozza létre, és a Service Fabric Java-ügyfél API-k használata a Service Fabric ügyfél REST API-specifikációnak használatával
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
ms.openlocfilehash: 987959742335940dca8eb57c54d593aea90dec15
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111184"
---
# <a name="azure-service-fabric-java-client-apis"></a>Az Azure Service Fabric Java ügyfél API-k

A Service Fabric ügyfél API-k lehetővé teszi, hogy a központi telepítésére és felügyeletére mikroszolgáltatások alapuló alkalmazások és a tárolók a Service Fabric-fürt Azure, a helyszíni, helyi fejlesztési számítógépen vagy más felhőben. Ez a cikk ismerteti, hogyan hozhat létre, és a Service Fabric Java-ügyfél API-k fölött a Service Fabric-ügyfélnek REST API-k használata

## <a name="generate-the-client-code-using-autorest"></a>Az ügyfél kód generálása használatával AutoRest

[AutoRest](https://github.com/Azure/autorest) olyan eszköz, amely RESTful webes szolgáltatások eléréséhez klienskódtárak segítségével hoz létre. Bemenet AutoRest az egy által kidolgozott szabvány, amely leírja a REST API OpenAPI Specification formátumban. [Service Fabric-ügyfélnek REST API-k](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) hajtsa végre ezt a beállítást.

Kövesse az alábbiakban az AutoRest eszközzel a Service Fabric Java Ügyfélkód generálásához leírt lépéseket.

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

3. Elágazás és a Klónozás [azure-rest-api-specifikáció](https://github.com/Azure/azure-rest-api-specs) a helyi számítógép és a klónozott hely nyissa meg a terminálból a számítógép-tárház.


4. Nyissa meg a helyet a klónozott tárház az alábbiakban leírt.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Ha a fürt verziószáma nem 6.0. * Keresse meg a megfelelő könyvtár az stabil mappában.
    >   

5. A következő parancsot autorest a java Ügyfélkód generálásához.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Alább látható egy példa, az autorest használatát, amely tartalmazza.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   A következő parancsot a vesz ``servicefabric.json`` specification bemenetként fájlt, és létrehozza a java-Ügyfélkód ``java-rest-api-     code`` mappa és a kód elfedi ``servicefabricrest`` névtér. Ez a lépés után két mappák találjuk ``models``, ``implemenation`` és két fájlt ``ServiceFabricClientAPIs.java`` és ``package-info.java`` hozott létre a ``java-rest-api-code`` mappát.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Közé tartoznak, és használja a generált ügyféllel a projektben

1. Adja hozzá a generált kódot megfelelően a projektben. Azt javasoljuk, hogy hozzon létre egy könyvtár a generált kódot, és ezt a szalagtárat a projekt tartalmazza.
2. Ha a könyvtár létrehozása a következő függőségi szerepeljenek a hordozhatóosztálytár-projektjének. Ha egy másik módszert szerepeltesse a függőség megfelelően.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Például, ha a buildelési rendszer adja meg a következőket a Maven használata a ``pom.xml`` fájlt:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Hozzon létre egy RestClient, a következő kódot:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Az ügyfél objektum használja, és szükség szerint a megfelelő hívásokat. Íme néhány példa, amelyek bemutatják, az ügyfél objektum használatát. Feltételezzük, hogy az alkalmazáscsomag összeállítása és használata előtt kép tárolóba feltöltött az alábbi API.
    * Az alkalmazás telepítéséhez
    
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
Minden API-hoz. a végrehajtás négy túlterheléssel találja. Ha a választható paraméterek: majd találjuk négy további változata, beleértve a nem kötelező paraméterek. Tegyük fel az API-t ``removeReplica``.
 1. **nyilvános "void" removeReplica (karakterlánc csomópontnév, UUID partitionId, karakterlánc Replikaazonosítójú, logikai forceRemove, hosszú időtúllépés)**
    * Ez az a removeReplica API-hívás szinkron változatát
 2. **nyilvános ServiceFuture<Void> removeReplicaAsync (karakterlánc-csomópontnév, UUID partitionId, karakterlánc Replikaazonosítójú, logikai forceRemove, hosszú időtúllépés, a végső ServiceCallback<Void> serviceCallback)**
    * Ez az API-hívás változat használható, ha a jövőbeli alapján aszinkron programozás használja, és a visszahívások használata
 3. **nyilvános megfigyelhető<Void> removeReplicaAsync (karakterlánc csomópontnév, UUID partitionId, karakterlánc Replikaazonosítójú)**
    * Ez az API-hívás változat használható, ha szeretné használni a reaktív aszinkron programozás
 4. **nyilvános megfigyelhető < ServiceResponse<Void>> removeReplicaWithServiceResponseAsync (karakterlánc csomópontnév, UUID partitionId, karakterlánc Replikaazonosítójú)**
    * Ez az API-hívás változat használható, ha reaktív aszinkron programozás használni kívánt nyers rest válasz kezelése

## <a name="next-steps"></a>További lépések
* További tudnivalók [Service Fabric REST API-k](https://docs.microsoft.com/rest/api/servicefabric/)

