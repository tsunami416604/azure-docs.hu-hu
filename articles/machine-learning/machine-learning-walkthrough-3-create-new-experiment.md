<properties
    pageTitle="步驟 3：建立新的 Machine Learning 實驗 | Microsoft Azure"
    description="開發預測解決方案逐步解說的步驟 3：在 Azure Machine Learning Studio 中建立新的定型實驗。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015" 
    ms.author="garye"/>


# 逐步解說步驟 3：建立新的 Azure Machine Learning 實驗

這是逐步解說的第三個步驟 [開發使用 Azure ML 的預測方案](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立機器學習服務工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.  **建立新實驗**
4.  [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 Web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  [存取 Web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------

我們需要在 ML Studio 中建立新的實驗，並讓它使用我們上傳的資料集。  

1.  在 ML Studio 中，按一下 [ **+ 新增** 視窗的底部。
2.  選取 **實驗**, ，然後選取 [空白實驗]。 選取畫布頂端的預設實驗名稱，然後將它重新命名為有意義的名稱。

    > [AZURE.TIP] 最好填滿 **摘要** 和 **描述** 中為實驗 **屬性** 窗格。 這些屬性會提供記錄實驗的機會，以便稍後查看它的人員能了解您的目標和方法。

3.  在實驗畫布左側的模組調色盤中展開 **儲存的資料集**。
4.  找出您在下建立資料集 **我的資料集** 並將它拖曳到畫布。 您也可以藉由輸入中的名稱來尋找資料集 **搜尋** 在調色盤上方的方塊。  

##準備資料
您可以檢視前 100 個資料列的資料和整個資料集部分統計資訊，按一下 [資料集的輸出連接埠並選取 **檢視結果**。 請注意，ML Studio 已識別每個資料行的資料類型。 它也會為資料行指定一般標題，因為資料檔案並未隨附資料行標題。  

資料行標題並非必要，但這些標題可讓您更容易使用模型中的資料。 此外，當我們最終在 Web 服務中發佈此模型時，標題將有助於服務的使用者識別資料行。  

我們可以新增使用 [中繼資料編輯器] 和 [中繼資料編輯器] 模組的資料行標題。
[中繼資料編輯器] 和 [中繼資料編輯器] 模組用來變更資料集相關聯的中繼資料。 透過這種方式，可提供資料行標題更易記的名稱。 若要這樣做，我們將引導 [中繼資料編輯器] [中繼資料編輯器] 來對所有資料行，然後提供 [加入資料行名稱的清單。

1.  在模組調色盤中，輸入 「 中繼資料 」 **搜尋** 方塊。 您會看到 [中繼資料編輯器] 和 [中繼資料編輯器] 中的模組清單。
2.  按一下和拖曳到畫布上的 [中繼資料編輯器] 和 [中繼資料編輯器] 模組，把它放在資料集下方。
3.  資料集連接到 [中繼資料編輯器] [中繼資料編輯器]: 按一下 [資料集的輸出連接埠，拖曳至 [輸入連接埠的 [中繼資料編輯器] [中繼資料-編輯器]，然後放開滑鼠按鈕。 即使您在畫布上移動資料集或模組，這兩者仍會保持連線。
4.  使用 [中繼資料編輯器] 和 [中繼資料編輯器] 仍處於選取狀態，在 **屬性** 窗格右邊的畫布上，按一下 [ **啟動資料行選取器**。
5.  在 **選取資料行** 對話方塊上，將 **開始於** [所有資料行] 欄位。
6.  下方的資料列 **開始於** 可讓您包含或排除特定的資料行的 [中繼資料編輯器] [中繼資料編輯器] 來修改。 由於我們想要修改所有資料行，因此請按一下資料列右側的減號 ("-") 刪除這個資料列。 對話方塊應該看起來像這樣:
    ![所選取的所有資料行的資料行選取器][4]
7.  按一下 [ **確定** 核取記號。
8.  回到 **屬性** 窗格中，尋找 **新資料行名稱** 參數。 在這個欄位中，輸入資料集中 21 個資料行的名稱清單，並以逗號加以分隔，且依資料行順序排序。 您可以從 UCI 網站上的資料集文件中取得資料行名稱，或為求簡便，您可以複製並貼上下列內容：  

<!-- try the same thing without upper-case
        Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
-->

    status of checking account, duration in months, credit history, purpose, credit amount, savings account/bond, present employment since, installment rate in percentage of disposable income, personal status and sex, other debtors, present residence since, property, age in years, other installment plans, housing, number of existing credits, job, number of people providing maintenance for, telephone, foreign worker, credit risk  

[屬性] 窗格將如下所示：

![Properties for Metadata Editor][1]

> [AZURE.TIP] 如果您想要確認資料行標題，執行實驗 (按一下 **執行** 實驗畫布下方)，按一下 [中繼資料編輯器] 和 [中繼資料編輯器] 模組的輸出連接埠，然後選取 **檢視結果**。 您可以用相同方式檢視任何模組的輸出，以檢視資料在實驗中的執行進度。

實驗此時看起來應如下所示：  

![Adding Metadata Editor][2]

##建立訓練和測試資料集
實驗的下一個步驟，是產生將用來訓練及測試模型的個別資料集。 若要這樣做，我們使用 [分割] [分割] 模組。  

1.  找出 [分割] [分割] 模組，將它拖曳到畫布上，並將它連接到最後一個 [中繼資料編輯器] 和 [中繼資料編輯器] 模組。
2.  根據預設，分割率會是 0.5， **Randomized split** 參數設定。 這表示隨機資料有一半會透過 [分割] [分割] 模組中的一個連接埠和另一半的輸出。 您可以調整這些項目，並將 **隨機種子** 參數，以變更訓練與評分資料之間的分割。 在此範例中，我們會將其保留為原狀。
    > [AZURE.TIP] 分割率基本上會決定有多少資料會透過左側的輸出連接埠的輸出。 例如，如果您將分割率設為 0.7，則會有 70% 的資料透過左側連接埠輸出，30% 透過右側連接埠輸出。  

我們可以使用 [分割] [分割] 模組的輸出，但要讓我們選擇以左側的輸出作為訓練資料，右側輸出作為評分資料。  

如 UCI 網站上所說明，將高信用風險誤判為低風險的成本，會比將低信用風險誤判為高風險的成本多出 5 倍。 為了說明這一點，我們將產生新資料集以反映此成本函數。 在此新資料集中，每個高風險範例都會複寫 5 次，而每個低風險範例則不複寫。   

我們可以使用 R 程式碼來執行此複寫：  

1.  尋找並將 [執行 R 指令碼] [執行-r-指令碼] 模組拖曳到實驗畫布上，[分割] [分割] 模組的左側的輸出埠連接到 [執行 R 指令碼] [執行-r-指令碼] 模組的第一個輸入連接埠 ("Dataset1")。
2.  在 **屬性** ] 窗格中，刪除中的預設文字 **R 指令碼** 參數，然後輸入此指令碼:

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")


我們需要執行此一相同複寫作業每個輸出的 [分割] [分割] 模組，訓練和評分資料具有相同的成本調整。

1.  以滑鼠右鍵按一下 [執行 R 指令碼] [執行-r-指令碼] 模組，然後選取 **複製**。
2.  以滑鼠右鍵按一下實驗畫布，然後選取 **貼上**。
3.  此 [執行 R 指令碼] [執行-r-指令碼] 模組的第一個輸入的埠連接到 [分割] [分割] 模組的右側輸出連接埠。  

> [AZURE.TIP] 執行 R 指令碼模組的複本會包含與原始模組相同的指令碼。 當您在畫布上複製並貼上模組時，複本將會保留原本的所有屬性。  
>
實驗此時看起來會如下所示：

![Adding Split module and R scripts][3]

如需有關您的實驗中使用 R 指令碼的詳細資訊，請參閱 [擴展您的經驗，透過 R](machine-learning-extend-your-experiment-with-r.md)。

**下一步: [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

