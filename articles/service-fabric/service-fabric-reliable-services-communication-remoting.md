---
title: Szolgáltatás távelérésének lehetővé tétele a Service Fabric C# használatával |} A Microsoft Docs
description: Remoting Service Fabric lehetővé teszi, hogy az ügyfelek és a szolgáltatásokat a C# szolgáltatásokkal kommunikálni a távoli eljáráshívás segítségével.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: f9cd6e2fee738d2d42c790b4eb7b9a876a44b01d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670795"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Szolgáltatás távelérésének lehetővé tétele a C# Reliable Services szolgáltatással

> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A szolgáltatások, amelyek egy adott kommunikációs protokollt vagy a veremben, mint például a webes API-k, a Windows Communication Foundation vagy mások számára, nem kapcsolódik a Reliable Services-keretrendszer gyorsan és egyszerűen beállítása a távoli eljáráshívások távoli eljáráshívás mechanizmust biztosít szolgáltatások. Ez a cikk ismerteti, hogyan állítható be a C# használatával írt szolgáltatásokhoz a távoli eljáráshívásokat.

## <a name="set-up-remoting-on-a-service"></a>Állítsa be a szolgáltatás távelérésének lehetővé tétele

Beállíthat két egyszerű lépésben szolgáltatás távelérésének lehetővé tétele:

1. Hozzon létre egy kapcsolatot a szolgáltatás megvalósítása. Toto rozhraní definuje módszerek érhetők el a szolgáltatást a távoli eljáráshívás. A módszerek kell lennie, a feladat-adatszolgáltató aszinkron módszereket. A felület musí implementovat `Microsoft.ServiceFabric.Services.Remoting.IService` , hogy jelezze, hogy a szolgáltatás távelérésének lehetővé tétele felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. A távoli eljáráshívás-figyelő egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívás képességeket biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmazza a metódust `CreateServiceRemotingListener` is állapot nélküli és állapotalapú szolgáltatásokhoz, amelyek segítségével hozzon létre egy távoli eljáráshívás figyelőt az alapértelmezett távelérési átviteli protokoll használatával.

>[!NOTE]
>A `Remoting` névtér érhető el NuGet-csomagként külön nevű `Microsoft.ServiceFabric.Services.Remoting`.

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
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Az argumentumok és a szolgáltatás felületén a návratové typy lehet olyan egyszerű, bonyolult vagy egyéni típusokat, de képesnek kell a .NET-szerializálni kell lennie [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>A távoli szolgáltatás metódusok meghívása

Metódusok meghívása a szolgáltatás a távoli eljáráshívás stack használatával a helyi proxyn keresztül a szolgáltatás segítségével történik a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztály. A `ServiceProxy` módszer létrehoz egy helyi proxykiszolgáló ugyanazt a felületet, amely megvalósítja a szolgáltatás által. A proxyk meghívhatja módszerek kapcsolaton távolról.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A távoli eljáráshívás keretrendszer tölti ki az ügyfélnek a szolgáltatás által okozott kivételeket. Ennek eredményeképpen, amikor `ServiceProxy`van használja, az ügyfél kezeléséért felelős a a szolgáltatás által okozott kivételeket.

## <a name="service-proxy-lifetime"></a>Szolgáltatási proxy élettartamát

Szolgáltatási proxy létrehozása egy könnyen használható művelet, így annyi igény szerint hozhat létre. Proxy szolgáltatáspéldányok felhasználhatók a is szükség van rájuk. A távoli eljáráshívás kivételt jelez, ha továbbra is felhasználhatja a proxy-példányt. Minden szolgáltatási proxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Vzdálená volání meghívása, során annak megállapításához, hogy a kommunikáció ügyfél érvényes belső ellenőrzések elvégzése. Ezen ellenőrzés eredménye alapján, a kommunikáció ügyfél újra létrejön, ha szükséges. Ezért, ha kivétel történik, akkor nem kell újra létre kell hoznia `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Szolgáltatási proxy gyári élettartama

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) proxy példányok különböző távoli eljáráshívás adapterek létrehozó gyár van. Ha a API-val `ServiceProxyFactory.CreateServiceProxy` proxy létrehozására a keretrendszer hoz létre egy egyszeres szolgáltatási proxy.
Akkor hasznos, hozzon létre egyet manuálisan Ha felül kell bírálnia [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) tulajdonságait.
Gyári létrehozása drága művelet. A szolgáltatás proxy gyári egy belső gyorsítótár, a kommunikáció ügyfél megőrzi.
Ajánlott eljárás az, amíg a szolgáltatás proxy gyári gyorsítótárazni.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelés

Összes távoli által okozott kivételek feldolgozását a szolgáltatás API-t az ügyfélnek, AggregateException érkeznek. Távoli kivételek által DataContract szerializálható képesnek kell lennie. Ha nem, a proxy API jelez [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) szerepel a szerializálási hiba miatt.

A proxy kezeli a szolgáltatás-partíció létrehozása az összes feladatátvételi kivétel. Ez újra oldja fel a végpontok, ha feladatátvétel kivételek (kivétel nem átmeneti), és a hívást a megfelelő végpontra való próbálkozások. A feladatátvételi kivételeket az újrapróbálkozások számát kötelező befejezésére való határozatlan idejű.
Átmeneti kivétel lép fel, ha a proxy újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterek által biztosított [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

A felhasználók ezeket az értékeket konfigurálhatja a OperationRetrySettings objektum ServiceProxyFactory konstruktorának való átadásával.

## <a name="use-the-remoting-v2-stack"></a>A távoli eljáráshívás V2 verem használata

Kezdődően 2.8-as verziója, a távoli eljáráshívás NuGet-csomagot lehetősége van a távoli eljáráshívás V2 verem használata. A távoli eljáráshívás V2 verem teljesít jobban. Szolgáltatások, mint az egyéni sorba rendezésre és több moduláris API-kat is biztosít.
Sablon kód továbbra is a távoli eljáráshívás V1 vermet használja.
Távoli eljáráshívás V2, nem kompatibilis a V1 (a korábbi távoli eljáráshívás stack). Kövesse a cikk a [v2 a V1 verzióról](#upgrade-from-remoting-v1-to-remoting-v2) a szolgáltatás rendelkezésre állására hatások elkerülése érdekében.

A V2-verem engedélyezéséhez a következő módszerek érhetők el.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Egy szerelvény attribútum használata a V2-verem használata

Ezeket a lépéseket a sablonban lévő kód a V2-verem használata egy szerelvény attribútum használatával módosíthatja.

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
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Az ügyfélprojekt szükséges kód módosítása nélkül.
A felület szerelvény, győződjön meg arról, hogy használja-e a szerelvény attribútum korábban látható, hogy az ügyfél szerelvény hozhat létre.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>A V2-verem használata explicit V2 osztályok használatával

Ahelyett, hogy egy szerelvény attribútum segítségével, mint a V2-verem is engedélyezhető explicit V2 osztályok használatával.

Ezeket a lépéseket a sablonban lévő kód a V2-verem használata explicit V2 osztályok használatával módosíthatja.

1. Módosítsa a végponti erőforrás `"ServiceEndpoint"` való `"ServiceEndpointV2"` szolgáltatásjegyzékben.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Használat [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) származó a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` névtér.

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

3. Használat [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) származó a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` névtér létrehozása az ügyfelek számára.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Frissítse a távoli eljáráshívás V1 a távoli eljáráshívás V2

V2 a V1 frissíthet, két részből álló frissítések szükségesek. Kövesse az itt látható sorrendben.

1. Frissítés a V1 szolgáltatás V2 szolgáltatás Ez az attribútum használatával.
Ez a változás gondoskodik arról, hogy a szolgáltatás figyel-e a V1 és V2-figyelő.

    a. A szolgáltatásjegyzékben adjon hozzá egy végpont "ServiceEndpointV2" nevű erőforrás.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. A távoli eljáráshívás figyelő létrehozásához használja a következő metódust.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. A V1 és V2-figyelő és a V2-ügyfél használata a távoli eljáráshívás adapteren szerelvény attribútum hozzáadása.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Frissítés a V1-ügyfél a V2-ügyfél a V2-ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy az ügyfél a V2-vermet használ.
Nincs változás a projekt/ügyfélszolgáltatás nem szükséges. Alkalmazás készítése az ügyfél projektek a frissített felületet szerelvény is használhatók.

3. Ez a lépés nem kötelező. A V2-figyelő attribútumot használja, és utána frissítse a V2-szolgáltatás.
Ebben a lépésben gondoskodik arról, hogy a szolgáltatás csak a V2-figyelő figyeli.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>A távoli eljáráshívás V2 (interface-kompatibilis) verem használata

 A távoli eljáráshívás V2 (csatoló kompatibilis, más néven V2_1) stack rendelkezik a V2 távoli eljáráshívás verem összes funkcióját. A felület stack kompatibilis a távoli eljáráshívás V1-vermet, de már nem kompatibilis a V2 és V1. Frissítse a V1 V2_1 anélkül, hogy befolyásolná a szolgáltatás rendelkezésre állása, kövesse a cikk frissítése V1-től v2 (felület kompatibilis).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Egy szerelvény attribútum használata a távoli eljáráshívás V2 (interface-kompatibilis) verem használata

Kövesse az alábbi lépéseket egy V2_1 verem módosítani.

1. A szolgáltatásjegyzékben adjon hozzá egy végpont "ServiceEndpointV2_1" nevű erőforrás.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. A távoli eljáráshívás metódust használatával hozzon létre egy távoli eljáráshívás figyelőt.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adjon hozzá egy [szerelvény attribútum](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) lévő remoting felületek.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Nincs szükség módosításokra az ügyfél-projektben.
A felület sestavení do győződjön meg arról, hogy az előző szerelvény attribútum használatban van, az ügyfél szerelvény hozhat létre.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>A V2 (interface-kompatibilis) verziójának egy figyelő vagy ügyfél-előállító létrehozásához használja a kifejezett távoli eljáráshívás osztályokat

Kövesse az alábbi lépéseket:

1. A szolgáltatásjegyzékben adjon hozzá egy végpont "ServiceEndpointV2_1" nevű erőforrás.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Használja a [távelérése V2 figyelő](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Az alapértelmezett végpont erőforrás szolgáltatásnév használja az "ServiceEndpointV2_1." A szolgáltatásjegyzékben kell definiálni.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. A V2 használata [ügyfélgyára](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Frissítés a távoli eljáráshívás V1 táveléréssel V2 (felület kompatibilis)

V2 a V1 frissítése (felület kompatibilis, más néven V2_1), két részből álló frissítések szükségesek. Kövesse az itt látható sorrendben.

1. Frissítés a V1 szolgáltatás V2_1 szolgáltatás a következő attribútum használatával.
Ez a változás gondoskodik arról, hogy a szolgáltatás a V1-es és a V2_1 figyelő által figyelt.

    a. A szolgáltatásjegyzékben adjon hozzá egy végpont "ServiceEndpointV2_1" nevű erőforrás.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. A távoli eljáráshívás figyelő létrehozásához használja a következő metódust.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Szerelvény attribútum hozzáadása a távoli eljáráshívás adaptereken használja a V1, V2_1 figyelő és V2_1 ügyfél.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. A V1-ügyfél frissítése az V2_1 ügyfélnek V2_1 ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy az ügyfél a V2_1 vermet használ.
Nincs változás a projekt/ügyfélszolgáltatás nem szükséges. Alkalmazás készítése az ügyfél projektek a frissített felületet szerelvény is használhatók.

3. Ez a lépés nem kötelező. Távolítsa el a V1-figyelő verzió az attribútum, és utána frissítse a V2-szolgáltatás.
Ebben a lépésben gondoskodik arról, hogy a szolgáltatás csak a V2-figyelő figyeli.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Egyéni sorba rendezésre használata a távoli eljáráshívás burkolt üzenet

Távoli eljáráshívás burkolt üzenet hozunk létre egy becsomagolt objektumként és az összes paramétert egyik mezője.
Kövesse az alábbi lépéseket:

1. Alkalmazzon a `IServiceRemotingMessageSerializationProvider` felület implementáció egyéni sorba rendezésre.
    Ez a kódrészlet bemutatja a megvalósítás néz ki.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Bírálja felül az alapértelmezett szerializálási szolgáltató `JsonSerializationProvider` egy távoli eljáráshívás figyelőhöz.

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

3. Bírálja felül az alapértelmezett szerializálási szolgáltató `JsonSerializationProvider` távoli eljáráshívás ügyfél Factory.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>További lépések

* [Webes API-k az OWIN aktorai Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation-kommunikáció a Reliable Services szolgáltatással](service-fabric-reliable-services-communication-wcf.md)
* [Biztonságos kommunikáció a Reliable Services](service-fabric-reliable-services-secure-communication.md)
