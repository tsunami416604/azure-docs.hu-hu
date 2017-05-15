---
title: "Azure Service Fabric-tárolóapp létrehozása | Microsoft Docs"
description: "Hozza létre első saját tárolóappját az Azure Service Fabricban.  Az alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tároló-beállításjegyzékbe, és összeállíthat és üzembe helyezhet egy Service Fabric-tárolóappot."
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
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: df5832478bf7049a7bb883eacac4be961ef65cf9
ms.contentlocale: hu-hu
ms.lasthandoff: 05/09/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Az első Service Fabric-tárolóapp létrehozása
A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítani az alkalmazást. Ez a rövid útmutató végigvezeti a webappokat tartalmazó Docker-rendszerképek létrehozásának, az új rendszerkép Azure Container Registrybe való leküldésének, a Service Fabric-tárolóappok létrehozásának, illetve a tárolóappok Service Fabric-fürtökön való üzembe helyezésének folyamatán.  A cikk feltételezi, hogy rendelkezik a Docker használatára vonatkozó alapvető ismeretekkel. A Docker megismeréséhez olvassa el a [Docker áttekintő ismertetését](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Előfeltételek
Egy fejlesztői számítógép, amelyen a következők futnak:
* Visual Studio 2015 vagy Visual Studio 2017.
* [Service Fabric SDK és -eszközök](service-fabric-get-started.md).
*  Windows rendszerhez készült Docker.  [A Docker CE for Windows (stable) letöltése](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

Egy Windows-fürt legalább három, Windows Server 2016 rendszerű, a Containerst futtató csomóponttal. Ehhez [hozzon létre egy fürtöt](service-fabric-get-started-azure-cluster.md) vagy [próbálja ki ingyen a Service Fabricot](http://tryazureservicefabrictest.westus.cloudapp.azure.com/). 

Egy Azure Container Registry-beállításjegyzék – ehhez [hozzon létre egy tároló-beállításjegyzéket](../container-registry/container-registry-get-started-portal.md) Azure-előfizetésében. 

## <a name="create-a-simple-web-app"></a>Egyszerű webalkalmazás létrehozása
Gyűjtse össze a Docker-rendszerképbe betölteni kívánt összes objektumot egy helyre. A jelen rövid útmutató céljára hozzon létre egy „Hello World” webalkalmazást a fejlesztői számítógépen.

1. Hozzon létre egy könyvtárat, például *c:\temp\helloworldapp*.
2. Hozzon létre egy alkönyvtárat: *c:\temp\helloworldapp\content*.
3. Hozzon létre egy *index.html* fájlt a *c:\temp\helloworldapp\content* alkönyvtárban.
4. Módosítsa az *index.html* fájlt, és adja hozzá a következő sort:
    ```
    <h1>Hello World!</h1>
    ```
5. Mentse az *index.html* módosításait.

## <a name="build-the-docker-image"></a>A Docker-rendszerkép összeállítása
Állítson össze egy rendszerképet a Docker Hubban található [microsoft/iis rendszerkép](https://hub.docker.com/r/microsoft/iis/) alapján. A microsoft/iis rendszerkép a Windows Server Core operációs rendszer alaprendszerképéből van származtatva, és tartalmazza az Internet Information Services (IIS) szolgáltatást.  Ha futtatja a rendszerképet a tárolóban, ez automatikusan elindítja az IIS szolgáltatást és a telepített webhelyeket.

Definiálja a Docker-rendszerképet egy Docker-fájlban. A Docker-fájl tartalmazza a rendszerkép összeállítására és a futtatni kívánt alkalmazás betöltésére vonatkozó információkat. A Docker-fájl a ```docker build``` parancs bemenete, amely a rendszerképet létrehozza. 

1. Hozza létre a *Dockerfile* nevű fájlt (fájlkiterjesztés nélkül) a *c:\temp\helloworldapp* mappában, és adja hozzá a következőket:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Ebben a Docker-fájlban nincs ```ENTRYPOINT``` parancs, és nincs is szükség rá. Amikor a Windows Servert az IIS szolgáltatással futtatja, az IIS-folyamat az a belépési pont, amely az alaprendszerkép indítására van konfigurálva.

    További információkért olvassa el a [Docker-fájlra vonatkozó referenciákat](https://docs.docker.com/engine/reference/builder/).

2. Futtassa a ```docker build``` parancsot a webalkalmazást futtató rendszerkép létrehozásához. Nyisson meg egy PowerShell-ablakot, és lépjen a *c:\temp\helloworldapp* mappára. Futtassa az alábbi parancsot:

    ```
    docker build -t helloworldapp .
    ```
    Ez a parancs létrehozza az új rendszerképet a Docker-fájlban foglalt utasítások alapján, és elnevezi (-t címkézés) a rendszerképet „helloworldapp”-nak. A rendszerképek készítése során a rendszer lekéri az alaprendszerképet a Docker Hubból, és létrehoz egy olyan új rendszerképet, amelyben az alkalmazás hozzá van adva az alaprendszerképhez.  A [microsft/iis rendszerkép](https://hub.docker.com/r/microsoft/iis/) és az operációs rendszer alaprendszerképe 10,5 GB méretű, és időt vesz igénybe, amíg letöltődnek és kicsomagolódnak a fejlesztői gépen.  Érdemes lehet addig egy kávé- vagy ebédszünetet beiktatni.  A letöltés kevesebb időt vesz igénybe, ha korábban már lekérte az operációs rendszer alaprendszerképét a fejlesztői számítógépre.

3. Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>A rendszerkép helyi futásának ellenőrzése
Ellenőrizze helyben a rendszerkép működését, mielőtt leküldené azt a tároló-beállításjegyzékbe.  

1. A tárolót a ```docker run``` paranccsal indíthatja el:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    A *name* nevet ad a futtató tárolónak (a tárolóazonosító helyett).

2. Miután a tároló elindult, keresse meg az IP-címét, hogy böngészőből is el tudja érni a futó tárolót:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Csatlakozzon a futó tárolóhoz.  Nyisson meg egy webböngészőt, majd a 8000-es porton visszaadott IP-címet, például „http://172.31.194.61:8000”. A „Hello World!” címsornak kell megjelennie a böngészőben.

4. A tároló leállításához futtassa a következő parancsot:

    ```
    docker stop my-web-site
    ```

5. Törölje a tárolót a fejlesztői gépről:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>A rendszerkép leküldése a tároló-beállításjegyzékbe
Miután ellenőrizte, hogy a tároló fut-e a fejlesztői gépen, küldje le a rendszerképet a beállításjegyzékébe az Azure Container Registryben.

1. Futtassa a(z) ``docker login`` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](../container-registry/container-registry-authentication.md).

    Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/active-directory-application-objects.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a ```samples``` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Küldje le a rendszerképet tároló-beállításjegyzékbe:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>A tárolóalapú szolgáltatás létrehozása és csomagolása a Visual Studióban
A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amelynek segítségével a tároló üzembe helyezhető egy Service Fabric-fürtben.

1. Indítsa el a Visual Studiót.  Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza a **Service Fabric application** (Service Fabric-alkalmazás) lehetőséget, nevezze el „MyFirstContainer” néven, és kattintson az **OK** gombra.
3. A **szolgáltatássablonok** listájában válassza a **Guest Container** (Vendégtároló) elemet.
4. Az **Image Name** (Rendszerkép neve) mezőben adja meg a „myregistry.azurecr.io/samples/helloworldapp” rendszerképet, amelyet leküldött a tároló-beállításjegyzékbe. 
5. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.
6. Ha a tárolóalapú szolgáltatáshoz szükség van egy kommunikációs végpontra, most hozzáadhatja a protokoll, a port és a típus adatait egy ```Endpoint``` objektumhoz a ServiceManifest.xml fájlban. Jelen rövid útmutatóban a tárolóalapú szolgáltatás a 80-as portot figyeli: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Az ```UriScheme``` megadásával a tároló végpontja automatikusan regisztrálva lesz a Service Fabric elnevezési szolgáltatásban, így felderíthető lesz. A cikk végén talál egy például szolgáló teljes ServiceManifest.xml fájlt. 
7. A tárolóport-gazdagépport leképezés konfigurálásához adjon meg egy ```PortBinding``` szabályzatot az ApplicationManifest.xml fájl ```ContainerHostPolicies``` elemében.  Jelen rövid útmutatóban a ```ContainerPort``` értéke 8000 (a tároló a 8000-es portot használja a Docker-fájlban foglalt beállítások szerint), az ```EndpointRef``` pedig „Guest1TypeEndpoint” (a szolgáltatásjegyzékben definiált végpont).  A szolgáltatáshoz a 80-as porton beérkező kérések a tárolón a 8000-es portra vannak leképezve.  Ha a tárolót hitelesíteni kell egy magántárolóval, adja hozzá a ```RepositoryCredentials``` elemet.  Jelen rövid útmutatóban a myregistry.azurecr.io tároló-beállításjegyzék fióknevét és jelszavát adja meg. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    A cikk végén talál egy például szolgáló teljes ApplicationManifest.xml fájlt.
8. Konfigurálja a fürt csatlakozási végpontját, hogy közzé tudja tenni az alkalmazást a fürtön.  Az ügyfél csatlakozási végpontját a fürt Áttekintés paneljén találja az [Azure Portalon](https://portal.azure.com). A Megoldáskezelőben nyissa meg a *Cloud.xml* fájlt a **MyFirstContainer**->**PublishProfiles** helyen.  Adja hozzá a fürt nevét és csatlakozási portját a **ClusterConnectionParameters** elemhez.  Példa:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Mentse az összes fájlt, és hozza létre a projektet.  

10. Az alkalmazás becsomagolásához kattintson a jobb gombbal a **MyFirstContainer** elemre a Megoldáskezelőben, és válassza a **Package** (Csomagolás) lehetőséget. 

## <a name="deploy-the-container-app"></a>A tárolóapp üzembe helyezése
1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a **MyFirstContainer** elemre a Megoldáskezelőben, és válassza a **Publish** (Közzététel) lehetőséget.

2. A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) egy webalapú eszköz az alkalmazások és csomópontok vizsgálatához és kezeléséhez a Service Fabric-fürtökben. Nyisson meg egy böngészőt, lépjen a http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ helyre, és folytassa az alkalmazás üzembe helyezését.  Az alkalmazás üzembe lesz helyezve, azonban hibaállapotban van, amíg a rendszerkép le nem töltődik a fürtcsomópontokra (ami a rendszerkép méretétől függően némi időt vehet igénybe):  ![Error][1] (Hiba)

3. Az alkalmazás akkor kész, amikor ```Ready``` állapotba kerül:  ![Ready][2] (Kész)

4. Nyisson meg egy böngészőt, majd navigáljon a http://containercluster.westus2.cloudapp.azure.com helyre. A „Hello World!” címsornak kell megjelennie a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
A fürt futtatása költségekkel jár, ezért érdemes lehet [törölni a fürtöt](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  A [nyilvános fürtök](http://tryazureservicefabrictest.westus.cloudapp.azure.com/) néhány óra múlva automatikusan törlődnek.

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen rövid útmutatóban használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Következő lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-dotnet-containers) a GitHubon.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

