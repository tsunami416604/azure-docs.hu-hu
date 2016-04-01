<properties
   pageTitle="使用 Azure Machine Learning 分析資料 | Microsoft Azure"
   description="搭配使用 Azure 機器學習服務與 SQL 資料倉儲以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# 使用 Azure Machine Learning 分析資料
本教學課程將說明如何透過使用 Azure SQL 資料倉儲資料的 Azure Machine Learning 建置預測性機器學習模型。 在本教學課程中，我們將為 Adventure Work 建置鎖定目標的行銷活動，預測客戶是否可能購買自行車。


## 必要條件
若要逐步執行本教學課程，您需要

- 具備 AdventureWorksDW 範例資料庫的 SQL 資料倉儲。

[建立 SQL 資料倉儲][] 示範如何佈建資料庫中的範例資料。 如果您已經有 SQL 資料倉儲資料庫，但不是會有範例資料，您可以 [手動載入範例資料][]


## 步驟 1：取得資料 
我們將從 AdventureWorksDW 資料庫中的 dbo.vTargetMail 檢視讀取資料。

1. 登入 [Azure Machine Learning studio][] ，然後按一下 [我的實驗。
2. 按一下 [ **+ 新增** ，然後選取 **的空白實驗**。
3. 輸入您的實驗名稱：目標行銷。
4. 拖放到 **讀取器** 到畫布上的 [模組] 窗格中的模組。
5. 在 [屬性] 窗格中指定 SQL 資料倉儲資料庫的詳細資料。 
6. 指定資料庫 **查詢** 讀取感興趣的資料。
   
   ```
   SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
   ```

按一下以執行實驗 **執行** 實驗畫布下方。
![執行實驗][1]


實驗順利地完成執行之後，按一下底部的 [讀取器模組的輸出連接埠，然後選取 **視覺化** 若要查看匯入的資料。
![檢視匯入資料][3]





## 步驟 2：清除資料
我們將會卸除與模型無關的某些資料行。

1. 拖放到 **專案資料行** 到畫布上的模組。
2. 按一下 [ **啟動資料行選取器** 來指定哪些資料行屬性] 窗格中您想要卸除。
![專案資料行][4]

3. 排除兩個資料行：CustomerAlternateKey 和 GeographyKey。
![移除不必要的資料行][5]




## 步驟 3：建立模型
我們將會以 80-20 的比例分割資料：80% 訓練機器學習模型以及 20% 測試模型。 我們將使用「二元」演算法處理這個二進位分類問題。

1. 拖放到 **分割** 到畫布上的模組。
2. 在 [屬性] 窗格中的第一個輸出資料集輸入 0.8 做為資料列的分數。
![將資料分成訓練集和測試集][6]
3. 拖放到 **雙類別促進式決策樹** 到畫布上的模組。
4. 拖放到 **訓練模型** 畫布模組和指定的輸入。 然後按一下 [ **啟動資料行選取器** 屬性] 窗格中。
      - 第一個輸入：ML 演算法。
      - 第二個輸入：定型演算法的資料。
![連接培訓模型模組][7]
5. 選取 **BikeBuyer** 做為預測資料行的資料行。
![選取要預測資料行][8]





## 步驟 4：計分模型
現在，我們將測試模型如何在測試資料上執行。 我們將會比較我們選擇的演算法和不同的演算法，以查看何者的執行效果比較好。

1. 拖放到 **評分模型** 到畫布上的模組。
    第一個輸入 ︰ 定型的模型
    第二個輸入 ︰ 測試資料
![評分模型][9]
2. 拖放到 **二級貝氏點機器** 到實驗畫布。 我們將會比較這個演算法和二元促進式決策樹的執行效果。
3. 複製定型模型和分數模型等模組並貼在畫布上。
4. 拖放到 **評估模型** 到畫布上，以比較兩種演算法的模組。
5. **執行** 實驗。
![執行實驗][10]
6. 按一下 [評估模型] 模組底部的輸出連接埠，然後按一下 [視覺化]。
![將評估結果視覺化][11]



提供的度量資訊包括 ROC 曲線、正確性-召回圖表和升力曲線。 查看這些度量，我們可以看到第一個模型的執行效果優於第二個。 若要查看第一個模型的預測內容，請按一下 [分數模型] 的輸出連接埠，再按一下 [視覺化]。
![將評分結果視覺化][12]

您會看到另外兩個資料行加入至您的測試資料集。

- 評分機率：客戶成為自行車購買者的可能性。
- 評分標籤：由模型完成的分類 – 是自行車購買者 (1) 或不是 (0)。 標籤的機率臨界值設定為 50%，而且可以調整。

比較 BikeBuyer (實際) 資料行和評分標籤 (預測)，您可以看到模型的執行效果。 在接下來的步驟中，您可以使用此模型預測新的客戶，並將其發佈為 web 服務，或將結果寫回 SQL 資料倉儲。 

若要了解有關建立預測的機器學習模型的詳細資訊，請參閱 [Azure 上的機器學習服務簡介][]。



<!--Image references-->
[1]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]:./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-get-started-manually-load-samples.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md



