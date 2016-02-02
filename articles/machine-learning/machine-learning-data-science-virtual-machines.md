<properties
    pageTitle="Azure 中的資料科學虛擬機器 | Microsoft Azure"
    description="設定資料科學虛擬機器"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="paulettm" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/23/2015"
    ms.author="mohabib;xibingao;bradsev" />


# Azure 中的資料科學虛擬機器

此功能表會連結至說明如何設定 Cortana 分析程序 (CAP) 所用的各種資料科學環境的主題。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

有數種類型的 Azure 虛擬機器可佈建並設定來做為雲端架構資料科學環境的一部分。 決定要使用哪一個虛擬機器類別，取決於要使用機器學習服務進行模型化的資料類型和數量，以及該資料在雲端中的目標目的地。


* 如需進行這項決策時要考量的問題的指引，請參閱 [計劃您 Azure Machine Learning 資料科學環境](machine-learning-data-science-plan-your-environment.md)。
* 類別目錄的一些案例執行時，可能會遇到的進階分析，請參閱 [進階分析程序和技術在 Azure Machine Learning 中的案例](../machine-learning-data-science-plan-sample-scenarios.md)

所提供的指示會說明如何設定 Azure VM 和含有 SQL 服務的 Azure VM，以做為 IPython Notebook 伺服器。 Windows 虛擬機器是使用支援工具 (例如，IPython Notebook、Azure 儲存體總管及 AzCopy)，以及其他對於資料科學專案非常實用的公用程式來設定。 例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure 儲存體，或者從儲存體將資料下載到本機電腦。 提供兩組指示：

* [Azure 虛擬機器設定為 IPython Notebook 伺服器供進階分析](machine-learning-data-science-setup-virtual-machine.md) 示範如何使用 IPython Notebook 和其他用來進行資料科學的情況下，在其中一種 SQL 以外的 Azure 儲存體可以用來儲存資料的工具佈建 Azure 虛擬機器。

* [將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析](machine-learning-data-science-setup-sql-server-virtual-machine.md) 示範如何使用 IPython Notebook 和其他工具，用來進行資料科學的所在 SQL 資料庫可以用來儲存資料的情況下佈建 Azure SQL Server 虛擬機器。

在完成佈建並設定之後，這些虛擬機器就已經準備好用來做為 IPython Notebook 伺服器，以用於進行資料探索和處理，以及其他需要與 Azure Machine Learning 和 Cortana 分析程序 (CAP) 一起使用的工作。 資料科學程序的下一個步驟中的對應 [CAP 學習途徑](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) ，而且可能包括移到 SQL Server 或 HDInsight，資料處理和取樣，並在與 Azure Machine Learning 從資料學習的準備步驟。

> [AZURE.NOTE] Azure 虛擬機器的定價策略是「**只針對您使用的項目進行付費**」。 若要確定，您不會被計費不使用虛擬機器時，它必須為 **已停止 (取消配置)** 狀態從 [Azure 傳統入口網站](http://manage.windowsazure.com/)。 如需逐步指示或如何取消配置虛擬機器，請參閱  [關閉和解除配置非使用中的虛擬機器](machine-learning-data-science-setup-virtual-machine.md#shutdown)





