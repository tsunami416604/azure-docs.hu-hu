<properties 
   pageTitle="服務匯流排和 Python 與 AMQP 1.0 |Microsoft Azure"
   description="搭配使用 Python 的服務匯流排與 AMQP。"
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

# 搭配使用 Python 的服務匯流排與 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python 是繫結至 Proton-C 的 Python 語言；也就是說，Proton-Python 會當作以 C 實作之引擎的包裝函式實作。

## 下載 Proton 用戶端程式庫

您可以從下載 PROTON-C 和其相關聯的繫結 (包含 Python 在內) [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)。 下載採用原始程式碼形式。 若要建置程式碼，請遵循下載的套件中所包含的指示。

請注意，在撰寫本文時，Proton-C 中的 SSL 支援只適用於 Linux 作業系統。 因為 Azure 服務匯流排需要使用 SSL，所以 Proton-C (和語言繫結) 此時僅可用來從 Linux 存取服務匯流排。 在 Windows 上啟用 Proton-C 與 SSL 正在進行中，請經常檢查更新。

## 從 Python 使用服務匯流排佇列、主題和訂用帳戶

下列程式碼示範如何從服務匯流排傳訊實體傳送和接收訊息。

### 使用 Proton-Python 傳送訊息

下列程式碼示範如何將訊息傳送至服務匯流排傳訊實體。

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### 使用 Proton-Python 接收訊息

下列程式碼示範如何從服務匯流排傳訊實體接收訊息。

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## 在 .NET 與 Proton-Python 之間傳訊

### 應用程式屬性

#### Proton-Python 至服務匯流排 .NET API

Proton-python 訊息支援下列類型的應用程式屬性: **int**, ，**長**, ，**float**, ，**uuid**, ，**bool**, ，**字串**。 下列 Python 程式碼示範如何使用每一個屬性類型來設定訊息的屬性。

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

在服務匯流排.NET API 中，訊息應用程式屬性位於在 **屬性** 集合 [BrokeredMessage][]。 下列程式碼示範如何讀取從 Python 用戶端接收之訊息的應用程式屬性。

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

下表將 Python 屬性類型對應至 .NET 屬性類型。

| Python 屬性類型 | .NET 屬性類型 |
|----------------------|--------------------|
| int                  | int                |
| float                | double             |
| long                 | int64              |
| uuid                 | guid               |
| 布林                 | 布林               |
| 字串               | 字串             |

#### 服務匯流排 .NET API 至 Proton-Python

 [BrokeredMessage][] 型別支援下列類型的應用程式屬性: **位元組**, ，**sbyte**, ，**char**, ，**簡短**, ，**ushort**, ，**int**, ，**uint**, ，**長**, ，**ulong**, ，**float**, ，**double**, ，**十進位**, ，**bool**, ，**Guid**, ，**字串**, ，**Uri**, ，**DateTime**, ，**DateTimeOffset**, ，和 **TimeSpan**。 下列.NET 程式碼示範如何設定屬性 [BrokeredMessage][] 物件使用這些屬性的型別。

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

下列 Python 程式碼示範如何讀取從服務匯流排 .NET 用戶端接收之訊息的應用程式屬性。

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

下表將 .NET 屬性類型對應至 Python 屬性類型。

| .NET 屬性類型 | Python 屬性類型 | 注意事項                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | int                  | -                                                                                                                                                                     |
| sbyte              | int                  | -                                                                                                                                                                     |
| char               | char                 | Proton-python 類別                                                                                                                                                 |
| short              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| long               | int                  | -                                                                                                                                                                     |
| ulong              | long                 | Proton-python 類別                                                                                                                                                 |
| float              | float                | -                                                                                                                                                                     |
| double             | float                | -                                                                                                                                                                     |
| decimal            | String               | Proton 目前不支援 decimal。                                                                                                                     |
| 布林               | 布林                 | -                                                                                                                                                                     |
| Guid               | uuid                 | Proton-python 類別                                                                                                                                                 |
| 字串             | 字串               | -                                                                                                                                                                     |
| DateTime           | timestamp            | Proton-python 類別                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | Datetimeoffset.utcticks 會對應至 AMQP 類型:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Timespan.ticks 會對應至 AMQP 類型:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>                        |
| Uri                | DescribedType        | Uri.absoluteuri 會對應至 AMQP 類型:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>                               |

### 標準屬性

下表顯示 Proton-python 標準訊息屬性之間的對應和 [BrokeredMessage][] 標準訊息屬性。

#### Proton-Python 至服務匯流排 .NET API

| Proton-Python        | 服務匯流排 .NET         | 注意事項                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| 持久              | n/a                      | 服務匯流排僅支援持久的訊息。               |
| 優先順序             | n/a                      | 服務匯流排僅支援單一訊息優先順序。      |
| Ttl                  | Message.TimeToLive       | 轉換，Proton-Python TTL 是以毫秒定義。 |
| first\_acquirer      | n/a                      | -                                                           |
| delivery\_count      | n/a                      | -                                                           |
| 識別碼                   | Message.MessageID        | -                                                           |
| user\_id             | n/a                      | -                                                           |
| 地址              | Message.To               | -                                                           |
| 主體              | Message.Label            | -                                                           |
| reply\_to            | Message.ReplyTo          | -                                                           |
| correlation\_id      | Message.CorrelationID    | -                                                           |
| content\_type        | Message.ContentType      | -                                                           |
| content\_encoding    | n/a                      | -                                                           |
| expiry\_time         | n/a                      | -                                                           |
| creation\_time       | n/a                      | -                                                           |
| group\_id            | Message.SessionId        | -                                                           |
| group\_sequence      | n/a                      | -                                                           |
| reply\_to\_group\_id | Message.ReplyToSessionId | -                                                           |
| format               | n/a                      | -                                                           |

| 服務匯流排 .NET        | Proton                       | 注意事項                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.content\_type        | -                                                           |
| CorrelationId           | Message.correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/a                          | -                                                           |
| 標籤                   | Message.subject              | -                                                           |
| MessageId               | Message.id                   | -                                                           |
| ReplyTo                 | Message.reply\_to            | -                                                           |
| {1>replytosessionid        | Message.reply\_to\_group\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/a                          | -                                                           |
| SessionId               | Message.group\_id            | -                                                           |
| TimeToLive              | Message.ttl                  | 轉換，Proton-Python TTL 是以毫秒定義。 |
| 收件人                      | Message.address              | -                                                           |

## 後續步驟

準備好進行深入了解嗎？ 請造訪下列連結：

- [服務匯流排 AMQP 概觀]
- [Windows Server 服務匯流排中的 AMQP]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP in Service Bus for Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Service Bus AMQP overview]: service-bus-amqp-overview.md

