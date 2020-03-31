---
title: Példák az Azure Service Fabric tárolóalkalmazás-jegyzékfájlra
description: Ismerje meg, hogyan konfigurálhatja az alkalmazás- és szolgáltatásjegyzék-beállításokat egy többtárolós Service Fabric-alkalmazáshoz.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258394"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Példák többtárolós alkalmazások és szolgáltatások jegyzékeire
Az alábbiakban példákat az alkalmazás és a szolgáltatás jegyzékfájljai egy többtárolós Service Fabric-alkalmazás. Ezeknek a példáknak az a célja, hogy megmutassák, milyen beállítások érhetők el, és hogyan kell használni őket. Ezek az alkalmazás- és szolgáltatásjegyzékek a [Windows Server 2016-tárolóminta jegyzékfájljain](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) alapulnak.

A következő funkciók jelennek meg:

|Jegyzék|Szolgáltatások|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| [környezeti változók felülbírálása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [tárolóport-állomás leképezés konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [tárolóbeállítási hitelesítés konfigurálása](service-fabric-get-started-containers.md#configure-container-repository-authentication), [erőforrás-irányítási](service-fabric-resource-governance.md), [set isolation mode](service-fabric-get-started-containers.md#configure-isolation-mode), os [build-specifikus tárolólemezképek megadása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService szolgáltatásjegyzék](#frontendservice-service-manifest)| [környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), parancsok átadása a tárolóba, [tanúsítvány importálása tárolóba](service-fabric-securing-containers.md)| 
|[BackEndService szolgáltatásjegyzék](#backendservice-service-manifest)|[környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), [kötet-illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)| 

Az egyes XML-elemekről az [Alkalmazásjegyzék-elemek](#application-manifest-elements), [a FrontEndService szolgáltatás jegyzékelemei](#frontendservice-service-manifest-elements)és a [BackEndService szolgáltatás jegyzékfájlelemei](#backendservice-service-manifest-elements) című témakörben talál további információt.

## <a name="application-manifest"></a>Alkalmazásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

## <a name="frontendservice-service-manifest"></a>FrontEndService szolgáltatásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
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

## <a name="backendservice-service-manifest"></a>BackEndService szolgáltatásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
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

## <a name="application-manifest-elements"></a>Alkalmazásjegyzék-elemek
### <a name="applicationmanifest-element"></a>ApplicationManifest elem
Deklaratív módon leírja az alkalmazás típusát és verzióját. Az alkotószolgáltatások egy vagy több szolgáltatásjegyzéke alkalmazástípus összeállítására hivatkozik. Az alkotószolgáltatások konfigurációs beállításai felülbírálhatók a paraméteres alkalmazásbeállítások használatával. Az alapértelmezett szolgáltatások, szolgáltatássablonok, egyszerű biztonsági tagok, házirendek, diagnosztikai beállítások és tanúsítványok is deklarálhatók az alkalmazás szintjén. További információ: [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Paraméterek elem
Deklarálja az alkalmazásjegyzékben használt paramétereket. Ezeknek a paramétereknek az értéke az alkalmazás példányossága esetén adható meg, és az alkalmazás- vagy szolgáltatáskonfigurációs beállítások felülbírálására használható. További információ: [Parameters Element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Paraméterelem
A jegyzékfájlban használandó alkalmazásparaméter. A paraméterérték módosítható az alkalmazás példányosítása során, vagy ha nincs megadva érték, a program az alapértelmezett értéket használja. További információ: [Parameter Element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport elem
Importálja a szolgáltatásfejlesztő által létrehozott szolgáltatásjegyzéket. Az alkalmazás minden egyes összetevőszolgáltatásához szervizjegyzéket kell importálni. A konfiguráció felülbírálásaés a szabályzatok deklarálhatók a szolgáltatásjegyzékhez. További információ: [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef elem
A szolgáltatásjegyzéket hivatkozással importálja. Jelenleg a szolgáltatásjegyzékfájlnak (ServiceManifest.xml) jelen kell lennie a buildcsomagban. További információ: [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="policies-element"></a>Házirendelem
Az importált szolgáltatásjegyzékre alkalmazandó házirendek (végpontkötés, csomagmegosztás, futtatási és biztonsági hozzáférés) ismertetése. További információ: [Policies element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy elem
A teljes szolgáltatáscsomag szintjén alkalmazott erőforrás-irányítási házirendet határozza meg. További információ: [ServicePackageResourceResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
A kódcsomag erőforráskorlátait adja meg. További információ: [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies elem
A tárolóállomások aktiválására vonatkozó házirendeket határozza meg. További információ: [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials elem
Hitelesítő adatok a tárolóképtárház képek lekérése. További információ: [RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>Portkötési elem
Itt adható meg, hogy melyik végponti erőforrás kötődik a kitett tárolóporthoz. További információ: [PortBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Kötet elem
Megadja a tárolóhoz kötve lévő kötetet. További információ: [Volume Element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption elem
A járművezetőnek átadandó járművezetői lehetőségek. További információ: [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides elem
Előfordulhat, hogy a Windows Server-tárolók nem kompatibilisek az operációs rendszer különböző verzióiban.  Tárolónként több operációsrendszer-lemezképet is megadhat, és megjelölheti őket az operációs rendszer build-verzióival. Az operációs rendszer buildverziója a "winver" futtatásával a Windows parancssorában. Ha az alapul szolgáló operációs rendszer az 16299-es (Windows Server 1709-es verzió) buildverziója, a Service Fabric az Os="16299" címkével ellátott tárolólemezképet választja. Egy címkézetlen tárolórendszerkép az operációs rendszer összes verziójában működik, és felülbírálja a szolgáltatásjegyzékben megadott lemezképet. További információ: [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Képelem
Az operációs rendszer buildverziós verziójának megfelelő tárolórendszerkép-verziószám. Ha az Os attribútum nincs megadva, a tárolórendszerkép az operációs rendszer összes verziójában működik, és felülbírálja a szolgáltatásjegyzékben megadott lemezképet. További információ: [Image Element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides elem
 További információ: [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef elem
A tárolókörnyezetnek kitenni takarandó X509-tanúsítvány adatait adja meg. A tanúsítványt az összes fürtcsomópont LocalMachine tárolójában kell telepíteni.
Amikor az alkalmazás elindul, a futásidejű beolvassa a tanúsítványt, és létrehoz egy PFX fájlt és jelszót (Windows rendszeren) vagy egy PEM fájlt (Linuxon).
A PFX fájl és jelszó a tárolóban érhető el a Certificates_ServicePackageName_CodePackageName_CertName_PFX és Certificates_ServicePackageName_CodePackageName_CertName_Password környezeti változók használatával. A PEM-fájl a Certificates_ServicePackageName_CodePackageName_CertName_PEM és Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey környezeti változók használatával érhető el a tárolóban. További információ: [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices elem
Deklarálja azokat a szolgáltatáspéldányokat, amelyek automatikusan létrejönnek, amikor egy alkalmazás példányosan kapcsolódik ehhez az alkalmazástípushoz. További információ: [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatási elem
Deklarálja, hogy egy szolgáltatás automatikusan létrejön az alkalmazás példányosan. További információ: [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService elem
Állapotnélküli szolgáltatást határoz meg. További információ: [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService szolgáltatás jegyzékfájl-elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
Deklaratív módon leírja a szolgáltatás típusát és verzióját. Felsorolja a függetlenül frissíthető kódot, konfigurációt és adatcsomagokat, amelyek együttesen egy vagy több szolgáltatástípus ttámogató szolgáltatáscsomagot állítanak össze. Erőforrások, diagnosztikai beállítások és a szolgáltatás metaadatai, például a szolgáltatás típusa, az állapot-tulajdonságok és a terheléselosztási metrikák is meg vannak adva. További információ: [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Azt határozza meg, hogy a manitkulásban a codepackage milyen szolgáltatástípusokat támogat. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. A szolgáltatástípusok deklarálva a jegyzékfájl szintjén vannak deklarálva, nem pedig a kódcsomag szintjén. További információ: [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Állapotmentes szolgáltatástípus leírása. További információ: [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípust támogató kódcsomagot ismertet. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. Az eredményül kapott folyamatoknak futásidőben regisztrálniuk kell a támogatott szolgáltatástípusokat. Ha több kódcsomag van, azok mind aktiválódnak, amikor a rendszer a deklarált szolgáltatástípusok bármelyikét keresi. További információ: [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint elem
Az EntryPoint által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. Az EntryPoint által megadott végrehajtható fájl a SetupEntryPoint sikeres kilépése után fut. Az eredményül kapott folyamatot a rendszer figyeli és újraindítja (a SetupEntryPoint programmal kezdődően), ha az bármikor leáll vagy összeomlik. További információ: [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
 További információ: [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
A tárkocsi és [https://hub.docker.com](https://hub.docker.com) a rendszerkép vagy az Azure Container Registry. További információ: [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Környezeti változók át a tároló vagy exe.  További információ: [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
Deklarál egy, a Name attribútum által elnevezett mappát, amely Settings.xml fájlt tartalmaz. Ez a fájl a felhasználó által definiált, kulcs-érték pár beállítások azon szakaszait tartalmazza, amelyeket a folyamat futásközben vissza tud olvasni. A frissítés során, ha csak a ConfigPackage verzió változott, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a folyamatot, hogy a konfigurációs beállítások megváltoztak, így azok dinamikusan újratölthetők. További információ: [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage elem
Deklarál egy mappát, amelyet a Name attribútum nevez el, és amely statikus adatfájlokat tartalmaz. A Service Fabric újrahasznosítja az összes, a gazdagépben megadott és támogatási csomagokban megadott EXEs és DLLHOST-okat, amikor a szolgáltatásjegyzékben felsorolt adatcsomagok bármelyike frissül. További információ: [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
A szolgáltatás által használt erőforrások at írja le, és a szolgáltatás telepítésekor módosítható konkretizált. Ezekhez az erőforrásokhoz való hozzáférés az alkalmazásjegyzék rendszerszámos és házirend szakaszán keresztül szabályozott. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
A szolgáltatás végpontjait határozza meg. További információ: [Végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont-elem
További információ: [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService szolgáltatásjegyzék-elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
Deklaratív módon leírja a szolgáltatás típusát és verzióját. Felsorolja a függetlenül frissíthető kódot, konfigurációt és adatcsomagokat, amelyek együttesen egy vagy több szolgáltatástípus ttámogató szolgáltatáscsomagot állítanak össze. Erőforrások, diagnosztikai beállítások és a szolgáltatás metaadatai, például a szolgáltatás típusa, az állapot-tulajdonságok és a terheléselosztási metrikák is meg vannak adva. További információ: [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Azt határozza meg, hogy a manitkulásban a codepackage milyen szolgáltatástípusokat támogat. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. A szolgáltatástípusok deklarálva a jegyzékfájl szintjén vannak deklarálva, nem pedig a kódcsomag szintjén. További információ: [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Állapotmentes szolgáltatástípus leírása. További információ: [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípust támogató kódcsomagot ismertet. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. Az eredményül kapott folyamatoknak futásidőben regisztrálniuk kell a támogatott szolgáltatástípusokat. Ha több kódcsomag van, azok mind aktiválódnak, amikor a rendszer a deklarált szolgáltatástípusok bármelyikét keresi. További információ: [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint elem
Az EntryPoint által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. Az EntryPoint által megadott végrehajtható fájl a SetupEntryPoint sikeres kilépése után fut. Az eredményül kapott folyamatot a rendszer figyeli és újraindítja (a SetupEntryPoint programmal kezdődően), ha az bármikor leáll vagy összeomlik. További információ: [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
További információ: [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
A tárkocsi és [https://hub.docker.com](https://hub.docker.com) a rendszerkép vagy az Azure Container Registry. További információ: [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Parancsok elem
Adja át a parancslistával körülhatárolt listáját a tárolónak. További információt a Parancsok elem című témakörben [talál.](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Környezeti változók át a tároló vagy exe.  További információ: [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
Deklarál egy, a Name attribútum által elnevezett mappát, amely Settings.xml fájlt tartalmaz. Ez a fájl a felhasználó által definiált, kulcs-érték pár beállítások azon szakaszait tartalmazza, amelyeket a folyamat futásközben vissza tud olvasni. A frissítés során, ha csak a ConfigPackage verzió változott, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a folyamatot, hogy a konfigurációs beállítások megváltoztak, így azok dinamikusan újratölthetők. További információ: [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
A szolgáltatás által használt erőforrások at írja le, és a szolgáltatás telepítésekor módosítható konkretizált. Ezekhez az erőforrásokhoz való hozzáférés az alkalmazásjegyzék rendszerszámos és házirend szakaszán keresztül szabályozott. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
A szolgáltatás végpontjait határozza meg. További információ: [Végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont-elem
 További információ: [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

