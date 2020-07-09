---
title: Java Message Service-(JMS-) alkalmazások migrálása a ActiveMQ-ből a Azure Service Busba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíthet át olyan meglévő JMS-alkalmazásokat, amelyek együttműködnek az Active MQ-mel a Azure Service Bus való interakcióhoz.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122340"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Meglévő Java Message Service (JMS) 2,0-alkalmazások migrálása aktív MQ-ről Azure Service Bus

A Azure Service Bus támogatja a Java/J2EE és a Spring munkaterheléseket, amelyek a Java Message Service (JMS) 2,0 API-t használják a speciális Message Queuing protokoll (AMQP) protokollon keresztül.

Ez az útmutató ismerteti, hogy mit kell tudnia, ha módosítani kíván egy meglévő Java Message Service-(JMS-) 2,0-alkalmazást, amely együttműködik a JMS-közvetítővel (különösen az Apache ActiveMQ vagy az Amazon MQ-val) a Azure Service Bus való interakcióhoz.

## <a name="before-you-start"></a>Előkészületek

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus és Apache ActiveMQ közötti különbségek

A Azure Service Bus és az Apache ActiveMQ mind a JMS-szolgáltatóként működő üzenetsor-ügynökök, amelyek üzenetek küldésére és fogadására szolgálnak. Mindkettő lehetővé teszi, hogy a pont-pont típusú szemantika a **várólistákkal** és a közzétételi és előfizetési szemantikai **témákkal** és **előfizetésekkel**együtt történjen. 

Még így is vannak különbségek a kettőben.

| Kategória | Aktív MQ | Azure Service Bus |
| --- | --- | --- |
| Alkalmazás-rétegek | Fürtözött monolit | Kétszintű <br> (Átjáró + háttér) |
| Protokolltámogatás | <ul> <li>AMQP</li> <li> Trapp </li> <li> OpenWire </li> </ul> | AMQP |
| Kiépítési mód | <ul> <li> IaaS (helyszíni) </li> <li> Amazon MQ (felügyelt Péter) </li> | Felügyelt Péter |
| Üzenet mérete | Ügyfél által konfigurálható | 1 MB (prémium szint) |
| Magas rendelkezésre állás | Ügyfél által felügyelt | Platform által felügyelt |
| Vészhelyreállítás | Ügyfél által felügyelt | Platform által felügyelt | 

### <a name="current-supported-and-unsupported-features"></a>Jelenleg támogatott és nem támogatott funkciók

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Figyelmeztetések

A Azure Service Bus kétrétegű természete különböző üzletmenet-folytonossági képességeket biztosít (magas rendelkezésre állás és vész-helyreállítás). A JMS-szolgáltatások használata során azonban néhány szempontot figyelembe kell venni.

#### <a name="service-upgrades"></a>Szolgáltatások frissítése

A Service Bus frissítései és újraindítása esetén a rendszer törli az ideiglenes várólistákat vagy témaköröket.

Ha az alkalmazás érzékeny az ideiglenes várólisták vagy témakörök adatvesztésére, akkor azt javasoljuk, hogy **ne** használjon ideiglenes várólistákat vagy témaköröket, és ne használjon tartós várólistákat, témaköröket és előfizetéseket.

#### <a name="data-migration"></a>Adatok migrálása

Az ügyfélalkalmazások áttelepítésének/módosításának részeként a Azure Service Bus-mel folytatott kommunikáció során a ActiveMQ-ben tárolt adatai nem lesznek áttelepítve a Service Busra.

Egyéni alkalmazásra lehet szükség a ActiveMQ-várólisták,-témakörök és-előfizetések kiürítéséhez és az üzenetek visszajátszásához Service Bus "Queues, témakörök és előfizetések.

#### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Az Azure ActiveDirectory által támogatott szerepköralapú Access Control (RBAC) a Azure Service Bus előnyben részesített hitelesítési mechanizmusa.

Mivel azonban a RBAC jelenleg nem támogatott, mert az Apache csontos JMS nem támogatja a jogcím-alapú hitelesítés támogatását.

Egyelőre a hitelesítés csak SAS-kulcsokkal támogatott.

## <a name="pre-migration"></a>A migrálást megelőző folyamatok

### <a name="version-check"></a>Verzió-ellenőrzési

Alább láthatók a JMS-alkalmazások és a támogatott verziók írása közben felhasznált összetevők. 

| Összetevők | Verzió |
|---|---|
| Java Message Service-(JMS-) API | 1,1 vagy újabb |
| AMQP protokoll | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Győződjön meg arról, hogy a AMQP-portok nyitva vannak

A Azure Service Bus támogatja a AMQP protokollon keresztüli kommunikációt. Erre a célra a 5671 (AMQP) és a 443 (TCP) porton keresztüli kommunikációt engedélyezni kell. Attól függően, hogy hol találhatók az ügyfélalkalmazások, szükség lehet egy támogatási jegyre, amely lehetővé teszi az ilyen portokon keresztüli kommunikációt.

> [!IMPORTANT]
> A Azure Service Bus **csak** a AMQP 1,0 protokollt támogatja.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Vállalati konfigurációk beállítása (VNET, tűzfal, magánhálózati végpont stb.)

A Azure Service Bus különböző vállalati biztonsági és magas rendelkezésre állású funkciókat tesz lehetővé. Ha többet szeretne megtudni róluk, kövesse az alábbi dokumentációs hivatkozásokat.

  * [Virtual Network szolgáltatási végpontok](service-bus-service-endpoints.md)
  * [Tűzfal](service-bus-ip-filtering.md)
  * [Szolgáltatás oldali titkosítás az ügyfél által felügyelt kulccsal (BYOK)](configure-customer-managed-key.md)
  * [Privát végpontok](private-link-service.md)
  * [Hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Figyelés, riasztások és nyomkövetés

A metrikák az egyes Service Bus Azure Monitor névterekre lesznek közzétéve, és a rendszer kihasználható a névtérhez lefoglalt erőforrások riasztására és dinamikus skálázására.

További információk a különböző mérőszámokról és a riasztások beállításáról a [Azure Monitor Service Bus metrikái](service-bus-metrics-azure-monitor.md)című témakörben.

További információ [: ügyféloldali nyomkövetés az adatműveletekhez](service-bus-end-to-end-tracing.md) és [működési/diagnosztikai naplózás felügyeleti műveletekhez](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Metrikák – NewRelic

Az alábbiakban egy olyan hasznos útmutató látható, amely alapján a ActiveMQ-leképezések mérőszámait Azure Service Bus. Az alábbi hivatkozásokat a NewRelic találja.

  * [ActiveMQ/Amazon MQ NewRelic metrikák](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus NewRelic metrikák](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Jelenleg a NewRelic nem rendelkezik zökkenőmentes integrációval közvetlenül a ActiveMQ, de az Amazon MQ-hoz elérhető metrikák is rendelkezésre állnak.
> Mivel az Amazon MQ a ActiveMQ-ből származik, az alábbi útmutató a AmazonMQ NewRelic metrikáit képezi le a Azure Service Bus.
>

|Metrika csoportosítása| AmazonMQ/aktív MQ-metrika | Azure Service Bus metrika |
|------------|---------------------------|--------------------------|
|Bróker|`CpuUtilization`|`CPUXNS`|
|Bróker|`MemoryUsage`|`WSXNS`|
|Bróker|`CurrentConnectionsCount`|`activeConnections`|
|Bróker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Bróker|`InactiveDurableTopicSubscribersCount`|Előfizetési mérőszámok kihasználása|
|Bróker|`TotalMessageCount`|A várólista/témakör/előfizetés szintjének kihasználása`activeMessages`|
|Üzenetsor/témakör|`EnqueueCount`|`incomingMessages`|
|Üzenetsor/témakör|`DequeueCount`|`outgoingMessages`|
|Várólista|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrálás

Ha át szeretné telepíteni a meglévő JMS 2,0-alkalmazást a Azure Service Bus való interakcióra, el kell végeznie az alábbi lépéseket.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>A topológia exportálása a ActiveMQ és az entitások létrehozása Azure Service Busban (nem kötelező)

Annak biztosítása érdekében, hogy az ügyfélalkalmazások zökkenőmentesen csatlakozhassanak Azure Service Bushoz, a topológiához, amely tartalmazza a várólistákat, témákat és előfizetéseket, át kell telepíteni az **Apache ActiveMQ** -ből a **Azure Service Busba**.

> [!NOTE]
> A Java Message Service-(JMS-) alkalmazások esetében a várólisták, témakörök és előfizetések létrehozása egy futásidejű művelet. A legtöbb Java Message Service-(JMS-) szolgáltató (Message Broker) lehetőséget nyújt a *várólisták*, *témakörök* és *előfizetések* létrehozására futásidőben.
>
> Ezért a fenti lépés nem kötelező.
>
> Annak biztosítása érdekében, hogy az alkalmazás jogosult legyen a topológia létrehozásához futásidőben, győződjön meg arról, hogy a rendszer a ***sas "Manage"*** engedélyekkel rendelkező kapcsolódási karakterláncot használja.

Cél 
  * A [ActiveMQ parancssori eszközeinek](https://activemq.apache.org/activemq-command-line-tools-reference) kihasználása a topológia exportálásához
  * Azonos topológia újbóli létrehozása [Azure Resource Manager sablon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) használatával
  * Hajtsa végre a Azure Resource Manager sablont.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>A Maven-függőség importálása Service Bus JMS megvalósításához

A Azure Service Busekkel való zökkenőmentes kapcsolódás biztosításához az ***Azure-servicebus-JMS*** csomagot hozzá kell adni függőségként a Maven- `pom.xml` fájlhoz az alábbiak szerint.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Az alkalmazáskiszolgáló konfigurációjának módosításai

Ez a rész az Active MQ-hoz csatlakozó ügyfélalkalmazások üzemeltetésére szolgáló alkalmazáskiszolgáló Egyéni eleme.

#### <a name="tomcat"></a>Tomcat

Itt a fájlban látható módon kezdjük az aktív MQ-re jellemző konfigurációval `/META-INF/context.xml` .

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

amely az alábbiak szerint módosítható, hogy Azure Service Bus

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Rugós alkalmazások

##### <a name="update-applicationproperties-file"></a>`application.properties`Fájl frissítése

Ha Spring boot-alkalmazást használ a ActiveMQ való kapcsolódáshoz.

Itt az a cél, hogy ***eltávolítsa*** a ActiveMQ-specifikus tulajdonságokat a `application.properties` fájlból.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Ezután ***adja hozzá*** a Service Bus-specifikus tulajdonságokat a `application.properties` fájlhoz.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>A ActiveMQConnectionFactory cseréje a ServiceBusJmsConnectionFactory

A következő lépés a ActiveMQConnectionFactory példányának cseréje a ServiceBusJmsConnectionFactory.

> [!NOTE] 
> A kód tényleges módosításai az alkalmazásra és a függőségek felügyeletére vonatkoznak, de az alábbi minta útmutatást ***nyújt a*** módosításának módjáról.
>

Korábban előfordulhat, hogy az alábbi módon hozza létre a ActiveMQConnectionFactory egy objektumát.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Ez a ServiceBusJmsConnectionFactory objektum példányának példányára módosul.

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>A migrálást követő folyamatok

Most, hogy módosította az alkalmazást, hogy megkezdje az üzenetek küldését és fogadását Azure Service Busről, ellenőrizze, hogy a várt módon működik-e. Ha elkészült, folytathatja az alkalmazás-verem pontosítását és korszerűsítését.

## <a name="next-steps"></a>Következő lépések

Használja ki a [Spring boot starter Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) a zökkenőmentes integrációt Azure Service Busokkal.

Ha többet szeretne megtudni a Service Bus üzenetkezelésről és a Java-üzenetküldési szolgáltatásról (JMS), tekintse meg a következő témaköröket:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
