---
title: Biztonságos WCF-alapú szolgáltatáskommunikáció
description: Ismerje meg, hogyan biztonságos WCF-alapú kommunikáció megbízható szolgáltatások, amelyek futnak az Azure Service Fabric-fürtön.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ca5eafa4612503a13f80b7f238e4827979c0358b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614162"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Biztonságos WCF-alapú kommunikáció egy szolgáltatáshoz
A biztonság a kommunikáció egyik legfontosabb szempontja. A Megbízható szolgáltatások alkalmazáskeretrendszer néhány előre összeállított kommunikációs veremeket és eszközöket biztosít, amelyek a biztonság növelése érdekében használhatók. Ez a cikk arról szól, hogyan növelheti a biztonságot, ha szolgáltatás-átirányító használata.

Egy meglévő [példát](service-fabric-reliable-services-communication-wcf.md) használunk, amely bemutatja, hogyan állíthat be egy WCF-alapú kommunikációs verem a megbízható szolgáltatások. Ha wcF-alapú kommunikációs verem használata esetén szeretne biztonságossá tenni egy szolgáltatást, kövesse az alábbi lépéseket:

1. A szolgáltatás hoz létre a WCF kommunikációs figyelő`WcfCommunicationListener`( ) biztonságossá kell tenni. Ehhez módosítsa a `CreateServiceReplicaListeners` módszert.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Az ügyfélben `WcfCommunicationClient` az előző [példában](service-fabric-reliable-services-communication-wcf.md) létrehozott osztály változatlan marad. De felül kell bírálnia a `CreateClientAsync` `WcfCommunicationClientFactory`módszert:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    WcF kommunikációs ügyfél ( létrehozásához használható `SecureWcfCommunicationClientFactory` .`WcfCommunicationClient` Az ügyfél segítségével hívja meg a szolgáltatási metódusok.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

Következő lépésként olvassa el a [Web API-t az OWIN szolgáltatással a Reliable Services alkalmazásban.](service-fabric-reliable-services-communication-webapi.md)
