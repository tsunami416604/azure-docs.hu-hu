---
title: Adja meg a Service Fabric Szolgáltatásvégpontok |} Microsoft Docs
description: Hogyan leírására végpont erőforrások egy szolgáltatás jegyzékben, beleértve a HTTPS-végpontnak beállítása
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ce2bc8cc8d9b149b16aee9c5e601d9872621e277
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>Erőforrások meghatározása szolgáltatásjegyzékben
## <a name="overview"></a>Áttekintés
A szolgáltatás jegyzékfájl lehetővé teszi, hogy a lefordított kódjának módosítása nélkül kell a deklarálva, illetve nem módosítható a szolgáltatás által használt erőforrások. Azure Service Fabric endpoint erőforrás támogatja a szolgáltatáshoz. A hozzáférés az erőforrásokhoz, a szolgáltatás jegyzékben megadott szabályozhatja a az alkalmazás jegyzékében a(z) biztonsági csoporthoz keresztül. A nyilatkozat az erőforrások lehetővé teszi, hogy módosítani kell a központi telepítéskor, tehát a szolgáltatás nem szükséges egy új konfigurációs mechanizmusa bevezetni ezekhez az erőforrásokhoz. A séma meghatározása a ServiceManifest.xml fájl telepítve van a Service Fabric SDK-val, és az eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Végpontok
A szolgáltatás jegyzékben egy végpont erőforrás van definiálva, amikor a Service Fabric rendeli hozzá portokat a fenntartott alkalmazás porttartományát megadásakor a port nincs explicit módon. Keresse meg a végpont például *ServiceEndpoint1* a jegyzék részlet e után szerepel. Emellett szolgáltatások egy adott erőforrás-portot is kérheti. Különböző fürt csomópontjain futó szolgáltatás replikák rendelhetők hozzá különböző portszámokat, miközben ugyanazon a csomóponton futó szolgáltatás replikák megosztani a port. A szolgáltatás replikák használhatja ezeket a portokat szükség szerint a replikáció és a figyelő az ügyféli kérelmek részére.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Ha több kód csomagot egyetlen service-csomag, akkor a kód csomag is meg lehet hivatkozni a **végpontok** szakasz.  Például ha **ServiceEndpoint2a** és **ServiceEndpoint2b** a végpontok különböző kód csomagok, az egyes végpontok megfelelő kódcsomag hivatkozó azonos szolgáltatás csomagból van pontosítva lett azzal az alábbiak szerint:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Tekintse meg [állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md) bővebben a végpontok hivatkozik a konfigurációs csomag beállítások (settings.xml) fájl olvasásához.

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Példa: a szolgáltatás HTTP-végponttal megadása
A következő szolgáltatás jegyzékfájl egy TCP végponti erőforrás és a két HTTP-végpont erőforrást a &lt;erőforrások&gt; elemet.

HTTP-végpontokról a program automatikusan hozzáférés-vezérlési lista a Service Fabric használna.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Példa: a szolgáltatás HTTPS-végpontnak megadása
A HTTPS protokollt biztosít a kiszolgáló hitelesítése és ügyfél – kiszolgáló kommunikáció titkosítására is használja. A Service Fabric-szolgáltatás a HTTPS engedélyezéséhez adja meg a protokoll a *erőforrások -> Végpontok végpont ->* szakasz a szolgáltatás jegyzékfájl, amint azt korábban a végpont *ServiceEndpoint3*.

> [!NOTE]
> A szolgáltatás protokoll nem módosítható az alkalmazásfrissítés során. Amennyiben a frissítés során megváltoztak, akkor használhatatlanná tévő változást.
> 
> 

Íme egy példa, hogy be kell állítani a HTTPS-hez applicationmanifest jegyzékben. Meg kell adni a tanúsítvány ujjlenyomatát. A EndpointRef EndpointResource ServiceManifest, amelynek beállítása a HTTPS protokollt a hivatkozás. Egynél több EndpointCertificate adhat hozzá.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Linux-fürtök esetén a **MY** tárolja a mappát az alapértelmezett **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Végpontok ServiceManifest.xml felülbírálása

Adja hozzá egy testvér ConfigOverrides szakaszra használandó ResourceOverrides szakaszt az applicationmanifest jegyzékben. Ebben a szakaszban adja meg a felülbírálás a végpontok szakasz a szolgáltatás jegyzékben megadott erőforrások szakaszában. Futásidejű végpontok felülbírálása támogatott 5.7.217/SDK 2.7.217 és magasabb.

Ahhoz, hogy a végpont a következő ApplicationParameters módosítása az applicationmanifest jegyzékben használatával ServiceManifest felülbírálása:

A ServiceManifestImport szakaszban a "ResourceOverrides" új szakasz hozzáadása

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

A Paraméterek hozzáadása alatt:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Most már az alkalmazás telepítése közben átadhatók ezeket az értékeket a ApplicationParameters, például:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Megjegyzés: Ha az értékek biztosít a ApplicationParameters üres érték azt vissza a megfelelő EndPointName a ServiceManifest a megadott alapértelmezett érték.

Példa:

Ha a megadott ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

És Port1 és Protocol1 alkalmazás paraméter értéke null vagy üres. A port továbbra is, amelyekről ServiceFabric. És a protokoll lesz tcp.

Tegyük fel, adjon meg egy megfelelő értéket. Például a porthoz megadott karakterlánc-érték "Foo" helyett egy egész szám.  Új ServiceFabricApplication parancs egy hibával meghiúsul: name "ServiceEndpoint1" attribútum "Port1" szakaszban "ResourceOverrides" felülbíráló paraméter érvénytelen. A megadott érték "PEL" és "int" szükség.
