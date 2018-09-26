---
title: Az Azure Service Fabric tároló application manifest példák |} A Microsoft Docs
description: Ismerje meg, hogyan alkalmazás konfigurálása és a többtárolós Service Fabric-alkalmazás az alkalmazásjegyzék beállításai.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: ryanwi
ms.openlocfilehash: 6f538fa821e546d12c5a2bdb9585cc85871241fa
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094152"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Példák többtárolós alkalmazások és szolgáltatások jegyzékeire
A következő példák az alkalmazás és szolgáltatás jegyzékfájljainak többtárolós Service Fabric-alkalmazás. Ezekben a példákban az a célja, hogy milyen beállítások érhetők el, és azok használatát. Ezen alkalmazás és jegyzékek alapulnak a [Windows Server 2016 tároló minta](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) jegyzékfájlok.

A következő funkciók jelennek meg:
|Jegyzék|Szolgáltatások|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| [bírálja felül a környezeti változók](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [tárolóport-gazdagép-leképezés konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [tárolóregisztrációs adatbázis hitelesítésének konfigurálásához](service-fabric-get-started-containers.md#configure-container-registry-authentication), [erőforrás-szabályozás](service-fabric-resource-governance.md), [elkülönítési mód beállítása](service-fabric-get-started-containers.md#configure-isolation-mode), [OS build-specifikus tárolórendszerképek megadása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService szolgáltatásjegyzékben](#frontendservice-service-manifest)| [környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), parancsok adhatók meg a tároló [tanúsítvány importálása egy tárolóba](service-fabric-securing-containers.md)| 
|[BackEndService szolgáltatásjegyzékben](#backendservice-service-manifest)|[környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), [kötet illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)| 

Lásd: [Application manifest elemek](#application-manifest-elements), [FrontEndService service manifest elemek](#frontendservice-service-manifest-elements), és [BackEndService service manifest elemek](#backendservice-service-manifest-elements) vonatkozó további információ XML-elemeket.

## <a name="application-manifest"></a>Alkalmazásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService szolgáltatásjegyzékben

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService szolgáltatásjegyzékben

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>Alkalmazás-jegyzékfájl elemek
### <a name="applicationmanifest-element"></a>ApplicationManifest elem
Deklaratív ismerteti az alkalmazás típusát és verzióját. Egy vagy több szolgáltatásjegyzékek a különböző szolgáltatások hivatkozott alkalmazástípus írása. Konfigurációs beállítások a különböző szolgáltatások paraméteres Alkalmazásbeállítások bírálható felül. Alapértelmezés szerint a szolgáltatások, a szolgáltatássablonok, rendszerbiztonsági tagok, szabályzatok, diagnosztikai beállítás és az alkalmazás szintjén is deklarálva tanúsítványokat is. További információkért lásd: [ApplicationManifest elem](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Paraméterek elem
Deklarálja a paramétereket, az alkalmazásjegyzékben. Ezek a paraméterek értéke lehet biztosítani, az alkalmazás van példányosítva és az alkalmazás vagy szolgáltatás konfigurációs beállítások felülbírálása használható. További információkért lásd: [paraméterek elem](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>A paraméter eleme
Egy alkalmazás használható paraméter azonosítására a jegyzékfájlban. A paraméter értéke módosítható alkalmazás történt, vagy ha nincs érték van megadva az alapértelmezett érték lesz érvényben. További információkért lásd: [Paraméterelem](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport elem
A szolgáltatásjegyzék, a szolgáltatás fejlesztő által létrehozott importálja. A szolgáltatásjegyzék, az alkalmazás minden egyes megvalósítani a bennük foglalt szolgáltatás kell importálni. Konfiguráció felülírja, és a szabályzatok a szolgáltatásjegyzék számára deklarálható. További információkért lásd: [ServiceManifestImport elem](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef elem
Importálja a szolgáltatásjegyzék hivatkozással. Jelenleg a szolgáltatás jegyzékfájlját (ServiceManifest.xml) kell lennie a build csomagban megtalálható. További információkért lásd: [ServiceManifestRef elem](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Házirendek elem
Az importált Szolgáltatásjegyzék a alkalmazni (végponti kötést, a csomag megosztási, a futtató és a biztonság hozzáférés) házirendeket ismerteti. További információkért lásd: [házirendek elem](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
A teljes szolgáltatási csomag szintjén alkalmazzák-erőforrás-szabályozási házirend határozza meg. További információkért lásd: [ServicePackageResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
Egy kódcsomaghoz erőforráskorlátok adja meg. További információkért lásd: [ResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Adja meg a házirendek tárológazdagép aktiválásához. További információkért lásd: [ContainerHostPolicies elem](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials elem
Tároló lemezképtárban lekéréses képek a hitelesítő adatait. További információkért lásd: [RepositoryCredentials elem](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding elem
Adja meg a végpont erőforrás-kötést létrehozni a tároló elérhetővé tett port. További információkért lásd: [PortBinding elem](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Kötet elem
Adja meg a kötet a tároló lehet kötni. További információkért lásd: [kötet elem](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption elem
Illesztőprogram-beállítások illesztőprogram lesz elküldve. További információkért lásd: [DriverOption elem](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides elem
A Windows Server-tárolók előfordulhat, hogy nem kompatibilis az operációs rendszer különböző verzióiban.  Adjon meg több operációsrendszer-lemezképet, és felcímkézni azokat az operációs rendszer verzióiban. Az operációs rendszer buildverziója beolvasása egy Windows parancssorban futtassa a "winver parancsot". Az alapul szolgáló operációs rendszer build verziója 16299 (1709-es Windows Server), ha a Service Fabric választja ki a tároló rendszerképét az operációs rendszer címkézett = "16299". Egy címke nélküli tárolórendszerkép rendszer feltételezi, hogy az operációs rendszer összes verziójára keresztül működik, és felülírja a szolgáltatásjegyzék megadott rendszerkép. További információkért lásd: [ImageOverrides elem](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Kép elem
Az operációs rendszer buildszámának indítható tartozó tárolórendszerképet. Ha az operációs rendszer attribútum nincs megadva, a tároló rendszerképét rendszer feltételezi, hogy az operációs rendszer összes verziójára különböző, és felülírja a szolgáltatásjegyzék megadott rendszerkép. További információkért lásd: [kép elem](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides elem
 További információkért lásd: [EnvironmentOverrides elem](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>Environmentvariable változó elem
Környezeti változó. További információkért lásd: [environmentvariable változó elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef elem
Információt nyújt egy X509 ki vannak téve a tárolókörnyezet tanúsítvány. A tanúsítvány telepítve kell lennie a helyi gépen lévő tárolóban, a fürt összes csomópontján.
Az alkalmazás indításakor a modul olvassa be a tanúsítvány, és állít elő, a PFX-fájlok és a jelszót (a Windows) vagy egy PEM-fájlt (a Linux rendszeren).
A PFX-fájlt, és a jelszavát a tárolóban, a Certificates_ServicePackageName_CodePackageName_CertName_PFX és Certificates_ServicePackageName_CodePackageName_CertName_Password környezeti változók használatával érhetők el. A PEM-fájl a tárolóban, a Certificates_ServicePackageName_CodePackageName_CertName_PEM és Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey környezeti változók használatával érhető el. További információkért lásd: [CertificateRef elem](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices elem
Deklarálja a szolgáltatáspéldány, amely automatikusan létrejön, amikor egy alkalmazás van példányosítva tento typ aplikace ellen. További információkért lásd: [DefaultServices elem](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatás-elem
Deklarálja a szolgáltatás automatikusan létrejön, amikor létrejön az alkalmazás. További információkért lásd: [szolgáltatás elem](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService elem
Állapotmentes szolgáltatás határozza meg. További információkért lásd: [StatelessService elem](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService service manifest elemek
### <a name="servicemanifest-element"></a>ServiceManifest elemben
Szolgáltatás típusa és verziója deklaratív ismerteti. A egymástól függetlenül frissíthető kód, a konfiguráció és az adatok csomagokat, amelyek együttesen egy service-csomag egy vagy több szolgáltatás típusainak listáját. Erőforrások, a diagnosztikai beállítások és a szolgáltatás típusa, egészségügyi tulajdonságok és terheléselosztási mérőszámokat, például a szolgáltatás metaadatai is meg van adva. További információkért lásd: [ServiceManifest elemben](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Határozza meg, milyen szolgáltatástípusok támogatottak a CodePackage a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Szolgáltatástípusok deklarált a jegyzékfájl szintjén, és nem a kód szintű csomag. További információkért lásd: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Egy állapotmentes szolgáltatás típusának leírása. További információkért lásd: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Ismerteti egy kódcsomaghoz, amely támogatja a megadott szolgáltatási típusa. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Az amiatt végbemenő folyamatok várhatóan a támogatott szolgáltatástípusok regisztrálja a futási időben. Ha több kódcsomagok, az összes aktiválás, amikor a rendszer figyeli a nem a deklarált szolgáltatás típusok közül. További információkért lásd: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint elem
A belépési pont által megadott végrehajtható fájlt az általában a hosszú ideig futó szolgáltatás gazdagép. Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. A belépési pont által megadott végrehajtható fájl SetupEntryPoint sikeres kilépés után futtatott. Az eredményül kapott folyamat figyel, és (kezdődő újra SetupEntryPoint) újraindul, ha valaha leáll vagy összeomlik. További információkért lásd: [BelépésiPont-elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
 További információkért lásd: [ContainerHost elem](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
Az adattár és a lemezkép https://hub.docker.com vagy az Azure Container Registry. További információkért lásd: [ImageName elem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Adja meg a tároló vagy exe környezeti változókat.  További információkért lásd: [EnvironmentVariables elem](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Environmentvariable változó elem
Környezeti változó. További információkért lásd: [environmentvariable változó elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
Egy mappa deklarálja nevű attribútum nevét, egy Settings.xml fájlt tartalmazó. Ezt a fájlt a felhasználó által definiált, a kulcs-érték párt a beállításokat, amelyeket a folyamat futási időben vissza tudja olvasni szakaszokat tartalmazza. Frissítés alatt Ha csak a ConfigPackage verziója megváltozott, majd a futó folyamat nem indítja újra. Ehelyett egy visszahívást, amely a konfigurációs beállításai megváltoztak, így azok dinamikusan kell tölteni a folyamat értesítést küld. További információkért lásd: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage elem
Deklarálja a mappát, a Name attribútum, amely tartalmazza a statikus adatfájlok szerint. A Service Fabric exe, és a szolgáltatásjegyzékben szereplő adatok csomagok frissítésekor a gazdagép és a támogatási csomag megadott DLLHOSTs fog újrahasznosítása. További információkért lásd: [DataPackage elem](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ezt a szolgáltatást, amely lefordított kód módosítása nélkül deklarálva, és módosítani, ha a szolgáltatás telepítése által használt erőforrások ismerteti. Ezekhez az erőforrásokhoz való hozzáférésének a rendszerbiztonsági tagok és a szabályzatok szakaszok az alkalmazás-jegyzékfájl. További információkért lásd: [erőforrások elem](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
Végpontok a szolgáltatás határozza meg. További információkért lásd: [végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont elem
További információkért lásd: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService service manifest elemek
### <a name="servicemanifest-element"></a>ServiceManifest elemben
Szolgáltatás típusa és verziója deklaratív ismerteti. A egymástól függetlenül frissíthető kód, a konfiguráció és az adatok csomagokat, amelyek együttesen egy service-csomag egy vagy több szolgáltatás típusainak listáját. Erőforrások, a diagnosztikai beállítások és a szolgáltatás típusa, egészségügyi tulajdonságok és terheléselosztási mérőszámokat, például a szolgáltatás metaadatai is meg van adva. További információkért lásd: [ServiceManifest elemben](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Határozza meg, milyen szolgáltatástípusok támogatottak a CodePackage a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Szolgáltatástípusok deklarált a jegyzékfájl szintjén, és nem a kód szintű csomag. További információkért lásd: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Egy állapotmentes szolgáltatás típusának leírása. További információkért lásd: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Ismerteti egy kódcsomaghoz, amely támogatja a megadott szolgáltatási típusa. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Az amiatt végbemenő folyamatok várhatóan a támogatott szolgáltatástípusok regisztrálja a futási időben. Ha több kódcsomagok, az összes aktiválás, amikor a rendszer figyeli a nem a deklarált szolgáltatás típusok közül. További információkért lásd: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint elem
A belépési pont által megadott végrehajtható fájlt az általában a hosszú ideig futó szolgáltatás gazdagép. Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. A belépési pont által megadott végrehajtható fájl SetupEntryPoint sikeres kilépés után futtatott. Az eredményül kapott folyamat figyel, és (kezdődő újra SetupEntryPoint) újraindul, ha valaha leáll vagy összeomlik. További információkért lásd: [BelépésiPont-elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
További információkért lásd: [ContainerHost elem](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
Az adattár és a lemezkép https://hub.docker.com vagy az Azure Container Registry. További információkért lásd: [ImageName elem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Parancsok elem
Adja meg a tároló parancsok vesszővel tagolt listája. További információkért lásd: [parancsok elem](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Adja meg a tároló vagy exe környezeti változókat.  További információkért lásd: [EnvironmentVariables elem](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>Environmentvariable változó elem
Környezeti változó. További információkért lásd: [environmentvariable változó elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexTypeDefinedInEnvironmentVariablesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
Egy mappa deklarálja nevű attribútum nevét, egy Settings.xml fájlt tartalmazó. Ezt a fájlt a felhasználó által definiált, a kulcs-érték párt a beállításokat, amelyeket a folyamat futási időben vissza tudja olvasni szakaszokat tartalmazza. Frissítés alatt Ha csak a ConfigPackage verziója megváltozott, majd a futó folyamat nem indítja újra. Ehelyett egy visszahívást, amely a konfigurációs beállításai megváltoztak, így azok dinamikusan kell tölteni a folyamat értesítést küld. További információkért lásd: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ezt a szolgáltatást, amely lefordított kód módosítása nélkül deklarálva, és módosítani, ha a szolgáltatás telepítése által használt erőforrások ismerteti. Ezekhez az erőforrásokhoz való hozzáférésének a rendszerbiztonsági tagok és a szabályzatok szakaszok az alkalmazás-jegyzékfájl. További információkért lásd: [erőforrások elem](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
Végpontok a szolgáltatás határozza meg. További információkért lásd: [végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont elem
 További információkért lásd: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

