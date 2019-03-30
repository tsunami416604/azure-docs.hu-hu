---
title: Az Azure Service Fabric Java-szolgáltatás távelérésének lehetővé tétele kommunikáció biztonságossá tételéhez |} A Microsoft Docs
description: 'Útmutató: Java reliable Services, Azure Service Fabric-fürtben futó szolgáltatás távelérésének lehetővé tétele a-alapú kommunikáció biztonságossá tételére.'
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666996"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Szolgáltatás távelérésének lehetővé tétele kommunikáció egy Java service-ben
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

Biztonsági az egyik legfontosabb szempontja kommunikáció. A Reliable Services alkalmazási keretrendszer tartalmaz néhány előre létrehozott kommunikációs implementációt, eszközök, amelyek a biztonság növelése érdekében használhatja. Ez a cikk leírja, hogyan egy Java-szolgáltatás használatakor szolgáltatás távelérésének lehetővé tétele a biztonság növelése érdekében. Egy meglévő épül [példa](service-fabric-reliable-services-communication-remoting-java.md) , amely azt ismerteti, hogyan reliable Services Java nyelven írt távoli eljáráshívást beállítani. 

Szolgáltatás távelérésének lehetővé tétele a Java-szolgáltatások használatakor szolgáltatás védelme érdekében kövesse az alábbi lépéseket:

1. Hozzon létre egy adaptert `HelloWorldStateless`, amely meghatározza, hogy a módszereket, a távoli eljáráshívás a szolgáltatás számára elérhetők. A szolgáltatás által használt `FabricTransportServiceRemotingListener`, amely van deklarálva a `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` csomagot. Ez egy `CommunicationListener` megvalósítása, amely távoli eljáráshívás képességeket biztosít.

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
2. Adja hozzá a figyelő beállításai és biztonsági hitelesítő adatokat.

    Győződjön meg arról, hogy a fürt összes csomópontján telepítve van a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítványt. A linuxon futó szolgáltatások esetében a tanúsítványt a PEM-formmatted fájlként elérhetőnek kell lennie vagy egy `.pem` a tanúsítványt és titkos kulcsot tartalmazó fájl, vagy egy `.crt` fájlt, amely tartalmazza a tanúsítványt és a egy `.key` a titkos kulcsot tartalmazó fájlt. További tudnivalókért lásd: [helyét és a Linux-csomópontokat az X.509-tanúsítványokat formátumát](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Két módon, hogy megadhat figyelő beállításai és biztonsági hitelesítő adatokat:

   1. Adja meg azokat a egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md):

       Adjon hozzá egy elnevezett `TransportSettings` szakaszt az settings.xml fájlban.

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

        Ha hozzáad egy `TransportSettings` az összes előtag nélkül settings.xml fájl `FabricTransportListenerSettings` betölti alapértelmezés szerint ez a szakasz az összes beállítás.

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
3. Meghívásakor módszer a biztonságos service használata helyett a távoli eljáráshívás stack használatával a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` osztály egy szolgáltatási proxy létrehozásához, `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Ha az Ügyfélkód egy szolgáltatás részeként fut, akkor betöltheti `FabricTransportSettings` settings.xml fájlból. Hozzon létre egy TransportSettings szakaszt, amely hasonlít a szolgáltatás kódot, ahogy ezt korábban. Az Ügyfélkód hajtsa végre a következő módosításokat:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
