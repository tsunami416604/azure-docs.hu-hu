---
title: Biztonságos szolgáltatás távelérésének távoli kommunikációja Javával
description: Megtudhatja, hogyan védheti meg az Azure Service Fabric-fürtön futó Java-alapú megbízható szolgáltatások távelérési szolgáltatásának távoli kommunikációját.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609638"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Biztonságos szolgáltatás távelérésének kommunikációja Java-szolgáltatásokban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság a kommunikáció egyik legfontosabb aspektusa. A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. Ez a cikk azt ismerteti, hogyan javíthatja a biztonságot, ha a szolgáltatásban a távoli eljáráshívás szolgáltatást Java-szolgáltatásban használja. Egy meglévő [példára](service-fabric-reliable-services-communication-remoting-java.md) épül, amely elmagyarázza, hogyan állíthatja be a távelérést a Java-ban írt megbízható szolgáltatásokhoz. 

Az alábbi lépéseket követve biztonságossá teheti a szolgáltatást, ha a távelérési szolgáltatást a Java-szolgáltatásokkal együtt használja:

1. Hozzon létre egy felületet, `HelloWorldStateless`, amely meghatározza azokat a metódusokat, amelyek elérhetők lesznek a szolgáltatás távoli eljáráshívási hívásához. A szolgáltatás `FabricTransportServiceRemotingListener`fogja használni, amely a `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` csomagban van deklarálva. Ez egy `CommunicationListener` implementáció, amely távelérési funkciókat biztosít.

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
2. Figyelő-beállítások és biztonsági hitelesítő adatok hozzáadása.

    Győződjön meg arról, hogy a szolgáltatás kommunikációjának biztonságossá tételéhez használni kívánt tanúsítvány telepítve van a fürt összes csomópontján. A Linux rendszeren futó szolgáltatások esetében a tanúsítványnak PEM-formmatted-fájlként kell elérhetőnek lennie; egy `.pem` fájl, amely tartalmazza a tanúsítványt és a titkos kulcsot tartalmazó `.crt`-fájlt, valamint egy `.key` fájlt, amely tartalmazza a tanúsítványt. További információért lásd: [X. 509 tanúsítványok helye és formátuma Linux-csomópontokon](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Kétféle módon biztosíthat figyelő-beállításokat és biztonsági hitelesítő adatokat:

   1. Adja meg őket egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md)használatával:

       Vegyen fel egy nevesített `TransportSettings` szakaszt a Settings. xml fájlban.

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

       Ebben az esetben a `createServiceInstanceListeners` metódus a következőhöz hasonlóan fog kinézni:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Ha előtag nélkül ad hozzá `TransportSettings` szakaszt a Settings. xml fájlban `FabricTransportListenerSettings`, akkor a rendszer alapértelmezés szerint betölti a szakasz összes beállítását.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben a `CreateServiceInstanceListeners` metódus a következőhöz hasonlóan fog kinézni:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Ha metódusokat hív meg egy biztonságos szolgáltatásban a távelérési verem használatával, ahelyett, hogy a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` osztályt használja a szolgáltatás-proxy létrehozásához, használja a `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Ha az ügyfél kódja egy szolgáltatás részeként fut, akkor a Settings. XML fájlból `FabricTransportSettings` tölthető be. Hozzon létre egy olyan TransportSettings szakaszt, amely hasonló a szolgáltatás kódjához, ahogy az a korábban látható. Hajtsa végre a következő módosításokat az ügyfél kódjában:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
