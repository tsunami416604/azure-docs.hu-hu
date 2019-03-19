---
title: A rendszer és a helyi biztonsági fiókok egy Azure Service Fabric-szolgáltatás futtatásához |} A Microsoft Docs
description: Útmutató a Service Fabric-alkalmazás futtatását a rendszer és a helyi biztonsági fiókok.  Rendszerbiztonsági tagok létrehozásával és alkalmazásával a Futtatás mint házirend biztonságos futtatására a szolgáltatásait.
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
ms.openlocfilehash: f454ec7805db0a79f9346f252809c9d7f6869734
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871011"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Szolgáltatás futtatása egy helyi felhasználói fiók vagy helyi rendszerfiók
Az Azure Service Fabric használatával gondoskodhat a különböző felhasználói fiókok a fürtben futó alkalmazásokat. Alapértelmezés szerint Service Fabric-alkalmazásokat, amely a Fabric.exe folyamatban fut, a fiók alatt fut. A Service Fabric emellett lehetővé teszi a helyi felhasználói vagy rendszer fiókja alatt alkalmazások futtatására. Támogatott helyi rendszer fiók típusok a következők **LocalUser**, **NetworkService**, **LocalService**, és **LocalSystem**.  Ha a Service Fabric Windows önálló fürtön futtatja, a szolgáltatás alatt futtathatja [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoportosan felügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md).

Az alkalmazásjegyzék határoz meg a szolgáltatások és biztonságos erőforrások futtatásához szükséges felhasználói fiókokat a **rendszerbiztonsági tagok** szakaszban. Is határozza meg, és hozzon létre felhasználói csoportokat, hogy egy vagy több felhasználó együtt is kezelhető. Ez akkor hasznos, amikor több felhasználó számára a különböző belépési pontok és a közös jogosultságok, amelyek elérhetők a csoport szintjén kell.  A felhasználók ezután egy futtató szabályzatot, amely egy adott szolgáltatás vagy az alkalmazás összes szolgáltatásban alkalmazott hivatkozott. 

Alapértelmezés szerint a RunAs házirend vonatkozik a hlavní vstupní BOD.  Is alkalmazhat egy Futtatás mint házirend a beállítási belépési pontra, ha szeretné [bizonyos magas szintű jogosultsággal rendelkező telepítő műveletek egy rendszerfiókból futtatásához](service-fabric-run-script-at-service-startup.md), vagy mindkét fő és a beállítási belépési pontok.  

> [!NOTE] 
> Ha egy futtató házirend vonatkozik egy szolgáltatás, és a szolgáltatásjegyzék deklarálja a HTTP protokollt végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy**.  További információkért lásd: [HTTP vagy HTTPS-végpontokat a biztonsági hozzáférési szabályzat hozzárendelése](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Szolgáltatás futtatása helyi felhasználóként
Létrehozhat egy helyi felhasználót, amelyek segítségével biztonságossá tétele az alkalmazásban egy szolgáltatás. Ha egy **LocalUser** fióktípus szakaszban van megadva a rendszerbiztonsági tagok az alkalmazásjegyzékben, a Service Fabric hoz létre a helyi felhasználói fiókok gépekre, ahol az alkalmazás üzemel. Alapértelmezés szerint ezek a fiókok nem rendelkeznek az alkalmazásjegyzékben megadott nevével megegyező nevet (például *Customer3* a következő application manifest példában). Ehelyett dinamikusan jönnek létre, és véletlenszerű jelszavakat.

Az a **RunAsPolicy** szakasz egy **ServiceManifestImport**, adja meg a felhasználói fiókot a **rendszerbiztonsági tagok** futtatásához a szolgáltatáscsomag kód szakaszban.  Az alábbi példa bemutatja, hogyan hozzon létre egy helyi felhasználót, és a egy Futtatás mint házirend alkalmazása a hlavní vstupní BOD:

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
Hozzon létre felhasználói csoportokat, és a egy vagy több felhasználó hozzáadása a csoporthoz. Ez akkor hasznos, ha több felhasználó számára a különböző belépési pontok, és szükségük van bizonyos közös jogosultságok, amely a csoport szintjén érhető el. A következő application manifest példa mutatja egy helyi csoport nevű *LocalAdminGroup* , amely rendszergazdai jogosultságokkal rendelkezik. Két olyan felhasználó, *Customer1* és *Customer2*, a helyi csoport tagjai. Az a **ServiceManifestImport** részben, egy Futtatás mint házirend érvényes futtassa a *Stateful1Pkg* kód csomagot *Customer2*.  Egy másik futtató házirend érvényes futtassa a *Web1Pkg* kód csomagot *Customer1*.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Az összes szolgáltatás kódcsomagok egy alapértelmezett házirend alkalmazása
Használja a **DefaultRunAsPolicy** szakaszban adjon meg egy alapértelmezett felhasználói fiókja az összes kódot csomagokat, amelyek nincsenek egy adott **RunAsPolicy** definiálva. Ha meg van határozva a szolgáltatásjegyzék, az alkalmazás a kód csomagokat a legtöbb kell futni ugyanaz a felhasználó, az alkalmazás csak definiálhat egy alapértelmezett futtató szabályzatot, hogy felhasználói fiókkal. Az alábbi példa azt jelenti, hogy ha a kódcsomag nem rendelkezik egy **RunAsPolicy** megadva, a kódcsomag alatt futnia kell a **MyDefaultAccount** a rendszerbiztonsági tagok szakaszban megadott felhasználó.  Támogatott fióktípusok a következők: Helyifelhasznalo, NetworkService, LocalSystem és LocalService.  Ha egy helyi felhasználói vagy szolgáltatás használata esetén is meg a fiók nevét és jelszavát.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>A konzol-átirányítás használatával helyben kódcsomag hibakeresése
Bizonyos esetekben hasznos, megtekintheti a futó szolgáltatás konzolkimenete hibakeresés céljából. A belépési pont a szolgáltatásjegyzékben, amely a kimenetet egy fájlba írja be a konzol átirányítás házirendjében is megadhatja. Az alkalmazás nevű mappát a kimeneti fájl írt **log** a fürtcsomópontra, ahol az alkalmazás üzembe helyezését és futtatását. 

> [!WARNING]
> Soha ne használja a konzolon átirányítási házirendet egy alkalmazásban, amely az éles környezetben telepített, mert ez befolyásolhatja az alkalmazás feladatátvételt. *Csak* helyi fejlesztés és hibakeresés céljából használja.  
> 
> 

A következő Szolgáltatásjegyzék FileRetentionCount értékkel konzol-átirányítás engedélyezése példában látható:

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
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
