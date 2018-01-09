---
title: "A Felhőszolgáltatások szerepkörök közötti kommunikáció |} Microsoft Docs"
description: "Cloud Services szerepkör példánya lehet meghatározva a végpontjai (http, https, a tcp, udp) számukra, hogy a külső vagy egyéb szerepkör-példányok közötti kommunikáció."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 96ca9bb2d7a9f30a7d6492be43bfb44edc02fd93
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Az azure-ban a szerepkörpéldányokért kommunikáció engedélyezése
Felhőszolgáltatás szerepköreit belső és külső kapcsolatokon keresztül kommunikálnak. Külső kapcsolatot más néven **bemeneti végpontok** közben belső kapcsolatok nevezzük **belső végpont**. Ez a témakör ismerteti, hogyan lehet módosítani a [webszolgáltatás](cloud-services-model-and-package.md#csdef) végpontok létrehozásához.

## <a name="input-endpoint"></a>Bemeneti végpont
A bemeneti végpont akkor használja, ha szeretné tenni a port kívülre. Megadhatja, hogy a protokoll típusát és a végpont, amely mindkét a külső és belső portok a végpont majd alkalmazza a port. Ha azt szeretné, egy másik belső portot végpontjának megadhatja a [Helyi_port](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) attribútum.

A bemeneti végpont használhatja a következő protokollt: **http, https, a tcp, udp**.

Hozzon létre egy bemeneti végpontot, vegye fel a **bemeneti végponthoz** alárendelt elem a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Példány bemeneti végpont
Példány bemeneti végpont hasonlóak a bemeneti végpontok azonban lehetővé teszi adott nyilvánosan elérhető portok minden egyes szerepkörpéldányhoz leképezése továbbítással port a terheléselosztón. Egy nyilvánosan elérhető portot vagy egy porttartományt is megadhat.

A példány bemeneti végpont csak olyan használhat **tcp** vagy **udp** protokollt.

Hozzon létre egy példány bemeneti végpontot, vegye fel a **InstanceInputEndpoint** alárendelt elem a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>A belső végpontot
Belső végpont példány-példány kommunikációs érhetők el. A port nem kötelező, és ha nincs megadva, a dinamikus port hozzá van rendelve a végpont. Porttartomány is használható. Szerepkör / öt belső végpontok korlátozva van.

A belső végpont használhatja a következő protokollt: **http, az tcp, udp, bármely**.

Hozzon létre egy belső bemeneti végpontot, vegye fel a **InternalEndpoint** alárendelt elem a **végpontok** elem egy webes vagy feldolgozói szerepkör.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Porttartomány is használhatja.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Feldolgozói szerepkörök vs. Webes szerepkörök
A végpontok egy kisebb különbség van, ha a munkavégző és a webes szerepkörök használata. A webes szerepkörnek rendelkeznie kell legalább egy bemeneti végpontot használatával a **HTTP** protokoll.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>A végpont eléréséhez a .NET SDK használatával
Az Azure által felügyelt kódtár szerepkörpéldányokat futásidőben kommunikációhoz módszereket kínál. A szerepkör példánya belül futó kódból többi szerepkörpéldányon és a végpontok kapcsolatos információkat, valamint az aktuális példányon információ kérheti le.

> [!NOTE]
> Információ a szerepkörpéldányok, amelyek a felhőalapú szolgáltatás fut, és legalább egy belső végpont definiáló csak kérheti le. Egy másik szolgáltatást futtató szerepkör-példányok adatait nem lehet megszerezni.
> 
> 

Használhatja a [példányok](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) tulajdonság szerepkör példányainak beolvasása. Először használja a [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) térjen vissza a hivatkozás az aktuális példányon, majd a [szerepkör](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) tulajdonság vissza a szerepkör önmagára hivatkozik.

A szerepkör példánya programozott módon a .NET SDK használatával csatlakozik, esetén viszonylag könnyen elérhetők a végpont-információkat. Például után már csatlakozott egy adott szerepkör környezetben, egy adott végpont ezzel a kóddal port kaphat:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A **példányok** tulajdonság gyűjteményének beolvasása az **RoleInstance** objektumok. Ez a gyűjtemény mindig az aktuális példány tartalmaz. Ha a szerepkör nem határoz meg egy belső végpont, a gyűjtemény tartalmaz az aktuális példány, de nincs más példány. A gyűjtemény példány szerepkörpéldányainak számát minden esetben 1 abban az esetben, ha a szerepkör nem a belső végpontot van definiálva. A szerepkör a belső végpont határozza meg, ha a példányok felderíthető futásidőben, és a szerepkör a konfigurációs fájlban megadott példányok száma a gyűjteményben található példányok száma felel meg.

> [!NOTE]
> Az Azure által felügyelt kódtár nem teszik lehetővé a többi szerepkörpéldányon állapotának meghatározása, de Megvalósíthat ilyen állapotfigyelő értékelések saját kezűleg ezért ha a szolgáltatás ezt a funkciót. Használhat [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) beolvasni a szerepkörpéldányok futtatásával kapcsolatos információkat.
> 
> 

A példányon a belső végpont port számának megállapításához használja a [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) tulajdonság vissza egy Dictionary objektum, amely tartalmazza a végpont nevének és a megfelelő IP-címek és portok. A [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) tulajdonság IP-cím és port megadott végpont adja vissza. A **PublicIPEndpoint** tulajdonság adja vissza egy elosztott terhelésű végpont portja. Az IP cím része az **PublicIPEndpoint** tulajdonság nincs használatban.

Íme egy példa, amely megismétli a szerepkörpéldányok.

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

Íme egy példa egy feldolgozói szerepkört, amely lekérdezi a közzétett végpont szolgáltatásdefinícióban keresztül, és elindítja a kapcsolatfigyelést.

> [!WARNING]
> Ez a kód csak egy telepített szolgáltatáshoz fog működni. Az Azure Compute Emulator futtatásakor a szolgáltatás konfigurációs elemek, amelyek közvetlenül átemelésre végpontokat hoz létre (**InstanceInputEndpoint** elemek) figyelmen kívül lesznek hagyva.
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
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Szerepkör kommunikációt a hálózati forgalomra vonatkozó szabályok
Belső végpont meghatározása után adhat hozzá (a végpontok létrehozott alapján) a hálózati forgalomra vonatkozó szabályok vezérlő hogyan szerepkörpéldányokat kommunikálhatnak egymással. Az alábbi ábrán látható közötti szabályozásának olyan gyakori forgatókönyveket tartalmaz:

![A hálózati forgalom szabályok forgatókönyvek](./media/cloud-services-enable-communication-role-instances/scenarios.png "hálózati forgalmi szabályok forgatókönyvek")

Az alábbi példakód bemutatja a szerepkör-definíciók a szerepkörök az előző ábrán is látható. Minden egyes szerepkör-definíció definiált legalább egy belső végpontot tartalmaz:

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
> Rögzített, és automatikusan hozzárendeli a portok a belső végpontok szerepkörök közötti kommunikáció korlátozásának alakulhat ki.
> 
> 

Alapértelmezés szerint után belső végpont van definiálva, kommunikációs folytatódhat bármely szerepkörből, a korlátozások nélküli szerepkör a belső végpontot. A kommunikáció korlátozása érdekében hozzá kell adnia egy **NetworkTrafficRules** elem a **ServiceDefinition** elem a szolgáltatásdefiníciós fájlban.

### <a name="scenario-1"></a>1. forgatókönyv
Csak a hálózati forgalom engedélyezése **WebRole1** való **WorkerRole1**.

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
Csak lehetővé teszi a hálózati forgalmat a **WebRole1** való **WorkerRole1** és **WorkerRole2**.

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
Csak lehetővé teszi a hálózati forgalmat a **WebRole1** való **WorkerRole1**, és **WorkerRole1** való **WorkerRole2**.

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
Csak lehetővé teszi a hálózati forgalmat a **WebRole1** való **WorkerRole1**, **WebRole1** való **WorkerRole2**, és **WorkerRole1**  való **WorkerRole2**.

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

A fenti elemek egy XML-séma hivatkozása található [Itt](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>További lépések
További információk a felhőalapú szolgáltatás [modell](cloud-services-model-and-package.md).

