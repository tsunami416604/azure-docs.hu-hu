---
title: Szolgáltatás távelérésének lehetővé tétele, a Service Fabric C# használatával |} A Microsoft Docs
description: Remoting Service Fabric lehetővé teszi, hogy az ügyfelek és a szolgáltatásokat a C# szolgáltatásokkal kommunikálni a távoli eljáráshívás segítségével.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 9609a0fa5599bd34fa52f7c0311369fb27aaf955
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951158"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Szolgáltatás távelérésének lehetővé tétele a C# Reliable Services szolgáltatással
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A szolgáltatások, amelyek egy adott kommunikációs protokollt vagy a veremben, mint például a WebAPI, a Windows Communication Foundation (WCF) vagy más, nem kapcsolódik a Reliable Services-keretrendszer gyorsan és egyszerűen beállítása a távoli eljáráshívások távoli eljáráshívás mechanizmust biztosít szolgáltatások. Ez a cikk ismerteti, hogyan állítható be a C# használatával írt szolgáltatásokhoz a távoli eljáráshívásokat.

## <a name="set-up-remoting-on-a-service"></a>Állítsa be a szolgáltatás távelérésének lehetővé tétele
Szolgáltatás távelérésének lehetővé tétele beállítása két egyszerű lépésben történik:

1. Hozzon létre egy kapcsolatot a szolgáltatás megvalósítása. Toto rozhraní definuje módszerek érhetők el a szolgáltatást a távoli eljáráshívás. A módszerek kell lennie, a feladat-adatszolgáltató aszinkron módszereket. A felület musí implementovat `Microsoft.ServiceFabric.Services.Remoting.IService` , hogy jelezze, hogy a szolgáltatás távelérésének lehetővé tétele felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. RemotingListener van egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívás képességeket biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmaz metódust,`CreateServiceRemotingListener` is állapot nélküli és állapotalapú szolgáltatásokhoz, amelyek segítségével hozzon létre egy távoli eljáráshívás figyelőt az alapértelmezett távelérési átviteli protokoll használatával.

>[!NOTE]
>A `Remoting` névtér NuGet-csomagként külön néven érhető el `Microsoft.ServiceFabric.Services.Remoting`

A következő állapotmentes szolgáltatás például a "Hello World" beszerezhet egy távoli eljáráshívási egyetlen metódus közzététele.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Az argumentumok és a szolgáltatás felületén a návratové typy lehet olyan egyszerű, bonyolult vagy egyéni típusokat, de a .NET-szerializálható kell lennie [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>A távoli szolgáltatás metódusok meghívása
Metódusok meghívása a szolgáltatás a távoli eljáráshívás stack használatával a helyi proxyn keresztül a szolgáltatás segítségével történik a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztály. A `ServiceProxy` módszer létrehoz egy helyi proxykiszolgáló ugyanazt a felületet, amely megvalósítja a szolgáltatás által. A proxyk meghívhatja módszerek kapcsolaton távolról.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A távoli eljáráshívás keretrendszer tölti ki az ügyfélnek a szolgáltatás által okozott kivételeket. Eredményeként használatakor `ServiceProxy`, az ügyfél kezeléséért felelős a a szolgáltatás által okozott kivételeket.

## <a name="service-proxy-lifetime"></a>Szolgáltatási Proxy élettartamát
ServiceProxy létrehozása egy könnyen használható művelet, így annyi igény szerint hozhat létre. Szolgáltatási Proxy példányok felhasználhatók, amennyiben azok szükségesek. A távoli eljáráshívás kivételt jelez, ha továbbra is felhasználhatja a proxy-példányt. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Vzdálená volání meghívása, során annak megállapításához, hogy a kommunikáció ügyfél érvényes belső ellenőrzések elvégzése. Ezen ellenőrzés eredménye alapján a szükség esetén újból a a kommunikációt ügyfél. Ezért, ha kivétel történik, akkor nem kell hozza létre újra `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) proxy példányok különböző távoli eljáráshívás adapterek létrehozó gyár van. Ha a API-val `ServiceProxy.Create` proxy létrehozására, majd a keretrendszer ServiceProxy egypéldányos létrehoz.
Akkor hasznos, hozzon létre egyet manuálisan Ha felül kell bírálnia [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) tulajdonságait.
Gyári létrehozása drága művelet. ServiceProxyFactory fenntart egy belső kommunikációs ügyfél gyorsítótárát.
Ajánlott eljárás az, amíg ServiceProxyFactory gyorsítótár.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelés
Összes távoli által okozott kivételek feldolgozását a szolgáltatás API-t az ügyfélnek, AggregateException érkeznek. RemoteExceptions szerializálható; DataContract kell lennie. Ha nem, a proxy API jelez [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) szerepel a szerializálási hiba miatt.

ServiceProxy kezeli a szolgáltatás-partíció létrehozása a minden feladatátvétel-kivétel. Ez újra oldja fel a végpontok, ha feladatátvétel kivételek (kivétel nem átmeneti), és a hívást a megfelelő végpontra való próbálkozások. Az újrapróbálkozások számát, feladatátvételi kivételek befejezésére való határozatlan idejű.
Átmeneti kivétel lép fel, ha a proxy újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterek a következők szerint paramétert [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Ezeket az értékeket konfigurálhatja OperationRetrySettings objektum ServiceProxyFactory konstruktorának való átadásával.

## <a name="how-to-use-the-remoting-v2-stack"></a>A távoli eljáráshívás V2 verem használata

Kezdődően a távelérés NuGet csomag verziója 2.8-as lehetősége van a távoli eljáráshívás V2 verem használata. A távoli eljáráshívás V2 verem további nagy teljesítményű és szolgáltatások, mint az egyéni sorba rendezésre és több moduláris API-kat biztosít.
Sablon kód továbbra is a távoli eljáráshívás V1 vermet használja.
Távoli eljáráshívás V2, nem kompatibilis a V1 (a korábbi távoli eljáráshívás-vermet), ezért kövesse az alábbi utasításokat a [v2 a V1 frissítése](#how-to-upgrade-from-remoting-v1-to-remoting-v2) szolgáltatás rendelkezésre állása befolyásolása nélkül.

A V2-verem engedélyezéséhez a következő módszerek érhetők el.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Egy szerelvény attribútum használata a V2-verem használata

Ezeket a lépéseket egy szerelvény attribútum használata a V2-verem használata a sablonban lévő kód módosítása

1. Módosítsa a végponti erőforrás `"ServiceEndpoint"` való `"ServiceEndpointV2"` szolgáltatásjegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Használja a `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` bővítmény metódussal hoz létre a távoli eljáráshívás figyelői (V1 és V2 egyenlő).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. A távoli eljáráshívás kapcsolódási pontok tartalmazó szerelvény megjelölni egy `FabricTransportServiceRemotingProvider` attribútum.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```

Az ügyfélprojekt szükséges kód módosítása nélkül.
A felület szerelvény, győződjön meg arról, hogy a fenti szerelvény attribútum szolgál, hogy az ügyfél szerelvény hozhat létre.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>A V2-verem használata explicit V2 osztályok használatával

Ahelyett, hogy egy szerelvény attribútum segítségével, mint a V2-verem is engedélyezhető explicit V2 osztályok használatával.

Ezeket a lépéseket az explicit V2-osztályokkal V2 verem használata a sablonban lévő kód módosítása

1. Módosítsa a végponti erőforrás `"ServiceEndpoint"` való `"ServiceEndpointV2"` szolgáltatásjegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Használja a [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) származó a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` névtér.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Használja a [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) származó a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` névtér létrehozása az ügyfelek számára.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Frissítse a távoli eljáráshívás V1 a távoli eljáráshívás V2 módja.
Annak érdekében, hogy a V2 a V1 frissíteni, a 2. lépés – frissítések szükségesek. A felsorolt sorrendben hajtja végre a következő lépéseket.

1. Frissítés a V1 szolgáltatás V2 szolgáltatás CompactListener attribútum használatával.
Ez a változás gondoskodik arról, hogy a szolgáltatás figyel-e a V1 és V2-figyelő.

    ) egy végpont nevű erőforrás hozzáadása "ServiceEndpointV2" a szolgáltatásjegyzékben.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    (b) távoli eljáráshívás figyelő létrehozásához a következő metódust használja.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) a szerelvény attribútum hozzáadása a távoli eljáráshívás adapteren CompatListener és a V2-ügyfél segítségével.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Frissítés a V1-ügyfél V2 ügyfél V2 ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy ügyfél V2-vermet használ.
Nincs változás a projekt/ügyfélszolgáltatás nem szükséges. Alkalmazás készítése az ügyfél projektek a frissített felületet szerelvény is használhatók.

3. Ez a lépés nem kötelező. V2Listener attribútumot használja, és utána frissítse a V2-szolgáltatás.
Ebben a lépésben gondoskodik arról, hogy a szolgáltatás figyel-e csak a V2-figyelőt.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Hogyan használható az egyéni szerializálás a távoli eljáráshívás V2.
Alábbi példa Json-szerializálás a távoli eljáráshívás V2 használ.
1. Egyéni sorba rendezésre implementáció IServiceRemotingMessageSerializationProvider felület megvalósításához.
    Itt van a kódtöredék hogyan néz végrehajtására.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    A távoli eljáráshívás figyelő alapértelmezett szerializálási szolgáltató JsonSerializationProvider felülbírálása

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    A távoli eljáráshívás Ügyfélgyára alapértelmezett szerializálási szolgáltató JsonSerializationProvider felülbírálása

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>További lépések
* [Webes API-k az OWIN aktorai Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Reliable Services szolgáltatással](service-fabric-reliable-services-communication-wcf.md)
* [Kommunikáció a Reliable Services](service-fabric-reliable-services-secure-communication.md)

