---
title: Reliable Services WCF kommunikációs verem
description: A Service Fabricban a beépített WCF kommunikációs verem a Reliable Services ügyfél-szolgáltatás WCF-kommunikációját biztosítja.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75433868"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-alapú kommunikációs verem a Reliable Serviceshoz
A Reliable Services-keretrendszer lehetővé teszi, hogy a szolgáltatás szerzője kiválassza a szolgáltatáshoz használni kívánt kommunikációs veremet. A [CreateServiceReplicaListeners vagy a CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metódusok által visszaadott **ICommunicationListener** keresztül képesek a választás kommunikációs verembe való csatlakoztatására. A keretrendszer a kommunikációs verem megvalósítását biztosítja azon szolgáltatás-szerzők Windows Communication Foundation (WCF) alapján, akik WCF-alapú kommunikációt kívánnak használni.

## <a name="wcf-communication-listener"></a>WCF kommunikációs figyelő
A **ICOMMUNICATIONLISTENER** WCF-specifikus implementációját a **Microsoft. ServiceFabric. Services. Communication. WCF. Runtime. WcfCommunicationListener** osztály nyújtja.

Tegyük fel, hogy a szolgáltatási szerződés típusa`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

A következő módon hozhatunk létre WCF kommunikációs figyelőt a szolgáltatásban.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Ügyfelek írása a WCF kommunikációs verembe
Ahhoz, hogy az ügyfelek a WCF használatával kommunikáljanak a szolgáltatásokkal, a keretrendszer biztosítja a **WcfClientCommunicationFactory**, amely a [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md)WCF-specifikus implementációja.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

A WCF kommunikációs csatornája a **WcfCommunicationClientFactory**által létrehozott **WcfCommunicationClient** érhető el.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Az ügyfél kódja használhatja a **WcfCommunicationClientFactory** , valamint a **WcfCommunicationClient** , amely a szolgáltatás végpontjának meghatározására és a szolgáltatással való kommunikációra szolgáló **ServicePartitionClient** implementálja.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Az alapértelmezett ServicePartitionResolver feltételezi, hogy az ügyfél ugyanazon a fürtön fut, mint a szolgáltatás. Ha ez nem igaz, hozzon létre egy ServicePartitionResolver objektumot, és adja át a fürt kapcsolatainak végpontját.
> 
> 

## <a name="next-steps"></a>További lépések
* [Távoli eljáráshívás Reliable Services táveléréssel](service-fabric-reliable-services-communication-remoting.md)
* [Webes API a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md)
* [A Reliable Services kommunikációjának biztonságossá tétele](service-fabric-reliable-services-secure-communication-wcf.md)

