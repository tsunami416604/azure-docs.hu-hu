<properties
    pageTitle="適用於機器學習 Web 服務的 Excel 增益集 | Microsoft Azure"
    description="如何在 Excel 中直接使用 Azure Machine Learning Web 服務，而不需要撰寫任何程式碼。"
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="paulettm"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="11/23/2015"
    ms.author="tedway;garye" />

# 適用於 Azure Machine Learning Web 服務的 Excel 增益集

Excel 可以讓您直接輕鬆呼叫 Web 服務，而不需要撰寫任何程式碼。

## 使用活頁簿中現有 Web 服務的步驟

1. 開啟 [範例 Excel 檔案](http://aka.ms/amlexcel-sample-2), ，其中包含的 Excel 增益集和乘客 Titanic 上的相關資料。
2. 按一下 Web 服務加以選擇，在此範例中為「鐵達尼號存活者預測工具 (Excel 增益集範例) [分數]」。

    ![選取 Web 服務][01]

3. 這樣會帶您到 **預測** 一節。  這個活頁簿已經包含資料的範例，但為空白的活頁簿可能也在 Excel 中，選取儲存格並按一下 **使用範例資料**。
4. 選取含有標頭的資料並按一下輸入資料範圍圖示。  請確定已核取 [我的資料有標頭] 方塊。
5. 在 **輸出**, ，輸入您想要的輸出，例如的資料格數目此觸的 "H1"。
6. 按一下 [ **預測**。

    ![預測區段][02]

## 加入新的 Web 服務的步驟

1. 發佈 web 服務 ([本頁](machine-learning-walkthrough-5-publish-web-service.md) 說明如何這麼做)，或使用現有的 web 服務。
2. 在 Excel 中，請移至 **Web 服務** 區段 (如果您是在 **預測** 區段中，按一下以移至 web 服務清單中向後鍵)。

    ![移至 web 服務選取][03]

3. 按一下 [ **加入 Web 服務**。
4. 在 Machine Learning Studio 中，按一下 [ **WEB 服務** 在左窗格中，區段，然後再選取 [web 服務。

    ![Studio 選取 Web 服務][04]

5. 複製 web 服務的 API 金鑰。

    ![Studio API 金鑰][05]

6. 將 API 金鑰貼到 Excel 增益集的文字方塊中標示為 **API 金鑰**。
7. 在 **儀表板** web 服務] 索引標籤上，按一下 **要求/回應** 連結。
8. 尋找 **OData 端點位址** 一節。  將 URL 複製並貼上，在文字方塊中標示為 **URL**。
9. 按一下 [ **新增**。

    ![URL 和 API 金鑰][06]

10. 若要使用 Web 服務，請遵循上述指示「使用現有 Web 服務的步驟」。

## 共用活頁簿

如果您儲存您的活頁簿，則您加入的 Web 服務之 API 金鑰也會一併儲存。  這表示您應該只與您信任的人共用活頁簿。

任何發問或下面我們 [論壇](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)。

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png


