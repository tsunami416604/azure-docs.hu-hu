<properties 
   pageTitle="服務匯流排和 Java 與 AMQP 1.0 | Microsoft Azure"
   description="搭配使用 Java 的服務匯流排與 AMQP。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="sethm" />


# 搭配使用 Java 的服務匯流排與 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java 訊息服務 (JMS) 是在 Java 平台上搭配訊息導向中介軟體一起使用的標準 API。 我們已根據 Apache Qpid 專案開發的 JMS 用戶端程式庫，使用 AMQP 1.0 測試過 Azure 服務匯流排。 此程式庫支援完整的 JMS 1.1 API，可以搭配 AMQP 1.0 相容的任何訊息服務一起使用。 在 Service Bus for Windows Server (服務匯流排內部部署) 中也支援這種情況。 如需詳細資訊，請參閱 [服務匯流排的 Windows Server [] 中的 AMQP][]。

## 下載 Apache Qpid AMQP 1.0 JMS 用戶端程式庫

如需下載最新版 Apache Qpid JMS AMQP 1.0 用戶端程式庫的詳細資訊，請瀏覽 [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)。

您使用服務匯流排建立和執行 JMS 應用程式時，必須從 Apache Qpid JMS AMQP 1.0 散發封裝將下列 4 個 JAR 檔加入 Java CLASSPATH：

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## 從 JMS 使用服務匯流排佇列、主題和訂用帳戶

### Java 命名及目錄介面 (JNDI)

JMS 使用 Java 命名及目錄介面 (JNDI) 建立邏輯名稱與實際名稱之間的區別。 使用 JNDI 可以解析兩種 JMS 物件：**ConnectionFactory** 和 **Destination**。 JNDI 使用提供者模型，您可以在其中插入不同的目錄服務處理名稱解析作業。 Apache Qpid JMS AMQP 1.0 程式庫隨附一個簡單的屬性檔 JNDI 提供者，可使用文字檔來設定。

Qpid 屬性檔 JNDI 提供者是透過下列格式的屬性檔來設定：

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### 設定連接工廠

在 Qpid 內容檔案 JNDI 提供者中用來定義 **ConnectionFactory** 的項目使用下列格式：

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

其中 `[jndi\_name]` 和 `[ConnectionURL]` 具有下列意義:

| 名稱| 意義| | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[] jndi\_name`| 連接工廠的邏輯名稱。這個名稱會解析 Java 應用程式中使用 JNDI `intialcontext.lookup ()` 方法。| | | | |
| `[ConnectionURL]`| 將包含所需資訊的 JMS 程式庫提供給 AMQP 代理人的 URL。| | | | |

連接 URL 的格式如下：

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Where `[namespace]`, `[username]`, and `[password]` have the following meanings:

| Name          | Meaning                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | The Service Bus namespace obtained from the [Azure classic portal][].                      |   |   |   |   |
| `[username]`  | The Service Bus issuer name obtained from the [Azure classic portal][].                    |   |   |   |   |
| `[password]`  | URL-encoded form of the Service Bus issuer key obtained from the [Azure classic portal][]. |   |   |   |   |

> [AZURE.NOTE] You must URL-encode the password manually. A useful URL encoding utility is available at [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

For example, if the information obtained from the portal is as follows:

| Namespace:   | test.servicebus.windows.net                  |
|--------------|----------------------------------------------|
| Issuer name: | owner                                        |
| Issuer key:  | abcdefg |

Then in order to define a **ConnectionFactory** object named `SBCONNECTIONFACTORY`, the configuration string would be as follows:
```
connectionfactory。SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### Configuring destinations

The entry that defines a destination in the Qpid Properties File JNDI Provider is of the following format:
```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Where `[jndi\_name]` and `[physical\_name]` have the following meanings:

| Name              | Meaning                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | The logical name of the destination. This is name is resolved in the Java application by using the JNDI `IntialContext.lookup()` method. |
| `[physical\name]` | The name of the Service Bus entity to which the application sends or receives messages.                                                  |

Note the following:

- The `[physical\name]` value can be a Service Bus queue or topic.
- When receiving from a Service Bus topic subscription, the physical name specified in JNDI should be the name of the topic. The subscription name is provided when the durable subscription is created in the JMS application code.
- It is also possible to treat a Service Bus topic subscription as a JMS Queue. There are several advantages to this approach: the same receiver code can be used for queues and topic subscriptions, and all the address information (the topic and subscription names) is externalized in the properties file.
- To treat a Service Bus topic subscription as a JMS Queue, the entry in properties file should be of the form: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

To define a logical JMS destination named "TOPIC" that maps to a Service Bus topic named "topic1," the entry in the properties file would be as follows:
```
主題。主題 = topic1
```

### Sending messages using JMS

The following code shows how to send a message to a Service Bus topic. It is assumed that `SBCONNECTIONFACTORY` and `TOPIC` are defined in a **servicebus.properties** configuration file as described in the previous section.
```
雜湊表<String, String> env = 新的雜湊表<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties");

InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
主題主題 = (主題) context.lookup("TOPIC");
Connection connection = cf.createConnection();
工作階段的工作階段 = connection.createSession (false、 Session.AUTO_ACKNOWLEDGE;)
對於生產者 = session.createProducer(topic);
TextMessage 訊息 = session.createTextMessage (「 這是文字字串"); 
producer.send(message)
```

### Receiving messages using JMS

The following code shows `how` to receive a message from a Service Bus topic subscription. It is assumed that `SBCONNECTIONFACTORY` and TOPIC are defined in a **servicebus.properties** configuration file as described in the previous section. It is also assumed that the subscription name is `subscription1`.
```
雜湊表<String, String> env = 新的雜湊表<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties");

InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
主題主題 = (主題) context.lookup("TOPIC");
Connection connection = cf.createConnection();
工作階段的工作階段 = connection.createSession (false、 Session.AUTO_ACKNOWLEDGE;)
TopicSubscriber 訂閱者 = session.createDurableSubscriber (主題、"subscription1");
connection.start();
訊息的訊息 = messageConsumer.receive();
```

### Guidelines for building robust applications

The JMS specification defines how the exception contract of the API methods and application code should be written to handle such exceptions. Here are some other points to consider regarding exception handling:

-   Register an **ExceptionListener** with the JMS connection using **connection.setExceptionListener**. This enables a client to be notified of a problem asynchronously. This notification is particularly important for connections that only consume messages, as they would have no other way to learn that their connection has failed. The **ExceptionListener** is called if there is a problem with the underlying AMQP connection, session, or link. In this situation, the application program should recreate the **JMS Connection**, **Session**, **MessageProducer** and **MessageConsumer** objects from scratch.

-   To verify that a message has been successfully sent from a **MessageProducer** to a Service Bus entity, ensure that the application has been configured with the **qpid.sync\_publish** system property set. You can do this by starting the program with the **-Dqpid.sync\_publish=true** Java VM option set on the command line when starting the application. Setting this option configures the library to not return from the send call until confirmation has been received that the message has been accepted by Service Bus. If a problem occurs during the send operation, a **JMSException** is raised. There are two possible causes: 
    1. If the problem is due to Service Bus rejecting the particular message being sent, then a **MessageRejectedException** exception will be raised. This error is either transitory, or due to some problem with the message. The recommended course of action is to make several attempts to retry the operation with some back-off logic. If the problem persists, then the message should be abandoned with an error logged locally. There is no need to recreate the **JMS Connection**, **Session**, or **MessageProducer** objects in this situation. 
    2. If the problem is due to Service Bus closing the AMQP Link, then an **InvalidDestinationException** exception will be raised. This can be due to a transient problem, or due to the message entity being deleted. In either case, the **JMS Connection**, **Session**, and **MessageProducer** objects should be recreated. If the error condition was transient, then this operation will eventually be successful. If the entity has been deleted, the failure will be permanent.

## Messaging between .NET and JMS

### Message bodies

JMS defines five different message types: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage**, and **TextMessage**. The Service Bus .NET API has a single message type, [BrokeredMessage][].

#### JMS to Service Bus .NET API

The following sections show how to consume messages of each of the JMS message types from .NET. An **ObjectMessage** example has not been included, as the body of an **ObjectMessage** contains a serializable object in the Java programming language, which is not interpretable by a .NET application.

##### BytesMessage

The following code shows how to consume the body of a **BytesMessage** object by using the Service Bus .NET APIs.
```
資料流的資料流 = 訊息。GetBody<Stream>();
int streamLength = (int) 資料流。長度。

byte [] byteArray = 新的 byte [streamLength];
資料流。讀取 (byteArray，0，streamLength);

Console.WriteLine ("長度 ="+ streamLength);
for (int 我 = 0; 我 < 資料流。長度。i + +)
{
  Console.Write ("["+ (sbyte) byteArray [i] +"]");
}
```

##### MapMessage

The following code shows how to consume the body of a **MapMessage** object by using the Service Bus .NET APIs. This code iterates through the elements of the map, displaying the name and value of each element.
```
字典<String, Object> 字典 = 訊息。GetBody<Dictionary<String, Object>> ();

foreach (字串 mapItemName 字典中。索引鍵)
{
  物件 mapItemValue = null;
  如果 (字典。TryGetValue (出 mapItemValue mapItemName))
  {
    Console.WriteLine (mapItemName +":"+ mapItemValue);
  }
}
```

##### StreamMessage

The following code shows how to consume the body of a **StreamMessage** object by using the Service Bus .NET APIs. This code lists each of the items from the stream, together with their types.
```
清單<Object> 清單 = 訊息。GetBody<List<Object>> ();

foreach (在清單中的物件項目)
{
  Console.WriteLine (項目 +"("+ 項目。GetType() +")");
}
```

##### TextMessage

The following code shows how to consume the body of a **TextMessage** object by using the Service Bus .NET APIs. This code displays the text string contained in the body of the message.
```
Console.WriteLine ("文字:"+ 訊息。GetBody<String>())。
```

#### Service Bus .NET APIs to JMS

The following sections show how a .NET application can create a message that is received in JMS in each of the different JMS message types. An **ObjectMessage** example has not been included, as the body of an **ObjectMessage** contains a serializable object in the Java programming language, which is not interpretable by a .NET application.

##### BytesMessage

The following code shows how to create a [BrokeredMessage][] object in .NET that is received by a JMS client as a **BytesMessage**.
```
byte [] 位元組 = {33、 12、 45、 33、 12、 45、 33、 12、 45、 33、 12、 45};
訊息 = 新 BrokeredMessage(bytes);
```

##### StreamMessage

The following code shows how to create a [BrokeredMessage][] object in .NET that is received by a JMS client as a **StreamMessage**.
```
清單<Object> 清單 = new List<Object>();
清單。新增 (「 字串 1 」)。
清單。新增 (「 字串 2 」)。
清單。新增 (「 字串 3 」)。
清單。Add((double)3.14159)
訊息 = 新 BrokeredMessage(list);
```

##### TextMessage

The following code shows how to consume the body of a **TextMessage** using the Service Bus .NET API. This code displays the text string contained in the body of the message.
```
訊息 = 新 BrokeredMessage (「 這是文字字串");
```

### Application properties

####JMS to Service Bus .NET APIs

JMS messages support application properties of the following types: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double**, and **String**. The following Java code shows how to set properties on a message by using each of these property types.
```
message.setBooleanProperty (「 TestBoolean 」，true); 
message.setByteProperty (「 TestByte 」，(位元組) 33)。 
message.setDoubleProperty (「 TestDouble 」，3.14159 D); 
message.setFloatProperty (「 TestFloat 」，3.13159F); 
message.setIntProperty (「 TestInt 」，100)。 
message.setStringProperty (「 TestString 」，「 服務匯流排 」)。
```

In the Service Bus .NET APIs, message application properties are carried in the **Properties** collection of [BrokeredMessage][]. The following code shows how to read the application properties of a message received from a JMS client.
```
如果 (訊息。Properties.Keys.Count > 0)
{
  foreach (訊息中的字串名稱。) Properties.Keys
  {
    物件值 = 訊息。屬性 [名稱]。
    Console.WriteLine (名稱 +":"+ 值 +"("+ 值。GetType() +")");
  }
  Console.WriteLine();
}
```

The following table shows how the JMS property types map to the .NET property types.

| JMS Property Type | .NET Property Type |
|-------------------|--------------------|
| Byte              | sbyte              |
| Integer           | int                |
| Float             | float              |
| Double            | double             |
| Boolean           | bool               |
| String            | string             |

The [BrokeredMessage][] type supports application properties of the following types: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset**, and **TimeSpan**. The following .NET code shows how to set properties on a [BrokeredMessage][] object using each of these property types.
```
訊息。屬性 ["TestByte"] = (位元組) 128。
訊息。屬性 ["TestSbyte"] = (sbyte)-22;
訊息。屬性 ["TestChar"] = (char) 'X';
訊息。屬性 ["TestShort"] = (簡短)-12345;
訊息。屬性 ["TestUshort"] = (ushort) 12345;
訊息。屬性 ["TestInt"] = (int)-100。
訊息。屬性 ["TestUint"] = (uint) 100。
訊息。屬性 ["TestLong"] = (長整數)-12345;
訊息。屬性 ["TestUlong"] = (ulong) 12345;
訊息。屬性 ["TestFloat"] = (float) 3.14159;
訊息。屬性 ["TestDouble"] = (double) 3.14159;
訊息。屬性 ["TestDecimal"] = (十進位) 是 3.14159;
訊息。屬性 ["TestBoolean"] = true;
訊息。屬性 ["TestGuid"] = Guid.NewGuid();
訊息。屬性 ["TestString"] = 「 服務匯流排 」;
訊息。屬性 ["TestUri"] = new Uri (「 http://www.bing.com 」)。
訊息。屬性 ["TestDateTime"] = Ticks;
訊息。屬性 ["TestDateTimeOffSet"] = DateTimeOffset.Now;
訊息。屬性 ["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

The following Java code shows how to read the application properties of a message received from a Service Bus .NET client.
```
列舉型別 propertyNames = message.getPropertyNames(); 
(propertyNames.hasMoreElements()) 時 
{ 
  字串名稱 = (字串) propertyNames.nextElement(); 
  物件值 = message.getObjectProperty(name); 
  System.out.println (名稱 +":"+ 值 +"("+ value.getClass() +") 」); 
}
```

下表顯示 .NET 屬性類型如何對應至 JMS 屬性類型。

| .NET 屬性類型| JMS 屬性類型| 注意事項|
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte| UnsignedByte| -|
| sbyte| 位元組| -|
| char| 字元| -|
| short| 簡短| -|
| ushort| UnsignedShort| -|
| int| Integer| -|
| uint| UnsignedInteger| -|
| long| Long| -|
| ulong| UnsignedLong| -|
| float| Float| -|
| double| 兩倍| -|
| decimal| BigDecimal| -|
| 布林| Boolean| -|
| Guid| UUID| -|
| 字串| String| -|
| DateTime| Date| -|
| DateTimeOffset| DescribedType| Datetimeoffset.utcticks 會對應至 AMQP 類型:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>|
| TimeSpan| DescribedType| Timespan.ticks 會對應至 AMQP 類型:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>|
| Uri| DescribedType| Uri.absoluteuri 會對應至 AMQP 類型:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>|

### 標準標頭

下表顯示如何 JMS 標準標頭和 [BrokeredMessage []][] 對應使用 AMQP 1.0 標準屬性。

#### JMS 至服務匯流排 .NET API

| JMS| 服務匯流排 .NET| 注意事項|
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID| Message.CorrelationID| -|
| JMSDeliveryMode| 目前無法使用| 服務匯流排僅支援長期訊息;例如，DeliveryMode.PERSISTENT，不論指定的內容。|
| JMSDestination| Message.To| -|
| JMSExpiration| 訊息。TimeToLive| 轉換|
| JMSMessageID| Message.MessageID| 根據預設，JMSMessageID 在 AMQP 訊息中的二進位格式編碼。收到二進位訊息識別碼時，.NET 用戶端程式庫會根據位元組的 unicode 值，將它轉換為字串表示法。若要將 JMS 程式庫切換為使用字串訊息識別碼，請將 “binary-messageid=false” 字串附加至 JNDI ConnectionURL 的查詢參數。例如: 「 amqps://[username]:[password]@[namespace].servicebus.windows.net 嗎?二進位訊息識別碼 = false"。|
| JMSPriority| 目前無法使用| 服務匯流排不支援訊息優先順序。|
| JMSRedelivered| 目前無法使用| -|
| JMSReplyTo| 訊息。ReplyTo| -|
| JMSTimestamp| Message.EnqueuedTimeUtc| 轉換|
| JMSType| Message.Properties ["jms-type"]| -|

#### 服務匯流排 .NET API 至 JMS

| 服務匯流排 .NET| JMS| 注意事項|
|-------------------------|------------------|-------------------------|
| ContentType| -| 目前無法使用|
| CorrelationId| JMSCorrelationID| -|
| EnqueuedTimeUtc| JMSTimestamp| 轉換|
| 標籤| n/a| 目前無法使用|
| MessageId| JMSMessageID| -|
| ReplyTo| JMSReplyTo| -|
| {1>replytosessionid| n/a| 目前無法使用|
| ScheduledEnqueueTimeUtc| n/a| 目前無法使用|
| SessionId| n/a| 目前無法使用|
| TimeToLive| JMSExpiration| 轉換|
| 收件人| JMSDestination| -|

## 不支援的功能和限制

JMS over AMQP 1.0 和服務匯流排一起使用時有下列限制：

-   每工作階段僅允許一個 **MessageProducer** 或 **MessageConsumer**。 如果您想要在應用程式中建立多個 **MessageProducer** 或 **MessageConsumer** 物件，請分別建立專用的工作階段。

-   目前不支援可變更的主題訂用帳戶。

-   不支援 **MessageSelector** 物件。

-   不支援 **TemporaryQueue** 或 **TemporaryTopic** 這些暫時目的地，也不支援使用它們的 **QueueRequestor** 和 **TopicRequestor** API。

-   不支援交易式工作階段。

-   不支援分散式工作階段。

## 後續步驟

準備好進行深入了解嗎？ 請造訪下列連結：

- [服務匯流排 AMQP 概觀]
- [Windows Server 適用的服務匯流排中的 AMQP]


[amqp in service bus for windows server]: https://msdn.microsoft.com/library/dn574799.aspx 
[brokeredmessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx 
[service bus amqp overview]: service-bus-amqp-overview.md 
[azure classic portal]: http://manage.windowsazure.com 

