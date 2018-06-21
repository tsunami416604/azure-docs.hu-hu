---
title: Service Fabric-tárolóalkalmazás csomagolása és üzembe helyezése | Microsoft Docs
description: Az oktatóanyag azt ismerteti, hogyan hozhat létre egy Azure Service Fabric-alkalmazásdefiníciót a Yeoman használatával, és hogyan csomagolhatja be az alkalmazást.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, tárolók, mikroszolgáltatások, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b98c1995aa9051029049711ece18aeb23c444f1a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642794"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application"></a>Oktatóanyag: tárolók csomagolása és üzembe helyezése Service Fabric-alkalmazásként

Ez az oktatóanyag egy sorozat második része. Az oktatóanyag azt ismerteti, hogyan lehet létrehozni Service Fabric-alkalmazásdefiníciót egy sablonkészítő eszközzel (Yeoman). Az alkalmazással ezután tárolókat helyezhet üzembe a Service Fabric rendszerében. Ezen oktatóanyag segítségével megtanulhatja a következőket: 

> [!div class="checklist"]
> * A Yeoman telepítése  
> * Alkalmazáscsomag létrehozása a Yeoman használatával
> * Az alkalmazáscsomag beállításainak konfigurálása a tárolókkal való használathoz
> * Az alkalmazás létrehozása  
> * Az alkalmazás üzembe helyezése és futtatása 
> * Az alkalmazás fölösleges elemeinek az eltávolítása

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Container Registrybe leküldött, az oktatóanyag-sorozat [1. részében](service-fabric-tutorial-create-container-images.md) létrehozott tárolórendszerképek.
- [Beállított](service-fabric-tutorial-create-container-images.md) Linux fejlesztési környezet.

## <a name="install-yeoman"></a>A Yeoman telepítése
A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyekkel alkalmazásokat hozhat létre a terminálról a Yeoman sablongenerátor használatával. Az alábbi lépések végrehajtásával ellenőrizze, hogy rendelkezik-e Yeoman sablongenerátorral. 

1. Telepítse a node.js és az NPM eszközt a gépre. Vegye figyelembe, hogy a Mac OSX-felhasználóknak a Homebrew csomagkezelőt kell használniuk.

    ```bash
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash –
    sudo apt-get install -y nodejs 
    ```
2. Telepítse a Yeoman sablongenerátort a gépre az NPM-ből. 

    ```bash
    sudo npm install -g yo
    ```
3. Telepítse a Service Fabric Yeoman tárológenerátort.

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Docker-rendszerképtároló becsomagolása a Yeomannal

1. Service Fabric-tárolóalkalmazás létrehozásához futtassa a következő parancsot a klónozott adattár „container-tutorial” könyvtárában.

    ```bash
    yo azuresfcontainer
    ```
2. Adja meg a „TestContainer” nevet az alkalmazás neveként.
3. Adja meg az „azurevotefront” nevet az alkalmazásszolgáltatás neveként.
4. Adja meg az előtérbeli adattár tárolórendszerkép-útvonalát az ACR-ben – például „\<acrName>.azurecr.io/azure-vote-front:v1”. Az \<acrName > mezőnek egyeznie kell az előző oktatóanyagban használt értékkel.
5. Nyomja le az Enter billentyűt, hogy a Parancsok szakasz üres maradjon.
6. A példányszám legyen 1.

A yo parancs futtatásának bemenete és kimenete az alábbiak szerint fog kinézni:

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

Ha egy másik tárolószolgáltatást szeretne hozzáadni a Yeoman használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Módosítsa a könyvtár első szintjét a **TestContainer** könyvtárra, például *./TestContainer*
2. Futtassa a `yo azuresfcontainer:AddService` parancsot. 
3. Adja a szolgáltatásnak az „azurevoteback” nevet.
4. Adja meg a Redis tárolórendszerkép-útvonalát: „alpine:redis”.
5. Nyomja le az Enter billentyűt, hogy a Parancsok szakasz üres maradjon.
6. Adja meg az „1” példányszámát.

A használt szolgáltatás hozzáadásának bejegyzései:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Az oktatóanyag fennmaradó részében a **TestContainer** könyvtárban fogunk dolgozni. Például: *./TestContainer/TestContainer*. A könyvtár tartalmának az alábbinak kell lennie.
```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Az alkalmazásjegyzék konfigurálása az Azure Container Registry hitelesítő adataival
Ahhoz, hogy a Service Fabric le tudja kérni a tárolórendszerképeket az Azure Container Registryből, meg kell adnia a hitelesítő adatokat az **ApplicationManifest.xml** fájlban. 

Jelentkezzen be az ACR-példányba. Használja az **az acr login** parancsot a művelet befejezéséhez. Adja meg a tárolóregisztrációs adatbázis egyedi nevét, amelyet a létrehozásakor adott meg.

```bash
az acr login --name <acrName>
```

A parancs a **Bejelentkezés sikeres** üzenetet adja vissza, ha befejeződött.

Ezután futtassa a következő parancsot a tárolóregisztrációs adatbázis jelszavának lekéréséhez. A Service Fabric ezt a jelszót használja ahhoz, hogy hitelesíteni tudjon az ACR-rel, és le tudja kérni a tárolórendszerképeket.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

Az **ApplicationManifest.xml** fájlban adja a kódrészletet a kezelőfelületi szolgáltatás **ServiceManifestImport** eleméhez. Illessze be az **acrName** nevét az **AccountName** mezőbe. A rendszer a **Jelszó** mezőben az előző parancs által visszaadott jelszót használja. A dokumentum végén talál egy teljes **ApplicationManifest.xml** fájlt. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>A kommunikáció és a tárolóport–gazdagépport hozzárendelés konfigurálása

### <a name="configure-communication-port"></a>Kommunikációs port konfigurálása

Konfiguráljon egy olyan HTTP-végpontot, amelyen az ügyfelek kommunikálhatnak a szolgáltatással. Nyissa meg a *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* fájlt, és adjon meg egy végponterőforrást a **ServiceManifest** elemben.  Adja hozzá a protokoll, a port és a név adatokat. Ebben az oktatóanyagban a szolgáltatás a 80-as portot figyeli. Az alábbi kódrészlet a *ServiceManifest* címke alá kerül az erőforrásban.
  
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
  
A háttérszolgáltatás szolgáltatásjegyzékét is módosítsa. Nyissa meg a *./TestContainer/azurevotebackPkg/ServiceManifest.xml* fájlt, és adjon meg egy végponterőforrást a **ServiceManifest** elemben. Ebben az oktatóanyagban megmarad a Redis alapértelmezett 6379-es értéke. Az alábbi kódrészlet a *ServiceManifest* címke alá kerül az erőforrásban.

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
Az **UriScheme** megadásával a tároló végpontja automatikusan regisztrálva lesz a Service Fabric elnevezési szolgáltatásban, így felderíthető lesz. A cikk végén talál egy például szolgáló teljes ServiceManifest.xml fájlt a háttérszolgáltatáshoz. 

### <a name="map-container-ports-to-a-service"></a>Tárolóportok leképezése egy szolgáltatáshoz
Ahhoz, hogy elérhetővé tegye a tárolókat a fürt számára, egy portkötést is létre kell hoznia az „ApplicationManifest.xml” fájlban. A **PortBinding** szabályzat a **ServiceManifest.xml** fájlokban meghatározott **végpontokra** hivatkozik. Az ezen végpontokra érkező kérések az itt megnyitott és lekötött tárolóportokra lesznek leképezve. Az **ApplicationManifest.xml** fájlban adja meg a következő kódot, hogy a 80-as és a 6379-es portot a végpontokhoz kösse. A dokumentum végén talál egy teljes **ApplicationManifest.xml** fájlt. 
  
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

### <a name="add-a-dns-name-to-the-backend-service"></a>DNS-név hozzáadása a háttérszolgáltatáshoz
  
Ahhoz, hogy a Service Fabric ezt a DNS-nevet a háttérszolgáltatáshoz rendelje, meg kell adni a nevet az **ApplicationManifest.xml** fájlban. Adja a **ServiceDnsName** attribútumot a **Service** elemhez az itt látható módon: 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

A kezelőfelületi szolgáltatás beolvas egy környezeti változót, hogy megtudja a Redis-példány DNS-nevét. Ez a környezeti változó már meg van határozva a Docker-rendszerkép létrehozásához használt Docker-fájlban, így külön műveletet nem kell elvégezni.
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
A következő kódrészletet bemutatja, hogyan szerzi be az előtérbeli Python-kód a Docker-fájlban leírt környezeti változót. Ehhez nem kell külön műveletet elvégezni. 

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Az oktatóanyag ezen pontján üzembe lehet helyezni egy szolgáltatáscsomag-alkalmazás sablonját a fürtön. A következő oktatóanyagban ezt az alkalmazást fogjuk üzembe helyezni és futtatni egy Service Fabric-fürtben.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása
Az alkalmazás Azure-fürtön történő üzembe helyezéséhez hozzon létre egy saját fürtöt.

A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök. Ezeket a Service Fabric csapata üzemelteti, és bárki üzembe helyezhet rajtuk alkalmazásokat, illetve megismerkedhet a platform használatával. A nyilvános fürt eléréséhez [kövesse az alábbi utasításokat](http://aka.ms/tryservicefabric). 

Ha kezelési műveleteket szeretne végrehajtani a biztonságos fél fürtjén, használhatja a Service Fabric Explorert, a parancssori felületet vagy a Powershellt. A Service Fabric Explorer használatához le kell töltenie a PFX-fájlt a nyilvános fürt webhelyéről, és importálnia kell a tanúsítványt a tanúsítványtárolóba (Windows vagy Mac) vagy a böngészőbe (Ubuntu). A nyilvános fürtből származó önaláírt tanúsítványoknak nincs jelszavuk. 

Ha kezelési műveleteket szeretne végrehajtani a Powershell-lel vagy a parancssori felületről, szüksége lesz a következőkre: PFX (Powershell) vagy PEM (parancssori felület). A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot:  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

További információk saját fürtök létrehozásáról: [Service Fabric-fürt létrehozása az Azure-on](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Az alkalmazás felépítése és üzembe helyezése a fürtön
A Service Fabric parancssori felületével helyezheti üzembe az alkalmazást az Azure-fürtben. Ha a Service Fabric parancssori felülete nincs telepítve a gépre, kövesse az [itt](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) található utasításokat a telepítéséhez. 

Csatlakozzon az Azure Service Fabric-fürthöz. Cserélje le a példában szereplő végpontot a sajátjára. A végpontnak az alábbihoz hasonló teljes URL-címnek kell lennie.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

Használja a **TestContainer** könyvtárban megadott telepítési szkriptet, hogy az alkalmazáscsomagot a fürt lemezképtárolójába másolja, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

```bash
./install.sh
```

Nyisson meg egy böngészőt, majd navigáljon a Service Fabric Explorerre a http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer helyen. Bontsa ki az alkalmazások csomópontját, és figyelje meg, hogy megjelenik benne egy bejegyzés az alkalmazás típusához, egy másik pedig a példányhoz.

![Service Fabric Explorer][sfx]

Ha a futó alkalmazáshoz szeretne csatlakozni, nyisson meg egy webböngészőt, és lépjen a fürt URL-címére, például: http://lin0823ryf2he.cloudapp.azure.com:80. A webes felhasználói felületen a szavazóalkalmazásnak kell megjelennie.

![szavazóalkalmazás][votingapp]

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Használja a sablonban megadott eltávolítási szkriptet az alkalmazáspéldánynak a fürtről történő törléséhez, és törölje az alkalmazástípus regisztrációját. A parancsnak némi időre van szüksége, hogy kiürítse a példányt, ezért az „install.sh” parancs nem futtatható azonnal a szkript után. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Példák teljes jegyzékfájlokra

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

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml 
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

Ebben az oktatóanyagban több tárolót csomagolt egy Service Fabric-alkalmazásba a Yeoman használatával, majd az alkalmazást üzembe helyezte és futtatta egy Service Fabric-fürtön. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * A Yeoman telepítése  
> * Alkalmazáscsomag létrehozása a Yeoman használatával
> * Az alkalmazáscsomag beállításainak konfigurálása a tárolókkal való használathoz
> * Az alkalmazás létrehozása  
> * Az alkalmazás üzembe helyezése és futtatása 
> * Az alkalmazás fölösleges elemeinek az eltávolítása

Folytassa a következő oktatóanyaggal, amely az alkalmazás feladatátvételét és méretezését ismerteti a Service Fabricben.

> [!div class="nextstepaction"]
> [Tudnivalók az alkalmazások feladatátvételéről és méretezéséről](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


