---
title: "A Service Fabric szolgáltatás távelérése |} Microsoft Docs"
description: "A Service Fabric távoli eljáráshívás lehetővé teszi, hogy az ügyfelek és a szolgáltatások a távoli eljáráshívás segítségével szolgáltatásokkal kommunikálni."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 438eeee7353cbd1d534f27471c9c9054aecc12e8
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="service-remoting-with-reliable-services"></a>A távelérés szolgáltatás megbízható szolgáltatásokkal
Azon szolgáltatások vannak társítva, egy adott kommunikációs protokollt vagy a veremben, mint például a WebAPI, a Windows Communication Foundation (WCF) vagy más, nem a Reliable Services keretrendszer lehetővé teszi a távoli eljáráshívás szolgáltatásokhoz a távoli eljáráshívás beállítása gyorsan és egyszerűen.

## <a name="set-up-remoting-on-a-service"></a>A szolgáltatás távoli eljáráshívást beállítani
A szolgáltatás távoli eljáráshívás beállítása két egyszerű lépésben történik:

1. Hozzon létre egy felület, a szolgáltatás megvalósítása. Ez az interfész határozza meg a módszereket, amelyek a szolgáltatás a távoli eljáráshívás érhetők el. A módszerek kell lennie a feladatot visszaadó aszinkron módszereket. Az illesztőfelületet kell megvalósítania `Microsoft.ServiceFabric.Services.Remoting.IService` jelezze, hogy a szolgáltatás távoli eljáráshívási felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. RemotingListener van egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmaz egy kiterjesztésmetódus`CreateServiceRemotingListener` mind az állapotmentes és állapotalapú szolgáltatások, amelyek segítségével az alapértelmezett távelérési átviteli protokollal távoli eljáráshívási figyelő létrehozása.

>[!NOTE]
>A `Remoting` névtér NuGet-csomagként külön néven érhető el`Microsoft.ServiceFabric.Services.Remoting`

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

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
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

A távoli eljáráshívás keretrendszer kivételek, a szolgáltatás, az ügyfél propagálása zajlik. Ezért kivételkezelést logika használatával az ügyfél `ServiceProxy` közvetlenül kezelheti a kivételeket, amelyek a szolgáltatás jelez.

## <a name="service-proxy-lifetime"></a>Szolgáltatási Proxy élettartamát
ServiceProxy létrehozása egy egyszerűsített művelet, így a felhasználók hozhatnak létre tetszőleges számú van szükségük. Szolgáltatási Proxy példányok újrafelhasználható, amíg a felhasználók szükség lenne rá. A távoli eljáráshívás kivételt jelez, ha felhasználók továbbra is felhasználhatja a proxy-példányt. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Közben, hogy a távoli hívásokat, azt belső ellenőrizze, hogy ha a kommunikációs ügyfél esetében érvényes. Az adott eredménye alapján, most újra létrehozzuk a kommunikációs ügyfél szükség esetén. Ezért ha kivétel történik, a felhasználók nem kell újból serviceproxy tartalmaz, de transzparens módon történik.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory élettartama
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) van olyan adat-előállítóval, amely különböző távoli eljáráshívási felületek proxy létrehozza. Ha az API-t használja `ServiceProxy.Create` proxy létrehozására, majd a keretrendszer ServiceProxy egypéldányos létrehozza.
Akkor célszerű manuálisan létrehozhat egyet az felül kell bírálni [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) tulajdonságait.
Létrehozása során drága. ServiceProxyFactory kommunikációs ügyfél belső gyorsítótárában megtalálhatók.
Ajánlott eljárás ServiceProxyFactory gyorsítótárazásához, amíg.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelést
A távoli kivétel lépett fel az service API, majd az AggregateException elküldi az ügyfélnek. Ellenkező esetben RemoteExceptions kell DataContract szerializálható [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) a proxykiszolgálóhoz API azt a szerializálási hiba történt.

ServiceProxy a szolgáltatás partíció létrejön az összes feladatátvételi kivétel kezelése. Újra feloldja a végpontok feladatátvételi Exceptions(Non-Transient Exceptions) esetén és a megfelelő végponttal hívás újrapróbálja a telepítést. Feladatátvétel kivétel újrapróbálkozások száma: nincs meghatározva.
Átmeneti kivételek esetén a proxy újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterei [OperationRetrySettings] által biztosított. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) Felhasználó OperationRetrySettings objektum ServiceProxyFactory konstruktorának való átadásával konfigurálhatja ezeket az értékeket.
## <a name="how-to-use-remoting-v2-stack"></a>Távoli eljáráshívás V2 verem használata
2.8 NuGet távoli eljáráshívás csomaggal lehetősége van a távelérése V2 verem használható. Távoli eljáráshívás V2 verem további performant és szolgáltatások, mint az egyéni szerializálható és több moduláris Api biztosít.
Alapértelmezés szerint nem a következő módosításokat, ha továbbra is távelérése V1 verem használható.
Távoli eljáráshívás V2 nem összeegyeztethető V1-es (előző távoli eljáráshívási stack), így kövesse az alábbi szóló frissítése a V1 V2 szolgáltatás rendelkezésre állása befolyásolása nélkül.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Szerelvény attribútummal V2 verem használható.

Az alábbiakban V2 verem módosításához kövesse a lépéseket.

1. Mint "ServiceEndpointV2" nevű végpont erőforrás hozzáadása a szolgáltatás jegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Módszerrel távoli eljáráshívási kiterjesztés távelérést figyelő létrehozása.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Adja hozzá a szerelvény attribútumot távoli eljáráshívási kapcsolaton sem.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Nincs szükség módosításra ügyfél projektben.
Az ügyfél szerelvény felület szerelvény összeállítása, hogy gondoskodik arról, hogy fent szerelvény attribútum használatban van.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Explicit V2 osztályok használatával hozzon létre figyelőt / ClientFactory
Az alábbiakban a lépést kell végrehajtania.
1.  Mint "ServiceEndpointV2" nevű végpont erőforrás hozzáadása a szolgáltatás jegyzékben.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Használjon [távoli eljáráshívás V2Listener](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). Alapértelmezés szerint használt szolgáltatási végpont erőforrás név "ServiceEndpointV2" és a Service Manifest definiálni kell.

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

3. Használja a V2 [Ügyfélgyára](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
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

3. Ez a lépés nem kötelező megadni. V2Listener attribútumot használják, és ezután frissítse a V2 szolgáltatást.
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

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
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

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
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
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
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

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
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

## <a name="next-steps"></a>Következő lépések
* [Webes API-t a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [A Reliable Services kommunikáció biztonságához](service-fabric-reliable-services-secure-communication.md)
