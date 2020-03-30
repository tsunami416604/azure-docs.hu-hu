---
title: A Service Fabric szolgáltatásvégpontjainak megadása
description: Végponti erőforrások leírása egy szolgáltatásjegyzékben, beleértve a HTTPS-végpontok beállítását is
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cc4eedf5e5fee0bbfa0a763e9b9ec0dd25409afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282158"
---
# <a name="specify-resources-in-a-service-manifest"></a>Erőforrások megadása szolgáltatásjegyzékben
## <a name="overview"></a>Áttekintés
A szolgáltatásjegyzék lehetővé teszi, hogy a szolgáltatás által használt erőforrások deklarálhatók/módosíthatók a lefordított kód módosítása nélkül. Az Azure Service Fabric támogatja a szolgáltatás végponterőforrásainak konfigurálását. A szolgáltatásjegyzékben megadott erőforrásokhoz való hozzáférés az alkalmazásjegyzéksecuritycsoporton keresztül vezérelhető. Az erőforrások deklarációja lehetővé teszi, hogy ezeket az erőforrásokat az üzembe helyezés kor módosítsa, ami azt jelenti, hogy a szolgáltatásnak nem kell új konfigurációs mechanizmust bevezetnie. A ServiceManifest.xml fájl sémadefiníciója a Service Fabric SDK-val és a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*eszközökkel van telepítve.

## <a name="endpoints"></a>Végpontok
Ha egy végponti erőforrás definiálva van a szolgáltatásjegyzékben, a Service Fabric portokat rendel a fenntartott alkalmazásport-tartományból, ha egy port nincs megadva explicit módon. Tekintse meg például a jelen bekezdés után megadott jegyzékkódrészletben megadott *Végpont ServiceEndpoint1.For* example, look at the endpoint ServiceEndpoint1 specified in the manifest snippet provided after this paragraph. Emellett a szolgáltatások is kérhetnek egy adott portot egy erőforrásban. A különböző fürtcsomópontokon futó szolgáltatásreplikák különböző portszámokat rendelhetnek hozzá, míg az ugyanazon a csomóponton futó szolgáltatás replikái megosztják a portot. A szolgáltatásreplikák ezután használhatják ezeket a portokat, ha szükséges a replikációhoz és az ügyfélkérelmek figyeléséhez.

> [!WARNING] 
> A statikus portok a tervek szerint nem fedhetik át a ClusterManifest-ben megadott alkalmazásport-tartományt. Ha statikus portot ad meg, rendelje hozzá az alkalmazásport tartományán kívülre, ellenkező esetben portütközéseket fog eredményezni. A 6.5CU2 kiadással egészségügyi figyelmeztetést adunk **ki,** amikor ilyen ütközést észlelünk, de hagyjuk, hogy a telepítés szinkronban maradjon a szállított 6.5 viselkedéssel. Azonban megakadályozhatja, hogy az alkalmazás központi telepítése a következő fő kiadások.
>
> A kiadás 7.0 adunk ki **egy egészségügyi figyelmeztetés,** ha azt észleljük, alkalmazásport tartomány használata túlmutat HostingConfig::ApplicationPortExhaustThresholdPercentage (alapértelmezett 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Ha egyetlen szolgáltatáscsomagban több kódcsomag van, akkor a kódcsomagra is hivatkozni kell a **Végpontok** szakaszban.  Ha például **a ServiceEndpoint2a** és a **ServiceEndpoint2b** ugyanabból a szolgáltatáscsomagból származó, különböző kódcsomagokra hivatkozó végpontok, az egyes végpontokhoz tartozó kódcsomag a következőképpen tisztázódik:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Az [állapotalapú megbízható szolgáltatások konfigurálása](service-fabric-reliable-services-configuration.md) című témakörben olvashat bővebben a végpontok hivatkozásáról a konfigurációs csomag beállításfájljából (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Példa: HTTP-végpont megadása a szolgáltatáshoz
A következő szolgáltatásjegyzék egy TCP-végpont-erőforrást és két &lt;&gt; HTTP-végpont-erőforrást határoz meg az Erőforrások elemben.

A HTTP-végpontokat a Service Fabric automatikusan acl'd.HTTP endpoints are automatically ACL'd by Service Fabric.

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

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Példa: HTTPS-végpont megadása a szolgáltatáshoz
A HTTPS protokoll kiszolgálói hitelesítést biztosít, és az ügyfél-kiszolgáló kommunikáció titkosítására is használható. A HTTPS engedélyezéséhez a Service Fabric szolgáltatásban adja meg a protokollt a szolgáltatásjegyzék *Erőforrások ->-> Végpont okán,* ahogy azt korábban a *ServiceEndpoint3*végpont mutatja.

> [!NOTE]
> A szolgáltatás protokollja nem módosítható az alkalmazás frissítése során. Ha a frissítés során megváltozik, az töréses változás.
> 

> [!WARNING] 
> HTTPS használataesetén ne használja ugyanazt a portot és tanúsítványt az ugyanazon csomópontra telepített különböző szolgáltatáspéldányokhoz (az alkalmazástól függetlenül). Két különböző szolgáltatás frissítése ugyanazon port használatával különböző alkalmazáspéldányokban frissítési hibát eredményez. További információt a [Több alkalmazás frissítése HTTPS-végponttal ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)című témakörben talál.
>

Íme egy példa ApplicationManifest, hogy be kell állítani a HTTPS. A tanúsítvány ujjlenyomatát meg kell adni. Az EndpointRef az EndpointResource szolgáltatásra mutató hivatkozás a ServiceManifest alkalmazásban, amelyhez beállította a HTTPS protokollt. Több EndpointCertificate tanúsítványt is hozzáadhat.  

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

Linux-fürtök esetén a **MY** áruház alapértelmezés szerint a **/var/lib/sfcerts mappát támogatja.**


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Végpontok felülbírálása a ServiceManifest.xml fájlban

Az ApplicationManifest adjon hozzá egy ResourceOverrides szakaszt, amely a ConfigOverrides szakasz testvére lesz. Ebben a szakaszban megadhatja a végpontok szakasz felülbírálása az erőforrások szakaszban megadott service manifest. Az 5.7.217/SDK 2.7.217 és újabb futásidejű végpontok támogatottak.

Az EndPoint serviceManifest-ben az ApplicationParameters használatával történő felülbírálásához módosítsa az ApplicationManifest-et a következőképpen:

A ServiceManifestImport szakaszban adjon hozzá egy új szakaszt "ResourceOverrides".

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

A Paraméterek add alább:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Az alkalmazás telepítése közben ezeket az értékeket ApplicationParameters-ként adhatja át.  Példa:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Megjegyzés: Ha az ApplicationParameters értékek értéke üres, akkor visszatérünk a ServiceManifest-ben a megfelelő EndPointName-hez megadott alapértelmezett értékre.

Példa:

Ha a ServiceManifest-ben megadott

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

És az Alkalmazás paraméterek Port1 és Protocol1 értéke null vagy üres. A port továbbra is dönt servicefabric. És a Protokoll tcp lesz.

Tegyük fel, hogy rossz értéket ad meg. A Port hoz hasonlóan int helyett "Foo" karakterláncértéket adott meg.  A New-ServiceFabricApplication parancs hiba esetén sikertelen lesz: A "ResourceOverrides" szakaszban a "ServiceEndpoint1" attribútummal rendelkező felülbírálási paraméter érvénytelen. A megadott érték "Foo", a kötelező pedig az "int".
