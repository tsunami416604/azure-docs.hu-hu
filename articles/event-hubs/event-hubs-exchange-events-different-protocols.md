---
title: Azure Event Hubs – Exchange-események különböző protokollok használatával
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók hogyan cserélhetnek eseményeket az Azure Event Hubs használatakor.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372216"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Különböző protokollokat használó felhasználók és gyártók közötti események cseréje: AMQP, Kafka és HTTPS
Az Azure Event Hubs három protokollt támogat a fogyasztók és a gyártók számára: AMQP, Kafka és HTTPS. Mindegyik protokoll nak megvan a maga módja egy üzenet megjelenítésére, így természetesen felmerül a következő kérdés: ha egy alkalmazás eseményeket küld egy Eseményközpontba egy protokollal, és más protokollal használja fel őket, mit jelentenek a különböző részei és értékei a rendezvénytermében? Ez a cikk ismerteti a gyártó és a fogyasztó ajánlott gyakorlatát annak biztosítására, hogy az eseményen belüli értékeket a fogyasztó alkalmazás megfelelően értelmezze.

Az ebben a cikkben található tanácsok kifejezetten ezekre az ügyfelekre vonatkoznak, a kódrészletek fejlesztéséhez használt felsorolt verziókkal:

* Kafka Java kliens (1.1.1-es verzióhttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Client for Java (1.1.0-s verzióhttps://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs client for .NET (2.1.0-s verzióhttps://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (5.0.0-s verzióhttps://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (csak a gyártókat támogatja)

Más AMQP-ügyfelek kissé eltérően viselkedhetnek. Az AMQP jól definiált típusú rendszerrel rendelkezik, de az adott típusú rendszerbe történő nyelvspecifikus típusok szerializálásának sajátosságai az ügyféltől függenek, csakúgy, mint az ügyfél hozzáférésének módja az AMQP-üzenetek részeihez.

## <a name="event-body"></a>Esemény törzse
Az összes Microsoft AMQP-ügyfél az eseménytörzset értelmezetlen bájtok csomagként jelöli. Az előállító alkalmazás bájtok sorozatát továbbítja az ügyfélnek, és egy fogyasztó alkalmazás ugyanazt a sorozatot kapja az ügyféltől. A bájtsorozat értelmezése az alkalmazáskódon belül történik.

Amikor egy eseményt HTTPS-en keresztül küld, az esemény törzse a POSTed tartalom, amely szintén értelmezetlen bájtként kezeli. A Kafka-előproducerrel vagy -fogyasztóval könnyen ellehet érni ugyanazt az állapotot a megadott ByteArraySerializer és ByteArrayDeserializer használatával, ahogy az a következő kódban látható:

### <a name="kafka-byte-producer"></a>Kafka byte[] gyártó

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka bájt[] fogyasztó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ez a kód egy átlátszó bájtfolyamatot hoz létre az alkalmazás két fele között, és lehetővé teszi az alkalmazásfejlesztő számára, hogy manuálisan szerializálja és deszerializálja a kívánt módon, beleértve a deszerializációs döntéseket futásidőben, például a típus alapján vagy a feladó adatait az esemény felhasználóáltal beállított tulajdonságaiban.

Alkalmazások, amelyek egyetlen, rögzített esemény törzstípus lehet, hogy más Kafka szerializálók, és deszerializálók átlátható módon konvertálni az adatokat. Vegyünk például egy alkalmazást, amely JSON-t használ. A JSON-karakterlánc felépítése és értelmezése az alkalmazás szintjén történik. Az Event Hubs szintjén az eseménytörzs mindig egy karakterlánc, az UTF-8 kódolás karaktereit jelölő bájtok sorozata. Ebben az esetben a Kafka gyártó vagy fogyasztó kihasználhatja a megadott StringSerializer vagy StringDeserializer akövetkező kódszerint:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 húrgyártó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 húr fogyasztó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Az AMQP oldalon a Java és a .NET egyaránt biztosítja a karakterláncok UTF-8 bájtsorozatokra és UTF-8 bájtos szekvenciákra történő konvertálásának beépített módjait. A Microsoft AMQP-ügyfelek az eseményeket EventData nevű osztályként jelölik. Az alábbi példák bemutatják, hogyan szerializálhatja az UTF-8 karakterláncot egy AMQP-gyártó EventData eseménytörzsébe, és hogyan deszerializálhatja az EventData eseménytörzset UTF-8 karakterláncsá egy AMQP-fogyasztóban.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 string producer
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 string fogyasztó
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 string producer
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 string fogyasztó
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Mivel a Kafka nyílt forráskódú, az alkalmazásfejlesztő megvizsgálhatja bármely szerializáló vagy deszerializáló implementációját, és implementálhat egy kódot, amely kompatibilis bájtok sorozatát állítja elő vagy használja fel az AMQP oldalán.

## <a name="event-user-properties"></a>Esemény felhasználói tulajdonságai

A felhasználó által beállított tulajdonságok beállíthatók és lehívhatók mind az AMQP-ügyfelekből (a Microsoft AMQP-ügyfelekben tulajdonságoknak nevezett) és a Kafka (ahol fejléceknek nevezik). A HTTPS-küldők úgy állíthatják be az események felhasználói tulajdonságait, hogy a POST műveletben HTTP-fejlécként adják meg őket. A Kafka azonban az eseménytesteket és az eseményfejléc-értékeket is bájtsorozatként kezeli. Míg az AMQP-ügyfeleknél a tulajdonságértékek nek típusa van, amelyeket a tulajdonságértékek aMQP típusú rendszer szerinti kódolásával kommunikálnak.

A HTTPS egy különleges eset. A küldés ipontjában az összes tulajdonságérték UTF-8 szöveg. Az Event Hubs szolgáltatás korlátozott értelmezést végez a megfelelő tulajdonságértékek AMQP-kódolású 32 bites és 64 bites aláírt egész számokra, 64 bites lebegőpontos számokra és logikai értékekre való konvertálásához. Minden olyan tulajdonságértéket, amely nem felel meg az ilyen típusok egyikének, a függvény karakterláncként kezeli.

Ezeket a megközelítéseket a tulajdonsággépelés azt jelenti, hogy a Kafka-fogyasztó látja a nyers AMQP-kódolású bájt szekvenciát, beleértve az AMQP-típusadatait is. Míg az AMQP-fogyasztó látja a Kafka-gyártó által küldött, nem gépelt bájtos szekvenciát, amelyet a kérelemnek értelmeznie kell.

Az AMQP-vagy HTTPS-gyártóktól tulajdonságokat fogadó Kafka-felhasználók számára használja az AmqpDeserializer osztályt, amely a Kafka-ökoszisztéma többi deszerializálója után van modellezve. Az AMQP kódolású bájtszekvenciák ban lévő típusadatokat értelmezi, hogy az adatbájtok Java-típussá deszerializálják.

Ajánlott eljárásként azt javasoljuk, hogy az AMQP-n vagy HTTPS-en keresztül küldött üzenetekben adjon meg egy tulajdont. A Kafka-fogyasztó használhatja annak meghatározására, hogy a fejlécértékek aMQP deszerializálása szükséges.The Kafka consumer can use it to determine whether header values need AMQP deszerialization. Az ingatlan értéke nem fontos. Ez csak szükséges egy jól- tudni név amit a Kafka fogyasztó tud talál -ban oldalra dől -ból fejlécek és elintéz -a viselkedés megfelelően.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP a Kafka 1.
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP a Kafka rész 2: használja AmqPDeserializer deszerializálja ezeket a tulajdonságokat a Kafka fogyasztói
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Ha az alkalmazás ismeri egy tulajdonság várt típusát, vannak olyan deszerializálási módszerek, amelyek után nem igényelnek leadott értéket, de hibát okoznak, ha a tulajdonság nem a várt típusú.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP a Kafka rész 3: egy másik módja a használata AmqpDeserializer a Kafka fogyasztói
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

A másik irányba való váltás jobban fontos, mivel a Kafka-előállító által beállított fejléceket az AMQP-felhasználók mindig nyers bájtokként látják (írja be az `System.Byte[]` org.apache.qpid.proton.amqp.Binary parancsot a Microsoft Azure Event Hubs Client for Java vagy a Microsoft .NET AMQP-ügyfeleihez). A legegyszerűbb út az egyik Kafka által szállított szerializáló használatával hozza létre a fejlécértékek bájtjait a Kafka gyártói oldalán, majd írjon egy kompatibilis deszerializációs kódot az AMQP fogyasztói oldalára.

Az AMQP-to-Kafka-hoz ugyancsak azt javasoljuk, hogy a Kafkán keresztül küldött üzenetekben szerepeljen egy tulajdonság. Az AMQP-fogyasztó a tulajdonság segítségével meghatározhatja, hogy a fejlécértékeket deszerializálásra kell-e. Az ingatlan értéke nem fontos. Csak szüksége van egy jól ismert nevet, hogy az AMQP fogyasztó megtalálja a fejlécek listájában, és állítsa be a viselkedését kell. Ha a Kafka-előproducer nem módosítható, a fogyasztó alkalmazás ellenőrizheti, hogy a tulajdonság értéke bináris vagy bájt típusú-e, és a típus alapján megkísérelheti a deszerializálást.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka az AMQP 1.
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka az AMQP 2.
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka az AMQP 3.
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan streamelhet az Event Hubs-ba a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Ha többet szeretne megtudni a Kafka eseményközpontjairól és eseményközpontjairól, olvassa el az alábbi cikkeket:  

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
* [További minták megismerése a Kafkához készült Event Hubs GitHub-oldalán](https://github.com/Azure/azure-event-hubs-for-kafka)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) segítségével [eseményeket streamelhet a Kafka-ból a helyszíni Eseményközpontokba a felhőben.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelhet az Event Hubs-ba [natív Kafka-alkalmazások,](event-hubs-quickstart-kafka-enabled-event-hubs.md) [Apache Flink](event-hubs-kafka-flink-tutorial.md)vagy [Akka-adatfolyamok](event-hubs-kafka-akka-streams-tutorial.md) használatával
