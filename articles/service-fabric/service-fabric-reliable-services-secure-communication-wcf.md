---
title: Biztonságos WCF-alapú szolgáltatás-kommunikáció
description: Ismerje meg, hogyan védheti meg a WCF-alapú kommunikációt az Azure Service Fabric-fürtön futó megbízható szolgáltatások esetében.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ca5eafa4612503a13f80b7f238e4827979c0358b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75614162"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Biztonságos WCF-alapú kommunikáció egy szolgáltatáshoz
A biztonság a kommunikáció egyik legfontosabb aspektusa. A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. Ez a cikk arról beszél, hogyan javíthatja a biztonságot a szolgáltatás távelérésének használatakor.

Egy meglévő [példát](service-fabric-reliable-services-communication-wcf.md) használunk, amely elmagyarázza, hogyan állíthat be egy WCF-alapú kommunikációs veremet a megbízható szolgáltatásokhoz. Ha WCF-alapú kommunikációs verem használatával kívánja biztonságossá tenni a szolgáltatást, kövesse az alábbi lépéseket:

1. A szolgáltatáshoz segítenie kell a létrehozott WCF kommunikációs figyelő ( `WcfCommunicationListener` ) védelmét. Ehhez módosítsa a `CreateServiceReplicaListeners` metódust.

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
2. Az ügyfélben az `WcfCommunicationClient` előző [példában](service-fabric-reliable-services-communication-wcf.md) létrehozott osztály változatlan marad. Azonban felül kell bírálni a `CreateClientAsync` metódust `WcfCommunicationClientFactory` :

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

    A használatával `SecureWcfCommunicationClientFactory` WCF kommunikációs ügyfelet () hozhat létre `WcfCommunicationClient` . Használja az ügyfelet a szolgáltatási módszerek meghívására.

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

A következő lépésként olvassa el a [webes API-t a OWIN Reliable Services-ben](service-fabric-reliable-services-communication-webapi.md)című témakörben.
