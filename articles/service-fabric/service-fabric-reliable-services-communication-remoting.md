---
title: Szolgáltatás-átirányító szolgáltatás c# használatával a Service Fabric-ben
description: A Service Fabric távoli eljáráshívással kommunikálhat a C# szolgáltatásokkal.
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433884"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Szolgáltatás-smoting C# megbízható szolgáltatásokkal

> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Olyan szolgáltatások esetében, amelyek nem kötődnek egy adott kommunikációs protokollhoz vagy veremhez, például egy webes API-hoz, a Windows Kommunikációs alaprendszerhez vagy másokhoz, a Reliable Services keretrendszer egy távoli eljáráshívások gyors és egyszerű beállításához biztosít távoli eljáráshívásokat. Szolgáltatások. Ez a cikk a C#-val írt szolgáltatások távoli eljáráshívásainak beállítását ismerteti.

## <a name="set-up-remoting-on-a-service"></a>Átirányító beállítás szolgáltatáson

A szolgáltatás hoz a remoting két egyszerű lépésben állítható be:

1. Hozzon létre egy felületet a szolgáltatás számára. Ez a felület határozza meg azokat a módszereket, amelyek a szolgáltatás távoli eljáráshívásához rendelkezésre állnak. A metódusok kell feladat-visszatérő aszinkron módszerek. Az interfésznek `Microsoft.ServiceFabric.Services.Remoting.IService` azt a jelzést kell végrehajtania, hogy a szolgáltatás nak van egy átirányító interfésze.
2. Használjon egy moting figyelőt a szolgáltatásban. A részletes figyelő olyan `ICommunicationListener` megvalósítás, amely átirányító képességeket biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmazza `CreateServiceRemotingInstanceListeners` a kiterjesztés izolált és állapotalapú szolgáltatások, amelyek segítségével hozzon létre egy távalló figyelő segítségével az alapértelmezett átirányítási átviteli protokoll.

>[!NOTE]
>A `Remoting` névtér külön NuGet csomagként `Microsoft.ServiceFabric.Services.Remoting`érhető el, amelynek neve .

Például a következő állapotmentes szolgáltatás egyetlen módszert tesz elérhetővé a "Hello World" távoli eljáráshíváson keresztül.

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
> A szolgáltatásfelület argumentumai és visszatérési típusai bármilyen egyszerű, összetett vagy egyéni típusúak lehetnek, de a .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)által szerializálhatónak kell lenniük.
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatásmetódusok hívása

A szolgáltatás hívási metódusai a szolgáltatás on the remoting stack `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` használatával történik egy helyi proxy a szolgáltatás az osztályon keresztül. A `ServiceProxy` metódus helyi proxyt hoz létre a szolgáltatás által megvalósított felület használatával. Ezzel a proxyval távolról is meghívhat metódust a kapcsolaton.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A kapcsolati átirányító keretrendszer a szolgáltatás által az ügyfélnek okozott kivételeket propagálja. Ennek eredményeképpen, `ServiceProxy`ha használják, az ügyfél felelős a szolgáltatás által okozott kivételek kezeléséért.

## <a name="service-proxy-lifetime"></a>Szolgáltatásproxy élettartama

A szolgáltatásproxy létrehozása egy könnyű művelet, így annyi térhet el, amennyire szüksége van. A szolgáltatásproxy-példányok addig használhatók fel, amíg szükség van rájuk. Ha egy távoli eljáráshívás kivételt okoz, akkor is újra felhasználhatja ugyanazt a proxypéldányt. Minden szolgáltatásproxy tartalmaz egy kommunikációs ügyfelet, amely et használnak üzenetek küldésére a hálózaton keresztül. Távoli hívások meghívása közben a rendszer belső ellenőrzéseket végez annak megállapítására, hogy a kommunikációs ügyfél érvényes-e. Az ellenőrzések eredményei alapján a kommunikációs ügyfél szükség esetén újra létrejön. Ezért ha kivétel történik, nem kell újra létrehoznia. `ServiceProxy`

### <a name="service-proxy-factory-lifetime"></a>Szolgáltatásproxy gyári élettartama

[A ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) egy olyan gyár, amely proxypéldányokat hoz létre a különböző átirányító felületekhez. Ha az API-t `ServiceProxyFactory.CreateServiceProxy` proxy létrehozásához használja, a keretrendszer létrehoz egy singleton szolgáltatásproxyt.
Hasznos, ha manuálisan hoz létre egyet, ha felül kell írnia az [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) tulajdonságokat.
A gyár létrehozása költséges művelet. A szolgáltatásproxy-gyár a kommunikációs ügyfél belső gyorsítótárát tartja fenn.
Ajánlott eljárás a szolgáltatásproxy-gyár gyorsítótárazása a lehető leghosszú ideig.

## <a name="remoting-exception-handling"></a>A kapcsolatoni kivételkezelés

A szolgáltatás API-ja által okozott összes távoli kivételt a rendszer összesített kivételként küldi vissza az ügyfélnek. A távoli kivételeket a DataContract szerializálandónak kell lennie. Ha nem, a proxy API a [ServiceException-et](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) a szerializálási hibával dobja.

A szolgáltatásproxy kezeli az összes feladatátvételi kivételt a szolgáltatáspartícióhoz, amelyhez létrehozva van. Újra feloldja a végpontokat, ha vannak feladatátvételi kivételek (nem átmeneti kivételek), és újrapróbálkozik a hívással a megfelelő végponttal. A feladatátvételi kivételek újrapróbálkozásainak száma határozatlan.
Ha átmeneti kivételek fordulnak elő, a proxy újrapróbálkozik a hívással.

Az alapértelmezett újrapróbálkozási paramétereket az [OperationRetrySettings biztosítja.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings)

A felhasználó konfigurálhatja ezeket az értékeket, ha átadja az OperationRetrySettings objektumot a ServiceProxyFactory konstruktornak.

## <a name="use-the-remoting-v2-stack"></a>A smoting V2 verem használata

A NuGet remoting csomag 2.8-as verziójának verziójában lehetősége van a remoting V2 verem használatára. A remoting V2 verem jobban teljesít. Olyan funkciókat is kínál, mint az egyéni szerializálás és a több dugattyújható API.It also provides features like custom serialization and more pluggable API-k.
A sablonkód továbbra is a moting V1 verem használatát használja.
A v2-es átirányító nem kompatibilis a V1-es sel (az előző átirányító verem). Kövesse a cikkben található [frissítés V1-ről V2-re](#upgrade-from-remoting-v1-to-remoting-v2) a szolgáltatás rendelkezésre állására gyakorolt hatások elkerülése érdekében.

A v2-verem engedélyezéséhez a következő megközelítések érhetők el.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>A V2-verem használata összeállítási attribútummal

Ezek a lépések módosítják a sablonkódot a V2 verem használatával egy összeállítási attribútum használatával.

1. Módosítsa a végponterőforrást `"ServiceEndpointV2"` a szolgáltatásjegyzékben lévőről. `"ServiceEndpoint"`

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. A `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` bővítmény módszerrel hozzon létre átmatolási figyelők (egyenlő mind a V1 és V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Jelölje meg a kapcsolat-szelvény felületeket tartalmazó `FabricTransportServiceRemotingProvider` szerelvényt attribútummal.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Az ügyfélprojektben nincs szükség kódmódosításra.
Építse fel az ügyfélszerelvényt a csatolószerelvénysel, és győződjön meg arról, hogy a korábban megjelenített összeállítási attribútum ot használja.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Explicit V2 osztályok használata a V2 verem használatához

A szerelvényattribútum okának alternatívájaként a V2-verem explicit V2-osztályok használatával is engedélyezhető.

Ezek a lépések módosítják a sablonkódot a V2 verem explicit V2 osztályok használatával.

1. Módosítsa a végponterőforrást `"ServiceEndpointV2"` a szolgáltatásjegyzékben lévőről. `"ServiceEndpoint"`

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Használja [a FabricTransportServiceRemotingListener-t](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` névtérből.

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

3. Használja [fabrictransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` a névtérből ügyfelek létrehozásához.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Frissítés a v1-es és a v2-es rendszer remotingjából

A V1-ről V2-re való frissítéshez kétlépcsős frissítések szükségesek. Kövesse a lépéseket ebben a sorrendben.

1. Frissítse a V1 szolgáltatást V2 szolgáltatásra ezzel az attribútummal.
Ez a módosítás biztosítja, hogy a szolgáltatás figyeli a V1 és V2 figyelő.

    a. Adjon hozzá egy "ServiceEndpointV2" nevű végponterőforrást a szolgáltatásjegyzékben.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. A következő bővítménymetódus sal hozzon létre egy átirányító figyelőt.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Vegyen fel egy összeállítási attribútumot a szoftveres és v2-es figyelő és a V2-ügyfél használatához.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Frissítse a V1-ügyfél egy V2-ügyfél a V2-ügyfél attribútum használatával.
Ez a lépés biztosítja, hogy az ügyfél használja a V2 verem.
Nincs szükség az ügyfélprojekt/szolgáltatás módosítására. A frissített illesztőfelület-összeállítással rendelkező ügyfélprojektek létrehozása elegendő.

3. Ez a lépés nem kötelező. Használja a V2 figyelő attribútumot, majd frissítse a V2 szolgáltatást.
Ez a lépés gondoskodik arról, hogy a szolgáltatás csak a V2 figyelő figyel.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>A smoting V2 (interface compatible) verem használata

 A remoting V2 (felület kompatibilis, más néven V2_1) verem rendelkezik a V2-es áthágás verem összes funkciójával. A felület verem kompatibilis a mutatóval V1 verem, de ez nem visszafelé kompatibilis a V2 és V1. Ha a szolgáltatás rendelkezésre állásának befolyásolása nélkül szeretne V1-ről V2_1 frissíteni, kövesse a V1-ről V2-re (felületkompatibilis) frissítés című cikkben ismertetett lépéseket.


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>A remoting V2 (interface compatible) verem használatához szerelvényattribútum használatával

Az alábbi lépésekkel válthat V2_1 veremre.

1. Adjon hozzá egy "ServiceEndpointV2_1" nevű végponterőforrást a szolgáltatásjegyzékben.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. A feladatáteresztő bővítmény módszer segítségével hozzon létre egy feladatot kereső figyelő.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. [Összeállítási attribútum](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) hozzáadása a kapcsolatonkénti kapcsolódási pontokhoz.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Az ügyfélprojektben nincs szükség módosításokra.
Építse fel az ügyfélszerelvényt a csatolószerelvénysegítségével, és győződjön meg arról, hogy az előző összeállítási attribútum használatban van.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Explicit átmatolási osztályok használatával hozzon létre egy figyelő/ügyfél gyárat a V2 (interface kompatibilis) verzióhoz

Kövesse az alábbi lépéseket:

1. Adjon hozzá egy "ServiceEndpointV2_1" nevű végponterőforrást a szolgáltatásjegyzékben.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Használja a [sokmindenkereső V2 figyelőt.](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet) A szolgáltatásvégpont-erőforrás alapértelmezett neve "ServiceEndpointV2_1". Meg kell határozni a szolgáltatásjegyzékben.

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

3. Használja a V2 [ügyfélgyárat](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Frissítés a szoftverkapcsolatosításról a v2-es (felülettel kompatibilis)

A V1-ről V2-re (felületkompatibilis, V2_1) való frissítéshez kétlépcsős frissítésekszükségesek. Kövesse a lépéseket ebben a sorrendben.

> [!NOTE]
> V1-ről V2-re való `Remoting` frissítéskor győződjön meg arról, hogy a névtér a V2 használatára frissül. Példa: 'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client'
>
>

1. Frissítse a V1 szolgáltatást V2_1 szolgáltatásra a következő attribútum használatával.
Ez a módosítás gondoskodik arról, hogy a szolgáltatás figyeli a V1 és a V2_1 figyelő.

    a. Adjon hozzá egy "ServiceEndpointV2_1" nevű végponterőforrást a szolgáltatásjegyzékben.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. A következő bővítménymetódus sal hozzon létre egy átirányító figyelőt.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Vegyen fel egy összeállítási attribútumot a keresztkezelő felületekhez a V1, V2_1 figyelő és V2_1 ügyfél használatához.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Frissítse a V1-ügyfelet a V2_1 ügyfélre a V2_1 ügyfélattribútum használatával.
Ez a lépés biztosítja, hogy az ügyfél használja a V2_1 verem.
Nincs szükség az ügyfélprojekt/szolgáltatás módosítására. A frissített illesztőfelület-összeállítással rendelkező ügyfélprojektek létrehozása elegendő.

3. Ez a lépés nem kötelező. Távolítsa el a V1 figyelő verziója az attribútumból, majd frissítse a V2 szolgáltatás.
Ez a lépés gondoskodik arról, hogy a szolgáltatás csak a V2 figyelő figyel.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Egyéni szerializálás használata egy visszavont burkolt üzenettel

A szálkás burkolt üzenethez egyetlen burkolt objektumot hozunk létre, amely az összes paramétert mezőként adja meg.
Kövesse az alábbi lépéseket:

1. Valósítsa meg a `IServiceRemotingMessageSerializationProvider` felületet az egyéni szerializálás megvalósításának biztosításához.
    Ez a kódrészlet megmutatja, hogyan néz ki a megvalósítás.

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

2. Felülbírálja az alapértelmezett szerializálási szolgáltatót egy terhelésmutató `JsonSerializationProvider` figyelővel.

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

3. Az alapértelmezett szerializálási szolgáltató felülbírálása `JsonSerializationProvider` egy ügyfél-előállító esetében.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>További lépések

* [Webes API az OWIN szolgáltatással a megbízható szolgáltatásokban](service-fabric-reliable-services-communication-webapi.md)
* [A Windows Kommunikációs alaprendszer kommunikációja megbízható szolgáltatásokkal](service-fabric-reliable-services-communication-wcf.md)
* [Biztonságos kommunikáció a megbízható szolgáltatásokhoz](service-fabric-reliable-services-secure-communication.md)
