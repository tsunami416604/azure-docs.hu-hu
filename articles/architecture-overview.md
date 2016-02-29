<properties 
    pageTitle="Microsoft Azure 上的應用程式架構" 
    description="涵蓋常見設計模式的架構概觀。" 
    services="" 
    documentationCenter="" 
    authors="Rboucher" 
    manager="jwhit" 
    editor="mattshel"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/16/2015" 
    ms.author="robb"/>

#Microsoft Azure 上的應用程式架構
可用於建置使用 Microsoft Azure 應用程式的資源。 這包括可協助您繪製圖表，以視覺化方式描述軟體系統的工具。 



##Azure 架構設計模式
Microsoft 發行了一系列的架構設計模式，可協助您撰寫自己的自訂設計。 該模式旨在做為簡明扼要的架構指南，可井然有序地組合在一起，提供如何以最佳方式運用 Microsoft Azure 平台解決您的組織商務需求之指導方針。


[概觀](../azure-architectures-cpif-overview/) - 
[混合式網路功能](../azure-architectures-cpif-infrastructure-hybrid-networking/) - 
[異地批次處理](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) -
[多站台資料層](../azure-architectures-cpif-foundation-multi-site-data-tier/) -
[全域負載平衡的 web 層](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) -
[Azure 搜尋服務層](../azure-architectures-cpif-foundation-azure-search-tier/)
 
每個模式包含
 
- 服務描述
- 利用模式所需的 Azure 服務清單
- 架構圖表
- 架構相依性
- 可能會影響模式的設計限制或考量
- 介面和端點
- 反向模式
- 重要的高階架構考量包括可用性和復原、使用之服務的複合 SLA、規模和效能，以及成本和作業考量。

![Azure 架構設計模式](./media/architecture-overview/AzureArchPatterns.jpg)


##設計模式海報
Microsoft 典範與實例已出版 [雲端設計模式](http://msdn.microsoft.com/library/dn568099.aspx) 可在 MSDN 和 PDF 下載中。 另外，也有列出所有模式的大型海報。 

![Patterns and Practices 雲端模式海報](./media/architecture-overview/PnPPatternPosterThumb.jpg)



##Microsoft 架構憑證課程

Microsoft 剛剛推出新的架構課程，支援 Microsoft 憑證測驗 70-534。 它有 [免費提供 EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)。  它會使用新 [3D 藍圖 Visio 範本](#3d-blueprint-visio-template)。 

![Microsoft 架構憑證課程](./media/architecture-overview/EDXCourse.png)


##Microsoft 架構藍圖

Microsoft 發佈一組的高層級 [架構藍圖](http://aka.ms/azblueprints) 示範如何建置使用 Microsoft 產品的系統特定的類型。 

每個藍圖都包含

- 一般 **2D Visio** 2003年為基礎的檔案，您可以下載並修改 
- 彩色 **3D 遠近景深 PDF** 藍圖介紹較少技術的對象的檔案
- **視訊** 的逐步解說的 3D 版本。 

將藍圖使用 [雲端和企業符號組](#symbol-and-icon-sets)。   

![Microsoft 架構藍圖 3D 圖表](./media/architecture-overview/BluePrintThumb.jpg)



##3D 藍圖 Visio 範本

3D 版本 [Microsoft 架構藍圖](http://aka.ms/azblueprints) 一開始所建立的非 Microsoft 工具。 新的 Visio 2013 (和更新版本) 範本隨附於 2015 年 8 月 5 日的 [Microsoft 架構認證課程分佈在 EDX.ORG](#microsoft-architecture-certification-course)。 

此範本在課程外也可供使用。 

- [檢視訓練影片](http://aka.ms/3dBlueprintTemplateVideo) 第一個讓您知道它可以執行的動作   
- 下載 [Microsoft 3d 藍圖 Visio 範本](http://aka.ms/3DBlueprintTemplate)
- 下載 [雲端和企業符號](#drawing-symbol-and-icon-sets) 3D 範本使用。 

電子郵件 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 未回答的教育訓練資料，或提供意見反應的特定問題。 可用性是此範本的主要目標之一，請讓我們知道我們的優點及缺失。

![Microsoft 3D 藍圖 Visio 範本](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##繪製符號和圖示集 

[檢視 Visio 和訓練影片的符號](http://aka.ms/CnESymbolsVideo) 然後 [下載雲端和企業符號集](http://aka.ms/CnESymbols) 來協助建立可說明 Azure、 Windows Server、 SQL Server 和其他技術資料。 您可以將符號用在架構圖表、訓練教材、簡報、資料工作表、資訊圖、白皮書，甚至是協力廠商書籍 (如果書籍訓練人員使用 Microsoft 產品)。 不過，它們不一定用於使用者介面。

CnE 符號都為 Visio 和 PNG 格式。 集合中包含如何在 PowerPoint 中使用 PNG 的其他指示。 

符號集每季出貨，並隨著新服務的發行進行更新。 

Microsoft Office 和相關的技術的其他符號都在 [Microsoft Office Visio 樣板](http://www.microsoft.com/en-us/download/details.aspx?id=35772), ，但是不適合架構圖表，但是 CnE 集是。   

**意見反應:** 如果您已使用但是 CnE 符號，填寫簡短的 5 個問題 [調查](http://aka.ms/azuresymbolssurveyv2) 我們的電子郵件或 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) 的特定問題和問題。 我們想要知道您的想法，包括正面意見回應，讓我們知道可以繼續投入時間。 

![雲端和企業符號/圖示集](./media/architecture-overview/CnESymbols.png)


##架構資訊圖

Microsoft 出版了數個與架構相關的海報/資訊圖。 其中包括 [建置真實世界雲端應用程式](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) 和 [與雲端服務調整](http://azure.microsoft.com/documentation/infographics/cloud-services/) 。 

![Azure 架構資訊圖](./media/architecture-overview/AzureArchInfographicThumb.jpg)

