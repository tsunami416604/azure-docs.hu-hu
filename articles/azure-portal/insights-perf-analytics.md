<properties 
    pageTitle="監視 Azure Web 應用程式效能" 
    description="圖表載入和回應時間、相依性資訊以及設定效能警示。" 
    services="azure-portal"
    documentationCenter="na"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2015" 
    ms.author="awills"/>


# 監視 Azure Web 應用程式效能



Azure 運用*擴充功能*支援「應用程式」效能監視功能 (或 *APM*)。 這些擴充功能已安裝在應用程式中，可收集資料並回報給監視服務。

Application Insights 和 New Relic 是兩個可用的效能監視延伸模組。 若要使用這些模組，您需要在執行階段安裝代理程式。 藉由 Application Insights，還有可用 SDK 建置程式碼的選項。 SDK 可讓您撰寫程式碼來監視應用程式的詳細使用狀況和效能。

## 啟用擴充功能

1. 按一下 [**瀏覽**]，然後選取您要檢測的 Web 應用程式或虛擬機器。

2. 加入 Application Insights 或 New Relic 延伸模組。

    如果您要檢測 Web 應用程式：

![設定、延伸模組、加入、Application Insights](./media/insights-perf-analytics/05-extend.png)

如果您使用的是虛擬機器：

![按一下 ](./media/insights-perf-analytics/10-vm1.png)

### Application Insights 的選項：請使用 SDK 重建

Application Insights 可以提供更詳細的遙測，方法是將 SDK 安裝至您的 App。

在 Visual Studio 中，將 Application Insights SDK 加入專案。

![以滑鼠右鍵按一下 Web 專案，然後選擇 ](./media/insights-perf-analytics/03-add.png)

系統要求您登入時，請使用 Azure 帳戶的認證。

您可以在開發電腦中執行應用程式來測試遙測，或是直接繼續執行並將重新發佈遙測。



## 探索資料

使用您的應用程式一段時間以產生一些遙測。

1. 接著，從您的 Web 應用程式或虛擬機器刀鋒視窗，您將會看到已安裝的擴充功能
2. 
![按一下 ](./media/insights-perf-analytics/06-overview.png)

您也可以按一下 [**瀏覽**] 直接移至 Application Insights 元件，或您使用的 New Relic 帳戶。


- 

![在 Application Insights 的概觀刀鋒視窗中，按一下 ](./media/insights-perf-analytics/07-dependency.png)

- 逐步深入以查看個別要求：

![在方格中，按一下相依性，查看相關的要求。](./media/insights-perf-analytics/08-requests.png)

- 以下範例說明在 SQL 相依性中花費的時間，該相依性中包括 SQL 的呼叫數和相關的統計資料 (例如平均持續時間和標準差)。

![](./media/insights-perf-analytics/01-example.png)



## 後續步驟

* 
* 
* 
* 
* 





