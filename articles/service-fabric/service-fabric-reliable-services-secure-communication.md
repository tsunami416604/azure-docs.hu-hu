---
title: A C# az Azure Service Fabric szolgáltatás távoli eljáráshívási kommunikáció |} Microsoft Docs
description: Ismerje meg a C# megbízható futó szolgáltatások az Azure Service Fabric-fürt szolgáltatások alapú távelérés közötti kommunikáció biztonságossá tétele.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: be5dab7b9714f13a4bd30e6ab33a5a0e2016212d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020019"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Biztonságos szolgáltatás távoli eljáráshívási kommunikáció egy C# szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság az egyik legfontosabb szempontja a kommunikáció. A Reliable Services alkalmazás-keretrendszer tartalmaz néhány előre elkészített kommunikációs verem és eszközöket, amelyek a biztonság növelése érdekében használhatja. A cikkből megtudhatja, hogyan lehet fokozni biztonságát, a C# szolgáltatás használatakor a távelérési szolgáltatás. Egy olyan buildekről nyújtanak [példa](service-fabric-reliable-services-communication-remoting.md) , amely ismerteti a C# nyelven írt megbízható szolgáltatások távoli eljáráshívást beállítani. 

Amikor C#-szolgáltatásokkal, használja a szolgáltatás távoli eljáráshívási szolgáltatás biztonságossá kövesse az alábbi lépéseket:

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

    Győződjön meg arról, hogy a fürt összes csomópontján telepítve van a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítványt. 
    
    > [!NOTE]
    > Linux-csomópont, a tanúsítványt a PEM-formátumú fájlként jelen kell lennie a */var/lib/sfcerts* könyvtár. További tudnivalókért lásd: [helyét és a Linux csomópontján X.509-tanúsítvány formátuma](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Két módon is megadható figyelő beállításai és a hitelesítő adatokat:

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

       Hozzáadása egy elnevezett `TransportSettings` szakasz a settings.xml fájlban.

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


A következő lépésben, olvassa el a [Web API-t a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md).
