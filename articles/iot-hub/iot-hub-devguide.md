<properties
 pageTitle="IoT 中樞開發人員指南主題 | Microsoft Azure"
 description="Azure IoT 中樞開發人員指南包含 IoT 中樞端點、安全性、裝置身分識別登錄和傳訊"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT 中樞開發人員指南

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。

Azure IoT 中樞能夠︰

* 使用每一裝置的安全性認證和存取控制來保護通訊的安全，
* 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊，以及
* 透過最受歡迎的語言和平台的裝置程式庫，進行簡單的裝置連線。

本文件涵蓋下列領域：

- [端點](#endpoints)。 本節說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。
- [裝置識別登錄](#device-identity-registry)。 本節說明哪些資訊會儲存在每個 IoT 中樞的裝置身分識別登錄，以及如何加以存取和修改。
- [安全性](#security)。 本節說明用來授與 IoT 中樞功能存取權之裝置和雲端元件的安全性模型。
- [傳訊](#messaging)。 本節說明 IoT 中樞所公開的傳訊功能 (裝置到雲端和雲端到裝置)。
- [配額和節流](#throttling)。 本節摘要說明適用於您的 IoT 中樞的使用配額。

## 端點 <a id="endpoints"></a>

Azure IoT 中樞是多租用戶服務，且公開功能給各種執行者。 下圖顯示 IoT 中樞所公開的各種端點。

![IoT 中樞端點][img-endpoints]

以下是端點的說明︰

* **資源提供者**: IoT 中心資源提供者會公開 [Azure 資源管理員][lnk-arm] 允許建立 IoT 中心、 更新 IoT 中心屬性，以及刪除 IoT 中心的 Azure 訂用帳戶擁有者的介面。 IoT 中樞屬性掌管中樞層級安全性原則 (而非裝置層級存取控制。 請參閱 [存取控制](#accesscontrol)) 和定域機組對裝置和裝置對雲端的訊息傳遞功能選項。 資源提供者也可讓您 [匯出裝置身分識別](#importexport)。
* **裝置身分識別管理**: 每個 IoT 中心會公開一組管理裝置的身分識別的 HTTP REST 端點 (建立、 擷取、 更新和刪除)。 裝置識別用於裝置驗證和存取控制。 請參閱 [裝置身分識別登錄](#device-identity-registry) 如需詳細資訊。
* **裝置端點**: 佈建裝置身分識別登錄中的每個裝置 IoT 中心都會公開一組用來與該裝置進行通訊的端點。 在這兩個 HTTP 目前會公開這些端點和 [AMQP][lnk-amqp]:
    - *傳送至雲端裝置訊息*。 此端點用來傳送裝置到雲端的訊息。 如需詳細資訊，請參閱 [裝置至雲端傳訊](#d2c)。
    - *接收雲端-裝置訊息*。 裝置會使用此端點來接收已鎖定為目標的雲端到裝置訊息。 如需詳細資訊，請參閱 [定域機組裝置傳訊](#c2d)。
* **服務端點**: 每個 IoT 中心也會公開一組應用程式後端所使用的端點 (*服務*) 與您的裝置進行通訊。 這些端點有目前只公開使用 [AMQP][lnk-amqp] 通訊協定。
    - *接收裝置對雲端訊息*。 此端點會與相容 [Azure 事件中樞][lnk-event-hubs] 而且可用來讀取您的裝置所傳送的所有裝置對雲端訊息。 如需詳細資訊，請參閱 [裝置至雲端傳訊](#d2c)。
    - *定域機組對裝置的訊息傳送和接收傳遞通知*。 這些端點可讓您的應用程式後端傳送雲端到裝置的可靠訊息，以及接收對應的傳遞或到期通知。 如需詳細資訊，請參閱 [定域機組裝置傳訊](#c2d)。

 [IoT 中心 Api 和 Sdk][lnk-apis-sdks] 文章說明您可以存取這些端點的各種方式。

最後，值得注意所有的 IoT 中心端點會公開透過 [TLS][lnk-tls], ，而且沒有端點可曾公開加密/不安全的通道上。

### 如何讀取事件中心相容的端點 <a id="eventhubcompatible"></a>

當使用 [適用於.NET 的 Azure 服務匯流排 SDK](https://www.nuget.org/packages/WindowsAzure.ServiceBus) 或 [事件中樞-事件處理器主機][], ，您可以使用正確的權限的任何 IoT 中心連接字串，然後使用 `messages/events` 做為事件中樞名稱。

使用 Sdk (或產品整合的工作) 時不會察覺 IoT 中心，您必須擷取事件中心相容的端點與事件中心名稱中的 IoT 中心設定 [Azure 入口網站][]:

1. 在 [IoT 中心] 分頁中，按一下 [ **設定**, ，然後 **Messaging**,，
2. 在 **裝置對雲端設定** 區段中，您會發現 **事件中樞相容端點**, ，**事件中樞相容名稱**, ，和 **資料分割** 方塊。

    ![][img-eventhubcompatible]

> [AZURE.NOTE] SDK 有時候需要 **Hostname** 或 **命名空間** 值。 在此情況下，移除從 **事件中樞相容端點**。 比方說，如果您事件中心相容的端點 `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`, 、 **Hostname** 是 `iothub-ns-myiothub-1234.servicebus.windows.net`, ，和 **命名空間** 是 `iothub-ns-myiothub-1234`。

然後，您可以使用任何共用的存取安全性原則具有 **ServiceConnect** 連接到指定的事件中心權限。

如果您必須使用上述資訊建立事件中樞連接字串，您可以使用下列模式：

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


以下是可搭配 IoT 中樞使用的 SDK 和整合清單︰

* [Java 事件中樞用戶端](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 [spout 來源](https://github.com/apache/storm/tree/master/external/storm-eventhubs) GitHub 上。
* [Apache Spark 整合](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## 裝置身分識別登錄

每個 IoT 中心已經用來建立服務，例如包含之傳遞的定域機組對裝置訊息的佇列中的每一裝置的資源，可讓您存取面對裝置的端點中所述的裝置識別登錄 [存取控制](#accesscontrol) 一節。

總括來說，裝置身分識別登錄是支援 REST 的裝置身分識別資源集合。 下列各節將詳細說明裝置身分識別資源屬性，以及登錄允許對身分識別執行的作業。

> [AZURE.NOTE] 請參閱 [IoT 中心 Api 和 Sdk][lnk-apis-sdks] 如需詳細資訊的 HTTP 通訊協定和互動的裝置識別登錄的 Sdk。

### 裝置識別屬性 <a id="deviceproperties"></a>

裝置識別會以具有下列屬性的 JSON 文件表示。

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| deviceId | 必要，只能讀取更新 | ASCII 7 位元英數字元 + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| generationId | 必要，唯讀 | 中樞產生的區分大小寫字串最長為 128 個字元。 這用來區別具有相同的裝置 **deviceId** 當它們已被刪除並重新建立。 |
| etag | 必要，唯讀 | 字串，表示為每個裝置身分識別，弱式 etag [RFC7232][lnk-rfc7232]。|
| auth | 選用 | 包含驗證資訊和安全性資料的複合物件。 |
| auth.symkey | 選用 | 包含主要和次要金鑰 (以 base64 格式儲存) 的複合物件。 |
| status | 必要 | 可以是 **啟用** 或 **已停用**。 如果 **啟用**, ，裝置可以連線。 如果 **已停用**, ，此裝置無法存取任何面對裝置的端點。 |
| statusReason | 選用 | 128 個字元的字串，用來儲存裝置身分識別狀態的原因。 允許所有 UTF-8 字元。 |
| statusUpdateTime | 唯讀 | 上次狀態更新的日期和時間。 |
| connectionState | 唯讀 | **連接** 或 **已中斷連接**, ，表示裝置連接狀態的 IoT 中心檢視。 |
| connectionStateUpdatedTime | 唯讀 | 上次更新連線狀態的日期和時間。 |
| lastActivityTime  | 唯讀 | 裝置上次連接、接收或傳送訊息的日期和時間。 |

> [AZURE.NOTE] 連接狀態，僅能代表 IoT 中心的檢視連線的狀態。 此狀態的顯示與否取決於網路狀況和設定。

### 裝置身分識別作業

IoT 中樞裝置身分識別登錄會公開下列作業︰

* 建立裝置身分識別
* 更新裝置身分識別
* 依照 ID 擷取裝置身分識別別
* 刪除裝置身分識別
* 列出多達 1000 個識別

所有這些作業允許在使用中所指定的開放式並行存取 [RFC7232][lnk-rfc7232]。

> [AZURE.IMPORTANT] 要擷取中心的身分識別登錄中的所有識別的唯一方法是使用 [匯出](#importexport) 功能。

中樞的裝置識別登錄不包含任何應用程式中繼資料、 可以存取像是字典使用 **deviceId** 做為索引鍵，且易懂的查詢不支援。 任何 IoT 解決方案都有解決方案特定的存放區，其中包含應用程式特定的中繼資料，例如在智慧型建築解決方案中部署溫度感應器的場所。

### 停用裝置

您可以藉由更新停用裝置 **狀態** 登錄中的識別屬性。 通常這使用於兩個案例中：

1. 在佈建協調程序期間。 如需詳細資訊，請參閱 [Azure IoT 中心指導方針-佈建][lnk-guidance-provisioning]。
2. 基於任何原因，您認為裝置遭到入侵或暫時未經授權。

### 匯出裝置身分識別 <a id="importexport"></a>

匯出是長時間執行的工作，需要使用客戶提供的 Blob 容器來讀取和寫入裝置身分識別資料。

您可以匯出大量的裝置識別 IoT 中心的身分識別登錄中，從使用 IoT 中心資源提供者端點上的非同步作業 ([端點](#endpoints))。

以下是可能對匯出工作執行的作業︰

* 建立匯出工作
* 擷取執行中工作的狀態
* 取消執行中的工作

> [AZURE.NOTE] 每個集線器可以有只有一個工作在任何給定時間執行。

如需匯入和匯出 Api 的詳細資訊，請參閱 [Azure IoT 中心資源提供者 Api][lnk-resource-provider-apis]。

#### 作業

所有匯出工作都具有下列屬性︰

| 屬性 | 選項 | 說明 |
| -------- | ------- | ----------- |
| jobId | 由系統產生，建立時略過 | |
| creationTime | 由系統產生，建立時略過 | |
| endOfProcessingTime | 由系統產生，建立時略過 | |
| 類型 | 唯讀 | **匯出** |
| status | 由系統產生，建立時略過 | **加入佇列**, ，**啟動**, ，**完成**, ，**失敗** |
| progress | 由系統產生，建立時略過 | 完成百分比的整數值。 |
| outputBlobContainerURI | 所有工作都需要 | Blob 的 blob 容器的寫入權限的共用存取簽章 URI (請參閱 [建立並使用與 Blob 服務 SAS][lnk-createuse-sas] 和 [以 Java 建立共用存取簽章][lnk-sas-java])。 這用來輸出工作的狀態和結果。 |
| includeKeysInExport | 選用 | 如果 **true**, ，匯出輸出中包含索引鍵，否則將金鑰匯出為 **null**。 預設值是 **false**。 |
| failureReason | 由系統產生，建立時略過 | 如果狀態是 **失敗**, 包含原因的字串。 |

#### 匯出工作

匯出工作接受 Blob 共用存取簽章 URI 做為參數。 這會授與 blob 容器的寫入權限。 與用來輸出作業的結果。

輸出結果會寫入名為 `job_{job_id}_devices.txt` 的檔案中指定的 Blob 容器。 這個檔案包含序列化為 JSON，如中所指定的裝置識別 [裝置身分識別屬性](#deviceproperties)。 安全性內容會設定為 **null** 如果 **includeKeysInExport** 設為 **false**。

**範例**:

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## 安全性 <a id="security"></a>

本節說明用來保護 Azure IoT 中樞的選項。

### 存取控制 <a id="accesscontrol"></a>

IoT 中心會使用下列一組 *權限* 授與每個的 IoT 中心端點的存取權。 權限可根據功能限制 IoT 中樞的存取權。

* **RegistryRead**。 此權限可授與裝置身分識別登錄的讀取權限。 如需詳細資訊，請參閱 [裝置身分識別登錄](#device-identity-registry)。
* **RegistryReadWrite**。 授與裝置身分識別登錄的讀取和寫入權限。 如需詳細資訊，請參閱 [裝置身分識別登錄](#device-identity-registry)。
* **ServiceConnect**。 授與雲端服務面向通訊和監視端點的存取權。 例如，它授權雲端服務接收裝置到雲端的訊息、傳送雲端到裝置的訊息，以及擷取對應的傳遞通知。
* **DeviceConnect**。 授與裝置面向通訊端點的存取權。 例如，它會授與傳送裝置到雲端的訊息和接收雲端到裝置的訊息的權限。 此權限最常由裝置使用。

授與權限的方式如下：

* **中樞層級共用存取原則**。 *共用存取原則* 可以授與上一節中列出的權任何的限組合。 您可以定義原則中的 [Azure 入口網站][lnk-management-portal] 或以程式設計方式使用 [Azure IoT 中心資源提供者 Api][lnk-resource-provider-apis]。 新建立的 IoT 中樞有下列預設原則︰

    - *iothubowner*: 以所有權限原則
    - *服務*: 原則 **ServiceConnect** 權限
    - *裝置*: 原則 **DeviceConnect** 權限
    - *registryRead*: 原則 **RegistryRead** 權限
    - *registryReadWrite*: 原則 **RegistryRead** 和 **RegistryWrite** 權限

* **每一裝置的安全性認證**。 每個 IoT 中心包含 [裝置身分識別登錄](#device-identity-registry)。 這項登錄中的每個裝置，您可以設定授與的安全性認證 **DeviceConnect** 範圍設定為對應的裝置端點的權限。

**範例**。 在 IoT 解決方案中，通常會有裝置的管理元件，它使用 *registryReadWrite* 原則，以及事件處理器元件和執行階段裝置商務邏輯元件都使用 *服務* 原則。 個別裝置會使用 IoT 中樞身分識別登錄內儲存的認證進行連接。

IoT 中心安全性主題的指引，請參閱 [安全性] 區段中的 [Azure IoT 中心指引][lnk-guidance-security]。

### 驗證

Azure IoT 中樞可根據共用存取原則和裝置身分識別登錄安全性認證驗證權杖，以授與端點的存取權。

安全性認證 (例如對稱金鑰) 決不會在網路上傳送。

> [AZURE.NOTE] Azure IoT 中心資源提供者會透過受到保護您的 Azure 訂閱中的所有提供者時 [Azure 資源管理員][lnk-azure-resource-manager]。

#### 安全性權杖格式 <a id="tokenformat"></a>

安全性權杖具有下列格式：

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值：

| 值 | 說明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 簽章字串，格式為：`{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | 可使用此權杖存取之端點的 URI 前置詞 (由區段)。 例如，`/events` |
| {expiry} | 從新紀元時間 (Epoch) 1970 年 1 月 1日 00:00:00 UTC 時間至今秒數的 UTF8 字串。 |
| {URL-encoded-resourceURI} | URL 編碼的資源 URI (小寫) |
| {policyName} | 此權杖所參考的共用存取原則名稱。 在權杖參考裝置登錄認證的情況下不存在。 |

**請注意，前置詞上的**: 計算區段而不是字元的 URI 前置詞。 例如，`/a/b` 是 `/a/b/c` 的前置詞，而不是 `/a/bc` 的前置詞。

#### 通訊協定詳細規格

每個支援的通訊協定 (例如 AMQP 和 HTTP) 會以不同的方式傳輸權杖。

HTTP 驗證由包含有效的語彙基元中實作 **授權** 要求標頭。 查詢參數呼叫 **授權** 也會傳輸權杖。

當使用 [AMQP][lnk-amqp], ，IoT 中心支援 [SASL 純][lnk-sasl-plain] 和 [AMQP 宣告--安全性][lnk-cbs]。

在 AMQP 宣告式安全性中，標準指定如何傳輸這些權杖。

針對 SASL 純 **username** 可以是:

* 以中樞層級權杖而言，`{policyName}&commat;sas.root.{iothubName}`。
* 以裝置範圍權杖而言，`{deviceId}`。

在這兩種情況下，[密碼] 欄位包含語彙基元，如下所示 [權杖格式](#tokenformat) 一節。

> [AZURE.NOTE]  [Azure IoT 中心 Sdk][lnk-apis-sdks] 連接到服務時，自動產生的權杖。 在某些情況下，SDK 在其所支援的通訊協定或可用的驗證方法中會受限。 如需詳細資訊，請參閱 [Azure IoT 中心 Sdk][lnk-apis-sdks] 文件。

#### 相較於 CBS 的 SASL PLAIN

使用 SASL PLAIN 時，連接至 IoT 中樞的用戶端可為每個 TCP 連線使用單一權杖。 當權杖到期時，服務的 TCP 連線會中斷，並觸發重新連線。 此行為雖不會對應用程式後端元件造成問題，但是對裝置端應用程式極為不利，其原因如下︰

*  閘道器通常會代表許多裝置連線。 使用 SASL PLAIN 時，它們必須針對連接至 IoT 中樞的每個裝置不同的建立 TCP 連線。 這會大幅提高電力與網路資源的耗用量並增加每個裝置連線的延遲。
* 在每個權杖到期後，增加使用要重新連接的資源通常會影響資源受限的裝置。

### 設定中心層級認證的範圍

使用受限制的資源 URI 建立權杖，可以設定中心層級安全性原則的範圍。 例如，負責從裝置傳送裝置到雲端訊息的端點是 `/devices/{deviceId}/events`。 您也可以使用中樞層級的共用的存取原則與 **DeviceConnect** 權限登入的語彙基元是其 resourceURI `/devices/{deviceId}`, ，才可傳送代表裝置的裝置建立權杖 **deviceId**。

這項機制是類似於 [事件中心發行者原則][lnk-event-hubs-publisher-policy] 的 [安全性] 區段中所述，允許實作自訂驗證方法和 [Azure IoT 中心指引][lnk-guidance-security]。

## 訊息

IoT 中心提供訊息的基本類型，從應用程式後端通訊 (*服務* 或 *定域機組*) 至裝置，反之亦然。 我們會將這些功能為 [裝置對雲端](#d2c) 和 [定域機組對裝置](#c2d)。

IoT 中樞傳訊功能的核心屬性是訊息的可靠性和持久性。 這可在裝置端上恢復間歇性連線，以及在雲端恢復事件處理的負載尖峰。 IoT 中心會實作 *至少一次* 傳遞可保證 D2C 和 C2D 訊息。

IoT 中樞支援多個裝置面向通訊協定 (例如 AMQP 和 HTTP/1)。 為了支援完美的跨通訊協定互通性，IoT 中樞定義了所有裝置面向通訊協定均可支援的通用訊息格式。

### 訊息格式 <a id="messageformat"></a>

IoT 中樞訊息包含︰

* 一組 *系統內容*。 這些是由 IoT 中樞所解譯或設定的屬性。 這個集合是預先決定的。
* 一組 *應用程式屬性*。 這是應用程式可以定義的字串屬性字典，而不需將本文還原序列化即可加以存取。 IoT 中樞永遠不會修改這些屬性。
* 不透明的二進位主體。

請參閱 [IoT 中心 Api 和 Sdk][lnk-apis-sdks] 如需有關如何在不同的通訊協定編碼訊息。

這是 IoT 中樞訊息中的系統屬性集合。

| 屬性 | 說明 |
| -------- | ----------- |
| MessageId | 使用者可設定的訊息識別碼，通常用於要求-回覆模式。 格式︰ASCII 7 位元英數字元 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` 的區分大小寫字串 (最長為 128 個字元)。 |
| 序號 | IoT 中樞指派給每則 C2D 訊息的數字 (每一裝置-佇列特有)。 |
| 收件人 | 用於 [定域機組對裝置](#c2d) 訊息的目的地。|
| ExpiryTimeUtc | 訊息到期的日期和時間。 |
| EnqueuedTime | IoT 中樞收到訊息的時間。 |
| CorrelationId | 通常包含要求-回覆模式中要求的訊息識別碼的字串屬性。 |
| UserId | 用來指定訊息的來源。 當 IoT 中樞產生訊息時，它會設定為 `{iot hub name}`。 |
| Ack | 使用於 C2D 訊息，要求 IoT 中樞因為裝置取用訊息而產生意見反應訊息。 可能的值: **無** (預設值): 不產生任何回應訊息， **正**: 訊息已完成，如果收到的意見反應 **負**: 如果沒有依裝置而無法完成訊息過期 (或已達到最大傳遞計數) 收到的意見反應 **完整**: 正數和負數。 如需詳細資訊，請參閱 [訊息意見](#feedback)。 |
| ConnectionDeviceId | 由 IoT 中樞設定於 D2C 訊息上。 它包含 **deviceId** 傳送訊息的裝置。 |
| ConnectionDeviceGenerationId | 由 IoT 中樞設定於 D2C 訊息上。 它包含 **generationId** (為每個 [裝置身分識別屬性](#deviceproperties)) 傳送訊息的裝置。 |
| ConnectionAuthMethod | 由 IoT 中樞設定於 D2C 訊息上。 用來驗證傳送訊息之裝置的驗證方法的相關資訊。 如需詳細資訊，請參閱 [D2C 反詐騙](#antispoofing)。|

### 選擇通訊協定 <a id="amqpvshttp"></a>

Iot 中心同時支援 [AMQP][lnk-amqp] 和 HTTP/1 裝置端通訊的通訊協定。 以下是有關其用途的考量清單。

* **雲端-裝置模式**。 HTTP/1 沒有有效的方式可實作伺服器發送。 因此，使用 HTTP/1 時，裝置會向 IoT 中樞輪詢雲端到裝置的訊息。 這對於裝置和 IoT 中樞而言都非常沒有效率。 在目前的指導方針中，使用 HTTP/1 時，每個裝置的輪詢間隔會設定為小於每 25 分鐘一次。 另一方面，AMQP 在接收雲端到裝置的訊息時支援伺服器推送，而且能立即將訊息從 IoT 中樞推送至裝置。 如果傳遞延遲是一大考量，最好使用 AMQP 通訊協定。 另一方面，HTTP/1 也適用於幾乎不連接的裝置。
* **欄位閘道**。 指定相對於伺服器發送的 HTTP/1 限制，並不適用於 [欄位閘道案例][lnk-azure-gateway-guidance]。
* **低資源裝置**。 HTTP/1 程式庫比 AMQP 程式庫小得多。 因此，裝置擁有的資源很少 (例如，小於 1 Mb RAM)，HTTP/1 可能是唯一可用的通訊協定實作。
* **網路周遊**。 AMQP 標準會在連接埠 5672 上接聽。 這可能會導致對非 HTTP 通訊協定關閉的網路發生問題。
* **承載大小**。 AMQP 是明顯比 HTTP/1 更為精簡的二進位通訊協定。

總括來說，我們建議盡可能使用 AMQP，而如果裝置資源或網路設定不允許 AMQP，則使用 HTTP/1。 此外，使用 HTTP/1 時，每個裝置的輪詢頻率應設定為小於每 25 分鐘一次。 很顯然在開發期間，可接受更頻繁的輪詢頻率。

作為最後的考量，請務必查看 [Azure IoT 通訊協定閘道][lnk-azure-protocol-gateway], ，可讓您部署高效能 MQTT 閘道直接與 IoT 中心。 MQTT 支援伺服器發送 (因此能夠將 C2D 訊息立即傳遞到裝置)，而且適用於資源非常少的裝置。 這個方法的主要缺點是需要自我裝載和管理通訊協定閘道器。

### 若要定域機組的裝置 <a id="d2c"></a>

中的詳細 [端點](#endpoints) ] 區段中，裝置對雲端透過傳送郵件的裝置後端端點 (特別是 `/devices/{deviceId}/messages/events`)，以及透過服務後端端點接收 (`/messages/events`)，也就是與相容 [事件中樞][lnk-event-hubs]。 因此，您可以使用標準事件中樞整合和 SDK 來接收訊息。

實作裝置對雲端訊息的方式類似於以 IoT 中心 [事件中樞][lnk-event-hubs], ，正在比較類似事件中樞的 IoT 中心裝置對雲端訊息 *事件* 比 [服務匯流排][lnk-servicebus] *訊息*。

此實作具有下列含意：

* 類似於事件中樞 *事件*, ，裝置對雲端訊息都會持久保留在 IoT 中心會在 7 天內 (請參閱 [裝置對雲端的組態選項](#d2cconfiguration))。
* 裝置對雲端的訊息會在一組固定的磁碟分割，在建立時設定中分割 (請參閱 [裝置對雲端的組態選項](#d2cconfiguration))。
* 與事件中樞類似，讀取裝置到雲端訊息的用戶端必須處理資料分割和檢查點， 請參閱 [取用事件的事件中樞-][lnk-event-hubs-consuming-events]。
* 如同事件中樞的事件，裝置到雲端的訊息最大可能為 256 Kb，而且可分成數個批次以最佳化傳送。 批次最大可能為 256 Kb，最多包含 500 則訊息。

不過，IoT 中樞裝置到雲端與事件中樞之間還有一些重要差異：

* 中所述 [安全性](#security) ] 區段中，IoT 中心可讓每一裝置的驗證和存取控制，
* IoT 中心可讓數百萬個同時連線的裝置 (請參閱 [配額和節流](#throttling))，而事件中心是限制為 5000 AMQP 連線，每個命名空間。
* IoT 中心不允許任意資料分割使用 **PartitionKey**。 裝置對雲端訊息相對於其原始的分割 **deviceId**。
* IoT 中樞的調整方式稍微不同於事件中樞。 如需詳細資訊，請參閱 [調整 IoT 中心][lnk-guidance-scale]。

請注意，這並不表示 IoT 中樞可以完全代替事件中樞。 例如，在某些事件處理運算中，有可能需要在分析資料串流之前，根據不同屬性或欄位而重新分割事件。 在此情況下，事件中樞可用來解離串流處理管線的兩個部分。

如需有關如何使用裝置對雲端訊息的詳細資訊，請參閱 [IoT 中心 Api 和 Sdk][lnk-apis-sdks]。

#### 非遙測流量

在許多情況下，裝置傳送不僅遙測資料點的應用程式後端，但也 *互動式* 訊息與要求需要執行和處理從應用程式商務邏輯層。 具代表性的範例包括必須在後端觸發特定動作的重要警示，或命令的裝置回覆。

請參閱 [裝置對雲端處理][lnk-guidance-d2c-processing] 如需最佳的方式來處理這種訊息 「 IoT 中心指引 」 一節。

#### 裝置對雲端的組態選項 <a id="d2cconfiguration"></a>

IoT 中樞公開下列屬性以控制 D2C 傳訊。

* **資料分割計數**。 在建立時設定此屬性，以定義 D2C 事件擷取的資料分割數目。
* **保留時間**。 此屬性指定裝置到雲端訊息的保留時間。 預設值為 1 天，但可延長到 7 天。

而且，與事件中樞類似，IoT 中樞允許管理裝置到雲端的接收端點上的取用者群組。

您可以修改所有使用這些內容 [Azure 入口網站][lnk-management-portal], ，或以程式設計方式透過 [Azure IoT 中心資源提供者 Api][lnk-resource-provider-apis]。

#### 反詐騙屬性 <a id="antispoofing"></a>

為了避免裝置到雲端的訊息中出現裝置詐騙，IoT 中樞使用下列屬性在所有訊息上加上戳記：

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

前兩個包含 **deviceId** 和 **generationId** 的原始裝置中，為每個 [裝置身分識別屬性](#deviceproperties)。

 **ConnectionAuthMethod** 屬性包含具有下列屬性的 JSON 序列化物件:

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### 雲端至裝置 <a id="c2d"></a>

如上文中詳述 [端點](#endpoints) ] 區段中，定域機組對裝置透過傳送郵件的面向服務的端點 (`/messages/devicebound`)，並透過一系列的裝置向端點接收 (`/devices/{deviceId}/messages/devicebound`)。

每個定域機組對裝置的訊息為目標的單一裝置，設定 **至** 屬性 `/devices/{deviceId}/messages/devicebound`。

**重要**: 每個裝置佇列可以保留最多 50 個定域機組對裝置訊息。 嘗試將更多訊息傳送至相同的裝置會導致錯誤。

#### 訊息的生命週期 <a id="message lifecycle"></a>

為了實作 *至少一次* 傳遞保證，定域機組對裝置的訊息會保存在每一裝置的佇列，以及裝置必須明確地認可 *完成* IoT 中心，以便從佇列中移除的順序。 這保證連線失敗和裝置故障能夠恢復。

下圖顯示雲端到裝置訊息的生命週期狀態圖。

![雲端到裝置的訊息生命週期][img-lifecycle]

當服務所傳送訊息時，它會被視為 *佇列*。 當裝置想要 *接收* 訊息時，IoT 中心 *鎖定* 訊息 (狀態設為 **不可見**)，以便讓其他執行緒在相同的裝置，以開始接收其他訊息。 當裝置執行緒完成處理的裝置時，它會通知的 IoT 中心 *完成* 訊息。 它也可以決定 *拒絕* 訊息，將它傳送至 **停止傳送** 狀態，或 *放棄* 其中將訊息放回佇列中 (狀態 **佇列**)。

因為執行緒可能會失敗，且不會通知 IoT 中心處理訊息，訊息就會自動轉換從 **看不見** 回到 **佇列** 之後 *可見性 (或鎖定) 的逾時* (預設值: 1 分鐘)。 訊息可以切換 **佇列** 和 **看不見** 狀態最多指定的時間中指定數 *最大傳遞計數* IoT 中心上的屬性。 在該轉換次數之後，訊息將會自動停止傳送。 同樣地，訊息將會自動停止傳送的到期時間之後 (請參閱 [存留時間](#ttl))。

教學課程，說明定域機組對裝置的訊息，請參閱 [開始使用 Azure IoT 中心定域機組對裝置訊息][lnk-getstarted-c2d-tutorial]。 在不同的 Api 和 Sdk 的參考主題公開定域機組-裝置功能，請參閱 [IoT 中心 Api 和 Sdk][lnk-apis-sdks]。

> [AZURE.NOTE] 一般而言，每當訊息的遺失不會影響應用程式邏輯，應該完成定域機組對裝置的訊息。 這種情形可能會發生於許多不同的案例中，例如：訊息內容已成功保存在本機儲存體，或已成功執行作業，或訊息帶有遺失時不會影響應用程式功能的暫時性資訊。 有時候，對於長時間執行的工作，通常會在本機儲存體上保存工作描述之後，完成雲端到裝置的訊息，然後在工作進度的各個階段，以一或多則裝置到雲端的訊息通知應用程式後端。

#### 存留時間 <a id="ttl"></a>

每個雲端到裝置的訊息都有到期時間。 這可以明確地設定服務 (在 **ExpiryTimeUtc** 屬性)，或使用預設的 IoT 中心設定 *存留時間* 指定為 IoT 中心屬性。 請參閱 [定域機組對裝置的組態選項](#c2dconfiguration)。

#### 訊息的意見反應 <a id="feedback"></a>

傳送雲端到裝置的訊息時，服務可以要求傳遞每則訊息的意見反應 (關於該訊息的最終狀態)。 設定時 **Ack** 屬性 **正**, ，IoT 中心會產生回應訊息，才定域機組對裝置訊息已到達 **完成** 狀態。 藉由設定 **Ack** 屬性 **負**, ，IoT 中心產生回應訊息，如果且只有在定域機組對裝置的訊息抵達 **Deadletterd** 狀態。 藉由設定 **Ack** 屬性 **完整**, ，IoT 中心在任一情況下產生回應訊息。

中所述 [端點](#endpoints), ，透過服務後端端點傳送意見反應 (`/messages/servicebound/feedback`) 訊息。 對於具有相同 [訊息生命週期](#訊息生命週期) 的雲端到裝置的訊息，意見反應的接收語意相同。 可能的話，訊息意見反應會放入單一訊息中，其格式如下。

從意見反應端點擷取的每則訊息具有下列屬性。

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTime | 指出建立批次時的時間戳記。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性 | 說明 |
| -------- | ----------- |
| EnqueuedTimeUtc | 指出訊息的結果出現時的時間戳記。 例如，完成已裝置或訊息已到期。 |
| OriginalMessageId | **MessageId** 此意見反應資訊與相關的定域機組對裝置訊息。 |
| 說明 | 先前結果的字串值。 |
| DeviceId | **DeviceId** 的意見反應這個片段適用於雲端-裝置訊息的目標裝置。 |
| DeviceGenerationId | **DeviceGenerationId** 的意見反應這個片段適用於雲端-裝置訊息的目標裝置。 |

**重要**。 必須指定服務 **MessageId** 定域機組對裝置訊息，以便與原始訊息的相互關聯的意見反應。

**範例**。 以下是意見反應訊息的主體範例。

    [
        {
            "OriginalMessageId": "0987654321",
            "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### 定域機組對裝置的組態選項 <a id="c2dconfiguration"></a>

每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項。

| 屬性 | 說明 | 範圍和預設值 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | 雲端到裝置訊息的預設 TTL。 | ISO_8601 間隔高達 2D (最小為 1 分鐘)。 預設值︰1 小時。 |
| maxDeliveryCount | 每個裝置佇列的雲端到裝置最大傳遞計數。 | 1 到 100。 預設值：10 |
| feedback.ttlAsIso8601 | 保留服務繫結的意見反應訊息。 | ISO_8601 間隔高達 2D (最小為 1 分鐘)。 預設值︰1 小時。 |
| feedback.maxDeliveryCount | 意見反應佇列的最大傳遞計數。 | 1 到 100。 預設值 = 100。 |

## 配額和節流 <a id="throttling"></a>

每個 Azure 訂用帳戶最多可以有 10 個 IoT 中樞。

每個 IoT 中心將會佈建特定 SKU 的單位數目 (如需詳細資訊，請參閱 [Azure IoT 中心定價][lnk-pricing])。 SKU 和單位數目可決定可以傳送的每日訊息配額上限以及身分識別登錄中的裝置身分識別數目上限。 同時連接的裝置數目受限於登錄中的識別數目。

它們也決定 IoT 中樞對作業強制執行的節流限制。

### 裝置身分識別登錄配額

IoT 中樞允許每天每個單位 (不管 SKU) 最多有 1100 次裝置更新 (建立、更新、刪除)。

### 作業節流

作業節流是在分鐘範圍內套用的速率限制，主要是為了避免不當使用。 IoT 中樞會試著儘可能避免傳回錯誤，但如果違反節流太久，就會開始傳回例外狀況。

以下是強制執行的節流清單。 個別中心的值如下。

| 節流 | 每個中心的值 |
| -------- | ------------- |
| 身分識別登錄作業 (建立、擷取、列出、更新、刪除)，個別或大量匯入/匯出 | 100/分鐘/單位，最高 5000/分鐘 |
| 裝置連線 | 100/秒/單位 |
| D2C 傳送 | 2000/分鐘/單位 (適用於 S2)，60/分鐘/單位 (適用於 S1)。 最小值為 100/秒。 |
| C2D 作業 (傳送、接收、意見反應) | 100/分鐘/單位 |

**請注意**。 不論何時，都可以藉由增加 IoT 中樞佈建的單位來提高配額或節流限制。

## 後續步驟

既然您已了解開發 IoT 中樞的概觀，請遵循下列連結深入了解：

- [開始使用 IoT 中樞 (教學課程)][lnk-get-started]
- [作業系統平台與硬體相容性][lnk-compatibility]
- [Azure IoT 開發人員中心][lnk-iotdev]
- [規劃 IoT 實作][lnk-guidance]

[Event Hubs - Event Processor Host]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure portal]: https://portal.azure.com

[img-summary]: ./media/iot-hub-devguide/summary.png
[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

