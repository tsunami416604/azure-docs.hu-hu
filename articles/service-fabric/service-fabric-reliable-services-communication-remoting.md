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
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205816"
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
     return this.CreateServiceRemotingInstanceListeners();
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

Alapértelmezett újrapróbálkozási paraméterek által biztosított [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Felhasználó-OperationRetrySettings objektum átadását ServiceProxyFactory konstruktor konfigurálhatja ezeket az értékeket.

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
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
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

1. Frissítés a V1 szolgáltatás V2 szolgáltatás alábbi attribútum használatával.
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

    c) a szerelvény attribútum hozzáadása a V1 és V2-figyelő és V2-ügyfél használata a távoli eljáráshívás adapteren.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Frissítés a V1-ügyfél V2 ügyfél V2 ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy ügyfél V2-vermet használ.
Nincs változás a projekt/ügyfélszolgáltatás nem szükséges. Alkalmazás készítése az ügyfél projektek a frissített felületet szerelvény is használhatók.

3. Ez a lépés nem kötelező. V2Listener attribútumot használja, és utána frissítse a V2-szolgáltatás.
Ebben a lépésben gondoskodik arról, hogy a szolgáltatás figyel-e csak a V2-figyelőt.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Távoli eljáráshívás V2(InterfaceCompatible) stack használata
 Távoli eljáráshívás V2 (más néven V2_1 InterfaceCompatible) verem minden V2 távoli eljáráshívás funkcióját a verem mellett felület kompatibilis stack távoli eljáráshívás V1 stack, de nem kompatibilis a V2 és V1 rendelkezik. Nyugodtan Dolgozhatok addig is a frissítés V1-től V2_1 a szolgáltatás rendelkezésre állása befolyásolása nélkül, kövesse az alábbi cikk[frissítése V1-től a V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Szerelvény attribútum használata a távoli eljáráshívás V2(InterfaceCompatible) verem használata.

Az alábbiakban V2_1 stack módosításához kövesse a lépéseket.

1. A szolgáltatásjegyzékben "ServiceEndpointV2_1" hozzáadása egy végpont nevű erőforrás.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Távoli eljáráshívás figyelő létrehozásához használja a távoli eljáráshívás metódust.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Adjon hozzá [szerelvény attribútum](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) lévő Remoting felületek.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Nincs szükség módosításokra ügyfél-projektben.
A felület szerelvény ügyfél szerelvényt, a a gondoskodik arról, hogy fent szerelvény attribútum használatban van.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Explicit távoli eljáráshívás osztályok használatával hozzon létre figyelőt / ClientFactory V2(InterfaceCompatible) verzióhoz.
Az alábbiakban kövesse a lépéseket.
1.  A szolgáltatásjegyzékben "ServiceEndpointV2_1" hozzáadása egy végpont nevű erőforrás.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Használat [távoli eljáráshívás V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Alapértelmezés szerint használt szolgáltatás végponti erőforrás név "ServiceEndpointV2_1" és a szolgáltatásjegyzék meg kell adni.

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

3. Használja a V2 [Ügyfélgyára](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Frissítse a távoli eljáráshívás V1 a távoli eljáráshívás V2(InterfaceCompatible) módja.
Annak érdekében, hogy a V2 a V1 verzióról (más néven V2_1 InterfaceCompatible), 2. lépés – frissítések szükségesek. A felsorolt sorrendben hajtja végre a következő lépéseket.

1. Frissítés a V1 szolgáltatás V2_1 szolgáltatás a következő attribútum használatával.
Ez a változás gondoskodik arról, hogy a szolgáltatás figyel-e a V1 és V2_1 figyelő.

    ) egy végpont nevű erőforrás hozzáadása "ServiceEndpointV2_1" a szolgáltatásjegyzékben.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
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

    c) a szerelvény attribútum hozzáadása a távoli eljáráshívás adapteren V1, V2_1 figyelő és V2_1 ügyfél használatához.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Frissítés a V1-ügyfél V2_1 ügyfél V2_1 ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy ügyfél V2_1 stack használ.
Nincs változás a projekt/ügyfélszolgáltatás nem szükséges. Alkalmazás készítése az ügyfél projektek a frissített felületet szerelvény is használhatók.

3. Ez a lépés nem kötelező. Távolítsa el a V1-figyelő verzió attribútumból, és utána frissítse a V2-szolgáltatás.
Ebben a lépésben gondoskodik arról, hogy a szolgáltatás figyel-e csak a V2-figyelőt.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Hogyan használható az egyéni szerializálás távoli eljáráshívás burkolt be üzenettel.
A távoli eljáráshívás burkolt be üzenetben hozunk létre egyetlen burkolt objektumot és az összes paramétert egyik mezője.
A lépések a következők:

1. Egyéni sorba rendezésre implementáció IServiceRemotingMessageSerializationProvider felület megvalósításához.
    Itt van a kódtöredék hogyan néz végrehajtására.

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
