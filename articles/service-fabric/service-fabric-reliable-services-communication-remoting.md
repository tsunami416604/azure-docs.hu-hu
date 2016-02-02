<properties
   pageTitle="Service Fabric 的服務遠端處理 | Microsoft Azure"
   description="Service Fabric 遠端處理可讓用戶端和服務使用遠端程序呼叫與服務進行通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>


# 使用 Reliable Services 的遠端服務

對於未繫結至特定通訊協定或堆疊 (例如 WebAPI、WCF，或是其他) 的服務，此架構會提供遠端機制，以便快速且輕鬆設定服務遠端程序呼叫。

## 設定在服務上的遠端處理

只要兩個簡單步驟，就能設定服務的遠端處理。

1. 建立服務實作的介面。 這個介面會定義方法，可在您的服務用於遠端程序呼叫，該方法也必須是傳回工作的非同步方法。 必須實作介面 `Microsoft.ServiceFabric.Services.Remoting.IService` ，表示服務具有遠端處理介面。
2. 使用 `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` 在您的服務。 這是 `ICommunicationListener` 提供遠端功能的實作。

例如，這個 Hello World 服務會公開單一方法，透過遠端程序呼叫來取得 "Hello World"：

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}
```
> [AZURE.NOTE] 引數和傳回型別在服務介面可以任何簡單、 複雜或自訂型別，但必須是可序列化的.net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)。


## 呼叫遠端服務方法

使用遠端處理堆疊在服務上呼叫方法的方式使用本機 proxy，以透過 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 類別。  `ServiceProxy` 建立本機 proxy，使用相同的服務會實作的介面。 您可以使用該 Proxy 直接在介面上遠端呼叫方法。


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();
```

遠端架構會將在服務擲回的例外狀況傳播給用戶端。 這樣的例外狀況處理邏輯，在用戶端會使用 `ServiceProxy` 可以直接處理的服務就會擲回。

## 後續步驟

* [Web API 與 OWIN 中可靠的服務](service-fabric-reliable-services-communication-webapi.md)

* [WCF 的通訊使用可靠的服務](service-fabric-reliable-services-communication-wcf.md)






