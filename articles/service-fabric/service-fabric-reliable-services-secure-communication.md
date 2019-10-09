---
title: Biztonságos szolgáltatás C# – távelérési kommunikáció az Azure Service Fabricban | Microsoft Docs
description: Megtudhatja, hogyan védheti meg az Azure C# Service Fabric-fürtön futó megbízható szolgáltatások távelérési szolgáltatáson alapuló kommunikációját.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: c252ec31a64fa3a11973db7a8de0a440d8eed6f5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166559"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Biztonságos szolgáltatás távelérési kommunikációja C# egy szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság a kommunikáció egyik legfontosabb aspektusa. A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. Ez a cikk azt ismerteti, hogyan javíthatja a biztonságot, ha a szolgáltatásban C# a távoli eljáráshívás szolgáltatást használja. Egy meglévő [példára](service-fabric-reliable-services-communication-remoting.md) épül, amely elmagyarázza, hogyan állíthatja be a távelérést a szolgáltatásban írt megbízható szolgáltatások számára C#. 

Az alábbi lépéseket követve biztonságossá teheti a szolgáltatást, ha C# a távelérési szolgáltatást használja a szolgáltatásokkal:

1. Hozzon létre egy `IHelloWorldStateful` illesztőfelületet, amely meghatározza azokat a metódusokat, amelyek elérhetők lesznek a szolgáltatás távoli eljáráshívási hívásához. A szolgáltatás a `FabricTransportServiceRemotingListener` értéket fogja használni, amely a `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` névtérben van deklarálva. Ez egy `ICommunicationListener` implementáció, amely távelérési funkciókat biztosít.

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
2. Figyelő-beállítások és biztonsági hitelesítő adatok hozzáadása.

    Győződjön meg arról, hogy a szolgáltatás kommunikációjának biztonságossá tételéhez használni kívánt tanúsítvány telepítve van a fürt összes csomópontján. 
    
    > [!NOTE]
    > Linux-csomópontokon a tanúsítványnak PEM formátumú fájlként kell lennie a */var/lib/sfcerts* könyvtárban. További információért lásd: [X. 509 tanúsítványok helye és formátuma Linux-csomópontokon](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Kétféle módon biztosíthat figyelő-beállításokat és biztonsági hitelesítő adatokat:

   1. Adja meg azokat közvetlenül a szolgáltatás kódjában:

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

       Adja hozzá a named `TransportSettings` szakaszt a Settings. xml fájlban.

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

       Ebben az esetben a `CreateServiceReplicaListeners` metódus a következőhöz hasonlóan fog kinézni:

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

        Ha a Settings. xml fájlban `TransportSettings` szakaszt ad hozzá, akkor a (`FabricTransportRemotingListenerSettings`) alapértelmezés szerint a szakasz összes beállítását betölti.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben a `CreateServiceReplicaListeners` metódus a következőhöz hasonlóan fog kinézni:

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
3. Ha metódusokat hív meg egy biztonságos szolgáltatásban a távelérési verem használatával, ahelyett, hogy a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztályt használja a szolgáltatás-proxy létrehozásához, használja a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` értéket. A `FabricTransportRemotingSettings` értéket adja meg, amely a `SecurityCredentials` értéket tartalmazza.

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

    Ha az ügyfél kódja egy szolgáltatás részeként fut, akkor a Settings. XML fájlból betöltheti a `FabricTransportRemotingSettings` értéket. Hozzon létre egy olyan HelloWorldClientTransportSettings szakaszt, amely hasonló a szolgáltatás kódjához, ahogy az a korábban látható. Hajtsa végre a következő módosításokat az ügyfél kódjában:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél nem a szolgáltatás részeként fut, létrehozhat egy client_name. Settings. xml fájlt ugyanazon a helyen, ahol a client_name. exe fájl található. Ezután hozzon létre egy TransportSettings szakaszt a fájlban.

    A szolgáltatáshoz hasonlóan, ha hozzáad egy `TransportSettings` szakaszt az ügyfélbeállítások. XML/client_name. Settings. xml fájlban, a `FabricTransportRemotingSettings` alapértelmezés szerint betölti a szakasz összes beállítását.

    Ebben az esetben a korábbi kód még egyszerűbbé válik:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


A következő lépésként olvassa el a [webes API-t a OWIN Reliable Services-ben](service-fabric-reliable-services-communication-webapi.md)című témakörben.
