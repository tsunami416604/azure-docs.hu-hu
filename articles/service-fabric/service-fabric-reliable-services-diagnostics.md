<properties
   pageTitle="具狀態可靠的服務診斷"
   description="具狀態可靠的服務診斷功能"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# 具狀態可靠的服務診斷功能
可設定狀態可靠的服務 StatefulServiceBase 類別會發出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件 
可用於偵錯服務、 提供深入了解執行階段如何運作，並協助進行疑難排解。 

## EventSource 事件
具狀態之可靠的服務 StatefulServiceBase 類別的 EventSource 名稱是 "Microsoft-ServiceFabric-Services"。 此事件來源的事件會出現在 
[診斷事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) 視窗時，服務 
正在 [在 Visual Studio 中偵錯](service-fabric-debugging-your-application.md)。

工具和技術可協助您收集和/或檢視 EventSource 事件的範例包括 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567),， 
[Microsoft Azure 診斷](../cloud-services-dotnet-diagnostics.md) 和 
[Microsoft TraceEvent 程式庫](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## 事件

|事件名稱|事件識別碼|Level|事件說明|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|資訊|啟動服務 RunAsync 工作時發出|
|StatefulRunAsyncCancellation|2|資訊|取消服務 RunAsync 工作時發出|
|StatefulRunAsyncCompletion|3|資訊|完成服務 RunAsync 工作時發出|
|StatefulRunAsyncSlowCancellation|4|警告|服務 RunAsync 工作花太多時間才能完成取消時發出|
|StatefulRunAsyncFailure|5|錯誤|服務 RunAsync 工作擲回例外狀況時發出|

## 解譯事件

StatefulRunAsyncInvocation、 StatefulRunAsyncCompletion 和 StatefulRunAsyncCancellation 事件可在服務寫入器，以了解服務，以及何時計時的生命週期 
啟動、 取消及完成服務。 這可能適用於偵錯服務問題或了解服務生命週期。 

服務撰寫者應密切注意 
StatefulRunAsyncSlowCancellation 及 StatefulRunAsyncFailure 事件，表示服務的問題。 

就會發出 StatefulRunAsyncFailure 時
服務 runasync () 工作擲回例外狀況。 通常，擲回例外狀況表示服務中的錯誤。 此外，會導致服務失敗，例外狀況，並因此移至 
另一個節點可以是昂貴的作業和服務移動時的延遲時間內送要求。 服務撰寫者應該判斷例外狀況的原因，並盡可能減少這種情況。 

只要 RunAsync 工作的取消要求所花費的時間超過 4 秒，就會發出 StatefulRunAsyncSlowCancellation。 服務太多時間完成取消時，會影響
快速在另一個節點上重新啟動服務，可能會影響服務的整體可用性。 

