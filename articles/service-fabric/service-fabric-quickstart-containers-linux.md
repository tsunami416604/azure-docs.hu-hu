---
title: "Azure Service Fabric-tárolóalkalmazás létrehozása Linuxon | Microsoft Docs"
description: "Hozza létre első saját, Linux-alapú tárolóalkalmazását az Azure Service Fabricban.  Az alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba, majd összeállíthat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: hu-hu
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Linux-alapú Service Fabric-tároló üzembe helyezése az Azure-on
Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

A meglévő alkalmazások Service Fabric-fürtökön lévő Linux-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Docker-tárolók előre összeállított rendszerképeit egy Service Fabric-alkalmazásban. Ha elkészült, rendelkezni fog egy futó Nginx-tárolóval.  Ez a rövid útmutató a Linux-tárolók üzembe helyezését mutatja be. A Windows-tárolók üzembe helyezését lásd [ebben a rövid útmutatóban](service-fabric-quickstart-containers.md).

![Nginx][nginx]

Ezen rövid útmutató segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Docker-rendszerképtároló becsomagolása
> * A kommunikáció konfigurálása
> * Service Fabric-alkalmazás felépítése és becsomagolása
> * A tárolóalkalmazás üzembe helyezése az Azure-on

## <a name="prerequisites"></a>Előfeltételek
Telepítse a következőket: [Service Fabric SDK, Service Fabric parancssori felület, Service Fabric Yeoman sablongenerátorok](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Docker-rendszerképtároló becsomagolása a Yeomannal
A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az alkalmazás létrehozását és egy tárolórendszerkép hozzáadását. 

Service Fabric-tárolóalkalmazás létrehozásához nyisson meg egy terminálablakot, és futtassa a következőt: `yo azuresfcontainer`.  

Nevezze el az alkalmazást „MyFirstContainer” néven, az alkalmazásszolgáltatást pedig „MyContainerService” néven.

Nevezze el a tároló rendszerképét „nginx:latest” névén (ez az [Nginx-tároló rendszerképe](https://hub.docker.com/r/_/nginx/) a Docker Hubon). 

Ez a rendszerkép meghatározott számításifeladat-belépési ponttal rendelkezik, így a bemeneti parancsokat explicit módon kell megadnia. 

Adja meg az „1” példányszámát.

![Tárolókhoz készült Service Fabric Yeoman-generátor][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>A kommunikáció és a tárolóport–gazdagépport hozzárendelés konfigurálása
Konfiguráljon egy olyan HTTP-végpontot, amelyen az ügyfelek kommunikálhatnak a szolgáltatással.  Nyissa meg a *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* fájlt, és adjon meg egy végponterőforrást a **ServiceManifest** elemben.  Adja hozzá a protokoll, a port és a név adatokat. Ebben a rövid útmutatóban a szolgáltatás a 80-as portot figyeli: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Az `UriScheme` megadásával a tároló végpontja automatikusan regisztrálva lesz a Service Fabric elnevezési szolgáltatásban, így felderíthető lesz. A cikk végén talál egy például szolgáló teljes ServiceManifest.xml fájlt. 

Végezze el egy tárolóport `Endpoint` szolgáltatásra történő leképezését egy `PortBinding` szabályzat használatával az ApplicationManifest.xml fájl `ContainerHostPolicies` elemében.  Ebben a rövid útmutatóban a(z) `ContainerPort` értéke 80 (a tároló a 80-as portot használja), a(z) `EndpointRef` értéke pedig „myserviceTypeEndpoint” (a szolgáltatásjegyzékben korábban definiált végpont).  A szolgáltatáshoz a 80-as porton beérkező kérések a tárolón a 80-as portra vannak leképezve.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Service Fabric-alkalmazás felépítése és becsomagolása
A Service Fabric Yeoman-sablonok tartalmaznak egy [Gradle](https://gradle.org/) felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához. Mentse az összes módosítást.  Az alkalmazás felépítéséhez és becsomagolásához futtassa a következő parancsot:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Fürt létrehozása
Az alkalmazás Azure-fürtön történő üzembe helyezéséhez létrehozhat egy saját fürtöt, vagy használhat nyilvános fürtöt is.

A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök, amelyek futtatását a Service Fabric csapata végzi, és amelyeken bárki üzembe helyezhet alkalmazásokat, és megismerkedhet a platform használatával. A nyilvános fürt eléréséhez [kövesse az alábbi utasításokat](http://aka.ms/tryservicefabric).  

További információk saját fürtök létrehozásáról: [Az első saját Service Fabric-fürt létrehozása az Azure-on](service-fabric-get-started-azure-cluster.md).

Jegyezze fel a kapcsolati végpont értékét, mert azt használni fogja a következő lépésben.

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás központi telepítése az Azure-ban
Az alkalmazást a létrehozása után a Service Fabric parancssori felülettel telepítheti az Azure-fürtben.

Csatlakozzon az Azure Service Fabric-fürthöz.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Használja a sablonban megadott telepítési szkriptet az alkalmazáscsomag a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

```bash
./install.sh
```

Nyisson meg egy böngészőt, és keresse fel a Service Fabric Explorert a http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer címen. Bontsa ki az Alkalmazások csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazása típusához, és egy másik a típus első példányához.

![Service Fabric Explorer][sfx]

Csatlakozzon a futó tárolóhoz.  Nyisson meg egy webböngészőt, majd a 80-as porton visszaadott IP-címet, például „lnxt10vkfz6.westus.cloudapp.azure.com:80”. Ekkor a Nginx kezdőlapjának kell megjelennie a böngészőben.

![Nginx][nginx]

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen rövid útmutatóban használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Következő lépések
Ezen rövid útmutató segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Docker-rendszerképtároló becsomagolása
> * A kommunikáció konfigurálása
> * Service Fabric-alkalmazás felépítése és becsomagolása
> * A tárolóalkalmazás üzembe helyezése az Azure-on

* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* Tekintse meg a [.NET-alkalmazás üzembe helyezését](service-fabric-host-app-in-a-container.md) ismertető oktatóanyagot.
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-dotnet-containers) a GitHubon.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

