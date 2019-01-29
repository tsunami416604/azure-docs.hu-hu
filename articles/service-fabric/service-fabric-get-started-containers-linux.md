---
title: Azure Service Fabric-tárolóalkalmazás létrehozása Linuxon | Microsoft Docs
description: Hozza létre első saját, Linux-alapú tárolóalkalmazását az Azure Service Fabricban. Az alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba, majd összeállíthat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/4/2019
ms.author: twhitney
ms.openlocfilehash: e91c6fcb7d1943136ab1c110dd81445ab43164a7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100836"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Az első Service Fabric-tárolóalkalmazás létrehozása Linux rendszeren
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

A meglévő alkalmazások Service Fabric-fürtökön lévő Linux-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a cikk ismerteti a Python [Flask](http://flask.pocoo.org/)-webalkalmazást tartalmazó Docker-rendszerképek létrehozását, illetve egy Service Fabric-fürtön való üzembe helyezését. Emellett meg is fogja osztani a tárolóalapú alkalmazást az [Azure Container Registry](/azure/container-registry/) használatával. A cikk feltételezi, hogy rendelkezik a Docker használatára vonatkozó alapvető ismeretekkel. A Docker megismeréséhez olvassa el a [Docker áttekintő ismertetését](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Ebben a cikkben egy Linux-fejlesztési környezetre vonatkozik.  A Service Fabric-fürt futtatókörnyezetének és a Docker-futtatókörnyezet az ugyanazon operációs rendszeren kell futnia.  Linux-tárolók nem futtatható a Windows-fürtön.

## <a name="prerequisites"></a>Előfeltételek
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * [Service Fabric SDK és -eszközök](service-fabric-get-started-linux.md).
  * [Linuxhoz készült Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric parancssori felület](service-fabric-cli.md)

* Egy Azure Container Registry-beállításjegyzék – ehhez [hozzon létre egy tároló-beállításjegyzéket](../container-registry/container-registry-get-started-portal.md) Azure-előfizetésében. 

## <a name="define-the-docker-container"></a>A Docker-tároló definiálása
Állítson össze egy rendszerképet a Docker Hubban található [Python-rendszerkép](https://hub.docker.com/_/python/) alapján. 

Adja meg a Docker-tárolót egy Docker-fájlban. A Docker-fájl a környezet tárolón belüli beállítására, a futtatni kívánt alkalmazás betöltésére és a portok hozzárendelésére vonatkozó utasításokat tartalmazza. A Docker-fájl a `docker build` parancs bemenete, amely a rendszerképet létrehozza. 

Hozzon létre egy üres könyvtárat és a *Docker-fájlt* (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

További információkért olvassa el a [Docker-fájlra vonatkozó referenciákat](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Alapszintű webalkalmazás létrehozása
Hozzon létre egy olyan Flask-webalkalmazást, amely a 80-as portot figyeli, és a „Hello World!” szöveget adja vissza. Ugyanebben a könyvtárban hozza létre a *requirements.txt* fájlt. Adja hozzá a következőket, és mentse a módosításokat:
```
Flask
```

Hozza létre az *app.py* fájlt, és adja hozzá a következő kódrészletet:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Rendszerkép létrehozása
Futtassa a(z) `docker build` parancsot a webalkalmazást futtató rendszerkép létrehozásához. Nyisson meg egy PowerShell-ablakot, és lépjen a *c:\temp\helloworldapp* mappára. Futtassa az alábbi parancsot:

```bash
docker build -t helloworldapp .
```

Ez a parancs létrehozza az új rendszerképet a Docker-fájlban foglalt utasítások alapján, és a `helloworldapp` nevet adja a rendszerképnek (-t címkézéssel). A tárolórendszerkép létrehozásához először az alaprendszerképet kell letöltenie a Docker Hubról, és ahhoz kell hozzáadnia az alkalmazást. 

Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása
Ellenőrizze a tárolóalapú alkalmazás helyi működését, mielőtt leküldené a tárolóregisztrációs adatbázisba. 

Futtassa az alkalmazást, amivel számítógép 4000-es portját a tároló elérhető 80-as portjához rendeli hozzá:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

A *name* nevet ad a futtató tárolónak (a tárolóazonosító helyett).

Csatlakozzon a futó tárolóhoz. Nyisson meg egy webböngészőt, majd a 4000-es porton visszaadott IP-címet, például: http://localhost:4000. A „Hello World!” címsornak kell megjelennie a böngészőben.

![Hello World!][hello-world]

A tároló leállításához futtassa a következő parancsot:

```bash
docker stop my-web-site
```

Törölje a tárolót a fejlesztői gépről:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>A rendszerkép leküldése a tároló-beállításjegyzékbe
Miután ellenőrizte, hogy az alkalmazás fut-e a Dockerben, küldje le a rendszerképet a regisztrációs adatbázisba az Azure Container Registryben.

Futtassa a(z) `docker login` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](../container-registry/container-registry-authentication.md).

Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy bejelentkezhet a beállításjegyzékhez tartozó felhasználónevével és jelszavával.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a `samples` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Küldje le a rendszerképet tároló-beállításjegyzékbe:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>A Docker-rendszerkép becsomagolása a Yeomannal
A Linux Service Fabric SDK tartalmaz egy [Yeoman](http://yeoman.io/)-generátort, amely megkönnyíti az alkalmazás létrehozását és egy tárolórendszerkép hozzáadását. Hozzunk létre egy alkalmazást, amely egyetlen, *SimpleContainerApp* nevű Docker-tárolóval rendelkezik.

Service Fabric-tárolóalkalmazás létrehozásához nyisson meg egy terminálablakot, és futtassa a következőt: `yo azuresfcontainer`. 

Adjon nevet az alkalmazásnak (például: `mycontainer`) és az alkalmazásszolgáltatásnak (például: `myservice`).

A rendszerkép neveként adja meg a tárolóregisztrációs adatbázisban található tároló rendszerképéhez tartozó URL-címet (például: „myregistry.azurecr.io/samples/helloworldapp”). 

Mivel ez a rendszerkép meghatározott számításifeladat-belépési ponttal rendelkezik, így nem kell explicit módon megadni a bemeneti parancsokat (tárolón belül futó parancsok, amelyek az indítás után biztosítják a tároló futtatását). 

Adja meg az „1” példányszámát.

A megfelelő formátumban adja meg a portleképezést. Ez a cikk meg kell adnia ```80:4000``` a port-hozzárendeléséhez. Ezzel konfigurálta, hogy minden olyan bejövő kérések várható a 4000-es portot a gazdagépen a rendszer átirányítja a tárolón a 80-as porton.

![Tárolókhoz készült Service Fabric Yeoman-generátor][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Tároló tárolóadattár hitelesítésének konfigurálása
 Ha a tárolót hitelesíteni kell egy magántárolóval, adja hozzá a `RepositoryCredentials` elemet. Ebben a cikkben a myregistry.azurecr.io tárolóregisztrációs adatbázis fióknevét és jelszavát adja meg. Ügyeljen arra, hogy a szabályzat hozzá legyen adva a „ServiceManifestImport” címke alatt, és a megfelelő szervizcsomagra vonatkozzon.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 

Azt javasoljuk, hogy titkosítsa az adattár jelszavát. Tekintse meg [ kezelése a Service Fabric-alkalmazásokat a titkosított titkos kulcsok](service-fabric-application-secret-management.md) útmutatást.

### <a name="configure-cluster-wide-credentials"></a>Fürtre kiterjedő hitelesítő adatainak konfigurálása
Tekintse meg [itt található dokumentáció](
service-fabric-get-started-containers.md#configure-cluster-wide-credentials)

## <a name="configure-isolation-mode"></a>Az elkülönítési mód konfigurálása
A 6.3 runtime kiadása, virtuális gépek elkülönítés Linux-tárolók, ezáltal támogatása a tárolók két elkülönítési módok esetén támogatott: folyamata, és a Hyper-v. A Hyper-v folyamatelkülönítési módban az kernelei elkülönülnek a tárolók és a tároló gazdagép között. A Hyper-v-elkülönítés használatával lett megvalósítva [egyértelmű tárolók](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker). Az elkülönítési mód van megadva, a Linux-fürtök a `ServicePackageContainerPolicy` eleme az Alkalmazásjegyzék-fájl. A megadható elkülönítési módok a következők: `process`, `hyperv` és `default`. Az alapértelmezett érték folyamatelkülönítési módban. A következő kódrészlet azt mutatja be, hogyan van határozható meg az elkülönítési mód az alkalmazásjegyzék-fájlban.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Az erőforrás-szabályozás konfigurálása
Az [erőforrás-szabályozás](service-fabric-resource-governance.md) korlátozza a tároló által a gazdagépen használható erőforrásokat. Az alkalmazásjegyzékben megadott `ResourceGovernancePolicy` elemmel határozhatók meg erőforráskorlátok a szolgáltatások kódcsomagjaihoz. Erőforráskorlátok állíthat be az alábbi forrásanyagokat: A memória, MemorySwap, CpuShares (CPU relatív súlya), MemoryReservationInMB, BlkioWeight (BlockIO relatív súlya). Ebben a példában a Guest1Pkg szolgáltatáscsomag egy magot kap a fürtcsomópontokon, amelyekre el van helyezve. A memóriakorlátok abszolútak, ezért a kódcsomag 1024 MB memóriára van korlátozva (és ugyanennyi a gyenge garanciás foglalás). A kódcsomagok (tárolók vagy folyamatok) nem tudnak ennél a korlátnál több memóriát lefoglalni, és ennek megkísérlése memóriahiány miatti kivételt eredményez. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK konfigurálása 
A 6.1-es verzióval kezdődően a Service Fabric automatikusan integrálja a [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) eseményeket a rendszerállapot-jelentésbe. Ez azt jelenti, hogy ha a tárolón engedélyezett a **HEALTHCHECK**, a Service Fabric jelenti az állapotát, valahányszor a tároló állapota módosul a Docker jelentése szerint. Egy **OK** állapotjelentés jelenik meg a [Service Fabric Explorerben](service-fabric-visualizing-your-cluster.md), amikor a *health_status* értéke *healthy* (megfelelő), és egy **WARNING** jelenik meg, ha a *health_status* értéke *unhealthy* (nem megfelelő). A tároló állapotának monitorozása céljából ténylegesen elvégzett ellenőrzésre mutató **HEALTHCHECK** utasításnak szerepelnie kell a tárolórendszerkép létrehozásához használt Docker-fájlban. 

![HealthCheckHealthy][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

A **HEALTHCHECK** viselkedését konfigurálhatja az egyes tárolókhoz, ha megadja a **HealthConfig** beállításait a **ContainerHostPolicies** részeként az ApplicationManifest fájlban.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Alapértelmezés szerint az *IncludeDockerHealthStatusInSystemHealthReport* beállítása **true**, és a *RestartContainerOnUnhealthyDockerHealthStatus* beállítása **false**. Ha a *RestartContainerOnUnhealthyDockerHealthStatus* beállítása **true**, egy újra és újra nem megfelelő állapotúnak jelentett tároló újraindul (lehetőleg más csomópontokon).

Ha az egész Service Fabric-fürthöz le szeretné tiltani a **HEALTHCHECK** integrációját, az [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) elemet **false** értékre kell állítania.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazást a létrehozása után a Service Fabric parancssori felülettel telepítheti a helyi fürtben.

Csatlakozzon a helyi Service Fabric-fürthöz.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Használja a sablonban megadott telepítési szkriptet az alkalmazáscsomag a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

```bash
./install.sh
```

Nyisson meg egy böngészőt, és keresse fel a Service Fabric Explorert a következő címen: http://localhost:19080/Explorer (a Vagrant Mac OS X rendszeren való használata esetében a localhost helyett használja a virtuális gép magánhálózati IP-címét). Bontsa ki az Alkalmazások csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazása típusához, és egy másik a típus első példányához.

Csatlakozzon a futó tárolóhoz. Nyisson meg egy webböngészőt, majd a 4000-es porton visszaadott IP-címet, például: http://localhost:4000. A „Hello World!” címsornak kell megjelennie a böngészőben.

![Hello World!][hello-world]


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldány helyi fejlesztési fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját.

```bash
./uninstall.sh
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen cikkben használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>További szolgáltatások hozzáadása meglévő alkalmazáshoz

Ha egy másik tárolószolgáltatást szeretne hozzáadni a Yeoman használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Lépjen a meglevő alkalmazás gyökérkönyvtárába. Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa a `yo azuresfcontainer:AddService` parancsot.

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>A tároló kényszerített leállítását megelőző időköz beállítása

Konfigurálhat egy időintervallumot a futtatókörnyezet számára, ezzel megadva, hogy az mennyit várjon a tároló eltávolítása előtt, miután megkezdődött a szolgáltatás törlése (vagy másik csomópontba áthelyezése). Az időintervallum konfigurálásával a `docker stop <time in seconds>` parancsot küldi a tárolónak.  További információ: [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). A várakozási időköz a `Hosting` szakaszban van meghatározva. Az alábbi fürtjegyzék kódrészlete azt mutatja be, hogyan adható meg a várakozási időköz:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Az alapértelmezett időintervallum 10 másodperc. Mivel ez egy dinamikus konfiguráció, a csak konfigurációs frissítés a fürtön frissíti az időkorlátot. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Futtatókörnyezet konfigurálása a nem használt tárolórendszerképek eltávolításához

A Service Fabric-fürtöt úgy is konfigurálhatja, hogy eltávolítsa a nem használt tárolórendszerképeket a csomópontról. Ez a konfiguráció lehetővé teszi a lemezterület visszanyerését, ha túl sok tárolórendszerkép található a csomóponton. A funkció engedélyezéséhez frissítse a fürtjegyzék `Hosting` szakaszát az alábbi kódrészletben látható módon: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

A `ContainerImagesToSkip` paraméternél megadhatja azokat a rendszerképeket, amelyeket nem szabad törölni. 

## <a name="configure-container-image-download-time"></a>Tárolórendszerkép letöltési idejének konfigurálása

A Service Fabric futtatókörnyezete 20 percet foglal le a tárolórendszerképek letöltésére és kicsomagolására, és ez a tárolórendszerképek többségénél elegendő is. Nagyobb rendszerképek esetében, vagy ha a hálózati kapcsolat lassú, szükséges lehet növelni a rendszerkép letöltésének és kibontásának megszakításáig rendelkezésre álló időtartamot. Ez az időtúllépési érték a **ContainerImageDownloadTimeout** attribútummal állítható be a fürtjegyzék **Üzemeltetés** szakaszában, az alábbi kódrészletben látható módon:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása

A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, a rendszer nem őriz meg tárolókat. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>A Docker-démon indítása egyéni argumentumokkal

A Service Fabric-futtatókörnyezet 6.2-es vagy újabb verzióiban a Docker-démon egyéni argumentumokkal is elindítható. Ha egyéni argumentumok vannak megadva, a Service Fabric csak a `--pidfile` argumentumot továbbítja a Docker-motornak. A `--pidfile` argumentumként való megadása ezért nem szükséges. Emellett az argumentumnak továbbra is meg kell határoznia, hogy a Docker-démon figyelje a Windows (vagy Linux esetén a Unix-tartománycsatorna) nevesített csövét, hogy a Service Fabric kommunikálni tudjon a démonnal. Az egyéni argumentumok a **ContainerServiceArguments** **Üzemeltetés** szakaszában, a fürtjegyzékben adhatók meg. Az alábbi kódrészletben látható erre egy példa: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* Tekintse meg a [.NET-alkalmazás üzembe helyezését](service-fabric-host-app-in-a-container.md) ismertető oktatóanyagot.
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-containers) a GitHubon.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
