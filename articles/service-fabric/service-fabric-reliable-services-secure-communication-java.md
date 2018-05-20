---
title: Azure Service Fabric-szolgáltatások biztonságos kommunikáció érdekében |} Microsoft Docs
description: Azure Service Fabric-fürtben lévő futó megbízható szolgáltatások biztonságos kommunikáció érdekében áttekintése.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 624d9d358145fb8b41013d686821cb157693d3c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Azure Service Fabric-szolgáltatások biztonságos kommunikáció érdekében
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Számítógépek biztonságossá tétele a szolgáltatás használatakor a távelérési szolgáltatás
Fogjuk használni egy meglévő [példa](service-fabric-reliable-services-communication-remoting-java.md) , amely ismerteti, hogyan megbízható szolgáltatások távoli eljáráshívást beállítani. Számítógépek biztonságossá tétele a szolgáltatás használatakor a távelérési szolgáltatás, kövesse az alábbi lépéseket:

1. Illesztőfelület, hozzon létre `HelloWorldStateless`, amely meghatározza, hogy a módszereket, amelyek számára a szolgáltatás a távoli eljáráshívás használható. A szolgáltatás által használt `FabricTransportServiceRemotingListener`, amely deklarálva van a `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` csomag. Ez egy `CommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Adja hozzá a figyelő beállításai és a hitelesítő adatokat.

    Győződjön meg arról, hogy a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítvány telepítve van-e a fürt összes csomópontján. Két módon is megadható figyelő beállításai és a hitelesítő adatokat:

   1. Adja meg azokat a egy [a konfigurációs csomag](service-fabric-application-and-service-manifests.md):

       Adja hozzá a `TransportSettings` szakasz a settings.xml fájlban.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       Ebben az esetben a `createServiceInstanceListeners` módszert fog kinézni:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Ha ad hozzá egy `TransportSettings` bármely előtag nélkül a settings.xml fájlban szakasz `FabricTransportListenerSettings` betölti alapértelmezés szerint ez a szakasz az összes beállítás.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben a `CreateServiceInstanceListeners` módszert fog kinézni:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Hívható módszerek biztonságos szolgáltatás használata helyett a távoli eljáráshívás verem használatával a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` a szolgáltatásproxy létrehozására, használja az osztály `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Ha az Ügyfélkód egy szolgáltatás részeként fut, betöltheti `FabricTransportSettings` a settings.xml fájlból. Hozzon létre egy TransportSettings szakaszt, amelyek hasonlóak a szolgáltatáskód hibáit, amint azt korábban. A következő módosításokat az ügyfél kód:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
