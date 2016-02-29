<properties 
    pageTitle="在 Azure Machine Learning 中撰寫自訂 R 模組 | Microsoft Azure" 
    description="在 Azure Machine Learning 中撰寫自訂 R 模組的快速入門。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"  
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="tbd" 
    ms.date="12/11/2015" 
    ms.author="bradsev;ankarloff" />


# 在 Azure Machine Learning 中撰寫自訂 R 模組

本主題描述如何在 Azure Machine Learning 中撰寫及部署自訂 R 模組。 它說明什麼是自訂 R 模組，以及使用哪些檔案定義這些模組； 並說明如何在 Machine Learning 工作區中建構這些用來定義模組的檔案，以及如何註冊模組以進行部署。 接著，詳細說明用於自訂模組定義中的元素和屬性。 此外，也討論如何使用輔助功能和檔案以及多個輸出。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 什麼是自訂 R 模組？
A **自訂模組** 是使用者定義的模組，可上傳至您的工作區，並在 Azure Machine Learning 實驗的一部分執行。 A **自訂 R 模組** 是執行使用者定義的 R 函數的自訂模組。 R 是一種適用於統計運算和圖形的程式設計語言，由實作演算法的統計學家和資料科學家廣泛使用。 目前，R 是自訂模組中唯一支援的語言，但未來版本將新增更多語言的支援。

自訂模組 **第一等狀態** Azure Machine Learning 中，也就是說，它們就像其他任何模組一樣使用中。 它們可以與已發行實驗或視覺效果中包含的其他模組一起執行。 使用者可以控制模組實作的演算法、要使用的輸入與輸出連接埠、模型參數，以及其他多種執行階段行為。 自訂模組只可在建立該模組的工作區中使用，而無法發行至社群實驗。

## 自訂 R 模組中的檔案
自訂 R 模組是由至少包含兩個檔案的 .zip 檔案所定義：

* A **原始程式檔** ，實作此模組所公開的 R 函數
*  **XML 定義檔** 描述自訂模組介面

其他輔助檔案也可以包含在提供可從自訂模組存取的功能的 .zip 檔案中。 以下將討論此選項。

## 快速入門範例：定義、封裝及註冊自訂 R 模組
這個範例說明如何建構自訂 R 模組所需的檔案、將檔案封裝成 zip 檔案，然後在您的 Machine Learning 工作區中註冊模組。 可以從下載範例 zip 封裝和範例檔案 [下載 CustomAddRows.zip 檔案](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409)。

舉例來說，假設 **自訂新增資料列** 修改從兩個資料集 (資料框架) 串連資料列 (觀察) 時所使用的 [Add Rows] 模組的標準實作的模組。 標準的 [加入資料列] 模組使用 rbind 演算法，將第二個輸入資料集的資料列附加到第一個輸入資料集的結尾。 自訂的 `CustomAddRows` 函數同樣會接受兩個資料集，但還會接受一個額外的布林交換參數做為輸入。 如果交換參數是 **FALSE**, ，它會傳回與標準實作相同的資料集。 但是，如果交換參數是 **TRUE**, ，它會將附加的第一個輸入資料集的資料列的第二個資料集的結尾改。 實作 R 之檔案的 `CustomAddRows` 函式公開 **自訂新增資料列** 模組包含下列 R 程式碼。

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

若要公開這個 `CustomAddRows` 函式做為 Azure Machine Learning 模組，XML 定義檔必須建立以指定如何 **自訂新增資料列** 模組的外觀及行為。 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is false, and vice versa when Swap is true.</Description>
    
    <!-- Specify the base language, script file and R function to use for this module. -->      
        <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />  
        
    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>Combined dataset</Description>
            </Output>
        </Ports>
        
    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>

 
請注意，值 **識別碼** 屬性 **輸入** 和 **Arg** XML 檔案中的項目必須完全符合 R 程式碼的函式參數名稱 (*dataset1*, ，*dataset2*, ，和 *交換* 在範例中)。 同樣地，值 **entryPoint** 屬性 **語言** 項目必須完全符合 R 指令碼中的函式名稱 (*CustomAddRows* 在範例中)。 相反地， **識別碼** 屬性 **輸出** 元素不會對應至 R 指令碼中的任何變數。 如果需要多個輸入，請直接從 R 函數傳回清單，其中包含的結果會依照輸出在 XML 檔案中宣告的順序來排列。

儲存這兩個檔案做為 *CustomAddRows.R* 和 *CustomAddRows.xml* ，然後一起到 *CustomAddRows.zip* 檔案。

若要註冊這些機器學習工作區中，移至 Machine Learning Studio 中的工作區中，按一下 **+ 新增** 按鈕下方，然後選擇 [ **模組-> 從 ZIP 封裝** ，以上傳新的自訂新增資料列模組。

![上傳 Zip 檔案](./media/machine-learning-custom-r-modules/upload-from-zip-package.png)

 **自訂新增資料列** 模組現在已準備好可供 Machine Learning 實驗存取。

## XML 定義檔中的元素

### Module 元素
 **模組** 項目用以在 XML 檔案中定義的自訂模組。 可以在一個 XML 檔案中使用多個定義多個模組 **模組** 項目。 工作區中的每個模組都必須有唯一的名稱。 使用與現有自訂模組相同的名稱來註冊自訂模組，會以這個新模組取代現有的模組。 不過，您可以使用與現有 Azure Machine Learning 模組相同的名稱來註冊自訂模組，該模組會出現在模組選擇區的 [自訂] 類別中。

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


內 **模組** 項目，您可以指定選擇性 **擁有者** 內嵌在模組中的項目，以及 **描述** 項目，這是在模組快速說明中所顯示的文字，並且當滑鼠停留在 Machine Learning UI 中的模組。

**Module 元素中的字元限制規則**:

* 值 **名稱** 屬性中 **模組** 項目不能超過 64 個英數字元。 
* 內容的 **描述** 項目不能超過 128 個字元的長度。
* 內容的 **擁有者** 項目不能超過 32 個字元長度。

** 表示模組的結果具決定性或不具決定性

預設會將所有模組視為具決定性。 也就是指定一組不變的參數，模組應該傳回相同的結果每次執行時。 在這個行為下，除非參數或輸入資料有所變更，否則 Azure Machine Learning Studio 不會重新執行標示為具決定性的模組。 傳回的快取結果會使實驗執行更快。

不過，如果您的模組使用的函式會傳回不同結果每次執行時，例如 RAND 或者傳回目前的日期或時間的函式可以指定為不具決定性藉由設定選擇性模組 **isDeterministic** 屬性設定為 **false**。 每次執行實驗時，都會重新執行模組，即使模組輸入和參數未變更亦然。 

### 語言定義
 **語言** XML 定義檔中的項目用來指定自訂模組的語言。 目前，R 是唯一支援的語言。 值 **sourceFile** 屬性必須是包含執行模組時要呼叫之函數的 R 檔案名稱。 這個檔案必須是 zip 封裝的一部分。 值 **entryPoint** 屬性是要呼叫之函數的名稱，而且必須符合原始程式檔中定義的有效函數。

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### 連接埠
子元素中指定自訂模組的輸入和輸出連接埠 **連接埠** 區段的 XML 定義檔。 這些元素的順序會決定使用者所經歷的配置 (UX)。 第一個子系 **輸入** 或 **輸出** 列在 **連接埠** XML 檔案的項目將會在 Machine Learning UX 中最左邊的輸入連接埠
每個輸入和輸出連接埠可能會有選擇性 **描述** 子項目，指定當滑鼠游標停留在 Machine Learning UI 中的連接埠所顯示的文字。

**連接埠規則**:

* 最大數目 **輸入和輸出連接埠** 各為 8 個。

### Input 元素
輸入連接埠可讓使用者將資料傳遞至 R 函數和工作區。  **資料型別** 支援的輸入連接埠如下: 

**DataTable:** 此型別傳遞至 R 函數當做 data.frame。 事實上，機器學習服務支援任何型別 (例如，CSV 檔案或 ARFF 檔案) 都是相容於 **DataTable** 都會自動轉換成 data.frame。 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
        </Input>

 **識別碼** 每個相關聯的屬性 **DataTable** 輸入連接埠必須有唯一的值，這個值必須符合其在 R 函數中的參數名稱的對應。
選擇性 **DataTable** 不會傳遞做為輸入，在實驗中的連接埠會有值 **NULL** 傳遞至 R 函數，而且選擇性 zip 連接埠將被忽略，如果未連接輸入。  **IsOptional** 屬性都是選擇性的 **DataTable** 和 **Zip** 類型，並為 *false* 預設。
       
**Zip:** 自訂模組可以接受 zip 檔案，做為輸入。 這個輸入會解壓縮到您函數的 R 工作目錄中

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files will be extracted to the R working directory.</Description>
        </Input>

對於自訂 R 模組，由於 zip 檔案會自動解壓縮到 R 工作目錄，因此 Zip 連接埠的 id 不一定要符合 R 函數的任何參數。

**輸入規則：**

* 值 **識別碼** 屬性 **輸入** 項目必須是有效的 R 變數名稱。
* 值 **識別碼** 屬性 **輸入** 項目不能超過 64 個字元。
* 值 **名稱** 屬性 **輸入** 項目不能超過 64 個字元。
* 內容的 **描述** 項目不能超過 128 個字元
* 值 **類型** 屬性 **輸入** 項目必須是 *Zip* 或 *DataTable*。
* 值 **isOptional** 屬性 **輸入** 項目不需要 (而且 *false* 時未指定，預設情況); 但如果有指定，它必須是 *true* 或 *false*。

### Output 元素

**標準輸出連接埠:**
輸出連接埠會對應至傳回值，從您的 R 函數，然後由後續的模組。 *DataTable* 是目前支援的唯一標準輸出連接埠類型。 (支援 *學習模組* 和 *轉換* 是即將推出。)A *DataTable* 輸出的定義如下:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

對於自訂 R 模組的值中的輸出 **識別碼** 屬性並沒有對應的 R 指令碼中的任何項目，但它必須是唯一的。 對於單一模組輸出，R 函式的傳回值必須是 *data.frame*。 若要輸出多個支援的資料類型的物件，必須在 XML 定義檔中指定適當的輸出連接埠，而且必須以清單形式傳回物件。 輸出物件將從左到右指派給輸出連接埠，以反映物件置於傳回清單中的順序。
 
例如，如果您要將 dataset、Dataset1 和 Dataset2 從左到右分別輸出到輸出連接埠 dataset、dataset1 和 dataset2，請在 ‘CustomAddRows.xml’ 檔案中定義輸出連接埠，如下所示：

比方說，如果您想要修改 **自訂新增資料列** 模組輸出原始的兩個資料集， *dataset1*, ，和 *dataset2*, ，除了新加入資料集 *資料集* (從左到右的順序為: *資料集*, ，*dataset1*, ，*dataset2*)，然後，如下所示在 CustomAddRows.xml 檔案中定義輸出連接埠:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


然後在清單中，以 ‘CustomAddRows.R’ 中的正確順序，傳回物件清單：

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 
    
**視覺效果輸出:**
您也可以指定類型的輸出連接埠 *視覺化* 以顯示 R 圖形裝置和主控台輸出的輸出。 這個連接埠不是 R 函數輸出的一部分，而且不會干擾其他輸出連接埠類型的順序。 若要將視覺效果連接埠新增至自訂模組，新增 **輸出** 項目值是 *視覺效果* 針對其 **類型** 屬性:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
    
**輸出規則：**

* 值 **識別碼** 屬性 **輸出** 項目必須是有效的 R 變數名稱。
* 值 **識別碼** 屬性 **輸出** 項目不能超過 32 個字元。
* 值 **名稱** 屬性 **輸出** 項目不能超過 64 個字元。
* 值 **類型** 屬性 **輸出** 項目必須是 *視覺化*。

### 引數
額外的資料可以透過模組中定義的參數傳遞至 R 函數 **引數** 項目。 選取模組時，這些參數會出現在 Machine Learning UI 最右側的屬性窗格中。 引數可以是任何支援的類型，或者您可以視需要建立自訂列舉。 類似於 **連接埠** 項目， **引數** 元素可以有選擇性 **描述** 項目，指定當滑鼠停留在參數名稱時所顯示的文字。
模組，例如 defaultValue、 minValue 和 maxValue 的選擇性屬性可以做為屬性加入至任何引數 **屬性** 項目。 有效的屬性，如 **屬性** 取決於引數型別項目，並說明下列支援的引數型別。
如同輸入和輸出，每個參數都必須有與其相關聯的 id 值。 此外，id 值必須對應到 R 函數中的具名參數。 在我們的快速入門範例相關聯的 id/參數是 *交換*。

### Arg 元素
模組參數定義使用 **Arg** 子項目 **引數** 區段的 XML 定義檔。 使用中的子項目 **連接埠** 一節中的參數順序 **引數** 區段會定義 UX 中遇到的配置。 參數會依照其在 XML 檔案中定義的相同順序，由上而下顯示在 UI 中。 Machine Learning 所支援的參數類型列示如下。 

**int** – 整數 (32 位元) 類型的參數。

        <Arg id="intValue1" name="Int Param" type="int">
            <Properties min="0" max="100" default="0" />
            <Description>Integer Parameter</Description>
       </Arg>



* *選擇性屬性*: **min**, ，**max** 和 **預設**

**double** – double 類型的參數。

       <Arg id="doubleValue1" name="Double Param" type="double">
           <Properties min="0.000" max="0.999" default="0.3" />
           <Description>Double Parameter</Description>
       </Arg>


* *選擇性屬性*: **min**, ，**max** 和 **預設**

**bool** – 布林值的參數，表示在 UX 中以核取方塊

        <Arg id="boolValue1" name="Boolean Param" type="bool">
            <Properties default="true" />
            <Description>Boolean Parameter</Description>
        </Arg>



* *選擇性屬性*: **預設** -false 如果未設定

**字串**: 標準字串

        <Arg id="stringValue1" name="My string Param" type="string">
           <Properties default="Default string value." isOptional="true" />
           <Description>String Parameter 1</Description>
        </Arg>


* *選擇性屬性*: **預設** 和 **isOptional** -沒有預設值的選擇性字串會傳遞為 null 至 R 函數如果不提供值的使用者。

**ColumnPicker**: 資料行選取參數。 此類型在 UX 中會轉譯成資料行選擇器。  **屬性** 項目此處用來指定選取之資料行就是，其中目標連接埠類型必須是連接埠 id *DataTable*。 資料行選取的結果會傳遞至 R 函數，做為包含所選取資料行名稱的字串清單。 

        <Arg id="colset" name="Column set" type="ColumnPicker">   
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *必要屬性*: **portId** -符合類型輸入項目的 id *DataTable*。
* *選擇性屬性*:
    * **allowedTypes** -篩選使用者可以從中挑選的資料行類型。 有效值包含： 
        *   數值
        *   Boolean
        *   類別
        *   String
        *   標籤
        *   功能
        *   分數
        *   全部

    * **預設** -資料行選擇器的有效預設選取項目包括: 
        * None
        * NumericFeature
        * NumericLabel
        * NumericScore
        * NumericAll
        * BooleanFeature
        * BooleanLabel
        * BooleanScore
        * BooleanAll
        * CategoricalFeature
        * CategoricalLabel
        * CategoricalScore
        * CategoricalAll
        * StringFeature
        * StringLabel
        * StringScore
        * StringAll
        * AllLabel
        * AllFeature
        * AllScore
        * 全部

                                                        
**下拉式清單中**: 使用者指定的列舉 (下拉式) 清單。 下拉式清單項目中指定 **屬性** 項目使用 **項目** 項目。  **識別碼** 每個 **項目** 必須是唯一且有效的 R 變數，而項目名稱是傳遞至 R 函數的值向使用者顯示的兩個的文字。

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>  

* *選擇性屬性*:
    * **預設** -預設屬性的值必須對應至其中的 id 值 **項目** 項目。


### 輔助檔案

放在自訂模組 ZIP 檔案中的所有檔案在執行期間都可供使用。 如果沒有出現目錄結構，將會保留該檔案。 也就是說，檔案的獲得在本機和在 Azure Machine Learning 執行中的運作相同。 請注意，所有檔案都會解壓縮到 ‘src’ 目錄中，因此所有路徑應該都有 ‘src/’ 前置詞。

例如，假設您想要移除任何資料列從資料集，並移除任何重複的資料列，然後輸出到 CustomAddRows 中，而且您已經撰寫一個 RemoveDupNARows.R 檔案中的 R 函數:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
您可以使用 CustomAddRows 函數獲得 removeDupNARows.R 輔助檔案：

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
            } else { 
                dataset <- rbind(dataset1, dataset2)) 
            } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

接著，上傳包含 ‘CustomAddRows.R’、‘CustomAddRows.xml’ 和 ‘RemoveDupNARows.R’ 的 zip 檔案，做為自訂 R 模組。

## 執行環境 ##
R 指令碼的執行環境使用相同版本的 R 為 **執行 R 指令碼** 模組，並且可以使用相同的預設封裝。 您可以像是在自己的 R 環境中一樣，將其他 R 封裝納入自訂模組 zip 封裝，然後在 R 指令碼中載入，以將其加入自訂模組。 

**執行環境的限制** 包括:

* 非持續性檔案系統：執行自訂模組時所撰寫的檔案無法在相同模組的多次執行間保留。
* 無法存取網路



 

