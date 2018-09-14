---
title: Java-alkalmazás létrehozása a Service Fabricben az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy megbízható szolgáltatású Java-alkalmazást a kezelőfelület segítségével, hogyan hozhat létre egy megbízható állapotalapú háttérszolgáltatást, majd hogyan helyezheti üzembe az alkalmazást egy fürtön.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/01/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: e4552157cab846356c57a135d4e273f5a545bce9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667217"
---
# <a name="tutorial-create-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service-on-service-fabric"></a>Oktatóanyag: Alkalmazás létrehozása egy Java Web API kezelőfelületi szolgáltatás és egy állapotalapú háttérszolgáltatás segítségével a Service Fabricben

Ez az oktatóanyag egy sorozat első része. Az útmutató elvégzése után rendelkezni fog egy webes kezelőfelületű Java-szavazóalkalmazással, amely egy, a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat. Az oktatóanyag elvégzésének egyik feltétele, hogy rendelkezzen egy működő Mac OSX vagy Linux fejlesztői géppel. Ha nem szeretné manuálisan létrehozni a szavazóalkalmazást, akkor [letöltheti a forráskódot a kész alkalmazáshoz](https://github.com/Azure-Samples/service-fabric-java-quickstart), és folytathatja a [mintául szolgáló szavazóalkalmazás bemutatásával](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application).

![Szavazóalkalmazás – helyi](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Állapotalapú megbízható Java-szolgáltatás létrehozása
> * Állapot nélküli Java-webalkalmazásszolgáltatás létrehozása
> * Szolgáltatás távelérésének lehetővé tétele az állapotalapú szolgáltatással folytatott kommunikációhoz
> * Alkalmazás üzembe helyezése helyi Service Fabric-fürtön

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Service Fabric Reliable Services Java-alkalmazás létrehozása
> * [Az alkalmazás üzembe helyezése és hibakeresése egy helyi fürtön](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Alkalmazás üzembe helyezése egy Azure-fürtön](service-fabric-tutorial-java-deploy-azure.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-java-elk.md)
> * [CI/CD beállítása](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Állítsa be a fejlesztési környezetet [Mac](service-fabric-get-started-mac.md) vagy [Linux](service-fabric-get-started-linux.md) rendszerhez. Az utasításokat követve telepítse a következőket: Eclipse beépülő modul, Gradle, Service Fabric SDK és Service Fabric parancssori felület (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>A kezelőfelületi állapotmentes Java-szolgáltatás létrehozása

Először hozza létre a szavazóalkalmazás webes kezelőfelületét. Az állapotmentes Java-szolgáltatás kiépít egy, az AngularJS által működtetett webes felhasználói felületet üzemeltető egyszerűsített HTTP-kiszolgálót. A felhasználók kéréseit ez az állapotmentes szolgáltatás dolgozza fel, és távoli eljáráshívásként küldi tovább az állapotmentes szolgáltatásnak, hogy az tárolja a szavazatokat. 

1. Indítsa el az Eclipse-t.

2. Hozzon létre egy projektet a **File (Fájl)**->**New (Új)**->**Other (Egyéb)**->**Service Fabric (Service Fabric)**->**Service Fabric Project (Service Fabric-project)** használatával.

    ![Az Eclipse Új projekt párbeszédpanelje](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. A **ServiceFabric Project Wizard** (ServiceFabric-projekt varázsló) párbeszédpanelén adja a projektnek a **Voting** nevet, és kattintson a **Next** (Tovább) gombra.

    ![Az állapotmentes Java-szolgáltatás kiválasztása az Új szolgáltatás párbeszédpanelen](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. Az **Add Service** (Szolgáltatás hozzáadása) oldalon válassza ki a **Stateless Service** (Állapotmentes szolgáltatás) elemet, majd adja a szolgáltatásnak a **VotingWeb** nevet. A projekt létrehozásához kattintson a **Finish** (Befejezés) gombra.

    ![Állapotmentes szolgáltatás létrehozása]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Az Eclipse létrehoz egy alkalmazás- és egy szolgáltatásprojektet, és megjeleníti őket a Package Explorerben.

    ![Az Eclipse Package Explorer folytatja az alkalmazás létrehozását]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

A táblázat röviden ismerteti az előző képernyőképen látható Package Explorer összes elemét. 
| **Package Explorer-elem** | **Leírás** |
| --- | --- |
| PublishProfiles | A helyi és Azure-beli Service Fabric-fürtök profilrészleteit ismertető JSON-fájlokat tartalmazza. A fájlok tartalmát a beépülő modul használja az alkalmazás üzembe helyezésekor. |
| Scripts | Olyan segítő szkripteket tartalmaz, amelyek segítségével a parancssorról gyorsan kezelhető az alkalmazás egy fürttel. |
| VotingApplication | A Service Fabric-fürtön közzétett Service Fabric-alkalmazást tartalmazza. |
| VotingWeb | A kezelőfelületi állapotmentes szolgáltatás forrásfájljait és a kapcsolódó Gradle-buildfájlt tartalmazza. |
| build.gradle | A projekt kezeléséhez használt Gradle-fájl. |
| settings.gradle | Az ebben a mappában lévő Gradle-projektek nevét tartalmazza. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>HTML és Javascript hozzáadása a VotingWeb szolgáltatáshoz

Az állapotmentes szolgáltatás által renderelhető felhasználói felület hozzáadásához vegyen fel egy HTML-fájlt a *VotingApplication/VotingWebPkg/Code* alatt. Ezt a HTML-fájlt aztán az állapotmentes Java-szolgáltatásba ágyazott egyszerűsített HTTP-kiszolgáló jeleníti meg.

1. A *VotingApplication/VotingWebPkg/Code* eléréséhez bontsa ki a *VotingApplication* könyvtárat.

2. Kattintson a jobb gombbal a *Code* könyvtárra, majd kattintson a **New (Új)**->**Other (Egyéb)** elemre.

3. A mappának adja a *wwwroot* nevet, majd kattintson a **Finish (Befejezés)** gombra.

    ![Az Eclipse wwwroot mappája](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Adjon hozzá egy **index.html** nevű fájlt a **wwwroot** mappához, és illessze be az alábbi tartalmakat ebbe a fájlba.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="http://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']); 
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
          {},
          ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {            
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}        
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });        
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>A VotingWeb.java fájl frissítése

A **VotingWeb** alprojektben nyissa meg a *VotingWeb/src/statelessservice/VotingWeb.java* fájlt. A **VotingWeb** szolgáltatás az átjáró az állapotmentes szolgáltatásba, és ez felelős az előtérbeli API kommunikációs figyelőjének beállításáért.

Cserélje le a fájl **createServiceInstanceListeners** metódusának tartalmát a következőre, majd mentse a módosításokat.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();
    
    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>A HTTPCommunicationListener.java fájl hozzáadása

A HTTP-kommunikáció figyelője úgy működik, mint egy vezérlő, amely beállítja a HTTP-kiszolgálót, és elérhetővé teszi a szavazási műveleteket meghatározó API-kat. A *VotingWeb/src/statelessservice* mappában kattintson a jobb gombbal a *statelessservice* mappára, majd válassza a **New->File (Új->Fájl)** lehetőséget.  A fájlnak adja a *HttpCommunicationListener.java* nevet, majd kattintson a **Finish** (Befejezés) gombra.

Cserélje le a fájl tartalmát a következőkkel, majd mentse a módosításokat.  A későbbiekben, [a HttpCommunicationListener.java fájl frissítését](#updatelistener_anchor) ismertető szakaszban ez a fájl úgy módosul, hogy a háttérszolgáltatásból a szavazás adatait renderelni, olvasni és írni is tudja.  Egyelőre a figyelő egyszerűen a szavazóalkalmazás statikus HTML-jét adja vissza.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404; 
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";  
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0); 
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated 
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();
    
                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {    
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }  
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>A figyelőport konfigurálása

A VotingWeb szolgáltatás kezelőfelületi szolgáltatás létrehozásakor a Service Fabric véletlenszerűen kiválaszt egy portot, amelyen a szolgáltatás a figyelést végzi.  A VotingWeb szolgáltatás az alkalmazás kezelőfelületeként működik, és fogadja a külső forgalmat, úgyhogy rendeljük ezt a szolgáltatást egy rögzített, jól ismert porthoz. A Package Explorerben nyissa meg a *VotingApplication/VotingWebPkg/ServiceManifest.xml* fájlt.  Keresse meg az **Endpoint** (Végpont) erőforrást a **Resources** (Erőforrások) szakaszban, és módosítsa a **Port** értékét 8080-ra, vagy egy másik portra. Az alkalmazás helyi üzembe helyezéséhez és futtatásához az alkalmazásfigyelő-portnak a számítógépen megnyitva és elérhető állapotban kell lennie. A **ServiceManifest** elemen belül illessze be az alábbi kódrészletet (közvetlenül az ```<DataPackage>``` elem alá).

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Állapotalapú háttérszolgáltatás hozzáadása az alkalmazáshoz

Most, hogy a webes Java API-szolgáltatás váza elkészült, haladjunk tovább, és fejezzük be az állapotalapú háttérszolgáltatást.

A Service Fabric megbízható gyűjtemények használatával konzisztens módon és megbízhatóan tárolja az adatokat a szolgáltatásban. A megbízható gyűjtemények magas rendelkezésre állású és megbízható gyűjteményosztályok. Az osztályok használatát mindenki jól ismerheti, aki már használt Java-gyűjteményeket.

1. A Package Explorerben kattintson a jobb gombbal a **Voting** elemre az alkalmazásprojektben, és válassza a **Service Fabric > Add Service Fabric Service** (Service Fabric > Service Fabric-szolgáltatás hozzáadása) lehetőséget.

2. Az **Add Service** (Szolgáltatás hozzáadása) párbeszédpanelen válassza a **Stateful Service** (Állapotalapú szolgáltatás) lehetőséget, nevezze el a szolgáltatást (**VotingDataService**), majd kattintson az **Add Service** (Szolgáltatás hozzáadása) elemre.

    A szolgáltatásprojekt létrehozása után az alkalmazás két szolgáltatást fog tartalmazni. Miközben létrehozza az alkalmazást, hasonlóképpen adhat hozzá további szolgáltatásokat. Mindegyik – a többitől függetlenül – verziószámmal ellátható és frissíthető.

3. Az Eclipse létrehoz egy szolgáltatásprojektet, és megjeleníti a Package Explorerben.

    ![Megoldáskezelő](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>A VotingDataService.java fájl hozzáadása

A *VotingDataService.java* fájl tartalmazza a szavazatok lekérésének, hozzáadásának és a megbízható gyűjteményekből való törlésének logikáját tartalmazó metódusokat. Adja hozzá az alábbi **VotingDataService** osztálymetódusokat a *VotingDataService/src/statefulservice/VotingDataService.java* fájlhoz.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List; 
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext; 

import rpcmethods.VotingRPC; 

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;
    
    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();
        
        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        
        return listeners;
    }
    
    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {                    

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
                        
            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue()); 
            }
            
            tx.close();                    
            

        } catch (Exception e) {
            e.printStackTrace();
        }  
        
        return CompletableFuture.completedFuture(tempMap);
    }
    
    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1); 

        try {
            
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();                    
            
            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;                            
                    return Integer.toString(numVotes);
                }
            }).get(); 
            
            tx.commitAsync().get();
            tx.close(); 

            status.set(1);                            
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1); 
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();
            
            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();                    
            
            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        return CompletableFuture.completedFuture(new Integer(status.get()));
    }
    
}
```

## <a name="create-the-communication-interface-to-your-application"></a>Az alkalmazás kommunikációs interfészének létrehozása

Elkészült a kezelőfelületi állapotmentes szolgáltatás és a háttérszolgáltatás váza. A következő lépés a két szolgáltatás összekapcsolása. A kezelőfelületi és a háttérszolgáltatás is használ egy VotingRPC nevű interfészt, amely meghatározza a szavazóalkalmazás műveleteit. Ezt az interfészt a kezelőfelület és a háttérszolgáltatás is implementálja, hogy lehetővé váljanak a távoli eljáráshívások (RPC) a két szolgáltatás között. Minthogy az Eclipse nem támogatja Gradle-alprojektek felvételét, az interfészt tartalmazó csomagot manuálisan kell felvenni.

1. Kattintson a Package Explorerben a jobb gombbal a **Voting** projektre, és kattintson a **New -> Folder** (Új -> Mappa) elemre. A mappának adja a **VotingRPC/src/rpcmethods** nevet.

    ![VotingRPC csomag létrehozása](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. Hozzon létre egy fájlt a *Voting/VotingRPC/src/rpcmethods* területen *VotingRPC.java* néven, és illessze be az alábbiakat a **VotingRPC.java** fájlba. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ``` 

4. Hozzon létre egy *build.gradle* nevű fájlt a *Voting/VotingRPC* könyvtárban, és illessze be a fájlba az alábbiakat. Ezzel a Gradle-fájllal állítható össze és hozható létre a .jar-fájl, amelyet más szolgáltatások importálnak. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar'
    ```

5. A *Voting/settings.gradle* fájlban adjon hozzá egy sort, amelyben szerepel a build újonnan létrehozott projektje. 

    ```gradle 
    include ':VotingRPC'
    ```

6. A *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* fájlban a megjegyzésblokkot cserélje a következőre.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {                    
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
                
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));                   
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();     
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");                    
                
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
                
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");
                
                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1) 
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Vegye fel a megfelelő importálási utasítást a *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* fájl elején. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

Ezen a ponton kész a kezelőfelület, a háttérszolgáltatás és az RPC-interfészek működése. A következő feladat a Gradle-szkriptek megfelelő konfigurálása a Service Fabric-fürtön való üzembe helyezés előtt. 

## <a name="walk-through-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás bemutatása
A szavazóalkalmazás két szolgáltatásból áll:
- Webes kezelőfelületi szolgáltatás (VotingWeb) – Olyan webes kezelőfelületi Java-szolgáltatás, amely kiszolgálja a weboldalt, és API-kat biztosít a háttérszolgáltatással folytatott kommunikációhoz.
- Háttérszolgáltatás (VotingDataService) – Olyan webes Java-szolgáltatás, amely meghatározza a távoli eljáráshívások (RPC) révén a szavazatok megőrzésére meghívott metódusokat.

![Alkalmazásdiagram](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

Amikor végrehajt egy műveletet az alkalmazásban (elem hozzáadása, szavazás, elem eltávolítása), az alábbi események történnek:
1. A JavaScript HTTP-kérésként elküldi a megfelelő kérést a webes API-nak a webes kezelőfelületi szolgáltatásban.

2. A webes kezelőfelületi szolgáltatás a Service Fabric beépített Szolgáltatás távelérésének lehetővé tétele funkciójával megkeresi és továbbítja a kérést a háttérszolgáltatásnak. 

3. A háttérszolgáltatás az eredményeket egy megbízható szótárban tároló metódusokat határoz meg. Ennek a megbízható szótárnak a tartalmát a rendszer több csomóponton replikálja, és megőrzi a lemezen. Az alkalmazás összes adata a fürtön tárolódik. 

## <a name="configure-gradle-scripts"></a>Gradle-szkriptek konfigurálása 

Ebben a szakaszban a projekthez tartozó Gradle-szkriptek konfigurálása történik meg. 

1. Cserélje le a *Voting/build.gradle* fájl tartalmát az alábbiakra.

    ```gradle 
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    } 
        
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Cserélje le a *Voting/VotingWeb/build.gradle* fájl tartalmát az alábbiakra.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ``` 

3. Cserélje le a *Voting/VotingDataService/build.gradle* fájl tartalmát. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
        
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
        
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA' 
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Alkalmazás üzembe helyezése helyi fürtön

Ezen a ponton az alkalmazás készen áll egy helyi Service Fabric-fürtön való üzembe helyezésre.

1. Kattintson a jobb gombbal a Package Explorerben a **Voting** projektre, és kattintson a **Service Fabric -> Build Application** (Service Fabric -> Alkalmazás létrehozása) elemre az alkalmazás létrehozásához.

2. Futtassa a helyi Service Fabric-fürtöt. Ez a lépés a fejlesztési környezettől (Mac vagy Linux) függ.

    Ha Mac gépet használ, a helyi fürtöt az alábbi paranccsal futtathatja: cserélje le a **-v** paraméterben átadott parancsot a saját munkaterületére.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ``` 

    Ha Linux gépen végzi a futtatást, az alábbi paranccsal indíthatja el a helyi fürtöt: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

4. Az Eclipse Package Explorerében kattintson a jobb gombbal a **Voting** projektre, és kattintson a **Service Fabric -> Publish Application …** (Service Fabric -> Alkalmazás közzététele…) elemre. 
5. A **Publish Application** (Alkalmazás közzététele) ablakban válassza ki a **Local.json** elemet a legördülő listából, és kattintson a **Publish** (Közzététel) elemre.
6. Lépjen a webböngészőjébe, és nyissa meg a **http://localhost:8080** címet, ahol megtekintheti a helyi Service Fabric-fürtön futó alkalmazását. 

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Java-szolgáltatás létrehozása állapotalapú megbízható szolgáltatásként
> * Java-szolgáltatás létrehozása állapot nélküli webszolgáltatásként
> * Java-interfész felvétele a távoli eljáráshívások (RPC) szolgáltatások közötti kezeléséhez
> * A Gradle-szkriptek konfigurálása
> * Az alkalmazás összeállítása és üzembe helyezése a helyi Service Fabric-fürtön

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Alkalmazások hibakeresése és naplózása helyi fürtön](service-fabric-tutorial-debug-log-local-cluster.md)
