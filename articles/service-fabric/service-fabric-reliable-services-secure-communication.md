---
title: Szolgáltatás távelérésének lehetővé tétele folytatott biztonságos C# az Azure Service Fabricben |} A Microsoft Docs
description: Ismerje meg, hogyan teheti biztonságossá a szolgáltatás távelérésének lehetővé tétele a-alapú kommunikációt C# megbízható Azure Service Fabric-fürtben futó szolgáltatásokat.
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
ms.author: suchiagicha
ms.openlocfilehash: b6d4a44a53ba553ab4fd514c81867156192b69f5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662533"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>A szolgáltatás távelérésének lehetővé tétele kommunikáció biztonságossá tételéhez egy C# szolgáltatás
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

Biztonsági az egyik legfontosabb szempontja kommunikáció. A Reliable Services alkalmazási keretrendszer tartalmaz néhány előre létrehozott kommunikációs implementációt, eszközök, amelyek a biztonság növelése érdekében használhatja. Ez a cikk bemutatja, miként szolgáltatás távelérésének lehetővé tétele a használatakor a biztonság növelése érdekében egy C# szolgáltatás. Egy meglévő épül [példa](service-fabric-reliable-services-communication-remoting.md) , amely azt ismerteti, hogyan állítsa be a távoli eljáráshívás reliable Services-ben írt C#. 

Egy szolgáltatás használatakor a szolgáltatás távelérésének lehetővé tétele biztonságossá C# szolgáltatások, kövesse az alábbi lépéseket:

1. Hozzon létre egy adaptert `IHelloWorldStateful`, amely meghatározza, hogy a módszereket, a távoli eljáráshívás a szolgáltatás számára elérhetők. A szolgáltatás által használt `FabricTransportServiceRemotingListener`, amely van deklarálva a `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` névtér. Ez egy `ICommunicationListener` megvalósítása, amely távoli eljáráshívás képességeket biztosít.

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
2. Adja hozzá a figyelő beállításai és biztonsági hitelesítő adatokat.

    Győződjön meg arról, hogy a fürt összes csomópontján telepítve van a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítványt. 
    
    > [!NOTE]
    > A Linux-csomópontokat, a tanúsítványt a PEM-formátumú fájlként jelen kell lennie a */var/lib/sfcerts* könyvtár. További tudnivalókért lásd: [helyét és a Linux-csomópontokat az X.509-tanúsítványokat formátumát](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Két módon, hogy megadhat figyelő beállításai és biztonsági hitelesítő adatokat:

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
   2. Adja meg azokat a egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md):

       Adjon hozzá egy elnevezett `TransportSettings` szakaszt az settings.xml fájlban.

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

        Ha hozzáad egy `TransportSettings` az settings.xml fájl `FabricTransportRemotingListenerSettings ` betölti alapértelmezés szerint ez a szakasz az összes beállítás.

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
3. Meghívásakor módszer a biztonságos service használata helyett a távoli eljáráshívás stack használatával a `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztály egy szolgáltatási proxy létrehozásához, `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Adja át `FabricTransportRemotingSettings`, amely tartalmaz `SecurityCredentials`.

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

    Ha az Ügyfélkód egy szolgáltatás részeként fut, akkor betöltheti `FabricTransportRemotingSettings` settings.xml fájlból. Hozzon létre egy HelloWorldClientTransportSettings szakaszt, amely hasonlít a szolgáltatás kódot, ahogy ezt korábban. Az Ügyfélkód hajtsa végre a következő módosításokat:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél nem fut a szolgáltatás részeként, létrehozhat egy client_name.settings.xml fájlt ugyanazon a helyen, ahol a client_name.exe van. Ezután hozzon létre egy TransportSettings szakaszban, az adott fájlban.

    Hasonló a szolgáltatásba, ha hozzáad egy `TransportSettings` ügyfél settings.xml/client_name.settings.xml, szakaszában `FabricTransportRemotingSettings` ebben a szakaszban lévő összes beállítás alapértelmezés szerint tölti be.

    Ebben az esetben a korábbi kódot még tovább egyszerűsített:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


A következő lépésben, olvassa el a [webes API-hoz a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md).
