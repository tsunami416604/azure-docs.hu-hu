---
title: Kommunikáció a felhőszolgáltatások szerepköreihez | Microsoft dokumentumok
description: A Cloud Services szerepkörpéldányai végpontokat (http, https, tcp, udp) definiálhatnak, amelyek a külső vagy más szerepkörpéldányokkal kommunikálnak.
services: cloud-services
documentationcenter: ''
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: tagore
ms.openlocfilehash: 094e08becf4f3a60c98d89bfae7e7c3a69b677f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386340"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Az azure-beli szerepkörpéldányok kommunikációjának engedélyezése
A felhőszolgáltatási szerepkörök belső és külső kapcsolatokon keresztül kommunikálnak. A külső kapcsolatokat **bemeneti végpontnak,** míg a belső kapcsolatokat **belső végpontnak nevezzük.** Ez a témakör azt ismerteti, hogyan módosíthatja a [szolgáltatásdefiníciót](cloud-services-model-and-package.md#csdef) végpontok létrehozásához.

## <a name="input-endpoint"></a>Bemeneti végpont
A bemeneti végpont akkor használatos, ha egy portot kívülről szeretne elérhetővé tenni. Megadhatja a végpont protokolltípusát és portját, amely ezután a végpont külső és belső portjaira is vonatkozik. Ha szeretné, megadhat egy másik belső portot a végponthoz a [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) attribútummal.

A bemeneti végpont a következő protokollokat használhatja: **http, https, tcp, udp**.

Bemeneti végpont létrehozásához adja hozzá a **InputEndpoint** gyermekelemet a webes vagy feldolgozói szerepkör **Végpontok** eleméhez.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Példány bemeneti végpontja
A példánybemeneti végpontok hasonlóak a bemeneti végpontokhoz, de lehetővé teszi, hogy a terheléselosztó porttovábbításával leképezze az egyes szerepkörpéldányok adott nyilvános néző portjait. Megadhat egyetlen nyilvános bevezető portot vagy porttartományt.

A példány bemeneti végpontja csak **tcp** vagy **udp** protokollként használható.

Példánybemeneti végpont létrehozásához adja hozzá a **InstanceInputEndpoint** gyermekelemet a webes vagy feldolgozói szerepkör **Végpontok** eleméhez.

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
Belső végpontok érhetők el a példányok közötti kommunikációhoz. A port nem kötelező, és ha nincs megadva, dinamikus port van rendelve a végponthoz. Porttartomány használható. Szerepkörenként legfeljebb öt belső végpont van.

A belső végpont a következő protokollokat használhatja: **http, tcp, udp, any**.

Belső bemeneti végpont létrehozásához adja hozzá a **InternalEndpoint** gyermekelemet a webes vagy feldolgozói szerepkör **Végpontok** eleméhez.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Porttartományt is használhat.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Feldolgozói szerepkörök és webes szerepkörök
Van egy kisebb különbség a végpontok, ha dolgozik mind a feldolgozó és a webes szerepkörök. A webes szerepkörnek legalább egy bemeneti végponttal kell rendelkeznie a **HTTP protokoll** használatával.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Végpont elérése a .NET SDK használatával
Az Azure Felügyelt könyvtár a szerepkörpéldányok futásidőben kommunikálhat metódusait. A szerepkörpéldányon belül futó kódból lekérheti a többi szerepkörpéldány és azok végpontjai létezésével kapcsolatos információkat, valamint az aktuális szerepkörpéldány adatait.

> [!NOTE]
> Csak a felhőszolgáltatásban futó és legalább egy belső végpontot definiáló szerepkörpéldányokról kérhet információt. Nem kaphat adatokat egy másik szolgáltatásban futó szerepkörpéldányokról.
> 
> 

A [Példányok](/previous-versions/azure/reference/ee741904(v=azure.100)) tulajdonsággal lekérheti egy szerepkör példányait. Először használja a [CurrentRoleInstance-t](/previous-versions/azure/reference/ee741907(v=azure.100)) az aktuális szerepkörpéldányra mutató hivatkozás visszaadására, majd a [Role](/previous-versions/azure/reference/ee741918(v=azure.100)) tulajdonsággal adja vissza magát a szerepkört.

Ha programozott módon csatlakozik egy szerepkörpéldányhoz a .NET SDK-n keresztül, viszonylag könnyen hozzáférhet a végpontadatokhoz. Ha például már csatlakozott egy adott szerepkörkörnyezethez, ezzel a kóddal lejuthat egy adott végpont portjára:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A **Példányok** tulajdonság **RoleInstance** objektumok gyűjteményét adja vissza. Ez a gyűjtemény mindig az aktuális példányt tartalmazza. Ha a szerepkör nem határoz meg belső végpontot, a gyűjtemény tartalmazza az aktuális példányt, de más példányokat nem. A gyűjteményben lévő szerepkörpéldányok száma mindig 1 lesz abban az esetben, ha nincs belső végpont definiálva a szerepkörhöz. Ha a szerepkör belső végpontot határoz meg, a példányok felderíthetők futásidőben, és a gyűjtemény példányainak száma megegyezik a szolgáltatás konfigurációs fájljában a szerepkörhöz megadott példányok számával.

> [!NOTE]
> Az Azure felügyelt könyvtár nem nyújt más szerepkörpéldányok állapotának meghatározásához, de az ilyen állapotfelméréseket saját maga is megvalósíthatja, ha a szolgáltatásnak szüksége van erre a funkcióra. Az [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) használatával információkat kaphat a futó szerepkörpéldányokról.
> 
> 

Egy szerepkörpéldány belső végpontjának portszámának meghatározásához a [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) tulajdonsággal visszaadhat egy szótárobjektumot, amely végpontneveket, valamint a hozzájuk tartozó IP-címeket és portokat tartalmaz. Az [IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) tulajdonság egy megadott végpont IP-címét és portját adja vissza. A **PublicIPEndpoint** tulajdonság egy elhatárolt terhelésű végpont portját adja vissza. A **PublicIPEndpoint** tulajdonság IP-cím része nem használatos.

Íme egy példa, amely a szerepkörpéldányokat iterálja.

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

Íme egy példa egy feldolgozói szerepkörre, amely a végpontot a szolgáltatásdefiníción keresztül elérhetővé teszi, és elkezdi figyelni a kapcsolatokat.

> [!WARNING]
> Ez a kód csak telepített szolgáltatás esetén működik. Az Azure Compute Emulator futtatásakor a közvetlen portvégpontokat **(InstanceInputEndpoint-elemeket)** létrehozó szolgáltatáskonfigurációs elemeket figyelmen kívül hagyja.
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

## <a name="network-traffic-rules-to-control-role-communication"></a>Hálózati forgalmi szabályok a szerepkör-kommunikáció szabályozására
A belső végpontok definiálása után hálózati forgalmi szabályokat adhat hozzá (a létrehozott végpontok alapján), hogy szabályozhassa, hogyan kommunikálhatnak egymással a szerepkörpéldányok. Az alábbi ábra a szerepkör-kommunikáció szabályozásának néhány gyakori forgatókönyvét mutatja be:

![Hálózati forgalmi szabályok forgatókönyvei](./media/cloud-services-enable-communication-role-instances/scenarios.png "Hálózati forgalmi szabályok forgatókönyvei")

A következő kódpélda az előző diagramon látható szerepkörök szerepkör-definícióit mutatja be. Minden szerepkör-definíció legalább egy definiált belső végpontot tartalmaz:

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
> A szerepkörök közötti kommunikáció korlátozása történhet a rögzített és automatikusan hozzárendelt portok belső végpontjaival.
> 
> 

Alapértelmezés szerint egy belső végpont definiálása után a kommunikáció bármely szerepkörből a szerepkör belső végpontjára korlátozás nélkül áramlhat. A kommunikáció korlátozásához hozzá kell adnia egy **NetworkTrafficRules** elemet a **szolgáltatásdefiníciós** fájl ServiceDefinition eleméhez.

### <a name="scenario-1"></a>1. példa
Csak a **WebRole1** és a **WorkerRole1**között engedélyezze a hálózati forgalmat.

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

### <a name="scenario-2"></a>2. példa
Csak a **WebRole1** és a **WorkerRole2** **WorkerRole1** között engedélyezi a hálózati forgalmat.

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

### <a name="scenario-3"></a>3. példa
Csak a **WebRole1** és a **WorkerRole1,** a **WorkerRole1** és a **WorkerRole2**között engedélyezi a hálózati forgalmat.

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

### <a name="scenario-4"></a>4. példa
Csak **a WebRole1** és **a WorkerRole1**, **a WebRole1** és **a WorkerRole2,** a **WorkerRole1** és a **WorkerRole2**között engedélyezi a hálózati forgalmat.

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

A fent használt elemekHEZ való XML-sémahivatkozás [itt](/previous-versions/azure/reference/gg557551(v=azure.100))található.

## <a name="next-steps"></a>További lépések
További információ a Felhőszolgáltatás [modelljéről.](cloud-services-model-and-package.md)




