<properties
 pageTitle="比較 Azure IoT 中樞與 Azure 事件中樞 |Microsoft Azure"
 description="透過反白顯示功能差異和使用案例來比較 Azure IoT 中樞和 Azure 事件中樞服務。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>


# IoT 中樞和事件中樞的比較

Azure IoT 中心的主要使用案例之一是從裝置收集遙測。

不過，這些服務有許多差異，將在以下章節詳述。

| 領域| IoT 中心| 事件中樞|
| ---- | ------- | ---------- |
| 通訊模式| 裝置到雲端事件輸入和雲端到裝置傳訊。| 僅限於事件輸入 (通常視為裝置到雲端案例)。|
| 安全性| 每個裝置的身分識別與可撤銷的存取控制。| 在 IoT 解決方案的內容中，通常需要實作自訂解決方案來支援每個裝置的認證以及防詐騙措施。|
| 調整| IoT 中樞會最佳化以支援數百萬同時連接的裝置。| 另一方面，事件中樞可讓您指定每個傳送訊息的分割。|
| 裝置 SDK| | .NET、C 都支援事件中樞，且事件中樞提供 AMQP 和 HTTP 傳送介面。|

總而言之，即使唯一的使用案例是裝置到雲端遙測輸入，IoT 中樞仍會提供專為 IoT 裝置連線所設計的服務，並持續擴充具備 IoT 功能之案例的價值主張。 事件中樞的設計對象是資料中心案例之間及其內部的大規模事件輸入。

合併使用 IoT 中心和事件中樞的情況並不常見，除非前者處理裝置到雲端通訊，而後者處理後期事件輸入至及時處理引擎。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- 
- 


[event hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md 
[iot hub developer guide - security]: iot-hub-devguide.md#security 
[event hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md 
[event hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks 
[service bus quotas]: ../service-bus/service-bus-quotas.md 
[azure iot hub sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md 
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md 
[what is azure iot hub?]: iot-hub-what-is-iot-hub.md 

