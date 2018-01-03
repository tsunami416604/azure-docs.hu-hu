---
title: "Azure Service Fabric-szolgáltatások biztonságos kommunikáció érdekében |} Microsoft Docs"
description: "Azure Service Fabric-fürtben lévő futó megbízható szolgáltatások biztonságos kommunikáció érdekében áttekintése."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 0804e43c3f1bb13bea92ebd661ca52c799ff2332
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Azure Service Fabric-szolgáltatások biztonságos kommunikáció érdekében
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság az egyik legfontosabb szempontja a kommunikáció. A Reliable Services alkalmazás-keretrendszer tartalmaz néhány előre elkészített kommunikációs verem és eszközöket, amelyek a biztonság növelése érdekében használhatja. Ez a cikk beszél biztonsági javítására, ha a szolgáltatás távoli eljáráshívás és a Windows Communication Foundation (WCF) kommunikációs verem használata.

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Számítógépek biztonságossá tétele a szolgáltatás használatakor a távelérési szolgáltatás
Egy meglévő használjuk [példa](service-fabric-reliable-services-communication-remoting.md) , amely ismerteti, hogyan megbízható szolgáltatások távoli eljáráshívást beállítani. Számítógépek biztonságossá tétele a szolgáltatás használatakor a távelérési szolgáltatás, kövesse az alábbi lépéseket:

1. Illesztőfelület, hozzon létre `IHelloWorldStateful`, amely meghatározza, hogy a módszereket, amelyek számára a szolgáltatás a távoli eljáráshívás használható. A szolgáltatás által használt `FabricTransportServiceRemotingListener`, amely deklarálva van a `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` névtér. Ez egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Adja hozzá a figyelő beállításai és a hitelesítő adatokat.

    Győződjön meg arról, hogy a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítvány telepítve van-e a fürt összes csomópontján. Két módon is megadható figyelő beállításai és a hitelesítő adatokat:

   1. Adja meg azokat közvetlenül a szolgáltatás-kódban:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Adja meg azokat a egy [a konfigurációs csomag](service-fabric-application-and-service-manifests.md):

       Adja hozzá a `TransportSettings` szakasz a settings.xml fájlban.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       Ebben az esetben a `CreateServiceReplicaListeners` módszert fog kinézni:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Ha ad hozzá egy `TransportSettings` szakasz a settings.xml fájlban `FabricTransportRemotingListenerSettings ` betölti alapértelmezés szerint ez a szakasz az összes beállítás.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben a `CreateServiceReplicaListeners` módszert fog kinézni:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Hívható módszerek biztonságos szolgáltatás használata helyett a távoli eljáráshívás verem használatával a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` a szolgáltatásproxy létrehozására, használja az osztály `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Adjon át `FabricTransportRemotingSettings`, amely tartalmazza `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az Ügyfélkód egy szolgáltatás részeként fut, betöltheti `FabricTransportRemotingSettings` a settings.xml fájlból. Hozzon létre egy HelloWorldClientTransportSettings szakaszt, amelyek hasonlóak a szolgáltatáskód hibáit, amint azt korábban. A következő módosításokat az ügyfél kód:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél nem fut a szolgáltatás részeként, ugyanazon a helyen, ahol a client_name.exe az client_name.settings.xml fájlt is létrehozhat. Ezután hozzon létre egy TransportSettings szakaszt, az adott fájlban.

    Hasonló a szolgáltatáshoz való hozzáadásakor a `TransportSettings` ügyfél settings.xml/client_name.settings.xml szakasz `FabricTransportRemotingSettings` tölt be az összes beállítás alapértelmezés szerint ebben a szakaszban ismertetett.

    Ebben az esetben a korábbi kód még tovább egyszerűsített:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Számítógépek biztonságossá tétele a szolgáltatás egy WCF-alapú kommunikációs verem használatakor
Egy meglévő használjuk [példa](service-fabric-reliable-services-communication-wcf.md) , amely elmagyarázza, hogyan állíthat be egy WCF-alapú kommunikációs verem megbízható szolgáltatásokhoz. Számítógépek biztonságossá tétele a szolgáltatás egy WCF-alapú kommunikációs verem használatakor, kövesse az alábbi lépéseket:

1. A szolgáltatás kell biztonságossá tétele a WCF-kommunikáció figyelő (`WcfCommunicationListener`) az Ön által létrehozott. Ehhez az szükséges, módosítsa a `CreateServiceReplicaListeners` metódust.

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
2. Az ügyfél a `WcfCommunicationClient` osztály, amely jött létre az előző [példa](service-fabric-reliable-services-communication-wcf.md) változatlan marad. Felül kell bírálni, de a `CreateClientAsync` metódusában `WcfCommunicationClientFactory`:

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

    Használjon `SecureWcfCommunicationClientFactory` WCF kommunikációs ügyfelet létrehozni a (`WcfCommunicationClient`). Az ügyfél segítségével szolgáltatás metódusok.

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

## <a name="next-steps"></a>További lépések
* [Webes API-t a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md)
