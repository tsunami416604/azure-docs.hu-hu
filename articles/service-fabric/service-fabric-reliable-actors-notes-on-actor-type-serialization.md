<properties
   pageTitle="Reliable Actor 的動作項目類型序列化註解"
   description="說明定義可用於定義 Service Fabric Reliable Actor 狀態與介面其可序列化類別的基本需求"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors 類型序列化的注意事項

定義動作項目介面和狀態時，須記住一些重要事項：必須資料合約序列化的類型。 資料合約的詳細資訊位於 [MSDN](https://msdn.microsoft.com/library/ms731923.aspx)。

## 動作項目介面中使用的類型

所有方法和中定義每個方法所傳回之工作的結果類型的引數 [動作項目介面](service-fabric-reliable-actors-introduction.md#actors) 需要資料合約序列化。 這也適用於方法中定義的引數 [動作項目事件介面](service-fabric-reliable-actors-events.md#actor-events)。 (動作項目事件介面方法一律會傳回無效)。
例如，如果 `IVoiceMail` 介面將方法定義為：

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` 是已資料合約序列化的標準 .NET 類型。 `Voicemail` 類型必須是可資料合約序列化。

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## 動作項目狀態類別

動作項目狀態必須是可資料合約序列化。 例如，如果我們有如下所示的動作項目類別定義：

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

定義狀態類別，並分別以 DataContract 與 DataMember 屬性註解類別及其成員。

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

