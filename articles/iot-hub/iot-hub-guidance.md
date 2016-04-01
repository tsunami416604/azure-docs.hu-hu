<properties
 pageTitle="IoT 中樞解決方案指引 | Microsoft Azure"
 description="使用 Azure IoT 中樞來開發 IoT 解決方案的閘道器、裝置佈建及驗證的相關指引主題。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="dobett"/>

# 設計您的解決方案

本文提供如何在您的 IoT 解決方案中設計下列功能的指引：

- 裝置佈建
- 現場閘道器
- 裝置驗證

## 裝置佈建

IoT 解決方案會儲存個別裝置的相關資料，例如：

- 裝置身分識別和驗證金鑰
- 裝置硬體類型和版本
- 裝置狀態
- 軟體版本和功能
- 裝置命令歷程記錄

給定的 IoT 解決方案儲存的裝置資料取決於該解決方案的特定需求，但是解決方案至少必須儲存裝置身分識別和驗證金鑰。 IoT 中心包含 [識別登錄][lnk-devguide-identityregistry] ，可以儲存每個裝置識別碼、 驗證金鑰等狀態碼的值。 解決方案可以使用其他 Azure 服務 (例如資料表、blob 或 DocumentDB) 來儲存任何其他裝置資料。

*裝置佈建* 是初始裝置將資料加入至方案中的存放區的程序。 若要啟用新的裝置，以連接到您的中心，您必須加入新的裝置識別碼和金鑰 
[IoT 中心識別登錄][lnk-devguide-identityregistry]。 做為佈建程序的一部分，您可能需要初始化其他解決方案存放區中的裝置特定資料。

發行項 [IoT 中心裝置管理指導][lnk-device-management] 描述裝置佈建一些常見策略。  [IoT 中心識別登錄 Api][lnk-devguide-identityregistry] 可讓您整合您的佈建程序的 IoT 中心。

## 現場閘道器

在 IoT 解決方案中， *領域閘道* 位於您的裝置和 IoT 中心之間，而且通常位於接近您的裝置。 您的裝置會使用裝置所支援的通訊協定，直接與現場閘道器通訊，而現場閘道器會使用 IoT 中樞所支援的通訊協定來與 IoT 中樞通訊。 現場閘道器可以是專用的獨立裝置，或是在現有硬體部分上執行的軟體。

現場閘道器與簡單的流量路由裝置 (例如 NAT 裝置或防火牆) 不同，因為它通常會在解決方案內管理存取和資訊流程中扮演主動的角色。 例如，現場閘道器可以：

- 管理本機裝置。 例如，現場閘道器可以執行事件規則處理，並將命令傳送至裝置以回應特定遙測資料。
- 篩選或彙總遙測資料，之後再將資料轉送到 IoT 中樞。 這可以減少傳送到 IoT 中樞的資料量，還可能降低您的解決方案中的成本。
- 有助於佈建裝置。
- 轉換遙測資料，以協助您解決方案後端的處理。
- 執行通訊協定轉譯可讓裝置與 IoT 中樞進行通訊，即使它們未使用 IoT 中樞所支援的傳輸通訊協定亦然。

> [AZURE.NOTE] 雖然您通常會部署到裝置的本機領域閘道，在某些情況下您可能會部署 [通訊協定閘道][lnk-gateway] 定域機組中。

### 現場閘道器的類型

可以是欄位閘道 *透明* 或 *不透明*:

| &nbsp; | Transparent | Opaque |
|--------|-------------|--------|
| 儲存在 IoT 中樞身分識別登錄中的身分識別 | 所有連接的裝置 | 僅現場閘道器的身分識別 |
| IoT 中心可提供 [裝置身分識別反詐騙][lnk-devguide-antispoofing] | 是 | 否 |
| [節流和配額][lnk-throttles-quotas] | 套用至每個裝置 | 套用至領域閘道 |

### 其他考量

您可以使用 [Azure IoT 裝置 Sdk][lnk-device-sdks] 實作領域閘道。 某些裝置 SDK 提供的特定功能可協助您實作現場閘道器，例如能夠將來自多個裝置到通往 IoT 中樞的相同連線上之通訊多工處理的能力。 中所述 [IoT 中心開發人員指南-選擇通訊協定][lnk-devguide-protocol], ，您應該避免使用 HTTP/1 做為傳輸通訊協定領域閘道。

## 自訂裝置驗證

您可以使用 IoT 中心 [裝置身分識別登錄][lnk-devguide-identityregistry] 來設定每一裝置的安全性認證和存取控制。 不過，如果 IoT 解決方案已經有相當大的投資，自訂裝置身分識別登錄和/或驗證配置中，您可以整合現有的基礎結構的 IoT 中心藉由建立 *token service 的*。 如此一來，您可以在解決方案中使用其他 IoT 功能。

語彙基元服務是自訂的雲端服務，它會使用 IoT 中心 *共用存取原則* 與 **DeviceConnect** 權限可建立 *裝置範圍* 啟用裝置以連接到您的 IoT 中心的語彙基元。

  ![][img-tokenservice]

以下是權杖服務模式的主要步驟：

1. 建立 [IoT 中心共用存取原則][lnk-devguide-security] 與 **DeviceConnect** IoT 中心權限。 您可以建立此原則中的 [Azure 入口網站][lnk-portal] 或以程式設計的方式。 權杖服務會使用此原則簽署它所建立的權杖。
2. 當裝置需要存取 IoT 中樞時，它會向您的權杖服務要求已簽署的權杖。 裝置可以使用您的自訂裝置身分識別登錄/驗證配置來進行驗證，以判斷權杖服務用來建立權杖的裝置身分識別。
3. 為每個建立的權杖，權杖服務傳回 [IoT 中心開發人員指南安全性][lnk-devguide-security], ，並使用 `/devices/{deviceId}` 為 `resourceURI`, ，與 `deviceId` 做為裝置正在進行驗證。 權杖服務會使用共用存取原則來建構權杖。
4. 裝置直接透過 IoT 中心使用權杖。

> [AZURE.NOTE] 您可以使用.NET 類別 [SharedAccessSignatureBuilder][lnk-dotnet-sas] 或 Java 類別 [IotHubServiceSasToken][lnk-java-sas] 語彙基元服務中建立權杖。

權杖服務可以視需要設定權杖到期日。 權杖到期時，IoT 中樞會切斷裝置連線，裝置必須向權杖服務要求新權杖。 如果您使用過短的到期時間，這會增加裝置與權杖服務上的負載。

為了讓裝置連線至中樞，您仍必須將它加入 IoT 中樞裝置身分識別登錄，即使裝置使用權杖而不是裝置金鑰來連線。 因此，您可以繼續使用啟用或停用裝置中的身分識別的每一裝置的存取控制 [IoT 中心識別登錄][lnk-devguide-identityregistry] 裝置時使用的權杖進行驗證。 如此可減輕使用較長到期時間權杖的風險。

### 和自訂閘道器的比較

權杖服務模式為使用 IoT 中樞實作自訂身分識別登錄/驗證配置的建議方式，因為 IoT 中樞會繼續處理大部份的解決方案流量。 不過，有自訂驗證配置，因此密不可分的通訊協定的情況下 (例如， [TLS PSK][lnk-tls-psk])，此服務會處理所有流量 (*自訂閘道*) 是必要。 如需詳細資訊，請參閱 [通訊協定閘道][lnk-gateway] 主題。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html

