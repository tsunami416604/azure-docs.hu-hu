<properties
 pageTitle="IoT 中樞 HA 和 DR | Microsoft Azure"
 description="描述可使用災害復原功能協助建立高可用性災解決方案的功能。"
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

# IoT 中樞高可用性和災害復原

作為 Azure 服務，IoT 中心提供高可用性 (HA)，在 Azure 區域層級使用備援，且解決方案不需任何額外的工作。 此外，Azure 會提供一些可協助建立解決方案的功能，必要時也提供災害復原 (DR) 功能或跨區域的可用性。 若要提供全域、跨區域的高可用性給裝置或使用者，必須設計及準備解決方案以善用這些功能。 發行項 [Azure 業務持續性技術指引][] 描述的商務持續性和 DR Azure 的內建功能。  [嚴重損壞修復及高可用性 Azure 應用程式][] 白皮書將提供的策略以達到 HADR 的 Azure 應用程式架構指南。

## 使用 IoT 中心的區域容錯移轉

完整的處理方式的 IoT 解決方案的部署拓撲是在本節中，範圍之外，但基於高可用性和災害復原目的，我們會考慮 *地區性容錯移轉* 部署模型。

在區域的容錯移轉模式下，解決方案的後端執行主要是放在一個資料中心位置，而將容錯移轉的情況，其他的資料中心區域中部署額外的 IoT 中心和後結束，萬一主要資料中心的 IoT 中心將會降低服務中斷或因故中斷裝置的網路連線到主要資料中心。 每當無法連接主要閘道器時，裝置會使用次要服務端點。 使用跨區域容錯移轉功能時，可改善解決方案的可用性，勝過單一區域的高可用性。

在較高層級，為了使用 IoT 中心實作區域容錯移轉模型，您需要下列內容。

* **次要的 IoT 中心與裝置的路由邏輯**︰ 在主要區域中的服務中斷，裝置必須啟動連線到您的次要區域。 由於大部分服務狀態感知的本質，解決方案的系統管理員通常會觸發區域間的容錯移轉程序。 通訊的新端點至裝置，同時保有控制程序中，最佳的作法是定期檢查 *指引* 服務，讓目前作用中的端點。 這裡的指引服務可以是簡單的 web 應用程式，複寫且保持連線到使用 DNS 重新導向技術 (例如，使用 [Azure 流量管理員][])。
* **識別登錄複寫** -為了成為可用狀態，次要的 IoT 中心必須包含可以連接至方案的所有裝置識別身分。 解決方案應該保留裝置身分識別的異地複寫備份，並在切換裝置的作用中端點之前將其上傳至次要 IoT 中樞。 IoT 中樞的裝置身分識別匯出功能在此內容中非常有用。 如需詳細資訊，請參閱 [IoT 中心開發人員指南識別登錄][]。
* **合併邏輯** -主要區域，可供使用時的狀態和已建立次要網站中的資料必須移轉所有回到主要區域。 這大多與裝置身分識別和應用程式中繼資料相關，必須與主要 IoT 中心合併，也可能要與主要區域中的其他應用程式專屬存放區合併。 若要簡化這個步驟，通常建議使用等冪作業。 這樣可以將副作用降到最低，不只包括來自最終一致的事件分佈的副作用，也包括來自事件的重複項目或失序傳遞的副作用。 此外，應用程式邏輯應該設計為能夠容忍潛在的不一致或「稍微」過期的狀態。 這是因為系統需要額外的時間來根據復原點目標 (RPO)「療癒」。 下列文章提供有關此主題提供更多指導 ︰ [保全 ︰ 的恢復功能雲端架構指引][]。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中心 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][]

[Azure Business Continuity Technical Guidance]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Disaster Recovery and High Availability for Azure Applications]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: Guidance for Resilient Cloud Architectures]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md


