<properties 
    pageTitle="服務匯流排 AMQP 概觀 | Microsoft Azure" 
    description="了解如何在 Azure 中使用 Advanced Message Queuing Protocol (AMQP)。" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/05/2015" 
    ms.author="sethm"/>




# 服務匯流排中的 AMQP 1.0 支援

Azure 服務匯流排雲端服務和內部 [Service Bus for Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) 支援進階訊息佇列通訊協定 (AMQP) 1.0。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。 您可以透過使用不同語言和架構所建立，且在不同作業系統上執行的元件來建構應用程式。 所有這些元件都可以連線到服務匯流排，並有效地且不失真地順暢交換結構化的商業訊息。

## 簡介：何謂 AMQP 1.0 以及它為什麼很重要？

傳統上，訊息導向的中介軟體產品會採用專屬的通訊協定，以進行用戶端應用程式和代理程式之間的通訊。 這表示一旦選取特定的廠商訊息代理程式，您就必須使用該廠商的程式庫來連接用戶端應用程式與該代理程式。 結果便是您會一定程度地與該廠商相依，因為若要將應用程式移植到其他產品，將需要變更所有已連接應用程式的程式碼。

此外，從不同廠商連接訊息代理程式有些麻煩。 通常需要應用程式層級的橋接功能，才能在不同系統間移動訊息，以及轉換成這些系統專屬的訊息格式。 舉例來說，在將新的統一介面提供給較舊的不同系統使用，或在合併公司之後欲整合 IT 系統時，便常常有這種需求。

軟體產業是門瞬息萬變的生意；有時還沒回應過來便又出現了新的程式設計語言和應用程式架構。 同樣的，IT 系統的需求也會隨著時間而進化，因此開發人員會想要充分利用最新的平台功能。 不過，選取的訊息廠商有時並不支援這些平台。 因為訊息通訊協定是專用的，所以其他人無法為這些新平台提供程式庫。 因此，您必須使用建置閘道或橋接器等方法，讓您得以繼續使用傳訊產品。

開發 進階訊息佇列通訊協定 (AMQP) 1.0 的動機就是為了處理這些問題。 AMQP 是 JP Morgan Chase 的發明，與大多數的金融服務公司一樣，JP Morgan Chase 是訊息導向中介軟體的重度使用者。 目的很簡單：建立一個開放式標準訊息通訊協定，讓透過使用不同語言、架構及作業系統所建置的元件能夠建置訊息架構應用程式，並且全都使用一組供應商所提供的最優質元件。

## AMQP 1.0 技術功能

AMQP 1.0 是一個有效率且可靠的有線等級訊息通訊協定，可以用來建置強大的跨平台訊息應用程式。 此通訊協定有一個簡單目的︰就是在兩個用戶端間定義安全、可靠且有效率的訊息傳輸機制。 訊息本身是使用可攜式資料表示法來編碼，讓異質傳送者和接收者可透過百分之百臨場感來交換結構化的商業訊息。 下列清單摘要說明最重要的功能：

*    **Efficient**: AMQP 1.0 is a connection-oriented protocol that uses a binary encoding for the protocol instructions and the business messages transferred over it. It incorporates sophisticated flow-control schemes to maximize the utilization of the network and the connected components. That said, the protocol was designed to strike a balance between efficiency, flexibility and interoperability.

*    **Reliable**: The AMQP 1.0 protocol allows messages to be exchanged with a range of reliability guarantees, from fire-and-forget to reliable, exactly-once acknowledged delivery.

*    **Flexible**: AMQP 1.0 is a flexible protocol that can be used to support different topologies. The same protocol can be used for client-to-client, client-to-broker, and broker-to-broker communications.

*    **Broker-model independent**: The AMQP 1.0 specification does not make any requirements on the messaging model used by a broker. This means that it's possible to easily add AMQP 1.0 support to existing messaging brokers.


## AMQP 1.0 是一項標準 (Standard 的 S 為大寫)

AMQP 1.0 是由 ISO 與 IEC 核定為 ISO/IEC 19464:2014 的國際標準。

有一個由包含技術提供者及使用者公司在內超過 20 家公司所組成的核心群組，從 2008 年起就開始開發 AMQP 1.0。 在這段期間，使用者公司貢獻了他們的實際商業需求，而技術廠商逐步開發出此通訊協定來符合這些需求。 在整個過程中，廠商參與了他們共同合作的研討會，一起驗證他們實作間的交互操作性。

在 2011 年 10 月，開發工作移轉到資訊結構標準發展組織 (OASIS) 內的一個技術委員會，並在 2012 年 10 月發佈 OASIS AMQP 1.0 標準。 以下為在標準開發期間參與了該技術委員會的公司：

*    **Technology vendors**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.

*    **User firms**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.


以下是一些關於開放標準之最常引用的優點：

*    Less chance of vendor lock-in

*    Interoperability

*    Broad availability of libraries and tooling

*    Protection against obsolescence

*    Availability of knowledgeable staff

*    Lower and manageable risk


## AMQP 1.0 和服務匯流排

Azure 服務匯流排中的 AMQP 1.0 支援代表您現在能夠從一組平台中使用有效率的二進位通訊協定，來運用服務匯流排佇列與發佈/訂閱代理訊息功能。 此外，您還可以建置使用混合語言、架構及作業系統所建置之元件所組成的應用程式。

下圖說明的是一個部署範例，其中的 Java 用戶端是在 Linux 上執行並使用標準的 Java 訊息服務 (JMS) API 撰寫而成，而 .NET 用戶端則是在 Windows 上執行並使用 AMQP 1.0 透過服務匯流排來交換訊息。

![][0]

**圖 1：範例部署案例示範使用服務匯流排和 AMQP 1.0 的跨平台訊息服務**

目前已知下列用戶端程式庫可以使用服務匯流排：

| 語言| 程式庫|
|----------|-------------------------------------------------------------------------------|
| Java| Apache Qpid Java 訊息服務 (JMS) 用戶端<br/>IIT Software SwiftMQ Java 用戶端|
| C| Apache Qpid Proton-C|
| PHP| Apache Qpid Proton-PHP|
| Python| Apache Qpid Proton-Python|
| C#| AMQP .Net Lite|

**圖 2：AMQP 1.0 用戶端程式庫的資料表**

## 摘要

*    AMQP 1.0 is an open, reliable messaging protocol that you can use to build cross-platform, hybrid applications. AMQP 1.0 is an OASIS standard.

*    AMQP 1.0 support is now available in Azure Service Bus as well as Service Bus for Windows Server (Service Bus 1.1). Pricing is the same as for the existing protocols.


## 後續步驟

準備好進行深入了解嗎？ 請造訪下列連結：

- [使用.NET 的服務匯流排與 AMQP]
- [使用 Java 的服務匯流排與 AMQP]
- [使用來自 Python 的服務匯流排與 AMQP]
- [使用 PHP 的服務匯流排與 AMQP]
- [在 Azure Linux VM 上安裝 Apache Qpid Proton C]
- [Windows Server 適用的服務匯流排中的 AMQP]


[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png 
[using service bus from .net with amqp]: service-bus-amqp-dotnet.md 
[using service bus from java with amqp]: service-bus-amqp-java.md 
[using service bus from python with amqp]: service-bus-amqp-python.md 
[using service bus from php with amqp]: service-bus-amqp-php.md 
[installing apache qpid proton-c on an azure linux vm]: service-bus-amqp-apache.md 
[amqp in service bus for windows server]: https://msdn.microsoft.com/library/dn574799.aspx 

