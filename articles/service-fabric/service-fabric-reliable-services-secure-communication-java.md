---
title: Biztonságos szolgáltatás-átirányító kommunikáció a Java-val
description: Ismerje meg, hogyan biztosíthat szolgáltatás-átfutási alapú kommunikáció java-alapú megbízható szolgáltatások, amelyek egy Azure Service Fabric-fürtön futnak.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609638"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Biztonságos szolgáltatás-átirányító kommunikáció Java szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság a kommunikáció egyik legfontosabb szempontja. A Megbízható szolgáltatások alkalmazáskeretrendszer néhány előre összeállított kommunikációs veremeket és eszközöket biztosít, amelyek a biztonság növelése érdekében használhatók. Ez a cikk ismerteti, hogyan növelheti a biztonságot, ha a szolgáltatás átirányító java szolgáltatás ban. Egy meglévő [példára](service-fabric-reliable-services-communication-remoting-java.md) épül, amely bemutatja, hogyan állíthat be a Java-ban írt megbízható szolgáltatások átirányító beállítását. 

A szolgáltatás Java-szolgáltatásokkal való használat közbeni szolgáltatáshasználatának biztonságossá tétele érdekében kövesse az alábbi lépéseket:

1. Hozzon létre `HelloWorldStateless`egy felületet, amely meghatározza azokat a módszereket, amelyek a szolgáltatás távoli eljáráshívásához rendelkezésre állnak. A szolgáltatás `FabricTransportServiceRemotingListener`a csomagban `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` deklarált szolgáltatást fogja használni. Ez egy `CommunicationListener` olyan megvalósítás, amely átirányító képességeket biztosít.

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
2. Adja hozzá a figyelő beállításait és a biztonsági hitelesítő adatokat.

    Győződjön meg arról, hogy a szolgáltatáskommunikáció védelméhez használni kívánt tanúsítvány telepítve van a fürt összes csomópontjára. A Linuxon futó szolgáltatások esetében a tanúsítványnak PEM-formmatted fájlként kell rendelkezésre állnia; vagy `.pem` egy fájlt, amely tartalmazza a `.crt` tanúsítványt és a `.key` személyes kulcsot, vagy egy fájlt, amely a tanúsítványt tartalmazza, és egy fájlt, amely tartalmazza a személyes kulcsot. További információ: [Az X.509 tanúsítványok helye és formátuma Linux csomópontokon](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    A figyelőbeállításokat és a biztonsági hitelesítő adatokat kétféleképpen adhatmeg:

   1. Adja meg őket egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md)használatával:

       Névvel `TransportSettings` ellátott szakasz hozzáadása a settings.xml fájlhoz.

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

       Ebben az esetben `createServiceInstanceListeners` a módszer így fog kinézni:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Ha előtag `TransportSettings` nélkül ad hozzá egy szakaszt `FabricTransportListenerSettings` a settings.xml fájlhoz, alapértelmezés szerint az összes beállítást betölti ebből a szakaszból.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben `CreateServiceInstanceListeners` a módszer így fog kinézni:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Ha egy biztonságos szolgáltatás metódusait a feladathívási verem `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` használatával hívja meg, ahelyett, hogy az osztályt használná szolgáltatásproxy létrehozásához, használja a használatát. `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`

    Ha az ügyfélkód egy szolgáltatás részeként fut, a settings.xml fájlból tölthető be. `FabricTransportSettings` Hozzon létre egy TransportSettings szakaszt, amely hasonlít a szolgáltatáskódhoz, ahogy azt korábban mutatod. Hajtsa végre a következő módosításokat az ügyfélkódon:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
