---
title: Szolgáltatás futtatása rendszer- és helyi biztonsági fiókok alatt
description: Ismerje meg, hogyan futtathat egy Service Fabric-alkalmazást rendszer- és helyi biztonsági fiókok alatt.  Hozzon létre rendszerbiztonsági tagokat, és alkalmazza a Futtatás as házirendet a szolgáltatások biztonságos futtatásához.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610121"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Szolgáltatás futtatása helyi felhasználói fiókként vagy helyi rendszerfiókként
Az Azure Service Fabric használatával biztonságossá teheti a fürtben különböző felhasználói fiókok alatt futó alkalmazásokat. Alapértelmezés szerint a Service Fabric-alkalmazások a Fabric.exe folyamat alatt futó fiók alatt futnak. A Service Fabric azt is lehetővé teszi, hogy alkalmazásokat futtasson egy helyi felhasználó vagy rendszerfiók alatt. A támogatott helyi rendszerfióktípusok a következők: **LocalUser**, **NetworkService**, **LocalService**és **LocalSystem**.  Ha a Service Fabric szolgáltatást önálló Windows-fürtön futtatja, egy szolgáltatást [az Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoportfelügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md)alatt futtathat.

Az alkalmazásjegyzékben megadhatja a szolgáltatások futtatásához vagy az erőforrások védelméhez szükséges felhasználói fiókokat a **Rendszerbiztonsági tagok** szakaszban. Felhasználói csoportokat is definiálhat és létrehozhat, hogy egy vagy több felhasználó együtt kezelhető legyen. Ez akkor hasznos, ha több felhasználó van különböző szolgáltatásbelépési pontokhoz, és közös jogosultságokra van szükségük, amelyek a csoport szintjén érhetők el.  A felhasználók ezután hivatkoznak egy RunAs-házirendben, amely egy adott szolgáltatásra vagy az alkalmazás összes szolgáltatására vonatkozik. 

Alapértelmezés szerint a RunAs házirend a fő belépési pontra vonatkozik.  RunAs házirendet is alkalmazhat a beállítási belépési pontra, ha [bizonyos magas jogosultságokkal rendelkező telepítési műveleteket kell futtatnia egy rendszerfiók alatt,](service-fabric-run-script-at-service-startup.md)vagy a fő és a beállítási belépési pontokon is.  

> [!NOTE] 
> Ha RunAs házirendet alkalmaz egy szolgáltatásra, és a szolgáltatásjegyzék deklarálja a végponterőforrásait a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy házirendet.**  További információt a [Http- és HTTPS-végpontokhoz szükséges biztonsági hozzáférési házirend hozzárendelése című témakörben talál.](service-fabric-assign-policy-to-endpoint.md) 
>

## <a name="run-a-service-as-a-local-user"></a>Szolgáltatás futtatása helyi felhasználóként
Létrehozhat egy helyi felhasználót, amely segítségével az alkalmazáson belül biztonságos sá válhat egy szolgáltatás. Ha egy **LocalUser** fióktípus meg van adva az alkalmazásjegyzék rendszerbiztonsági tagok szakaszában, a Service Fabric helyi felhasználói fiókokat hoz létre azokon a gépeken, ahol az alkalmazás telepítve van. Alapértelmezés szerint ezek a fiókok nem ugyanazokkal a nevekkel rendelkeznek, mint az alkalmazásjegyzékben megadottak (például a következő alkalmazásjegyzék-jegyzékben szereplő *Customer3).* Ehelyett dinamikusan generált, és véletlenszerű jelszavakat.

A **ServiceManifestImport** **RunAsPolicy** szakaszában adja meg a Rendszerkódcsomag futtatásához a **Rendszernévi akta** szakaszfelhasználói fiókját.  A következő példa bemutatja, hogyan hozhat létre helyi felhasználót, és hogyan alkalmazhat RunAs házirendet a fő belépési pontra:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Helyi felhasználói csoport létrehozása
Létrehozhat felhasználói csoportokat, és hozzáadhat egy vagy több felhasználót a csoporthoz. Ez akkor hasznos, ha több felhasználó van különböző szolgáltatásbelépési pontokhoz, és rendelkezniük kell bizonyos közös jogosultságokkal, amelyek a csoport szintjén érhetők el. A következő alkalmazásjegyzék-példa egy *LocalAdminGroup* nevű helyi csoportot mutat be, amely rendszergazdai jogosultságokkal rendelkezik. Két felhasználó, *a Customer1* és *a Customer2*a helyi csoport tagjaivá vált. A **ServiceManifestImport** szakaszban egy RunAs-házirend et alkalmaz a *Stateful1Pkg* kódcsomag *Customer2*néven való futtatására.  Egy másik RunAs-házirend et alkalmazanak a *Web1Pkg* kódcsomag *Customer1*néven történő futtatásához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Alapértelmezett házirend alkalmazása az összes szolgáltatáskódcsomagra
A **DefaultRunAsPolicy** szakaszsegítségével alapértelmezett felhasználói fiókot adhat meg minden olyan kódcsomaghoz, amelyhez nincs definiálva egy adott **RunAsPolicy.** Ha az alkalmazás által használt szolgáltatásjegyzékben megadott kódcsomagok többsége ugyanazon felhasználó alatt kell futnia, az alkalmazás csak definiálhat egy alapértelmezett RunAs-házirendet az adott felhasználói fiókkal. A következő példa azt adja meg, hogy ha egy kódcsomag nem rendelkezik **runAsPolicy megadott runAsPolicy,** a kódcsomag kell futtatni a **MyDefaultAccount** felhasználó meg a rendszernévi tagok szakaszban megadott.  A támogatott fióktípusok a Következők: LocalUser, NetworkService, LocalSystem és LocalService.  Ha helyi felhasználót vagy szolgáltatást használ, adja meg a fiók nevét és jelszavát is.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Kódcsomag helyi hibakeresése konzolátirányítással
Esetenként hibakeresés céljából hasznos, ha egy futó szolgáltatás konzolkimenetét látja. A szolgáltatásjegyzék belépési pontján konzolátirányítási házirendet állíthat be, amely a kimenetet egy fájlba írja. A fájlkimenet a **napló** nevű alkalmazásmappába kerül, amely az alkalmazás telepítését és futtatását tartalmazó fürtcsomóponton található. 

> [!WARNING]
> Soha ne használja a konzolátirányítási házirendet az éles környezetben telepített alkalmazásokban, mert ez hatással lehet az alkalmazás feladatátvételre. Ezt *csak* helyi fejlesztési és hibakeresési célokra használja.  
> 
> 

A következő szolgáltatásjegyzék-példa a konzol átirányítását engedélyezi FileRetentionCount értékkel:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
