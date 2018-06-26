---
title: Szolgáltatás remoting Service Fabric C# használatával |} Microsoft Docs
description: Service Fabric távoli eljáráshívási lehetővé teszi, hogy az ügyfelek és a szolgáltatások távoli eljáráshívás segítségével C# szolgáltatásokkal kommunikálni.
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
ms.openlocfilehash: ad56580e73c06acff95b3146f6dc2d83ab2ba3ae
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945972"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>A C# megbízható Services szolgáltatás távelérése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Szolgáltatások, amelyek egy adott kommunikációs protokollt vagy a veremben, mint például a WebAPI, a Windows Communication Foundation (WCF) vagy más, nem kapcsolódik a Reliable Services keretrendszer lehetővé teszi a távoli eljáráshívás gyorsan és egyszerűen állíthat be a távoli eljáráshívások szolgáltatások. A cikkből megtudhatja, hogyan állíthat be a C#-szolgáltatásokat a távoli eljáráshívásokat.

## <a name="set-up-remoting-on-a-service"></a>A szolgáltatás távoli eljáráshívást beállítani
A szolgáltatás távoli eljáráshívás beállítása két egyszerű lépésben történik:

1. Hozzon létre egy felület, a szolgáltatás megvalósítása. Ez az interfész határozza meg a módszereket, amelyek a szolgáltatás a távoli eljáráshívás érhetők el. A módszerek kell lennie a feladatot visszaadó aszinkron módszereket. Az illesztőfelületet kell megvalósítania `Microsoft.ServiceFabric.Services.Remoting.IService` jelezze, hogy a szolgáltatás távoli eljáráshívási felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. RemotingListener van egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmaz egy kiterjesztésmetódus`CreateServiceRemotingListener` mind az állapotmentes és állapotalapú szolgáltatások, amelyek segítségével az alapértelmezett távelérési átviteli protokollal távoli eljáráshívási figyelő létrehozása.

>[!NOTE]
>A `Remoting` névtér NuGet-csomagként külön néven érhető el `Microsoft.ServiceFabric.Services.Remoting`

A következő állapotmentes szolgáltatások például egy távoli eljáráshívással működik a "Hello, World" beolvasandó egyetlen metódus közzététele.

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
> Az argumentumok és visszatérési értékének típusa a szolgáltatási felület lehet bármely egyszerű, bonyolult vagy egyéni típusa, de a .NET által szerializálható kell [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatás metódushívások
A szolgáltatás a helyi proxyként metódusok meghívása a szolgáltatás a távoli eljáráshívás verem segítségével történik a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztály. A `ServiceProxy` hoz létre egy helyi proxykiszolgáló ugyanazon a felületen, amely a szolgáltatás használatával. A proxybeállítások hívása módszerek illesztőn az távoli.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A távoli eljáráshívás keretrendszer tölti ki az ügyfél a szolgáltatás által okozott kivételeket. Ennek eredményeképpen használatakor `ServiceProxy`, az ügyfél nem felelős a szolgáltatás által okozott kivételeket.

## <a name="service-proxy-lifetime"></a>Szolgáltatási Proxy élettartamát
Egy egyszerűsített művelet ServiceProxy létrehozása, hogy létrehozhasson annyi, szükség szerint. Szolgáltatási Proxy példányok felhasználhatók, mindaddig, amíg azok szükségesek. A távoli eljáráshívás kivételt jelez, ha továbbra is felhasználhatja a proxy-példányt. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Hogy a távoli hívásokat, miközben belső a rendszer ellenőrzi annak megállapításához, hogy a kommunikáció ügyfél érvényes. Ezen ellenőrzés eredménye alapján, a kommunikáció ügyfél újból létrejön szükség esetén. Ezért, ha kivétel történik, nem kell hozza létre újra `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) van olyan adat-előállítóval, amely különböző távoli eljáráshívási felületek proxy létrehozza. Ha az API-t használja `ServiceProxy.Create` proxy létrehozására, majd a keretrendszer ServiceProxy egypéldányos létrehozza.
Akkor célszerű manuálisan létrehozhat egyet az felül kell bírálni [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) tulajdonságait.
Létrehozása során drága. ServiceProxyFactory kommunikációs ügyfél belső gyorsítótárában megtalálhatók.
Ajánlott eljárás ServiceProxyFactory gyorsítótárazásához, amíg.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelést
A service API minden távoli kivételek küldése az ügyfélnek az AggregateException a. RemoteExceptions DataContract szerializálható; kell lennie. Ha nem, a proxy API jelez [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) szerepel a szerializálási hiba miatt.

ServiceProxy kezeli az összes feladatátvételi kivételeket a szolgáltatás partíció akkor jön létre. Újra oldja fel a végpontok, ha feladatátvételi kivételek (nem átmeneti kivételek), és újrapróbálkozik a hívást a megfelelő végponttal. Az újrapróbálkozások számát, feladatátvevő kivételek nincs meghatározva.
Átmeneti kivétel lép fel, ha a proxy újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterei által biztosított [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Ezeket az értékeket konfigurálhatja úgy, hogy OperationRetrySettings objektum ServiceProxyFactory konstruktor.

## <a name="how-to-use-the-remoting-v2-stack"></a>A távoli eljáráshívás V2-készlet használata

Től NuGet távoli eljáráshívás Csomagverzió 2.8 lehetősége van a távoli eljáráshívás V2 verem használatára. A távoli eljáráshívás V2 verem további performant és szolgáltatások, mint az egyedi szerializálás és több moduláris API biztosít.
Sablon kód továbbra is használja a távoli eljáráshívás V1 verem.
Távoli eljáráshívás V2 nem összeegyeztethető V1 (a korábbi távoli eljáráshívási stack), ezért kövesse az alábbi utasításokat a [frissítése a V1 V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) szolgáltatás rendelkezésre állása befolyásolása nélkül.

Az alábbi megközelítések amelyek lehetővé teszik a V2 verem.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Egy szerelvény attribútum segítségével a V2-készlet használata

Ezeket a lépéseket a szerelvény attribútumokat V2 verem használandó sablon kód módosítása.

1. Módosítsa a végpont erőforrás `"ServiceEndpoint"` való `"ServiceEndpointV2"` a szolgáltatás jegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Használja a `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` kiterjesztésmetódus (egyenlő V1 és V2) távoli eljáráshívási figyelői létrehozásához.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Jelölje meg a távoli eljáráshívás kapcsolódási pontok tartalmazó szerelvény a `FabricTransportServiceRemotingProvider` attribútum.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```

Nincs kódot kell módosítania az ügyfélprojekt.
A felület szerelvény, győződjön meg arról, hogy a fenti szerelvényattribútum szolgál az ügyfél szerelvény összeállítása.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>A V2 verem használható explicit V2 osztályok használatával

Egy szerelvény attribútum használata helyett, mint a V2 verem is engedélyezheti explicit V2 osztályok használatával.

Ezeket a lépéseket a V2 verem explicit V2 osztályokat használandó sablon kód módosítása.

1. Módosítsa a végpont erőforrás `"ServiceEndpoint"` való `"ServiceEndpointV2"` a szolgáltatás jegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Használja a [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) a a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` névtér.

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

3. Használja a [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) a a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` névtér ügyfelek létrehozásához.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Hogyan frissítheti a távoli eljáráshívás V1 távoli eljáráshívási V2.
A V1 rendszerre történő frissítés érdekében V2, 2. lépés frissítések szükségesek. A felsorolt sorrendben hajtja végre a következő lépéseket.

1. Frissítés a V1 szolgáltatás V2 szolgáltatás CompactListener attribútum használatával.
Ez a változás gondoskodik arról, hogy a szolgáltatás figyel-e a V1 és V2-figyelő.

    a) Endpoint erőforrás hozzáadása nevű, "ServiceEndpointV2" a szolgáltatás jegyzékben.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) használja a következő Kiterjesztésmetódus távoli eljáráshívás figyelő létrehozásához.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) távoli eljáráshívási felületek CompatListener és V2-ügyfél Szerelvényattribútum hozzáadni.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Frissítés a V1-es ügyfél V2 ügyfél V2 ügyfél attribútum használatával.
Ezt a lépést biztosítja, hogy az ügyfél által használt V2 verem.
Nincs változás a projekt/ügyfélszolgáltatás szükség. Ügyfél projektek létrehozása a következő szerelvénnyel: frissített felület is használhatók.

3. Ez a lépés nem kötelező. V2Listener attribútumot használják, és ezután frissítse a V2 szolgáltatást.
Ez a lépés gondoskodik arról, hogy csak a V2-figyelő szolgáltatás figyeli.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Hogyan használható az egyéni szerializálási a távoli eljáráshívás V2.
Példa a távoli eljáráshívás V2 Json-szerializálás használja.
1. Alkalmazzon IServiceRemotingMessageSerializationProvider felület implementálásához egyéni sorba rendezésre.
    A kódrészletet ez hogyan megvalósításától függően.

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

2.    Távoli eljáráshívás figyelő alapértelmezett szerializálási szolgáltató JsonSerializationProvider felülírása.

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
* [Webes API-t a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [A Reliable Services kommunikáció biztonságához](service-fabric-reliable-services-secure-communication.md)

