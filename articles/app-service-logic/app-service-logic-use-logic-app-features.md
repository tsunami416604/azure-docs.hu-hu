<properties 
    pageTitle="使用邏輯應用程式功能" 
    description="了解如何使用邏輯應用程式的進階功能。" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="stepsic"/> 
    
# 使用 Logic Apps 功能

在 [上一個主題] [建立新的邏輯應用程式]，建立第一個邏輯應用程式。 現在我們將說明如何使用 App Services Logic Apps，來建立更完整的程序。 本主題將介紹下列新的 Logic Apps 概念：

- 條件式邏輯，只有在符合特定條件時，才會執行動作。
- 重複的動作。
- 用以編輯現有邏輯應用程式的程式碼檢視。
- 啟動工作流程的選項。

完成本主題之前，您應該完成 [建立新的邏輯應用程式] 中的步驟。 在 [Azure 入口網站]，瀏覽至您的邏輯應用程式並按一下 **觸發程序和動作** 中編輯邏輯應用程式定義的摘要。

## 參考資料

您可能會發現下列文件很有用：

- [管理和執行階段 Api](https://msdn.microsoft.com/library/azure/dn948513.aspx) -包括如何直接叫用邏輯應用程式
- [語言參考](https://msdn.microsoft.com/library/azure/dn948512.aspx) -函式運算式所有支援的完整清單
- [觸發程序和動作類型](https://msdn.microsoft.com/library/azure/dn948511.aspx) -不同類型的動作和他們所採取的輸入
- [應用程式服務概觀](app-service-value-prop-what-is.md) -選擇何時要建置解決方案元件的描述

## 新增條件式邏輯和重複

雖然原始流程也可運作，但有某些區域是可以改善的。 首先，動作只會將傳回的最高排名推文傳送給您。 在邏輯上，您會想要收到所有含有關鍵字的推文。 若要對項目清單 (例如傳回的推文) 重複執行動作，您必須使用 `repeat` 屬性。

### 重複
重複會取用項目清單，並對該清單中的每個項目執行動作。 下列步驟會更新現有的動作以使用重複，使推文的清單更為合理。

1. 返回您所建立的工作流程，然後按一下 [ **定義** 中連結 **Essentials**。 

2. 若要編輯 **Dropbox 連接器** 動作中，按一下鉛筆圖示。

3. 按一下齒輪圖示，然後選取 **清單上重複**。 
 
2. 旁邊 **重複** 中，按一下 `...` ，然後選取 **主體**。 這會輸入：

        @body('twitterconnector')

    到文字方塊中。 此函數會輸出推文清單。 

3. 選取的文字中的所有 **內容** 文字然後加以刪除。 然後按一下 [ `...` ，然後選取 **推文文字**。 這會插入 **repeatitem ()** 函式，傳回清單中的每個項目。 

最後，請注意重複動作的輸出有其特殊性。 如果您想要參考 Dropbox 作業的結果，例如，您可能 *不* 執行一般 `@actions('dropboxconnector').outputs.body`, ，您必須改為執行: `@actions('dropboxconnector').outputs.repeatItems`。 這會傳回每次執行作業的所有清單，以及每一次的輸出。 例如，`@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` 將傳回第一個上傳之檔案的路徑。

### 條件式
此邏輯應用程式仍會產生很多上傳至 Dropbox 的檔案。 下列步驟中會新增其他邏輯，以確定您只有在推文有特定數量的回推時，才會收到檔案。 

1. 按一下動作，然後選取頂端的齒輪圖示 **新增條件，以符合**。

2. 在文字方塊中，輸入下列內容：

        @greater(repeatItem().Retweet_Count , 5)
    
    函式 **大於** 比較兩個值，並且只允許第一個值大於第二個值時要執行的動作。 您會存取由點 (.) 加上屬性名稱組成的給定屬性，例如上述的 `.Retweet_Count`。 

3. 按一下核取符號，以儲存 Dropbox 動作。

## 使用程式碼檢視編輯邏輯應用程式

除了設計工具以外，您也可以直接編輯定義邏輯應用程式的程式碼，如下所示。 

1. 按一下 [ **程式碼檢視** 命令列中的按鈕。 

    這會開啟一個完整的編輯器，顯示您剛建立的定義。

    ![程式碼檢視](./media/app-service-logic-use-logic-app-features/codeview.png)

    藉由使用文字編輯器，您可以在相同的邏輯應用程式或邏輯應用程式之間複製並貼上任何數量的動作。 您也可以輕鬆地在定義中新增或移除整個區段，以及與其他人共用定義。

2. 您在程式碼] 檢視中進行變更之後，只要按一下 **儲存**。 

### 參數
Logic Apps 的某些功能只能在程式碼檢視中使用。 例如，參數就是其中之一。 參數可讓您輕鬆地在整個邏輯應用程式中重複使用值。 例如，如果您想要在數個動作中使用同一個電子郵件地址，您應將該地址定義為參數。

下列作業會更新您現有的邏輯應用程式，而將參數用於查詢詞彙。

1. 在程式碼檢視中找出 `parameters : {}` 物件，並插入下列主題物件：

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. 捲動至 `twitterconnector` 動作、找出查詢值，並將其取代為 `#@{parameters('topic')}`。
    您也可以使用  **concat** 函式來結合兩個或多個字串，例如: `@concat('#',parameters('topic'))` 等同於上述程式碼。 
 
3. 最後，請移至 `dropboxconnector` 動作，並新增主題參數，如下所示：

        /tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

參數很適合用來提取您很可能經常變更的值。 當您需要在不同環境中覆寫參數時，參數特別有用。 如需有關如何根據環境覆寫參數的詳細資訊，請參閱我們 [REST API 文件](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)。

現在，當您按一下 **儲存**, ，就會有 5 個以上的回推數個傳遞給名為的資料夾的新推文每小時 **推文** 您 Dropbox 中。

若要深入了解邏輯應用程式定義，請參閱 [撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

## 啟動邏輯應用程式工作流程
有數個不同的選項可用來啟動您的邏輯應用程式中定義的工作流程。 工作流程可以永遠啟動隨選 [Azure 入口網站]。

### 循環觸發程序
循環觸發程序會依照您指定的間隔執行。 當觸發程序具有條件式邏輯時，觸發程序會判斷工作流程是否需要執行。 觸發程序透過傳回 `200` 狀態碼，表示應執行。 如果不需要執行，則會傳回 `202` 狀態碼。

### 使用 REST API 回呼
服務可以呼叫邏輯應用程式端點以啟動工作流程。 您可以找到的端點來存取瀏覽至 **屬性** 刀鋒視窗中，從 **設定** 命令列邏輯應用程式中的按鈕。 

您可以從自訂應用程式內使用此回呼來叫用邏輯應用程式。 您必須使用 **基本** 驗證。 使用者名稱 `default` 建立，且密碼為 **主要存取金鑰** 欄位 **屬性** 刀鋒視窗。 例如： 

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

您可以將輸出傳遞至工作流程，並在工作流程中加以參考。 以前述觸發程序為例，如果您包含 `@triggers().outputs.property`，將會取得 `value`。

如需詳細資訊，請參閱 [REST 文件](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)。 

### 手動執行
您可以定義沒有觸發程序的邏輯應用程式。 在此情況下，工作流程必須隨選啟動。 這種邏輯應用程式最適用於只需要間歇性執行的程序。 若要建立不含觸發程序的邏輯應用程式，請檢查 **手動執行此邏輯** 中 **啟動邏輯** 方塊在設計工具中。 

若要啟動邏輯應用程式依需求，請按一下 **立即執行** 命令列上的按鈕。 

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Azure portal]: https://portal.azure.com 
