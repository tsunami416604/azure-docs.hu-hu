---
title: Service Fabric szolgáltatási végpontjainak megadása |} A Microsoft Docs
description: Hogyan leírására végpont erőforrások szolgáltatásjegyzékben, illetve a HTTPS-végpontok beállítása
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8707a9cb90afe1bf72f3aef6377f8ada409a1c64
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667123"
---
# <a name="specify-resources-in-a-service-manifest"></a>Erőforrások meghatározása szolgáltatásjegyzékben
## <a name="overview"></a>Áttekintés
A szolgáltatásjegyzék lehetővé teszi, hogy a deklarált vagy módosítani kell a lefordított kód módosítása nélkül a szolgáltatás által használt erőforrások. Az Azure Service Fabric támogatja a végpont erőforrások a szolgáltatás. A szolgáltatásjegyzék megadott erőforrásokhoz való hozzáférés a SecurityGroup az alkalmazásjegyzékben keresztül lehet irányítani. A nyilatkozat az erőforrások ezekhez az erőforrásokhoz való módosítható üzembe helyezéskor, ami azt jelenti, a szolgáltatás nem szükséges, vezessen be egy új konfigurációs mechanizmusa lehetővé teszi. Az objektumhoz a ServiceManifest.xml fájlban sémadefiníciója telepítve van a Service Fabric SDK és -eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Végpontok
A szolgáltatásjegyzék-végponti erőforrás van definiálva, amikor a Service Fabric rendeli hozzá a portokat a fenntartott tartománya, ha a port nincs megadva explicit módon. Például, tekintse meg a végpont *ServiceEndpoint1* a fürtjegyzék kódrészlete a bekezdés utáni nyújtott megadott. Ezenkívül szolgáltatások is kérheti egy erőforrás egy adott portot. Különböző fürtcsomóponton futó szolgáltatás replikára rendelhetők különböző portszámokat, miközben ugyanazon a csomóponton futó szolgáltatás replikák megosztása a portot. A szolgáltatás replikák használhatja ezeket a portokat szükséges replikáció és a figyelő az ügyféli kérelmek részére.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Ha több kódcsomagok egyetlen szolgáltatási csomagban vannak, akkor a kódcsomag is meg lehet hivatkozni a **végpontok** szakaszban.  Például ha **ServiceEndpoint2a** és **ServiceEndpoint2b** a ugyanazon csomag hivatkozik másik kódot csomagok, a megfelelő végpontot kódcsomag végpontjainak van pontosítva az alábbiak szerint:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Tekintse meg [állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md) olvasható további információ a konfigurációs csomag beállítások végpontok hivatkozó fájlt (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Példa: Adjon meg egy HTTP-végpontot a szolgáltatáshoz
A következő Szolgáltatásjegyzék meghatározza egy TCP-végpont erőforrás és a két HTTP-végpont erőforrást a &lt;erőforrások&gt; elemet.

HTTP-végpontokat a program automatikusan ACL-t a Service Fabric által lenne.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Példa: a szolgáltatás HTTPS-végpont megadása
A HTTPS protokollt kiszolgálói hitelesítést biztosít, és ügyfél – kiszolgáló kommunikáció titkosítására is használja. A Service Fabric-szolgáltatás a HTTPS engedélyezéséhez adja meg, a protokoll a *erőforrás -> Végpontok -> Endpoint* a szolgáltatásjegyzék, ahogy ezt korábban a végpont szakaszában *ServiceEndpoint3*.

> [!NOTE]
> A szolgáltatás-protokoll nem módosítható az alkalmazásfrissítés során. Ha a frissítés során módosul, használhatatlanná tévő változást.
> 

> [!WARNING] 
> HTTPS használata esetén ne használjon port és a különböző szolgáltatáspéldányok (amely független az alkalmazás) ugyanazon a csomóponton telepített tanúsítványt. Két különböző szolgáltatások, a különböző alkalmazáspéldányok ugyanazt a portot használja a frissítés frissítési hibát eredményez. További információkért lásd: [frissítése több alkalmazás, a HTTPS-végpontok ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Íme egy példa ApplicationManifest, be kell állítania a HTTPS-hez. A tanúsítvány ujjlenyomatát kötelező megadni. A EndpointRef eszköztáblára mutató hivatkozás EndpointResource a ServiceManifest, amelynek beállítása a HTTPS protokollt. Egynél több EndpointCertificate adhat hozzá.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

A Linux-fürtök esetén a **MY** tárolja a mappát az alapértelmezett érték **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>A ServiceManifest.xml végpontok felülbírálása

Az az applicationmanifest fájlban adja hozzá egy ResourceOverrides szakaszt, ami egy testvér ConfigOverrides szakaszra. Ebben a szakaszban megadhatja a végpontok szakaszának szolgáltatásjegyzékben megadott erőforrások szakaszában a felülbírálást. Végpontok felülbírálása támogatott a futtatókörnyezeti 5.7.217/SDK 2.7.217 és újabb verzióit.

Annak érdekében, hogy a felülbírálás végpont a ServiceManifest ApplicationParameters módosítása az ApplicationManifest használata a következőképpen:

A ServiceManifestImport területen adja hozzá a "ResourceOverrides" című új szakasszal.

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

Az alkalmazás üzembe helyezése során ApplicationParameters, az ezeket az értékeket adhat át.  Példa:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Megjegyzés: Az értékeket ad meg, a ApplicationParameters érték üres, ha most visszalépünk a megfelelő Végpontneve rendszerképnevet a servicemanifest elemben megadott alapértelmezett érték.

Példa:

Ha a ServiceManifest elemben megadott

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

És Port1 és Protocol1 alkalmazás paraméter értéke null vagy üres. A port még ServiceFabric határozza meg. És a protokoll lesz tcp.

Tegyük fel, adjon meg egy megfelelő értéket. Például a porthoz megadott karakterlánc-érték "Foo" helyett egy: egész szám.  Új ServiceFabricApplication parancs sikertelen lesz, és hiba: Felülbírálás neve 'ServiceEndpoint1' attribútummal rendelkező "Port1" szakasz "ResourceOverrides" Érvénytelen paraméter. A megadott érték "Foo" és "int" Required (kötelező).
