---
title: Az Azure Service Fabric reliable services-alkalmazás-jegyzékfájl példák |} A Microsoft Docs
description: 'Útmutató: alkalmazás konfigurálása és a Service Fabric reliable services-alkalmazás jegyzékfájl beállításait.'
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665967"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Példák Reliable Services-alkalmazások és szolgáltatások jegyzékeire
A következő példák egy ASP.NET Core webes előtérrendszert és egy állapotalapú háttérszolgáltatás a Service Fabric-alkalmazás az alkalmazás és szolgáltatás jegyzékfájljainak. Ezekben a példákban az a célja, hogy milyen beállítások érhetők el, és azok használatát. Ezen alkalmazás és jegyzékek alapulnak a [Service Fabric .NET – rövid útmutató](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) jegyzékfájlok.

A következő funkciók jelennek meg:

|Jegyzék|Szolgáltatások|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| [Erőforrás-szabályozás](service-fabric-resource-governance.md), [szolgáltatás futtatása egy helyi rendszergazdai fiók](service-fabric-application-runas-security.md), [egy alapértelmezett házirend alkalmazása minden szolgáltatás kódcsomagok](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [hozzon létre felhasználói és rendszerbiztonsági tagok](service-fabric-application-runas-security.md), egy adatcsomag, szolgáltatáspéldányok közötti megosztása [Szolgáltatásvégpontok felülbírálása](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService szolgáltatásjegyzékben| [Parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md), [HTTPS-végpont meghatározása](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService szolgáltatásjegyzékben| [Deklarálja a konfigurációs csomag](service-fabric-application-and-service-manifests.md), [deklaráljon egy adatcsomag](service-fabric-application-and-service-manifests.md), [végpont konfigurálása](service-fabric-service-manifest-resources.md)| 

Lásd: [Application manifest elemek](#application-manifest-elements), [VotingWeb service manifest elemek](#votingweb-service-manifest-elements), és [VotingData service manifest elemek](#votingdata-service-manifest-elements) megadott XML-további információ elemek.

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
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

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

## <a name="votingweb-service-manifest"></a>VotingWeb szolgáltatásjegyzékben

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

## <a name="votingdata-service-manifest"></a>VotingData szolgáltatásjegyzékben

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

### <a name="resourceoverrides-element"></a>ResourceOverrides elem
Adja meg a végpontok szolgáltatásjegyzéki erőforrások deklarált erőforrás felülbírálásokat. További információkért lásd: [ResourceOverrides elem](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Végpontok elem
A végpont felülbírálása. További információkért lásd: [végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Végpont elem
A végpont felülbírálása a szolgáltatásjegyzékben deklarálva. További információkért lásd: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Házirendek elem
Az importált Szolgáltatásjegyzék a alkalmazni (végponti kötést, a csomag megosztási, a futtató és a biztonság hozzáférés) házirendeket ismerteti. További információkért lásd: [házirendek elem](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
A teljes szolgáltatási csomag szintjén alkalmazzák-erőforrás-szabályozási házirend határozza meg. További információkért lásd: [ServicePackageResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
Itt adható meg erőforráskorlátok a codepackage a. További információkért lásd: [ResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy Element
Azt jelzi, ha a code, config vagy data csomag azonos típusú szolgáltatás szolgáltatási példányai között lehetnek megosztva. További információkért lásd: [PackageSharingPolicy elem](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy Element
A hozzáférési a szolgáltatásjegyzékben definiált engedélyek egy egyszerű erőforrás (például egy végpont). Általában nagyon hasznos ellenőrzése és-szolgáltatások elérésének korlátozása különböző erőforrások minimalizálása érdekében a biztonsági kockázatokat. Ez akkor különösen fontos, ha az alkalmazást a létrehozása a gyűjteményből, a marketplace-ről szolgáltatásokat, amelyek különböző fejlesztők által fejlesztett. További információkért lásd: [SecurityAccessPolicy elem](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy elem
Megadja a helyi felhasználói vagy a helyi rendszer fiók, amely a szolgáltatások fognak futni. A Windows Server-telepítéseket, hol érhető el az Azure Active Directory tartományi fiókok támogatottak. Alapértelmezés szerint az alkalmazások, amely a Fabric.exe folyamatban fut, a fiók alatt futnak. Alkalmazásokat is futtathat más fiókok, amelyek a rendszerbiztonsági tagok szakaszt deklarálni kell. Ha egy futtató házirend vonatkozik egy szolgáltatás, és a szolgáltatásjegyzék deklarálja a HTTP protokollt végpont erőforrások, meg kell adni egy SecurityAccessPolicy annak érdekében, hogy a portok számára lefoglalt ezeket a végpontokat megfelelően-e hozzáférés-vezérlési értékében jelenik meg a Futtatás mint a szolgáltatás fut, a felhasználói fiók. HTTPS-végpontokat meg kell adnia egy EndpointBindingPolicy térjen vissza az ügyfél a tanúsítvány nevének is. További információkért lásd: [RunAsPolicy elem](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices elem
Deklarálja a szolgáltatáspéldány, amely automatikusan létrejön, amikor egy alkalmazás van példányosítva tento typ aplikace ellen. További információkért lásd: [DefaultServices elem](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatás-elem
Deklarálja a szolgáltatás automatikusan létrejön, amikor létrejön az alkalmazás. További információkért lásd: [szolgáltatás elem](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Elem statefulservice-ből
Az állapotalapú szolgáltatások határozza meg. További információkért lásd: [statefulservice-ből elem](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService Element
Állapotmentes szolgáltatás határozza meg. További információkért lásd: [StatelessService elem](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Rendszerbiztonsági tagok elem
Azt ismerteti, hogy a rendszerbiztonsági tagok (felhasználók, csoportok) szükséges az alkalmazás futtatásához, szolgáltatások és biztonságos erőforrásokhoz. A szabályzatok szakaszban hivatkozott rendszerbiztonsági tagok. További információkért lásd: [rendszerbiztonsági tagok elem](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Csoportok elem
Deklarálja csoportokat, biztonsági házirendeket lehet hivatkozni rendszerbiztonsági tagok. Csoportok hasznosak, ha több felhasználó számára a különböző belépési pontok, és szükségük van bizonyos közös jogosultságok, amely a csoport szintjén érhető el. További információkért lásd: [csoportok elem](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Csoportelemhez
Deklarálja a rendszerbiztonsági tag, mint egy csoportot, amely a házirendeket lehet hivatkozni. További információkért lásd: [Csoportelemhez](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Tagság elem
 További információkért lásd: [tagsági elem](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup Element
 További információkért lásd: [SystemGroup elem](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Felhasználók elem
Deklarálja a felhasználók egy csoportja, biztonsági házirendeket lehet hivatkozni rendszerbiztonsági tagok. További információkért lásd: [felhasználók elem](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Felhasználói elem
A felhasználó egyszerű, biztonsági házirendeket lehet hivatkozni, amely deklarálja. További információkért lásd: [felhasználói elem](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>Tagja(Pénzügy) elem
Felhasználók bármely meglévő tagságcsoportot hozzáadhatók, így is a tulajdonságok és biztonsági beállításait a csoportházirend is örököl. A csoportházirend segítségével különböző szolgáltatásokat, vagy ugyanazt a szolgáltatást (egy másik gépen) elérésének igénylő külső erőforrások védelmét. További információkért lásd: [tagja(Pénzügy) elem](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup Element
Adja hozzá a felhasználót a rendszer csoport.  A rendszer csoport a csoportok szakaszban kell definiálni. További információkért lásd: [SystemGroup elem](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Csoportelemhez
Adja hozzá a felhasználót, hogy a csoport.  A csoport a csoportok szakaszban kell definiálni. További információkért lásd: [Csoportelemhez](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Házirendek elem
Popisuje zásady (naplógyűjtés, alapértelmezett futtató, egészségügyi és biztonsági access) az alkalmazás szintjén alkalmazható. További információkért lásd: [házirendek elem](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy Element
Adja meg az összes szolgáltatás kód csomagokat, amelyek nem rendelkeznek egy adott RunAsPolicy az ServiceManifestImport szakaszban meghatározott alapértelmezett felhasználói fiók. További információkért lásd: [DefaultRunAsPolicy elem](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb service manifest elemek
### <a name="servicemanifest-element"></a>ServiceManifest elemben
Szolgáltatás típusa és verziója deklaratív ismerteti. A egymástól függetlenül frissíthető kód, a konfiguráció és az adatok csomagokat, amelyek együttesen egy service-csomag egy vagy több szolgáltatás típusainak listáját. Erőforrások, a diagnosztikai beállítások és a szolgáltatás típusa, egészségügyi tulajdonságok és terheléselosztási mérőszámokat, például a szolgáltatás metaadatai is meg van adva. További információkért lásd: [ServiceManifest elemben](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Határozza meg, milyen szolgáltatástípusok támogatottak a CodePackage a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Szolgáltatástípusok deklarált a jegyzékfájl szintjén, és nem a kód szintű csomag. További információkért lásd: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Egy állapotmentes szolgáltatás típusának leírása. További információkért lásd: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Ismerteti egy kódcsomaghoz, amely támogatja a megadott szolgáltatási típusa. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Az amiatt végbemenő folyamatok várhatóan a támogatott szolgáltatástípusok regisztrálja a futási időben. Ha több kódcsomagok, az összes aktiválás, amikor a rendszer figyeli a nem a deklarált szolgáltatás típusok közül. További információkért lásd: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
Egy emelt szintű belépési pont, amely alapértelmezés szerint a Service Fabric (általában a NETWORKSERVICE fiókot) bármely más belépési pont előtt azonos hitelesítő adatokkal futtatja. A belépési pont által megadott végrehajtható fájlt az általában a hosszú ideig futó szolgáltatás gazdagép. Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. További információkért lásd: [SetupEntryPoint elem](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információkért lásd: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program elem
A végrehajtható fájl nevét.  Ha például a "MySetup.bat" vagy "MyServiceHost.exe" lehetőséget. További információkért lásd: [Program elem](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Argumentumok elem
 További információkért lásd: [argumentumok elem](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
A folyamat a kódcsomag a fürtcsomópontra, ahol az alkalmazás központi telepítése a munkakönyvtár. Három értékeket adhat meg: Munkahelyi (alapértelmezett), a CodePackage vagy a kódbázis. Kódbázis Megadja, hogy a munkakönyvtárban arra a könyvtárra, amelyben az EXE a kódcsomag van definiálva van-e beállítva. CodePackage beállítja a munkakönyvtárból abba a kódcsomag, függetlenül attól, hol van definiálva az EXE a kód adatcsomag-könyvtár gyökerében lehet. Munka a munkahelyi könyvtár a csomóponton létrehozott egyedi mappát állítja be.  Ez a mappa megegyezik a teljes alkalmazás-példány. Alapértelmezés szerint az összes folyamat, az alkalmazás a munkakönyvtárban értéke az alkalmazás munkahelyi mappa. Ez az, ahol a folyamatok adatokat írni. Adatok írása a kódcsomag vagy kódbázis nem ajánlott, mivel sikerült a különböző alkalmazáspéldányok között megosztott mappákat, és előfordulhat, hogy törli. További információkért lásd: [WorkingFolder elem](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection Element

> [!WARNING]
> Konzol-átirányítás használata az éles alkalmazásokban, ne csak a helyi fejlesztés és hibakeresés használja. Átirányítja a konzol kimenete az indítási szkript a kimeneti fájl látható az alkalmazás mappájában "log" nevű a fürtcsomópontra, ahol az alkalmazás üzembe helyezését és futtatását. További információkért lásd: [ConsoleRedirection elem](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint Element
A belépési pont által megadott végrehajtható fájlt az általában a hosszú ideig futó szolgáltatás gazdagép. Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. A belépési pont által megadott végrehajtható fájl SetupEntryPoint sikeres kilépés után futtatott. Az eredményül kapott folyamat figyel, és (kezdődő újra SetupEntryPoint) újraindul, ha valaha leáll vagy összeomlik. További információkért lásd: [BelépésiPont-elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információkért lásd: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklarálja a mappát, a Name attribútum alatt PackageRoot Settings.xml fájlt tartalmaz. Ezt a fájlt a felhasználó által definiált, a kulcs-érték párt a beállításokat, amelyeket a folyamat futási időben vissza tudja olvasni szakaszokat tartalmazza. Frissítés alatt Ha csak a ConfigPackage verziója megváltozott, majd a futó folyamat nem indítja újra. Ehelyett egy visszahívást, amely a konfigurációs beállításai megváltoztak, így azok dinamikusan kell tölteni a folyamat értesítést küld. További információkért lásd: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ezt a szolgáltatást, amely lefordított kód módosítása nélkül deklarálva, és módosítani, ha a szolgáltatás telepítése által használt erőforrások ismerteti. Ezekhez az erőforrásokhoz való hozzáférésének a rendszerbiztonsági tagok és a szabályzatok szakaszok az alkalmazás-jegyzékfájl. További információkért lásd: [erőforrások elem](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
Végpontok a szolgáltatás határozza meg. További információkért lásd: [végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont elem
A végpont felülbírálása a szolgáltatásjegyzékben deklarálva. További információkért lásd: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData service manifest elemek
### <a name="servicemanifest-element"></a>ServiceManifest elemben
Szolgáltatás típusa és verziója deklaratív ismerteti. A egymástól függetlenül frissíthető kód, a konfiguráció és az adatok csomagokat, amelyek együttesen egy service-csomag egy vagy több szolgáltatás típusainak listáját. Erőforrások, a diagnosztikai beállítások és a szolgáltatás típusa, egészségügyi tulajdonságok és terheléselosztási mérőszámokat, például a szolgáltatás metaadatai is meg van adva. További információkért lásd: [ServiceManifest elemben](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Határozza meg, milyen szolgáltatástípusok támogatottak a CodePackage a jegyzékfájlban. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Szolgáltatástípusok deklarált a jegyzékfájl szintjén, és nem a kód szintű csomag. További információkért lásd: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType Element
Egy állapotalapú szolgáltatás típusának leírása. További információkért lásd: [StatefulServiceType elem](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Ismerteti egy kódcsomaghoz, amely támogatja a megadott szolgáltatási típusa. Amikor egy szolgáltatás létrejön az említett szolgáltatás ellen, a jegyzékfájlban deklarált összes kódcsomagok a rendszer a belépési pontok futtatásával aktiválja. Az amiatt végbemenő folyamatok várhatóan a támogatott szolgáltatástípusok regisztrálja a futási időben. Ha több kódcsomagok, az összes aktiválás, amikor a rendszer figyeli a nem a deklarált szolgáltatás típusok közül. További információkért lásd: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
A belépési pont által megadott végrehajtható fájlt az általában a hosszú ideig futó szolgáltatás gazdagép. Egy külön szolgáltatásbeállítás belépési pontjának jelenléte elkerülhető, hogy nem kell futtatni a szolgáltatásgazda magas szintű jogosultságokkal rendelkező huzamosabb ideig. A belépési pont által megadott végrehajtható fájl SetupEntryPoint sikeres kilépés után futtatott. Az eredményül kapott folyamat figyel, és (kezdődő újra SetupEntryPoint) újraindul, ha valaha leáll vagy összeomlik. További információkért lásd: [BelépésiPont-elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információkért lásd: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program elem
A végrehajtható fájl nevét.  Ha például a "MySetup.bat" vagy "MyServiceHost.exe" lehetőséget. További információkért lásd: [Program elem](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
A folyamat a kódcsomag a fürtcsomópontra, ahol az alkalmazás központi telepítése a munkakönyvtár. Három értékeket adhat meg: Munkahelyi (alapértelmezett), a CodePackage vagy a kódbázis. Kódbázis Megadja, hogy a munkakönyvtárban arra a könyvtárra, amelyben az EXE a kódcsomag van definiálva van-e beállítva. CodePackage beállítja a munkakönyvtárból abba a kódcsomag, függetlenül attól, hol van definiálva az EXE a kód adatcsomag-könyvtár gyökerében lehet. Munka a munkahelyi könyvtár a csomóponton létrehozott egyedi mappát állítja be.  Ez a mappa megegyezik a teljes alkalmazás-példány. Alapértelmezés szerint az összes folyamat, az alkalmazás a munkakönyvtárban értéke az alkalmazás munkahelyi mappa. Ez az, ahol a folyamatok adatokat írni. Adatok írása a kódcsomag vagy kódbázis nem ajánlott, mivel sikerült a különböző alkalmazáspéldányok között megosztott mappákat, és előfordulhat, hogy törli. További információkért lásd: [WorkingFolder elem](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklarálja a mappát, a Name attribútum alatt PackageRoot Settings.xml fájlt tartalmaz. Ezt a fájlt a felhasználó által definiált, a kulcs-érték párt a beállításokat, amelyeket a folyamat futási időben vissza tudja olvasni szakaszokat tartalmazza. Frissítés alatt Ha csak a ConfigPackage verziója megváltozott, majd a futó folyamat nem indítja újra. Ehelyett egy visszahívást, amely a konfigurációs beállításai megváltoztak, így azok dinamikusan kell tölteni a folyamat értesítést küld. További információkért lásd: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage elem
Deklarálja a mappát, a Name attribútum PackageRoot, amely tartalmazza a futási időben a folyamat által használt statikus adatfájlok alatt. A Service Fabric exe, és a szolgáltatásjegyzékben szereplő adatok csomagok frissítésekor a gazdagép és a támogatási csomag megadott DLLHOSTs fog újrahasznosítása. További információkért lásd: [DataPackage elem](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ezt a szolgáltatást, amely lefordított kód módosítása nélkül deklarálva, és módosítani, ha a szolgáltatás telepítése által használt erőforrások ismerteti. Ezekhez az erőforrásokhoz való hozzáférésének a rendszerbiztonsági tagok és a szabályzatok szakaszok az alkalmazás-jegyzékfájl. További információkért lásd: [erőforrások elem](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok elem
Végpontok a szolgáltatás határozza meg. További információkért lásd: [végpontok elem](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont elem
A végpont felülbírálása a szolgáltatásjegyzékben deklarálva. További információkért lásd: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

