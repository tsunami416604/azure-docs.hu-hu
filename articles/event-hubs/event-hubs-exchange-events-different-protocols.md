---
title: Exchange-események, amelyek más protokollt – Azure Event Hubs-alkalmazások közti |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan fogyasztók és a különböző protokollok (AMQP, az Apache Kafka és HTTPS) használó gyártók is exchange-eseményeket, amikor az Azure Event Hubs használatával.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 1345a5814faefd4074e7d9548d374bd79d977514
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015585"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Exchange-események a fogyasztók és más protokollt használó gyártók között: HTTPS, AMQP és a Kafka
Azure Event hubs szolgáltatás három protokollt támogat a fogyasztók: AMQP, a Kafka és a HTTPS. Ezeket a protokollokat mindegyike rendelkezik a saját módja egy üzenetet, így természetesen felmerül a következő kérdés jelölő: Ha egy alkalmazás küldi az eseményeket egy Eseményközpontba, egy protokollal, és a egy másik protokoll használja őket, mire a különböző részeit és értékeit a amikor megérkeznek a fogyasztó esemény néz? Ez a cikk ismerteti az ajánlott eljárások az előállítói és a fogyasztói annak érdekében, hogy az esemény található értékek helyesen értelmezi a fogyasztó alkalmazás általi.

Ebben a cikkben a tanácsadás ezek az ügyfelek kifejezetten felsorolt verzióival használható kódrészletek mutatja be:

* A Kafka-Java-ügyfél (1.1.1-es a https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* A Microsoft Azure Event Hubs-ügyfél (1.1.0-s a Java https://github.com/Azure/azure-event-hubs-java)
* A Microsoft Azure Event Hubs-ügyfél (2.1.0-ás a .NET https://github.com/Azure/azure-event-hubs-dotnet)
* A Microsoft Azure Service Bus (verziót 5.0.0 https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (csak a gyártók támogatja)

Az AMQP ügyfelek számára kissé különbözően viselkedik. Az AMQP jól meghatározott típusú rendszerrel rendelkezik, de típusú szerializálási nyelvét és az adott típusú rendszer adatait az ügyfélen, attól függ, mint hogy az ügyfél egy AMQP üzenet részeit hozzáférést biztosít.

## <a name="event-body"></a>Szövegrésze
Az esemény törzsét összes Microsoft AMQP ügyfél tartozik egy nem értelmezett tulajdonságcsomag bájt. Előállító alkalmazás bájt sorozatát átadja az ügyfél, és a egy fogyasztó alkalmazás fogadja az ügyfél ugyanabban a sorrendben. Bájt feladatütemezési értelmezése az alkalmazás kódja belül történik.

Amikor HTTPS-kapcsolaton keresztül egy eseményt küld, az esemény törzsét a feladott tartalmát, amelyet nem értelmezett bájt is számít. Ez Gyerekjáték ugyanazt az állapotot, a Kafka producer vagy fogyasztói eléréséhez megadott ByteArraySerializer és ByteArrayDeserializer az alábbi kódban látható módon:

### <a name="kafka-byte-producer"></a>A Kafka byte [] producer

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>A Kafka byte [] fogyasztói
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ez a kód az alkalmazás két fele közötti transzparens bájt folyamatot hoz létre, és lehetővé teszi, hogy az alkalmazás fejlesztőjének manuálisan szerializálható és deszerializálható semmilyen módon megfelelő, beleértve a deszerializálás döntéseket futásidőben, például típus alapján vagy az eseményt a felhasználó által beállított tulajdonságai küldő információkat.

Egyetlen rendelkező alkalmazások, rögzített törzs eseménytípus lehet használhatja más Kafka objektumszerializáló és deserializers transzparens módon konvertálni az adatokat. Például vegyünk egy alkalmazást, amely JSON-t használ. A fejlesztés és a JSON-karakterlánc értelmezése az alkalmazás szintjén történik. Az Event Hubs szintjén az esemény törzsét, mindig egy karakterlánc, bájtot jelölő az UTF-8 kódolást karakter sorozata. Ebben az esetben a Kafka producer és fogyasztói kihasználhatja a megadott StringSerializer vagy StringDeserializer az alábbi kódban látható módon:

### <a name="kafka-utf-8-string-producer"></a>A Kafka UTF-8 karakterlánc producer
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>A Kafka UTF-8 karakterlánc fogyasztói
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

AMQP oldalára a Java és .NET konvertálja a karakterláncokat, és az UTF-8 bájtos feladatütemezések beépített módszereket biztosítanak. A Microsoft az AMQP-ügyfelek EventData osztály események tartozik. Az alábbi példák bemutatják, hogyan UTF-8 karakterlánc szerializálni egy EventData esemény törzsébe egy AMQP termelő, és hogyan egy AMQP fogyasztói az UTF-8 alakít egy EventData szövegrésze deszerializálása.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 karakterlánc producer
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 karakterlánc fogyasztói
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C#.NET UTF-8 karakterlánc producer
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#.NET UTF-8 karakterlánc fogyasztói
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Mivel a Kafka nyílt forráskódú, az alkalmazás fejlesztőjének segít bármely szerializáló végrehajtásának vizsgálata vagy deserializer és kódra, amely hoz létre, vagy egy kompatibilis bájtsorrendet AMQP oldalán használ fel.

## <a name="event-user-properties"></a>Esemény-felhasználó tulajdonságai

Felhasználó által beállított tulajdonságok beállítása, és mindkét AMQP-ügyfél lekért (a Microsoft az AMQP-ügyfelekben akkor tulajdonságai) és a Kafka (ahol akkor fejlécek). HTTPS feladók állíthatják be felhasználói tulajdonságok esemény őket a POST műveletet a HTTP-fejléceket, megadásával. Azonban a Kafka tekinti esemény szervek és az esemény fejlécértékeket bájt feladatütemezések. Mivel az AMQP-ügyfelekben tulajdonságértékek típusok, amely közli a kódolás a tulajdonságértékek az AMQP típusú rendszernek megfelelően.

HTTPS egy különleges esetben. Folyamatos küld, az összes tulajdonság értékei UTF-8 szöveget. Az Event Hubs szolgáltatásnak nincs megfelelő tulajdonságértékek átalakítása AMQP-kódolású 32 bites és 64 bites aláírt egész számok, 64 bites lebegőpontos számok és logikai értékek értelmezése korlátozott mennyiségű. Bármilyen tulajdonság értéke, amely nem fér el egyet ezek közül egy karakterlánc számít.

Ezek a módszerek tulajdonság írja be a keverése azt jelenti, hogy a Kafka-fogyasztók látja-e az AMQP-kódolású nyers bájt feladatütemezési, beleértve a AMQP típussal kapcsolatos információk. Mivel egy AMQP fogyasztói látja, a Kafka producer, amely az alkalmazás kell értelmezni által küldött bájt nem típusos sorrendjét.

Tulajdonságok fogadjon az AMQP vagy HTTPS-előállítók Kafka-fogyasztók használja a AmqpDeserializer osztály, amely a többi deserializers, a Kafka-ökoszisztéma után van modellezve. A írja be az adatokat az AMQP-kódolású bájt feladatütemezések a bájtok deszerializálása egy Java-típussá értelmezi azt.

Ajánlott eljárásként azt javasoljuk, hogy tartalmazza-e egy tulajdonságot az AMQP vagy HTTPS használatával küldött üzenetek. A Kafka-fogyasztók segítségével azt határozza meg, hogy kell-e fejlécértékeket AMQP deszerializálása. A tulajdonság értéke nem számít. Ugyanúgy kell, hogy a Kafka-fogyasztók fejlécek listája található, és annak megfelelően módosítja annak viselkedését oldalmérettípus ismert nevét.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP a Kafka: 1. rész: hozzon létre, és küldünk egy eseményt C# (.NET) tulajdonságokkal
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>Az AMQP Kafka részére, 2: azokat a tulajdonságokat, a Kafka-fogyasztók deszerializálása AmqpDeserializer használata
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

Ha az alkalmazás tudja, hogy a várt típus egy tulajdonsághoz, ezt követően nincs szükség egy cast deszerializálás módszer, de azok hibaüzenetet küldjön, ha a tulajdonság nem a várt típusú.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP a Kafka 3. rész: AmqpDeserializer használata a Kafka-fogyasztók egy másik módja
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

A másik irányba is több érintett, mivel a Kafka producer által beállított fejlécek mindig tekinteni egy AMQP fogyasztó nyers bájt (írja be a Java, a Microsoft Azure Event Hubs ügyfél org.apache.qpid.proton.amqp.Binary vagy `System.Byte[]` a Microsoft .NET Az AMQP-ügyfelek). A legegyszerűbb elérési út, a Kafka által megadott objektumszerializáló egyik használatával hozhatja létre a bájtok fejléc az értékek a Kafka producer oldalon, majd egy kompatibilis deszerializálás kód majd írása a AMQP feldolgozói oldalon.

A AMQP Kafka, ajánlott eljárásként azt javasoljuk, hogy az tartalmazza a tulajdonságot a Kafka-n keresztül küldött üzenetek. Az AMQP-ügyfél a tulajdonság segítségével határozza meg, hogy kell-e fejlécértékeket deszerializálása. A tulajdonság értéke nem számít. Ugyanúgy kell, hogy az AMQP fogyasztói fejlécek listája található, és annak megfelelően módosítja annak viselkedését oldalmérettípus ismert nevét. Ha a Kafka producer nem lehet módosítani, akkor is a fogyasztó alkalmazás ellenőrizze, hogy a tulajdonság értékének bináris vagy bájt típusú, és megkísérli a deszerializálás típusa alapján.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka AMQP 1. rész: hozzon létre, és küldünk egy eseményt a Kafkából tulajdonságokkal
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>A Kafka AMQP részére, 2: manuálisan deszerializálni a megadott tulajdonságokkal C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka AMQP 3. rész: manuálisan deszerializálni a megadott tulajdonságokkal Java nyelven
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
Ebben a cikkben bemutattuk, hogyan streamelhet Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. További információ az Event Hubs és az Event Hubs a Kafka, tekintse meg a következő cikkeket:  

* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
* [További minták megismerése a Kafkához készült Event Hubs GitHub-oldalán](https://github.com/Azure/azure-event-hubs-for-kafka)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [eseményeket streamelhet a helyszíni Kafkából a felhőben található Kafka-kompatibilis Event Hubsba.](event-hubs-kafka-mirror-maker-tutorial.md)
* Ismerje meg, hogyan streamelése Kafka az engedélyezett az Event Hubs használatával [Kafka natív alkalmazások](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), vagy [Akka adatfolyamok](event-hubs-kafka-akka-streams-tutorial.md)
