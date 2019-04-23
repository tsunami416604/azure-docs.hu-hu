---
title: Kommunikáció a Cloud Services-szerepkörök |} A Microsoft Docs
description: A Felhőszolgáltatások szerepkörpéldányok lehet meghatározva a végpontjai (http, https, tcp, udp) számukra, hogy a külső vagy egyéb szerepkör példányai közötti kommunikációhoz.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 8b521ebe869210b66ac3b3efeebda873f7c0e50b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792476"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Az azure-szerepkörpéldányok kommunikáció engedélyezése
A felhőszolgáltatásokhoz tartozó szerepkörök belső és külső kapcsolatok keresztül kommunikálnak. A külső kapcsolatokat az úgynevezett **bemeneti végpontok** közben belső kapcsolatok nevezzük **belső végpont**. Ez a témakör ismerteti, hogyan lehet módosítani a [szolgáltatásdefiníciós](cloud-services-model-and-package.md#csdef) végpontok létrehozásához.

## <a name="input-endpoint"></a>Bemeneti végpont
A bemeneti végpont szeretné közzétenni a külső port használatos. Protokoll típusa és a végpont, amely mind a belső és külső portok a végpont majd alkalmazza a portot kell megadni. Ha azt szeretné, az a végpont egy másik belső portot is megadhat a [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#InputEndpoint) attribútum.

A bemeneti végpont használhatja az alábbi protokollok: **http, https, tcp, udp**.

Egy bemeneti végpont létrehozásához adja hozzá a **bemeneti végponthoz** gyermekelemet, a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Példány bemeneti végpontja
Példány bemeneti végpontok hasonlóak a bemeneti végpontok azonban lehetővé teszi adott nyilvános portok minden egyes szerepkör-példány leképezése porttovábbítást a load balancer használatával. Egy nyilvános portot vagy egy porttartományt is megadhat.

A példány bemeneti végpontja csak használhat **tcp** vagy **udp** protokollként.

Egy példány bemeneti végpont létrehozásához adja hozzá a **InstanceInputEndpoint** gyermekelemet, a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Belső végpont
Belső végpont instance-példány kommunikációs érhetők el. A port nem kötelező, és ha nincs megadva, a dinamikus port van-e rendelve a végpont. Egy porttartományt is használható. Öt belső végpont szerepkörönként korlátozva van.

A belső végpont használhatja az alábbi protokollok: **http, a tcp, udp, bármely**.

Egy belső bemeneti végpont létrehozásához adja hozzá a **InternalEndpoint** gyermekelemet, a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Használhatja egy porttartományt is.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Feldolgozói szerepkörök vs. Webes szerepkörök
A végpontok egy kisebb különbség van, amikor munkavégző és a webes szerepkörök. A webes szerepkörnek rendelkeznie kell legalább egy egyetlen bemeneti végpont használatával a **HTTP** protokollt.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>A .NET SDK használatával egy végpontot
Az Azure által felügyelt erőforrástár szerepkörpéldány kommunikáljanak futás módszert biztosít. Egy szerepkörpéldány belül futó kódból kérheti le információ a létezik-e a többi szerepkörpéldány és azok végpontjait, valamint az aktuális szerepkörpéldány kapcsolatos információkat.

> [!NOTE]
> Csak a cloud service-ben futó, és legalább egy belső végpont meghatározása, hogy szerepkörpéldányainak kérheti le. Egy másik service-ben futó szerepkörpéldányok adatait nem lehet megszerezni.
> 
> 

Használhatja a [példányok](/previous-versions/azure/reference/ee741904(v=azure.100)) szerepkör példányai beolvasására szolgáló tulajdonság. Első alkalommal használják a [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) küldhet vissza egy hivatkozást az aktuális szerepkörpéldány, majd a [szerepkör](/previous-versions/azure/reference/ee741918(v=azure.100)) tulajdonságát adja vissza egy hivatkozást a szerepkör magát.

Egy szerepkörpéldány programozott módon a .NET SDK használatával csatlakozik, esetén viszonylag egyszerű, és hozzáférést a végpont adatait. Például miután már csatlakozott egy adott szerepkör környezethez, kaphat egy adott végpontnak, ezzel a kóddal, a port:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A **példányok** tulajdonság gyűjteményét adja vissza **RoleInstance** objektumokat. Ez a gyűjtemény mindig az aktuális példány tartalmazza. A szerepkör nem határoz meg egy belső végpont, ha a gyűjtemény tartalmaz, az aktuális példány, de nincs a többi példány. Szerepkör-példányok a gyűjtemény minden esetben 1 abban az esetben, ahol a szerepkör nem a belső végpont van definiálva. Ha a szerepkör egy belső végpont, a példányok felderíthető futásidőben, és a szerepkör a konfigurációs fájlban megadott példányok száma a gyűjteményben lévő példányok felel meg.

> [!NOTE]
> Az Azure által felügyelt kódtár nem biztosít módszert a többi szerepkörpéldány állapotának meghatározása, de valósítható meg az ilyen egészségügyi értékelések saját magának, ha a szolgáltatást kell ezt a funkciót. Használhat [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) szerepkörpéldányok futtatásával kapcsolatos információkat.
> 
> 

Annak megállapításához, a belső végpont egy szerepkörpéldány a portnak a számát, használhatja a [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) tulajdonságát adja vissza egy szótárobjektum, amely tartalmazza a végpont neve és a megfelelő IP-címek és portok. A [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) tulajdonság IP-cím és port egy megadott végponton adja vissza. A **PublicIPEndpoint** tulajdonságot adja vissza egy elosztott terhelésű végpont portja. Az IP cím része az **PublicIPEndpoint** tulajdonság nincs használatban.

Íme egy példa, amely a szerepkörpéldányok ismétlődik.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Íme egy példa egy feldolgozói szerepkör, amely lekérdezi a közzétett végpontnak keresztül a szolgáltatás definíciós, és elindítja a kapcsolatok figyeli.

> [!WARNING]
> Ez a kód csak egy telepített szolgáltatáshoz fog működni. Az Azure Compute Emulator való futtatáskor szolgáltatás konfigurációs elemeket, amelyek közvetlenül átemelésre végpontok létrehozása (**InstanceInputEndpoint** elemek) figyelmen kívül hagyja.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Kommunikáció szabályozhatja a hálózati forgalomra vonatkozó szabályok
Belső végpontokat, meghatározása után (az Ön által létrehozott végpontok alapján) a hálózati forgalomra vonatkozó szabályokat is hozzáadhat a vezérlő szerepkör példányai miként kommunikál egymással. Az alábbi ábrán látható néhány gyakori helyzet való kommunikáció:

![Hálózati forgalmi szabályok forgatókönyvek](./media/cloud-services-enable-communication-role-instances/scenarios.png "hálózati forgalmi szabályok forgatókönyvek")

Az alábbi példakód bemutatja a szerepkör-definíciók az előző ábrán is látható a szerepkörökhöz. Minden egyes szerepkör-definíció definiált legalább egy belső végpont tartalmazza:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Szerepkörök közötti kommunikáció korlátozása előfordulhatnak belső végpont mindkét rögzített, és automatikusan hozzárendeli a portokat.
> 
> 

Alapértelmezés szerint után egy belső végpont van definiálva, kommunikációs folytatódhat bármely szerepkörből, a belső végpont egy szerepkör korlátozása nélkül. Korlátozni a kommunikációt, hozzá kell adnia egy **NetworkTrafficRules** elem a **szolgáltatásdefiníció** elem a szolgáltatásdefiníciós fájlban.

### <a name="scenario-1"></a>1. forgatókönyv
A csak hálózati forgalom **WebRole1** való **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>2. forgatókönyv
Csak lehetővé teszi, hogy a hálózati forgalom **WebRole1** való **WorkerRole1** és **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>3. forgatókönyv
Csak lehetővé teszi, hogy a hálózati forgalom **WebRole1** való **WorkerRole1**, és **WorkerRole1** való **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>4. forgatókönyv
Csak lehetővé teszi, hogy a hálózati forgalom **WebRole1** való **WorkerRole1**, **WebRole1** való **WorkerRole2**, és **WorkerRole1**  való **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Egy XML-séma hivatkozása a fenti elemek találhatók [Itt](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>További lépések
További információ a felhőalapú szolgáltatás [modell](cloud-services-model-and-package.md).

