---
title: 'Biztonságos szolgáltatás-átirányító kommunikáció C-vel #'
description: Ismerje meg, hogyan biztosíthat szolgáltatás-kapcsolaton alapuló kommunikáció c# megbízható szolgáltatások, amelyek egy Azure Service Fabric-fürtön futnak.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609621"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Biztonságos szolgáltatás-átirányító kommunikáció C# szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság a kommunikáció egyik legfontosabb szempontja. A Megbízható szolgáltatások alkalmazáskeretrendszer néhány előre összeállított kommunikációs veremeket és eszközöket biztosít, amelyek a biztonság növelése érdekében használhatók. Ez a cikk ismerteti, hogyan növelheti a biztonságot, ha a c# szolgáltatás ban szolgáltatás átirányító használata. Egy meglévő [példára](service-fabric-reliable-services-communication-remoting.md) épül, amely bemutatja, hogyan állítható be a C#-ban írt megbízható szolgáltatások kalkulációja. 

A szolgáltatás C# szolgáltatásokkal való használata során történő szolgáltatás-használat biztonságossá tétele érdekében kövesse az alábbi lépéseket:

1. Hozzon létre `IHelloWorldStateful`egy felületet, amely meghatározza azokat a módszereket, amelyek a szolgáltatás távoli eljáráshívásához rendelkezésre állnak. A szolgáltatás `FabricTransportServiceRemotingListener`a , a `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` névtérben deklarált szolgáltatást fogja használni. Ez egy `ICommunicationListener` olyan megvalósítás, amely átirányító képességeket biztosít.

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
2. Adja hozzá a figyelő beállításait és a biztonsági hitelesítő adatokat.

    Győződjön meg arról, hogy a szolgáltatáskommunikáció védelméhez használni kívánt tanúsítvány telepítve van a fürt összes csomópontjára. 
    
    > [!NOTE]
    > Linux-csomópontokon a tanúsítványnak PEM-formátumú fájlként kell lennie a */var/lib/sfcerts* könyvtárban. További információ: [Az X.509 tanúsítványok helye és formátuma Linux csomópontokon](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    A figyelőbeállításokat és a biztonsági hitelesítő adatokat kétféleképpen adhatmeg:

   1. Adja meg őket közvetlenül a szolgáltatási kódban:

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
   2. Adja meg őket egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md)használatával:

       Névvel `TransportSettings` ellátott szakasz hozzáadása a settings.xml fájlhoz.

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

       Ebben az esetben `CreateServiceReplicaListeners` a módszer így fog kinézni:

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

        Ha hozzáad `TransportSettings` egy szakaszt a settings.xml fájlhoz, `FabricTransportRemotingListenerSettings` alapértelmezés szerint az összes beállítást betölti ebből a szakaszból.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben `CreateServiceReplicaListeners` a módszer így fog kinézni:

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
3. Ha egy biztonságos szolgáltatás metódusait a feladathívási verem `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` használatával hívja meg, ahelyett, hogy az osztályt használná szolgáltatásproxy létrehozásához, használja a használatát. `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` Adja `FabricTransportRemotingSettings`be a `SecurityCredentials`.

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

    Ha az ügyfélkód egy szolgáltatás részeként fut, a settings.xml fájlból tölthető be. `FabricTransportRemotingSettings` Hozzon létre egy HelloWorldClientTransportSettings szakaszt, amely hasonló a szolgáltatáskódhoz, ahogy azt korábban mutatuk. Hajtsa végre a következő módosításokat az ügyfélkódon:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél nem egy szolgáltatás részeként fut, létrehozhat egy client_name.settings.xml fájlt ugyanazon a helyen, ahol a client_name.exe van. Ezután hozzon létre egy TransportSettings szakaszt a fájlban.

    A szolgáltatáshoz hasonlóan, ha `TransportSettings` az ügyfélsettings.xml/client_name.settings.xml `FabricTransportRemotingSettings` fájlban hozzáad egy szakaszt, alapértelmezés szerint az összes beállítást betölti ebből a szakaszból.

    Ebben az esetben a korábbi kód még tovább egyszerűsödik:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Következő lépésként olvassa el a [Web API-t az OWIN szolgáltatással a Reliable Services alkalmazásban.](service-fabric-reliable-services-communication-webapi.md)
