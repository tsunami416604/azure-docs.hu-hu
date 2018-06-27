---
title: Szolgáltatás távoli eljáráshívási Java az Azure Service Fabric folytatott kommunikáció biztosítása |} Microsoft Docs
description: Útmutató a Java megbízható szolgáltatások az Azure Service Fabric-fürt a futó szolgáltatás távoli eljáráshívás-alapú kommunikáció biztonságossá tételére.
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
ms.openlocfilehash: cbefb3ede6d0d1fe21065b49c84db9f4db5dd39c
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020813"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Biztonságos szolgáltatás távoli eljáráshívási kommunikáció egy Java szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság az egyik legfontosabb szempontja a kommunikáció. A Reliable Services alkalmazás-keretrendszer tartalmaz néhány előre elkészített kommunikációs verem és eszközöket, amelyek a biztonság növelése érdekében használhatja. Ez a cikk ismerteti a Java-szolgáltatás használatakor a távelérési szolgáltatás, a biztonság növelése érdekében. Egy olyan buildekről nyújtanak [példa](service-fabric-reliable-services-communication-remoting-java.md) , amely ismerteti a Java nyelven írt megbízható szolgáltatások távoli eljáráshívást beállítani. 

Számítógépek biztonságossá tétele a service szolgáltatás távoli eljáráshívási Java szolgáltatások használatakor, kövesse az alábbi lépéseket:

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

    Győződjön meg arról, hogy a fürt összes csomópontján telepítve van a szolgáltatások közötti kommunikáció biztonságossá tételéhez használni kívánt tanúsítványt. Linux rendszeren futó szolgáltatásokhoz a tanúsítványt a PEM-formmatted fájlként; elérhetőnek kell lennie vagy egy `.pem` a tanúsítvány és titkos kulcsot tartalmazó fájlt, vagy egy `.crt` fájl, a tanúsítványt és egy `.key` a titkos kulcsot tartalmazó fájlt. További tudnivalókért lásd: [helyét és a Linux csomópontján X.509-tanúsítvány formátuma](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Két módon is megadható figyelő beállításai és a hitelesítő adatokat:

   1. Adja meg azokat a egy [a konfigurációs csomag](service-fabric-application-and-service-manifests.md):

       Hozzáadása egy elnevezett `TransportSettings` szakasz a settings.xml fájlban.

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
