---
title: Azure Service Fabric Java ügyfél API-k
description: Service Fabric Java-ügyfél API-k létrehozása és használata a Service Fabric-ügyfél REST API-specifikációjának használatával
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451667"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java ügyfél API-k

A Service Fabric-ügyfél API-k lehetővé teszik a mikroszolgáltatásokon alapuló alkalmazások és tárolók üzembe helyezését és kezelését az Azure-beli Service Fabric-fürtben, a helyszínen, a helyi fejlesztőgépen vagy más felhőben. Ez a cikk ismerteti, hogyan hozhat létre és használhat Service Fabric Java ügyfél API-kat a Service Fabric-ügyfél REST API-k tetején

## <a name="generate-the-client-code-using-autorest"></a>Az ügyfélkód létrehozása az AutoRest használatával

[Az AutoRest](https://github.com/Azure/autorest) egy olyan eszköz, amely ügyfélkönyvtárakat hoz létre a RESTful webszolgáltatások eléréséhez. Az AutoRest bemenete egy olyan specifikáció, amely az OpenAPI Specifikáció formátumban írja le a REST API-t. [A Service Fabric ügyfél REST API-k](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) megfelelnek ennek a specifikációnak.

Kövesse az alábbi lépéseket a Service Fabric Java ügyfélkód generálásához az AutoRest eszközzel.

1. A node.js és az NPM telepítése a gépre

    Ha Linuxot használsz, akkor:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Ha Mac OS X-et használ, akkor:
    ```bash
    brew install node
    ```

2. Telepítse az Automatikus restámat az NPM használatával.
    ```bash
    npm install -g autorest
    ```

3. Elágazás és [klónozás abszkna-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) tárház a helyi gépen, és a klónozott helyre a terminál a gép.


4. Lépjen az alább említett helyre a klónozott tárházban.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Ha a fürt verziója nem 6.0.* akkor lépjen a stabil mappa megfelelő könyvtárába.
    >   

5. Futtassa a következő autorest parancsot a java ügyfélkód létrehozásához.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Az alábbiakban egy példa mutatja be a használatát autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   A következő ``servicefabric.json`` parancs a specifikációs fájlt bemenetként ``java-rest-api-     code`` veszi fel, és ``servicefabricrest`` java ügyfélkódot hoz létre a mappában, és a névtérbe foglalja a kódot. Ezt a lépést követően ``models``két ``implementation`` mappát ``ServiceFabricClientAPIs.java`` ``package-info.java`` és két ``java-rest-api-code`` fájlt talál, és a mappában keletkezik.


## <a name="include-and-use-the-generated-client-in-your-project"></a>A létrehozott ügyfél felvétele és használata a projektbe

1. Adja hozzá a létrehozott kódot megfelelően a projekthez. Javasoljuk, hogy hozzon létre egy tárat a létrehozott kód használatával, és foglalja bele ezt a könyvtárat a projektbe.
2. Ha könyvtárat hoz létre, akkor a következő függőséget foglalja bele a tár projektjébe. Ha egy másik megközelítést követ, majd a függőség megfelelően tartalmazza.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Ha például a Maven buildrendszert használja, ``pom.xml`` a fájlba a következőket adja meg:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Hozzon létre egy RestClient-t a következő kód dal:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Használja az ügyfélobjektumot, és szükség szerint kezdeményezzen megfelelő hívásokat. Íme néhány példa, amelyek bemutatják az ügyfélobjektum használatát. Feltételezzük, hogy az alkalmazáscsomag van építve, és feltöltött a képtárba, mielőtt az alábbi API-t.
    * Alkalmazás kiépítése
    
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

## <a name="understanding-the-generated-code"></a>A létrehozott kód ismertetése
Minden API-hoz négy túlterhelést talál a megvalósításhoz. Ha vannak választható paraméterek, akkor további négy változatot talál, beleértve azokat a választható paramétereket is. Vegyük például ``removeReplica``az API-t.
 1. **nyilvános void removeReplica(String nodeName, UUID partitionId, String replicaId, Logikai forceRemove, Hosszú időszám-szám)**
    * Ez az removeReplica API-hívás szinkron változata
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean\<forceRemove, Long timeout, final ServiceCallback Void> serviceCallback)**
    * Az API-hívás ezen változata akkor használható, ha jövőbeli aszinkron programozást és visszahívásokat szeretne használni
 3. **nyilvános megfigyelhető érvénytelenítési\<> removeReplicaAsync(Karakterlánc-csomópontnév, UUID partitionId, String replicaId)**
    * Az API-hívás ezen változata akkor használható, ha reaktív aszinkron programozást szeretne használni
 4. **nyilvános megfigyelhető\<\<ServiceResponse void>> removeReplicaWithServiceResponseAsync(Karakterlánc-nodeName, UUID partitionId, String replicaId)**
    * Az API-hívás ezen változata akkor használható, ha reaktív aszinkron programozást szeretne használni, és a RAW többi válaszával szeretne foglalkozni.

## <a name="next-steps"></a>További lépések
* További információ a [Service Fabric REST API-król](https://docs.microsoft.com/rest/api/servicefabric/)

