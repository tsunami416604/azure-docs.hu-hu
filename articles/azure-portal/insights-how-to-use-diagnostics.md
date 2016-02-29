<properties 
    pageTitle="啟用監視和診斷" 
    description="了解如何在 Azure 設定資源的診斷。" 
    authors="stepsic-microsoft-com" 
    manager="ronmart" 
    editor="" 
    services="azure-portal" 
    documentationCenter="na"/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2015" 
    ms.author="stepsic"/>

# 啟用監視和診斷

在 [Azure 入口網站](http://portal.azure.com), ，您可以設定內容豐富且經常產生的監視和診斷資料，您的資源。 您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式設計方式設定診斷。

在 Azure 中的診斷、監視和計量資料會被儲存到您所選擇的儲存體帳戶。 這可讓您使用任何工具來讀取資料，從儲存體總管到 Power BI 再到協力廠商工具。

## 建立資源的時機

大部分的服務可讓您啟用診斷功能，當您首次建立在 [Azure 入口網站](http://portal.azure.com)。

1. 移至 **新增** 並選擇您感興趣的資源。 

2. 選取 **選擇性組態**。
    ![診斷刀鋒伺服器](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. 選取 **診斷**, ，然後按一下 **上**。 您將必須選擇您要儲存診斷的儲存體帳戶。 將診斷傳送至儲存體帳戶時，您將需要支付儲存和交易的一般數據傳輸費用。

4. 按一下 [ **確定** ，並建立資源。 

## 變更現有資源的設定

如果您已經建立資源，而且您想要變更診斷設定 (例如，變更資料收集的層級)，您可以直接在 Azure 入口網站中執行此作業。

1. 移至資源，然後按一下 **設定** 命令。

2. 選取 **診斷**。

3.  **診斷** 分頁具有所有可能診斷和監視集合資料為該資源。 某些資源，也可以 **保留** 資料，以便將它清除從儲存體帳戶的原則。 
    ![儲存體診斷](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 選擇您的設定之後, 按一下 **儲存** 命令。 如果您是第一次啟用此選項，則顯示監視資料可能需要一點時間。 

### 虛擬機器的資料收集類別
在虛擬機器中，所有的計量與記錄都將以一分鐘的間隔來記錄，以便您隨時保有最新的機器相關資訊。

- **基本度量** : 您的虛擬機器，例如處理器與記憶體有關健康情況度量 
- **網路和 web 計量** : 有關您的網路連線與 web 服務的度量
- **.NET 計量** : 虛擬機器上執行的.NET 與 ASP.NET 應用程式相關的度量
- **SQL 標準** : 如果您正在執行 Microsoft SQL 服務，其效能計量
- **Windows 事件應用程式記錄檔** : 傳送至應用程式通道的 Windows 事件
- **Windows 事件系統記錄檔** : 傳送至系統通道的 Windows 事件。 這也包括所有事件 [Microsoft 反惡意程式碼](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)。 
- **Windows 事件安全性記錄檔** : 傳送至安全性通道的 Windows 事件
- **診斷基礎結構記錄檔** : 記錄有關診斷收集基礎結構
- **IIS 記錄檔** : 有關 IIS 伺服器的記錄

請注意，目前不支援特定的 Linux 散發套件，而且虛擬機器上必須安裝客體代理程式。

## 後續步驟

* [接收警示通知](insights-receive-alert-notifications.md) 每當發生操作事件或計量超過臨界值。
* [監視服務計量](insights-how-to-customize-monitoring.md) 並確認您的服務可用且可回應。
* [自動調整執行個體計數](insights-how-to-scale.md) 以確定服務規模根據需求。
* [監視應用程式效能](insights-perf-analytics.md) 如果您想要了解如何您的程式碼執行定域機組中。
* [檢視事件和稽核記錄檔](insights-debugging-with-events.md) 若要了解在您的服務內發生的所有項目。
* [追蹤服務健康狀況](insights-service-health.md) 找出 Azure 何時遭遇效能降低或服務中斷。 
 
