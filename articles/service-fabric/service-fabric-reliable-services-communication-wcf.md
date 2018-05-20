---
title: Megbízható szolgáltatások WCF kommunikációs verem |} Microsoft Docs
description: A beépített WCF kommunikációs verem a Service Fabric-ügyfélszolgáltatás WCF kommunikációt a Reliable Services biztosít.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: f5ca579b446e5d3608d53cea73fa9392cd00db06
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-alapú kommunikációs verem a Reliable Services
A Reliable Services keretrendszer lehetővé teszi, hogy a szolgáltatás szerzők kiválasztása a kommunikációs verem, amelyeket be szeretne használni a szolgáltatáshoz. Azok a kommunikációs verem az általuk választott keresztül is csatlakoztathatja a **ICommunicationListener** által visszaadott a [CreateServiceReplicaListeners vagy CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) módszerek. A keretrendszer a kommunikációs verem, a Windows Communication Foundation (WCF) szolgáltatást szeretné használni a WCF-alapú kommunikációt szerzőknek alapú megvalósítását.

## <a name="wcf-communication-listener"></a>WCF kommunikációs figyelő
A WCF-specifikus végrehajtásának **ICommunicationListener** biztosítja a **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** osztály.

Közpénzek ne mondja ki a szolgáltatási szerződés típusú tudunk. `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

A szolgáltatás a következő módon létrehozhatunk olyan WCF kommunikációs figyelő.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>A WCF kommunikációs verem ügyfelek írása
Ügyfelek szolgáltatásokkal kommunikálni a WCF írásra, a keretrendszer által biztosított **WcfClientCommunicationFactory**, vagyis a WCF-specifikus végrehajtásának [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

A WCF kommunikációs csatornát érhetők el a **WcfCommunicationClient** hozta létre a **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Ügyfélkód használhatja a **WcfCommunicationClientFactory** együtt a **WcfCommunicationClient** mely valósít meg **ServicePartitionClient** meghatározásához a végpont szolgáltatást, és a szolgáltatásra.

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
> Az alapértelmezett ServicePartitionResolver feltételezi, hogy az ügyfél fut a fürtön, amelyen a szolgáltatás. Ha ez nem ez a helyzet, ServicePartitionResolver objektum létrehozása, és a fürt kapcsolati végpontok adjon át.
> 
> 

## <a name="next-steps"></a>További lépések
* [A távoli eljáráshívás a Reliable Services távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md)
* [Webes API-t a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md)
* [A Reliable Services kommunikáció biztonságához](service-fabric-reliable-services-secure-communication.md)

