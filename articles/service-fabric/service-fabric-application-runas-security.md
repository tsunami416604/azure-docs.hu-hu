---
title: Egy Azure Service Fabric-szolgáltatás futtatásához a rendszer és a helyi biztonsági fiókok |} Microsoft Docs
description: Útmutató a Service Fabric-alkalmazás futtatása a rendszer és a helyi biztonsági fiókok.  Rendszerbiztonsági létrehozása, és a szolgáltatások biztonságosan futtatásához futtató házirend alkalmazása.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 33ca23834f35e631c6943ec22a88f4fe3dc853e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>A szolgáltatás futtatásához egy helyi felhasználói fiók vagy helyi rendszer fiókként
Azure Service Fabric használatával biztonságossá teheti a különböző felhasználói fiókok a fürtben futó alkalmazás számára. Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. A Service Fabric is lehetővé teszi az alkalmazások helyi felhasználói vagy rendszer fiókkal futtassa. Támogatott helyi rendszer fiók típusok **LocalUser**, **NetworkService**, **LocalService**, és **LocalSystem**.  Ha a Service Fabric Windows önálló fürtön fut, a szolgáltatás futtatható [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoport felügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md).

Az alkalmazásjegyzékben adhat meg a szolgáltatások vagy a biztonságos erőforrások futtatásához szükséges felhasználói fiókokat a **rendszerbiztonsági tagok** szakasz. Is határozza meg, és hozzon létre felhasználói csoportokat, így egyszerre egy vagy több felhasználó is felügyelhetők. Ez akkor hasznos, ha több felhasználó számára a különböző belépési pontok és a közös jogosultságok, a csoportok szintjén rendelkezésre álló van szükségük.  A felhasználók majd hivatkozott egy RunAs-házirendet, amely egy adott szolgáltatás vagy az alkalmazás összes szolgáltatását. 

Alapértelmezés szerint a RunAs-házirend alkalmazási a fő belépési ponthoz.  Is alkalmazhat egy RunAs házirendet a telepítés belépési ponthoz, ha szeretné [bizonyos magas szintű jogosultságokat igénylő telepítési műveletekhez, a rendszer fiókon futtassa](service-fabric-run-script-at-service-startup.md), vagy mindkét fő és belépési pontok beállítása.  

> [!NOTE] 
> Ha RunAs házirend vonatkozik egy szolgáltatás, és a szolgáltatás jegyzékfájl deklarálja a HTTP protokoll végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy**.  További információkért lásd: [rendelje hozzá a HTTP és HTTPS-végpont egy biztonsági házirend](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>A szolgáltatás futtatásához pedig helyi felhasználóként
Létrehozhat egy helyi felhasználót, amelyek segítségével biztonságossá tétele az alkalmazásban egy szolgáltatás. Ha egy **LocalUser** fióktípus szakaszban van megadva a rendszerbiztonsági tagoknak az alkalmazás jegyzékének Service Fabric helyi felhasználói fiókokat hoz gépekre, ahol az alkalmazás központi telepítése. Alapértelmezés szerint ezek a fiókok nem rendelkezik az alkalmazás jegyzékében megadottakkal azonos néven (például *Customer3* az alkalmazás manifest ügynökök). Ehelyett dinamikusan jön létre, és véletlenszerű jelszót.

A a **RunAsPolicy** szakasz egy **ServiceManifestImport**, adja meg a felhasználói fiókot a **rendszerbiztonsági tagok** szakasz a szolgáltatáscsomag kód futtatásához.  A következő példa bemutatja, hogyan hozzon létre egy helyi felhasználót, és egy RunAs házirend alkalmazása a fő belépési pont:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Hozzon létre felhasználói csoportokat, és egy vagy több felhasználó hozzáadása a csoporthoz. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük. A következő alkalmazás jegyzékének példában nevű helyi csoport *LocalAdminGroup* , amely rendszergazdai jogosultságokkal rendelkezik. Két felhasználók *Customer1* és *Customer2*, a helyi csoport tagjai. Az a **ServiceManifestImport** részben, a házirend hatálya alá futtatása RunAs a *Stateful1Pkg* kód csomag *Customer2*.  Egy másik Futtatás mint házirend hatálya alá futtassa a *Web1Pkg* kód csomag *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Minden szolgáltatás kód csomagok egy alapértelmezett házirend alkalmazása
Használja a **DefaultRunAsPolicy** szakaszban adja meg az összes kód alapértelmezett felhasználói fiók csomagok, amelyeket nem kell egy adott **RunAsPolicy** definiálva. Ha a szolgáltatás egy alkalmazás által használt jegyzékfájlban megadott kód csomagokat a legtöbb kell futni ugyanahhoz a felhasználóhoz, az alkalmazás csak definiálhat egy alapértelmezett RunAs házirendet, hogy felhasználói fiókkal. Az alábbi példa azt jelenti, hogy ha a kódcsomag nem rendelkezik egy **RunAsPolicy** megadva, a kódcsomag kell futnia a **MyDefaultAccount** a rendszerbiztonsági tagok szakaszban megadott felhasználó.  Támogatott fióktípusok a következők: LocalUser, NetworkService, LocalSystem és LocalService.  Egy helyi felhasználói vagy szolgáltatás használata esetén is adja meg, a fiók nevét és jelszavát.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>A kódcsomag helyileg a konzol átirányítással hibakeresése
Alkalmanként célszerű a konzol kimeneti futó szolgáltatásból megjelenítéséhez hibakeresés céljából. A belépési pont a jegyzékben szolgáltatás, amely a kimeneti fájlba írja a konzol átirányítási házirendet állíthat be. A kimeneti fájl beíródik nevű alkalmazás mappát **napló** a fürtcsomóponton, ha az alkalmazás üzemel, és futtassa. 

> [!WARNING]
> Soha ne használja a konzol átirányítási házirend a kérelmet, mert ez hatással lehet az alkalmazás feladatátvételi éles környezetben telepített. *Csak* használja ezt a helyi fejlesztési és hibakeresési célra.  
> 
> 

A következő Szolgáltatásjegyzék példa azt mutatja meg, konzol átirányítása FileRetentionCount értékű engedélyezése:

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
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
