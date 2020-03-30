---
title: Példák megbízható szolgáltatások alkalmazásjegyzék-jegyzékre
description: Ismerje meg, hogyan konfigurálhatja az alkalmazás- és szolgáltatásjegyzék-beállításokat egy megbízható szolgáltatás Service Fabric-alkalmazáshoz.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282353"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Példák Reliable Services-alkalmazások és szolgáltatások jegyzékeire
Az alábbiakban példákat az alkalmazás és a szolgáltatás jegyzékfájljai egy Service Fabric-alkalmazás egy ASP.NET Core webes előtér és egy állapotalapú háttér-. Ezeknek a példáknak az a célja, hogy megmutassák, milyen beállítások érhetők el, és hogyan kell használni őket. Ezek az alkalmazás- és szolgáltatásjegyzékek a [Service Fabric .NET rövid útmutató](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) jegyzékfájljain alapulnak.

A következő funkciók jelennek meg:

|Jegyzék|Szolgáltatások|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| [erőforrás-szabályozás](service-fabric-resource-governance.md), [szolgáltatás futtatása helyi rendszergazdai fiókként](service-fabric-application-runas-security.md), [alapértelmezett házirend alkalmazása az összes szolgáltatáskódcsomagra,](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)felhasználói és [csoportnévcsoportok létrehozása](service-fabric-application-runas-security.md), adatcsomag megosztása a szolgáltatáspéldányok között, [felülbírálás szolgáltatásvégpontok](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService szolgáltatásjegyzék| [Parancsfájl futtatása a szolgáltatás indításakor,](service-fabric-run-script-at-service-startup.md) [HTTPS-végpont definiálása](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService szolgáltatásjegyzék| [Konfigurációs csomag deklarálása](service-fabric-application-and-service-manifests.md), [adatcsomag deklarálása](service-fabric-application-and-service-manifests.md), [végpont konfigurálása](service-fabric-service-manifest-resources.md)| 

Az egyes XML-elemekről további információt [az Alkalmazásjegyzék-elemek](#application-manifest-elements), [a VotingWeb szolgáltatás jegyzékelemei](#votingweb-service-manifest-elements)és [a VotingData szolgáltatás jegyzékelemei](#votingdata-service-manifest-elements) című témakörben talál.

## <a name="application-manifest"></a>Alkalmazásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
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

### <a name="resourceoverrides-element"></a>ResourceOverrides elem
A szolgáltatásjegyzék-erőforrásokban deklarált végpontok erőforrás-felülbírálásait adja meg. További információ: [ResourceOverrides Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Végpontok elem
A felülbírálandó végpont(ok). További információ: [Végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Végpont-elem
A szolgáltatásjegyzékben deklarált végpont felülbírálása. További információ: [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Házirendelem
Az importált szolgáltatásjegyzékre alkalmazandó házirendek (végpontkötés, csomagmegosztás, futtatási és biztonsági hozzáférés) ismertetése. További információ: [Policies element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy elem
A teljes szolgáltatáscsomag szintjén alkalmazott erőforrás-irányítási házirendet határozza meg. További információ: [ServicePackageResourceResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
A codepackage erőforráskorlátait adja meg. További információ: [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy elem
Azt jelzi, hogy egy kódot, konfigurációs vagy adatcsomagot meg kell-e osztani az azonos szolgáltatástípusú szolgáltatáspéldányok között. További információ: [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy elem
Hozzáférési engedélyeket ad egy erőforrás (például egy végpont) szolgáltatásjegyzékben definiált főnévhez. A biztonsági kockázatok minimalizálása érdekében általában nagyon hasznos a szolgáltatások különböző erőforrásokhoz való hozzáférésének szabályozása és korlátozása. Ez különösen akkor fontos, ha az alkalmazás egy piactérről származó szolgáltatások gyűjteményéből épül fel, amelyeket különböző fejlesztők fejlesztettek ki. További információ: [SecurityAccessPolicy Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Runaspolicy elem
Megadja azt a helyi felhasználói vagy helyi rendszerfiókot, amelyként a szolgáltatáskódcsomag futni fog. Tartományi fiókok támogatottak a Windows Server központi telepítések, ahol az Azure Active Directory érhető el. Alapértelmezés szerint az alkalmazások a Fabric.exe folyamat alatt futó fiók alatt futnak. Az alkalmazások más fiókokként is futtathatók, amelyeket a Rendszermegkettőzések szakaszban kell deklarálni. Ha RunAs házirendet alkalmaz egy szolgáltatásra, és a szolgáltatásjegyzék deklarálja a végponterőforrásokat a HTTP protokollal, meg kell adnia egy SecurityAccessPolicy metódust is, hogy a végpontokhoz lefoglalt portok megfelelően legyenek a RunAs-ok hoz való hozzáférés-vezérlési listázása. felhasználói fiók, amely alatt a szolgáltatás fut. HTTPS-végpont esetén meg kell adnia egy EndpointBindingPolicy házirendet is, amely jelzi az ügyfélnek visszaadandó tanúsítvány nevét. További információ: [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices elem
Deklarálja azokat a szolgáltatáspéldányokat, amelyek automatikusan létrejönnek, amikor egy alkalmazás példányosan kapcsolódik ehhez az alkalmazástípushoz. További információ: [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatási elem
Deklarálja, hogy egy szolgáltatás automatikusan létrejön az alkalmazás példányosan. További információ: [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService elem
Állapotalapú szolgáltatást határoz meg. További információ: [StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService elem
Állapotnélküli szolgáltatást határoz meg. További információ: [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Megbízók elem
Ez a témakör azokat a rendszerbiztonsági tagokat (felhasználók, csoportok) ismerteti, amelyek az alkalmazás szolgáltatások futtatásához és az erőforrások védelméhez szükségesek. A rendszerbiztonsági tagokra a házirendek szakaszban hivatkozunk. További információ: [Megbízók elem](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Csoportok elem
Csoportok készletét rendszerbiztonsági tagokként deklarálja, amelyekre a házirendekben hivatkozni lehet. A csoportok akkor hasznosak, ha több felhasználó van különböző szolgáltatásbelépési pontokhoz, és rendelkezniük kell bizonyos közös jogosultságokkal, amelyek a csoport szintjén érhetők el. További információ: [Groups Element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Csoportelem
Egy csoportot rendszerbiztonsági tagként deklarál, amelyre a házirendekben hivatkozhat. További információ: [Group Element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Tagsági elem
 További információ: [Tagsági elem](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup elem
 További információ: [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Felhasználói elem
A felhasználók egy készletét rendszerbiztonsági tagokként deklarálja, amelyekre a házirendekben hivatkozhat. További információ: [Felhasználói elem](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Felhasználói elem
A felhasználót rendszerbiztonsági tagként deklarálja, amelyre a házirendekben hivatkozhat. További információ: [User Element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Memberof elem
A felhasználók bármely meglévő tagsági csoporthoz hozzáadhatók, így örökölhetik az adott tagsági csoport összes tulajdonságát és biztonsági beállítását. A tagsági csoport külső erőforrások védelmére használható, amelyeket különböző szolgáltatásokvagy ugyanazon szolgáltatás (egy másik gépen) kell elérni. További információ: [MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup elem
Az a rendszercsoport, amelyhez hozzá szeretné adni a felhasználót.  A rendszercsoportot a Csoportok szakaszban kell definiálni. További információ: [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Csoportelem
Az a csoport, amelyhez hozzá szeretné adni a felhasználót.  A csoportot a Csoportok szakaszban kell definiálni. További információ: [Group Element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Házirendelem
Ez a témakör az alkalmazás szintjén alkalmazandó házirendeket (naplógyűjtés, alapértelmezett futtatás, állapotés és biztonsági hozzáférés). További információ: [Policies element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>Defaultrunaspolicy elem
Adjon meg egy alapértelmezett felhasználói fiókot minden olyan szolgáltatáskódcsomaghoz, amely nem rendelkezik a ServiceManifestImport szakaszban definiált adott RunAsPolicy házirenddel. További információ: [DefaultRunAsPolicy Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb szolgáltatás jegyzékelemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
Deklaratív módon leírja a szolgáltatás típusát és verzióját. Felsorolja a függetlenül frissíthető kódot, konfigurációt és adatcsomagokat, amelyek együttesen egy vagy több szolgáltatástípus ttámogató szolgáltatáscsomagot állítanak össze. Erőforrások, diagnosztikai beállítások és a szolgáltatás metaadatai, például a szolgáltatás típusa, az állapot-tulajdonságok és a terheléselosztási metrikák is meg vannak adva. További információ: [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Azt határozza meg, hogy a manitkulásban a codepackage milyen szolgáltatástípusokat támogat. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. A szolgáltatástípusok deklarálva a jegyzékfájl szintjén vannak deklarálva, nem pedig a kódcsomag szintjén. További információ: [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Állapotmentes szolgáltatástípus leírása. További információ: [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípust támogató kódcsomagot ismertet. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. Az eredményül kapott folyamatoknak futásidőben regisztrálniuk kell a támogatott szolgáltatástípusokat. Ha több kódcsomag van, azok mind aktiválódnak, amikor a rendszer a deklarált szolgáltatástípusok bármelyikét keresi. További információ: [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint elem
Egy kiemelt belépési pont, amely alapértelmezés szerint fut ugyanazokkal a hitelesítő adatokkal, mint a Service Fabric (általában a NETWORKSERVICE fiók) bármely más belépési pont előtt. Az EntryPoint által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. További információ: [SetupEntryPoint Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program elem
A végrehajtható fájl neve.  Például "MySetup.bat" vagy "MyServiceHost.exe". További információ: [Program Element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Arguments elem
 További információ: [Arguments Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Munkamappa elem
A folyamat munkakönyvtára a kódcsomagban azon a fürtcsomóponton, ahol az alkalmazás telepítve van. Három értéket adhat meg: Munka (az alapértelmezett), CodePackage vagy CodeBase. A CodeBase azt adja meg, hogy a munkakönyvtár arra a könyvtárra van-e beállítva, amelyben az EXE definiálva van a kódcsomagban. A CodePackage a munkakönyvtárat állítja be a kódcsomag gyökérkönyvtárába, függetlenül attól, hogy az EXE hol van definiálva a kódcsomag könyvtárában. A munkabeiktatot a csomóponton létrehozott egyedi mappára állítja be.  Ez a mappa megegyezik a teljes alkalmazáspéldányra. Alapértelmezés szerint az alkalmazás összes folyamatának munkakönyvtára az alkalmazás munkamappájára van állítva. Ez az a hely, ahol a folyamatok írhatják az adatokat. A kódcsomagba vagy kódbázisba történő adatírás nem ajánlott, mivel ezek a mappák megoszthatók a különböző alkalmazáspéldányok között, és törlődhetnek. További információ: [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>Konzolátirányítási elem

> [!WARNING]
> Ne használja a konzol átirányítást éles alkalmazásban, csak helyi fejlesztéshez és hibakereséshez használja. Átirányítja a konzol kimenetét az indítási parancsfájlból egy "log" nevű kimeneti fájlba azon a fürtcsomóponton, ahol az alkalmazás telepítve van és fut. További információ: [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint elem
Az EntryPoint által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. Az EntryPoint által megadott végrehajtható fájl a SetupEntryPoint sikeres kilépése után fut. Az eredményül kapott folyamatot a rendszer figyeli és újraindítja (a SetupEntryPoint programmal kezdődően), ha az bármikor leáll vagy összeomlik. További információ: [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarálja a Settings.xml fájlt tartalmazó PackageRoot csoportban. Ez a fájl a felhasználó által definiált, kulcs-érték pár beállítások azon szakaszait tartalmazza, amelyeket a folyamat futásközben vissza tud olvasni. A frissítés során, ha csak a ConfigPackage verzió változott, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a folyamatot, hogy a konfigurációs beállítások megváltoztak, így azok dinamikusan újratölthetők. További információ: [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
A szolgáltatás által használt erőforrások at írja le, és a szolgáltatás telepítésekor módosítható konkretizált. Ezekhez az erőforrásokhoz való hozzáférés az alkalmazásjegyzék rendszerszámos és házirend szakaszán keresztül szabályozott. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
A szolgáltatás végpontjait határozza meg. További információ: [Végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont-elem
A szolgáltatásjegyzékben deklarált végpont felülbírálása. További információ: [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData szolgáltatás jegyzékelemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
Deklaratív módon leírja a szolgáltatás típusát és verzióját. Felsorolja a függetlenül frissíthető kódot, konfigurációt és adatcsomagokat, amelyek együttesen egy vagy több szolgáltatástípus ttámogató szolgáltatáscsomagot állítanak össze. Erőforrások, diagnosztikai beállítások és a szolgáltatás metaadatai, például a szolgáltatás típusa, az állapot-tulajdonságok és a terheléselosztási metrikák is meg vannak adva. További információ: [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Azt határozza meg, hogy a manitkulásban a codepackage milyen szolgáltatástípusokat támogat. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. A szolgáltatástípusok deklarálva a jegyzékfájl szintjén vannak deklarálva, nem pedig a kódcsomag szintjén. További információ: [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType elem
Állapotalapú szolgáltatástípus leírása. További információ: [StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípust támogató kódcsomagot ismertet. Ha egy szolgáltatást ezen szolgáltatástípusok egyikével hoznak meg, a jegyzékfájlban deklarált összes kódcsomag a belépési pontok futtatásával aktiválódik. Az eredményül kapott folyamatoknak futásidőben regisztrálniuk kell a támogatott szolgáltatástípusokat. Ha több kódcsomag van, azok mind aktiválódnak, amikor a rendszer a deklarált szolgáltatástípusok bármelyikét keresi. További információ: [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint elem
Az EntryPoint által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Egy külön beállítási belépési pont jelenléte elkerüli, hogy a szolgáltatásgazda nagy jogosultságokkal fusson hosszabb ideig. Az EntryPoint által megadott végrehajtható fájl a SetupEntryPoint sikeres kilépése után fut. Az eredményül kapott folyamatot a rendszer figyeli és újraindítja (a SetupEntryPoint programmal kezdődően), ha az bármikor leáll vagy összeomlik. További információ: [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program elem
A végrehajtható fájl neve.  Például "MySetup.bat" vagy "MyServiceHost.exe". További információ: [Program Element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Munkamappa elem
A folyamat munkakönyvtára a kódcsomagban azon a fürtcsomóponton, ahol az alkalmazás telepítve van. Három értéket adhat meg: Munka (az alapértelmezett), CodePackage vagy CodeBase. A CodeBase azt adja meg, hogy a munkakönyvtár arra a könyvtárra van-e beállítva, amelyben az EXE definiálva van a kódcsomagban. A CodePackage a munkakönyvtárat állítja be a kódcsomag gyökérkönyvtárába, függetlenül attól, hogy az EXE hol van definiálva a kódcsomag könyvtárában. A munkabeiktatot a csomóponton létrehozott egyedi mappára állítja be.  Ez a mappa megegyezik a teljes alkalmazáspéldányra. Alapértelmezés szerint az alkalmazás összes folyamatának munkakönyvtára az alkalmazás munkamappájára van állítva. Ez az a hely, ahol a folyamatok írhatják az adatokat. A kódcsomagba vagy kódbázisba történő adatírás nem ajánlott, mivel ezek a mappák megoszthatók a különböző alkalmazáspéldányok között, és törlődhetnek. További információ: [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarálja a Settings.xml fájlt tartalmazó PackageRoot csoportban. Ez a fájl a felhasználó által definiált, kulcs-érték pár beállítások azon szakaszait tartalmazza, amelyeket a folyamat futásközben vissza tud olvasni. A frissítés során, ha csak a ConfigPackage verzió változott, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a folyamatot, hogy a konfigurációs beállítások megváltoztak, így azok dinamikusan újratölthetők. További információ: [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage elem
Deklarál egy, a Name attribútum által elnevezett mappát a PackageRoot csoportban, amely a folyamat által futásidőben felhasználható statikus adatfájlokat tartalmaz. A Service Fabric újrahasznosítja az összes, a gazdagépben megadott és támogatási csomagokban megadott EXEs és DLLHOST-okat, amikor a szolgáltatásjegyzékben felsorolt adatcsomagok bármelyike frissül. További információ: [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
A szolgáltatás által használt erőforrások at írja le, és a szolgáltatás telepítésekor módosítható konkretizált. Ezekhez az erőforrásokhoz való hozzáférés az alkalmazásjegyzék rendszerszámos és házirend szakaszán keresztül szabályozott. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
A szolgáltatás végpontjait határozza meg. További információ: [Végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont-elem
A szolgáltatásjegyzékben deklarált végpont felülbírálása. További információ: [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

