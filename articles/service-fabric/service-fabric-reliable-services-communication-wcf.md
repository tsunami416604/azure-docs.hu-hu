---
title: A Reliable Services – WCF kommunikációs verem |} A Microsoft Docs
description: A Service Fabric beépített WCF kommunikációs verem ügyfél szolgáltatás WCF kommunikáció a Reliable Services biztosítja.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670576"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Reliable Services WCF-alapú kommunikációs verem
A Reliable Services-keretrendszer lehetővé teszi, hogy a szolgáltatás szerzők válasszon a kommunikációs verem, amely a szolgáltatásukhoz használni kívánja. Ezek a kommunikációs verem az általuk választott keresztül is csatlakoztathatja a **ICommunicationListener** által visszaadott a [CreateServiceReplicaListeners, CreateServiceInstanceListeners vagy](service-fabric-reliable-services-communication.md) módszereket. A keretrendszert biztosít a a Windows Communication Foundation (WCF) szolgáltatást szeretné használni a WCF-alapú kommunikáció szerzőknek-alapú kommunikációs verem megvalósítását.

## <a name="wcf-communication-listener"></a>WCF-kommunikáció figyelője
A WCF-specifikus megvalósítását **ICommunicationListener** biztosítják a **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** osztály.

Közpénzek, ne a szolgáltatási szerződés típusú van például: `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

A szolgáltatás az alábbi módon is létrehozunk egy WCF-kommunikáció figyelője.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>A WCF-kommunikációs verem ügyfelek írása
Kommunikáció a szolgáltatásokkal a WCF-ügyfelek írásra, a keretrendszert biztosít **WcfClientCommunicationFactory**, azaz a WCF-specifikus megvalósítását [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Elérhető a WCF-kommunikációs csatornát a **WcfCommunicationClient** hozta létre a **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Ügyfélkód használhatja a **WcfCommunicationClientFactory** együtt a **WcfCommunicationClient** melyik valósítja meg **ServicePartitionClient** meghatározni a végpont szolgáltatást, és a szolgáltatással folytatott kommunikációhoz.

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
> Az alapértelmezett ServicePartitionResolver azt feltételezi, hogy az ügyfél a fürtön, a szolgáltatás fut-e. Ha, hogy nem sikerül, ServicePartitionResolver objektum létrehozása, és adja át a fürt kapcsolati végpontok.
> 
> 

## <a name="next-steps"></a>További lépések
* [A Reliable Services-táveléréssel a távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md)
* [Webes API-k az OWIN aktorai Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Kommunikáció a Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

