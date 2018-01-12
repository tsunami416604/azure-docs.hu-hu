---
title: "Csomag és a Service Fabric-tárolók alkalmazás központi telepítése |} Microsoft Docs"
description: "Útmutató az Azure Service Fabric-alkalmazás használatával Yeoman definícióját létrehozásához, és az alkalmazás becsomagolása."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, tárolók, mikroszolgáltatások létrehozására, a Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: caa7f58860c4540fa6914b1c0f0cfcba437468fa
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="package-and-deploy-containers-as-a-service-fabric-application"></a>Csomag és a Service Fabric-alkalmazásként tároló üzembe helyezése

Ez az oktatóanyag egy sorozat két részre. Ebben az oktatóanyagban egy sablon készítő eszközzel (Yeoman) használja a Service Fabric definíciót készítéséhez. Ez az alkalmazás ezután használható a Service Fabric – tárolók üzembe helyezése. Ezen oktatóanyag segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * Yeoman telepítése  
> * Hozzon létre egy alkalmazáscsomagot, Yeoman használatával
> * Adja meg az alkalmazáscsomag tárolók való használatra beállításait
> * Az alkalmazás létrehozása  
> * Telepítheti és futtathatja az alkalmazást 
> * Az alkalmazás törlése

## <a name="prerequisites"></a>Előfeltételek

- A tároló képek leküldött létrehozott Azure-tároló beállításjegyzék [1. rész](service-fabric-tutorial-create-container-images.md) az oktatóanyagban szereplő is használ.
- Linux-fejlesztési környezet [beállítása](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Yeoman telepítése
A Service fabric állványok eszközöket Yeoman használó alkalmazások létrehozása sablon generátor biztosít. Kövesse az alábbi lépéseket, hogy a Yeoman sablon generátor. 

1. Nodejs és NPM telepítse a számítógépre. Vegye figyelembe, hogy, Mac OS x-felhasználók kell használnia a package manager Homebrew

    ```bash
    sudo apt-get install npm && sudo apt install nodejs-legacy
    ```
2. Yeoman telepítése sablon generátor NPM a számítógépen 

    ```bash
    sudo npm install -g yo
    ```
3. A Service Fabric Yeoman tároló generátor telepítése

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Docker-rendszerképtároló becsomagolása a Yeomannal

1. A Service Fabric-tároló létrehozása alkalmazás, a klónozott tárház "tároló-oktatóanyag" könyvtárban a következő parancsot.

    ```bash
    yo azuresfcontainer
    ```
2. Adja meg az "TestContainer" az alkalmazás neve
3. Adja meg az "azurevotefront" a szolgáltatás neve.
4. Adja meg például a tároló lemezképének elérési útja ACR az előtér - tárház "\<acrName >.azurecr.io / azure-szavazat-első: 1-es verzió". A \<acrName > mező, amelyet az előző oktatóanyag használt értékeként azonosnak kell lennie.
5. Nyomja le az ENTER billentyűt, hogy a parancsok szakasz üres.
6. Adjon meg egy példányszámot, 1.

A következő példa a bemeneti és kimeneti való futtatásának a yo parancsot:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Egy másik tárolószolgáltatás Yeoman már létrehozott alkalmazáshoz való hozzáadásához hajtsa végre az alábbi lépéseket:

1. Módosítsa a könyvtárat egy szintet a **TestContainer** címtár, például *. / TestContainer*
2. Futtassa a `yo azuresfcontainer:AddService` parancsot. 
3. A azurevoteback szolgáltatás neve
4. Adja meg a tároló kép elérési Redis - "alpine: redis'
5. Nyomja le az ENTER billentyűt, hogy a parancsok szakasz üres
6. Adja meg az „1” példányszámát.

A bejegyzések hozzáadásához használja a szolgáltatás összes látható:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Ez az oktatóanyag a hátralévő dolgozunk ennek az **TestContainer** könyvtár. Például *./TestContainer/TestContainer*. A könyvtár tartalmának az alábbinak kell lennie.
```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Az alkalmazás jegyzékében hitelesítő adatokkal rendelkező Azure tároló beállításjegyzék konfigurálása
A Service Fabric való lekérésére a tároló képek Azure tároló beállításjegyzékből, adja meg a hitelesítő adatokat kell a **ApplicationManifest.xml**. 

Jelentkezzen be a ACR példányát. Használja a **az acr bejelentkezési** parancs használatával végrehajtani a műveletet. Adjon meg egyedi név, a tároló beállításjegyzék létrehozásakor.

```bash
az acr login --name <acrName>
```

A parancs visszaadja a **sikeres bejelentkezés** üzenet amint befejeződött.

Ezután futtassa a következő parancs használatával beszerezheti a jelszót a tároló beállításjegyzék. Ezt a jelszót használják a Service Fabric ACR való lekérésére a tároló lemezképeket a hitelesítéséhez.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

Az a **ApplicationManifest.xml**, a kódrészletet alatt adja hozzá a **ServiceManifestImport** elem az előtér-szolgáltatás. Helyezze be a **acrName** a a **AccountName** mező, és a jelszót, az előző parancs által visszaadott szolgál a **jelszó** mező. Teljes **ApplicationManifest.xml** valósul meg ez a dokumentum végén. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>A kommunikáció és a tárolóport–gazdagépport hozzárendelés konfigurálása

### <a name="configure-communication-port"></a>Kommunikációs port konfigurálása

Konfiguráljon egy olyan HTTP-végpontot, amelyen az ügyfelek kommunikálhatnak a szolgáltatással. Nyissa meg a *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* fájlt, és egy végpont erőforrás deklarálja a **ServiceManifest** elemet.  Adja hozzá a protokoll, a port és a név adatokat. A szolgáltatás ebben az oktatóanyagban 80-as porton figyel. Az alábbi kódrészletben alatt helyezkedik el, a *ServiceManifest* az erőforrás-címke.
  
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
  
Hasonlóképpen módosítsa a háttérszolgáltatáshoz Service Manifest. Nyissa meg a *./TestContainer/azurevotebackPkg/ServiceManifest.xml* és egy végpont erőforrás deklarálja a **ServiceManifest** elemet. Ebben az oktatóanyagban a redis alapértelmezett 6379 megmarad. Az alábbi kódrészletben alatt helyezkedik el, a *ServiceManifest* az erőforrás-címke.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```
Így a **UriScheme**automatikusan regisztrálja a tároló végpont a Service Fabric-szolgáltatás felderítése a. A teljes ServiceManifest.xml példafájlja a háttérszolgáltatáshoz példaként, ez a cikk végén valósul meg. 

### <a name="map-container-ports-to-a-service"></a>Tároló leképezik a szolgáltatáshoz
Ahhoz, hogy teszi közzé a tárolók a fürtben, azt is kell létrehoznia a "ApplicationManifest.xml" port kötést. A **PortBinding** házirend hivatkozik a **végpontok** a meghatározott a **ServiceManifest.xml** fájlokat. Ezeket a végpontokat bejövő kérelmek tároló portok nyitva, és amelyet itt beolvasása leképezve. Az a **ApplicationManifest.xml** fájlt, az alábbi kódot a végpontok port a 80-as és 6379 kötődni. Teljes **ApplicationManifest.xml** érhető el ez a dokumentum végén. 
  
```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>A DNS-név felvétele a háttérszolgáltatáshoz
  
A Service Fabric által a háttérszolgáltatáshoz a DNS-név hozzárendelni, a nevének kell megadni: a **ApplicationManifest.xml**. Adja hozzá a **ServiceDnsName** attribútumot a **szolgáltatás** elem látható módon: 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Az előtér-szolgáltatás beolvassa a környezeti változó tudni, hogy a Redis-példány DNS-nevét. A környezeti változó már definiálva van a Dockerfile a Docker-lemezkép létrehozásához használt, és semmilyen műveletet meg kell itt kell venni.
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
A következő kódrészletet mutatja be, hogy az előtér-Python kódját hogyan szerzi be a környezeti változó a Dockerfile ismertetett. Nincs művelet kell itt kell venni. 

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Ezen a ponton az oktatóanyagban a sablon egy szolgáltatáscsomag alkalmazáshoz érhető el egy fürt üzembe helyezése. Az ezt követő oktatóanyag az alkalmazás telepítve van, és a Service Fabric-fürt futtatásakor.

## <a name="create-a-service-fabric-cluster"></a>A Service Fabric-fürt létrehozása
Az alkalmazás Azure-fürtön történő üzembe helyezéséhez használhat egy saját vagy egy nyilvános fürtöt is.

A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök. A Service Fabric csapat, ahol a további tudnivalók a platform bárki és alkalmazások telepítése üzemeltet. A nyilvános fürt eléréséhez [kövesse az alábbi utasításokat](http://aka.ms/tryservicefabric). 

További információk saját fürtök létrehozásáról: [Service Fabric-fürt létrehozása az Azure-on](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Hozza létre és telepítse központilag az alkalmazást a fürthöz
Az Azure-fürttel a Service Fabric parancssori felület használatával telepítheti az alkalmazást. Ha a Service Fabric parancssori felület nem települ a számítógépre, kövesse az utasításokat [Itt](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) a telepítéshez. 

Csatlakozzon az Azure Service Fabric-fürthöz. Cserélje le a helyőrző végpont az Ön által. A végpont egy teljes URL-címet az alábbihoz hasonló kell lennie.

```bash
sfctl cluster select --endpoint <http://lin4hjim3l4.westus.cloudapp.azure.com:19080>
```

A megadott telepítési parancsfájl használata a **TestContainer** directory másolja az alkalmazáscsomagot a fürt lemezképtárolóhoz, az alkalmazástípus regisztrálása és az alkalmazás egy példányának létrehozásakor.

```bash
./install.sh
```

Nyisson meg egy böngészőt, és keresse meg a Service Fabric Explorerben talál a következő http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer. Bontsa ki az alkalmazások csomópontot, és vegye figyelembe, hogy nincs-e az alkalmazás típusának bejegyzése és egy másik példány.

![Service Fabric Explorer][sfx]

Ahhoz, hogy a futó alkalmazás csatlakozzon, nyisson meg egy webböngészőt, és nyissa meg a fürt URL-cím - például http://lin0823ryf2he.cloudapp.azure.com:80. A webes felhasználói felület Voting alkalmazási kell megjelennie.

![votingapp][votingapp]

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. Ennek a parancsnak a példány karbantartása egy ideig, és a "install.sh" utasítás nem futtatható közvetlenül után ezt a parancsfájlt. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Befejezett jegyzékfájlokban példák

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies> 
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies> 
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Előtér-ServiceManifest.xml 
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
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
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
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
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```
## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban több tároló volt csomagolja a rendszer a Service Fabric-alkalmazás Yeoman használatával. Ez az alkalmazás ezután központilag telepített és futtassa a Service Fabric-fürt. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Yeoman telepítése  
> * Hozzon létre egy alkalmazáscsomagot, Yeoman használatával
> * Adja meg az alkalmazáscsomag tárolók való használatra beállításait
> * Az alkalmazás létrehozása  
> * Telepítheti és futtathatja az alkalmazást 
> * Az alkalmazás törlése

A következő oktatóanyag feladatátvételi és annak az alkalmazásnak a Service Fabric skálázás továbblépés.

> [!div class="nextstepaction"]
> [További tudnivalók a feladatátvétel és az alkalmazások méretezése](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


