---
title: Windows-alapú Azure Service Fabric-tárolóalkalmazás létrehozása | Microsoft Docs
description: Ebben a gyors útmutatóban létrehozza az első saját, Windows-alapú tárolóalkalmazását az Azure Service Fabricban.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/18
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: eb4de9d7781ae355e42a6fec9f7732ad67228e70
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>Gyors útmutató: Windows-alapú Service Fabric-tároló üzembe helyezése az Azure-on
Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Docker-tárolók előre összeállított rendszerképeit egy Service Fabric-alkalmazásban. Ha elkészült, rendelkezni fog egy futó Windows Server 2016 Nano Server- és IIS-tárolóval. Ez a rövid útmutató a Windows-tárolók üzembe helyezését mutatja be. A Linux-tárolók üzembe helyezését lásd [ebben a rövid útmutatóban](service-fabric-quickstart-containers-linux.md).

![Az IIS alapértelmezett webhelye][iis-default]

Ezen rövid útmutató segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Docker-rendszerképtároló becsomagolása
> * A kommunikáció konfigurálása
> * Service Fabric-alkalmazás felépítése és becsomagolása
> * A tárolóalkalmazás üzembe helyezése az Azure-on

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés (létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * Visual Studio 2015 vagy Visual Studio 2017.
  * [Service Fabric SDK és -eszközök](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Docker-rendszerképtároló becsomagolása a Visual Studióval
A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amelynek segítségével a tároló üzembe helyezhető egy Service Fabric-fürtben.

Indítsa el a Visual Studiót „rendszergazdaként”.  Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

Válassza a **Service Fabric application** (Service Fabric-alkalmazás) lehetőséget, nevezze el „MyFirstContainer” néven, és kattintson az **OK** gombra.

A **Hosted Containers and Applications** (Üzemeltetett tárolók és alkalmazások) sablonokból válassza a **Container** (Tároló) elemet.

A **Rendszerkép neve** mezőbe írja be a „microsoft/iis:nanoserver” karaktersort, amely a [Windows Server Nano Server- és IIS-alaprendszerképet](https://hub.docker.com/r/microsoft/iis/) jelöli. 

Nevezze el a szolgáltatást „MyContainerService” néven, majd kattintson az **OK** gombra.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>A kommunikáció és a tárolóport–gazdagépport hozzárendelés konfigurálása
A szolgáltatás igénybevételéhez szükség van egy kommunikációs végpontra.  Ebben a rövid útmutatóban a tárolóalapú szolgáltatás a 80-as portot figyeli.  Nyissa meg a *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml* fájlt a Megoldáskezelőben.  Frissítse a meglévő `Endpoint` elemet a ServiceManifest.xml a fájlban, és adja hozzá a protokollt, a portot és az URI-sémát: 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
Az `UriScheme` megadásával a tároló végpontja automatikusan regisztrálva lesz a Service Fabric elnevezési szolgáltatásban, így felderíthető lesz. A cikk végén talál egy például szolgáló teljes ServiceManifest.xml fájlt. 

Konfigurálja úgy a tárolóport–gazdagépport leképezést, hogy a szolgáltatáshoz a 80-as porton beérkező kérések a tárolón a 80-as portra legyenek leképezve.  Nyissa meg a *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* fájlt a Megoldáskezelőben, és adjon meg egy `PortBinding` szabályzatot a `ContainerHostPolicies` szakaszban.  Ebben a rövid útmutatóban a(z) `ContainerPort` értéke 80, a(z) `EndpointRef` értéke pedig „MyContainerServiceTypeEndpoint” (a szolgáltatásjegyzékben korábban definiált végpont).    

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

A cikk végén talál egy például szolgáló teljes ApplicationManifest.xml fájlt.

## <a name="create-a-cluster"></a>Fürt létrehozása
Az alkalmazás Azure-fürtön történő üzembe helyezése érdekében csatlakozhat egy nyilvános fürthöz. A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök, amelyek futtatását a Service Fabric csapata végzi, és amelyeken bárki üzembe helyezhet alkalmazásokat, és megismerkedhet a platform használatával.  A fürt egy önaláírt tanúsítványt használ a csomópontok közötti, valamint a kliens és a csomópont közötti biztonsághoz. A nyilvános fürtök támogatják a tárolókat. Ha úgy dönt, hogy saját fürtöt állít be és azt használja, akkor a fürtnek a tárolókat támogató termékváltozaton kell futnia (például tárolókkal rendelkező Windows Server 2016 Datacenteren).

Jelentkezzen be, és [csatlakozzon egy Windows-fürthöz](http://aka.ms/tryservicefabric). A **PFX** hivatkozásra kattintva töltse le a PFX-tanúsítványt a számítógépre. Kattintson a **Csatlakozás biztonságos fél fürtjéhez** nevű hivatkozásra, és másolja a tanúsítvány jelszavát. A tanúsítványra, a tanúsítvány jelszavára és a **Kapcsolati végpont** értékére a következő lépésekben szükség lesz.

![PFX és kapcsolati végpont](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Óránként korlátozott számú nyilvános fürt érhető el. Ha a nyilvános fürtre való regisztráláskor hiba lép fel, várjon egy ideig, mielőtt újrapróbálkozna, vagy kövesse a [.NET-alkalmazás üzembe helyezése](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) oktatóanyagban szereplő lépéseket, amellyel létrehozhat egy Service Fabric-fürtöt az Azure-előfizetésben, és üzembe helyezheti rajta az alkalmazást. A Visual Studióval létrehozott fürt támogatja a tárolók használatát. Miután üzembe helyezte és ellenőrizte az alkalmazást a fürtön, továbbléphet a rövid útmutató [Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre](#complete-example-service-fabric-application-and-service-manifests) című részéhez. 
>

Egy Windows-számítógépen telepítse a PFX-et a *CurrentUser\My* tanúsítványtárolóba.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Jegyezze meg az ujjlenyomatot a következő lépéshez.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Alkalmazás üzembe helyezése az Azure-ban a Visual Studio használatával
Az alkalmazást a létrehozása után telepítheti a fürtben, közvetlenül a Visual Studióból.

A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **MyFirstContainer** elemre, majd kattintson a **Publish** (Közzététel) parancsra. Ekkor megjelenik a Publish (Közzététel) párbeszédpanel.

Másolja be a **Kapcsolati végpont** értékét a nyilvános fürt lapjáról a **Connection Endpoint** (Kapcsolati végpont) mezőbe. Például: `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Kattintson az **Advanced Connection Parameters** (Speciális kapcsolati paraméterek) elemre, és ellenőrizze a kapcsolati paraméterek információit.  A *FindValue* és *ServerCertThumbprint* értékeknek egyezniük kell az előző lépésben telepített tanúsítvány ujjlenyomatával. 

![Publish (Közzététel) párbeszédpanel](./media/service-fabric-quickstart-containers/publish-app.png)

Kattintson a **Publish** (Közzététel) gombra.

A fürtben szereplő minden alkalmazásnak egyedi névvel kell rendelkeznie.  A nyilvános fürtök azonban nyilvános és megosztott környezetek, így ütközés léphet fel egy meglévő alkalmazással.  Névütközés esetén nevezze át a Visual Studio-projektet, és végezze el újra az üzembe helyezést.

Nyisson meg egy böngészőt, majd navigáljon a nyilvános fürt lapján megadott **kapcsolati végponthoz**. Azt is megteheti, hogy beilleszti előre a `http://` sémaazonosítót, vagy hozzáfűzheti a `:80` portot az URL-címhez. Például: http://zwin7fh14scd.westus.cloudapp.azure.com:80. Ekkor az IIS alapértelmezett webhelyének kell megjelennie: ![Az IIS alapértelmezett webhelye][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen rövid útmutatóban használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
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
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>További lépések
Ennek a rövid útmutatónak a segítségével megtanulta a következőket:
> [!div class="checklist"]
> * Docker-rendszerképtároló becsomagolása
> * A kommunikáció konfigurálása
> * Service Fabric-alkalmazás felépítése és becsomagolása
> * A tárolóalkalmazás üzembe helyezése az Azure-on

* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* Tekintse meg a [.NET-alkalmazás üzembe helyezését](service-fabric-host-app-in-a-container.md) ismertető oktatóanyagot.
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-containers) a GitHubon.

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
