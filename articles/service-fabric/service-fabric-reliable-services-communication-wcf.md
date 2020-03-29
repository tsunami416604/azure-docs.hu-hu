---
title: Megbízható szolgáltatások WCF kommunikációs verem
description: A Service Fabric beépített WCF kommunikációs verem je ügyfél-szolgáltatás WCF kommunikációt biztosít a megbízható szolgáltatásokhoz.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433868"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-alapú kommunikációs verem megbízható szolgáltatásokhoz
A Megbízható szolgáltatások keretrendszer lehetővé teszi a szolgáltatás szerzők számára, hogy válassza ki a kommunikációs verem, amely et használni kívánnak a szolgáltatáshoz. Csatlakoztathatják az általuk választott kommunikációs vermet a [CreateServiceReplicaListeners vagy a CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metódusoktól visszaadott **ICommunicationListener** segítségével. A keretrendszer a Windows Kommunikációs Alapítvány (WCF) alapján biztosítja a kommunikációs verem megvalósítását azon szolgáltatásszerzők számára, akik WCF-alapú kommunikációt szeretnének használni.

## <a name="wcf-communication-listener"></a>WCF kommunikációs figyelő
Az **ICommunicationListener** WCF-specifikus implementációját a **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** osztály biztosítja.

Nehogy azt mondják, hogy van egy szolgáltatási szerződés ünk.`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

A következő módon hozhatunk létre egy WCF kommunikációs figyelőt a szolgáltatásban.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Ügyfelek írása a WCF kommunikációs veremhez
Az ügyfelek wcf használatával történő kommunikációjának írásához a keretrendszer biztosítja a **WcfClientCommunicationFactory**programot, amely a [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md)WCF-specifikus implementációja.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

A WCF kommunikációs csatorna a **WcfCommunicationClientFactory**által létrehozott **WcfCommunicationClient** rendszerről érhető el.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Az ügyfélkód használhatja a **WcfCommunicationClientFactory-t** a **WcfCommunicationClient-lel** együtt, amely a **ServicePartitionClient** szolgáltatást implementálja a szolgáltatás végpontjának meghatározásához és a szolgáltatással való kommunikációhoz.

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
> Az alapértelmezett ServicePartitionResolver feltételezi, hogy az ügyfél ugyanabban a fürtben fut, mint a szolgáltatás. Ha nem ez a helyzet, hozzon létre egy ServicePartitionResolver objektumot, és adja át a fürt kapcsolati végpontok.
> 
> 

## <a name="next-steps"></a>További lépések
* [Távoli eljáráshívás megbízható szolgáltatások távoli hívásával](service-fabric-reliable-services-communication-remoting.md)
* [Webes API az OWIN szolgáltatással a megbízható szolgáltatásokban](service-fabric-reliable-services-communication-webapi.md)
* [Kommunikáció biztonságossá tétele megbízható szolgáltatásokhoz](service-fabric-reliable-services-secure-communication-wcf.md)

