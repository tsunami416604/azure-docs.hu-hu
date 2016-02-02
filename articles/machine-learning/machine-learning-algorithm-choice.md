<properties
    pageTitle="如何選擇機器學習演算法 | Microsoft Azure"
    description="如何在叢集、分類或迴歸實驗中選擇適用於經過指導和未經指導之學習的 Azure Machine Learning 演算法。"
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="paulettm"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/13/2015"
    ms.author="brohrer;garye" />


# 如何選擇 Microsoft Azure Machine Learning 的演算法

「 機器學習演算法應該使用嗎? 」 問題的答案 永遠是 「 這取決於。 」 這可視資料的大小、品質和本質而定。 也可取決於您想用這個答案來做些什麼。 或是取決於演算法的數學運算如何針對您正在使用的電腦轉譯成指令。 而這又需視您有多少時間。 即使經驗最豐富的資料科學家，在沒有嘗試之前，也無法確認哪一個演算法效果會最好。

## 機器學習演算法小祕技

**Microsoft Azure Machine Learning 演算法小祕技**可協助您從 Microsoft Azure Machine Learning 演算法資源庫中選擇適合您預測性分析解決方案的機器學習演算法。
本文將引導您如何使用它。
> [AZURE.NOTE] 若要下載小祕技，並依照這篇文章，請移至 [機器學習演算法小祕技的 Microsoft Azure Machine Learning Studio](machine-learning-algorithm-cheat-sheet.md)。

請記住，這份小祕技有非常特定的預設對象：一位剛起步的資料科學家，其機器學習的經驗為大學生程度，正試著在 Azure Machine Learning Studio 中選擇要開始使用的演算法。 這表示小祕技可能會比較概括且過於簡化，但它可指引您一個可靠的方向。 同時這也意味著還有許多演算法並未列入其中。 當 Azure 機器學習成長到擁有一組更完整的可用方法時，我們就會新增這些演算法。

這些建議是收集許多資料科學家與機器學習專家的意見反應和提示所編撰而成。 雖然我們的想法並不一致，但我已試著將我們的意見整理成粗略的共識。 而大部分的爭論其實都具有同一個考量：「視情況而定。」

### 如何使用小祕技

讀取做為圖表上的路徑和演算法標籤 」 的 * < 路徑
標籤 > * 使用 *< 演算法 >*。 」 例如，「 如 *速度* 使用 * 兩個
類別羅吉斯迴歸 *。 」 有時候會適用多個分支。
有時候則全部都不適用。 在要成為
法則的建議，所以不必擔心它正確。
談到的許多資料科學家說過，只確定方法
尋找最佳的演算法，就是試著它們全部。

以下是從範例 [Cortana 分析圖庫](http://gallery.azureml.net/) 嘗試的實驗
針對相同的資料和比較結果的數種演算法:
[比較多類別分類器: 字母
辨識] (http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92)。
>[AZURE.TIP] 若要下載及列印圖表，以提供 Machine Learning Studio 功能的概觀，請參閱 [Azure Machine Learning Studio 功能的概觀圖](machine-learning-studio-overview-diagram.md)。

## 機器學習的類型

### 監督式

經過指導的學習演算法進行一組為基礎的預測
範例。 例如，可以使用歷程記錄股票價格來危險
在未來的價格猜測。 訓練時使用的每個範例都會標示
感興趣的值，在此情況下股票價格。 經過指導
學習演算法會尋找這些值標籤中的模式。 它可以使用
可能是相關的任何資訊 — 旺季，該週的日
公司的財務資料，業界的目前狀態的型別
geopolicitical 干擾性事件，而每一個演算法查看是否有不同
模式類型。 找到演算法後最佳模式它
可以它會使用此模式中進行預測，如未標記測試
資料 — 未來的價格。

這是常見且實用的機器學習服務類型。 其中一個
例外狀況，所有 Azure Machine Learning 中的模組是監督都式學習
演算法。 有幾種特定類型的監督式學習
Azure Machine Learning 中的呈現: 分類、 迴歸和異常
偵測。

* **分類**。 當資料來預測
類別中，經過指導的學習也稱為分類。 這是
將影像指派為圖片中的其中一個 「 貓 」 的情況下或
「 狗 」。 當您有兩種選擇時，這就叫做 **二級**
或 **二項式分類**。 當有多個類別，做為
當預測的 ncaa 大學三月瘋聯賽中，這
問題就所謂 **多類別分類**。

* **迴歸**。 當值所預測，如同股價，
經過指導的學習稱為 「 回復 」。

* **異常偵測**。 有時候目的在於識別資料點
並只是不常見。 詐騙偵測，比方說，任何高
懷疑有不尋常的信用卡花費模式。 可能的
因此許多變化，會和因此幾個，它具有的訓練範例
若要了解哪些詐騙活動看起來不可行。 Auch die Eigenschaften
異常偵測採用的方法是，只要了解哪些正常
活動的外觀 (使用歷程記錄的非詐騙交易)，
找出任何明顯不同的項目。

### 未監督式

在未經指導的學習資料點有沒有相關聯的標籤
VM。 相反地，非監督式的學習演算法的目標是要
組織資料以某種方式描述其結構。 這可以
表示劃分為叢集，或尋找查看不同的方式
使它顯示更簡單、 更有組織地複雜的資料。

### 增強式學習

增強式學習演算法需選擇一個動作的動作
每個資料點的回應。 學習演算法也會收到
獎勵訊號在短時間，表示好的決策。
根據這個演算法會修改其策略以達到
最高的獎勵。 目前有無增強式學習
在 Azure Machine Learning 演算法模組。 增強式學習中很常見
機器人，其中的某個時間點的時間的感應器讀數集是
資料點，而演算法必須選擇機器人的下一個動作。 當您 [顯示選項] 並檢視所選取的憑證時，它
自然也符合物聯網應用程式。

## 選擇演算法時的考量

### 精確度

您不一定常常需要取得最準確的答案。
有時是大約是正確的根據您想要
使用它。 如果是這樣，您可以剪下您的處理
時間大幅的更多的近似方法行得通。 另一個
更多的近似方法的優點是，他們自然傾向於
避免 [過度配適](https://youtu.be/DQWI1kvmwRg)。

### 定型時間

數分鐘或小時來定型模型所需的變化很大的
處理演算法之間。 定型時間通常密切
精確度 — 其中一個通常伴隨著其他。 此外，某些
演算法為更敏感資料點數目比其他。
時間限制時，它可以特別是磁碟機演算法的選擇，
當資料集很大。

### 線性

許多機器學習演算法都會使用線性。 線性
分類演算法會假設類別可以使用來分隔
直線 (或其高維度的類比)。 這些包括羅吉斯
迴歸與支援向量機器 (如在 Azure Machine Learning 中實作)。
線性迴歸演算法會假設資料趨勢遵循直線
上方。 這些假設不是錯誤的某些問題，但在其他它們
關閉精確度。

![非線性類別界限][1]

*** 非線性類別界限 *** *-依賴線性分類
演算法也會導致低精確度 *

![具有非線性趨勢的資料][2]

*** 資料與非線性趨勢 *** *-使用線性迴歸方法會
產生更大的錯誤比必要 *

儘管有其危險性，是非常普遍，因為第一個線性演算法
攻擊的行。 它們通常優良簡單且快速
訓練。

### 參數數目

參數是資料科學家取得開啟設定時的旋鈕
演算法。 它們是數字，會影響此演算法的行為
例如誤差或反覆項目，或選項之間的數字
此演算法的行為變體。 定型時間和精確度的
演算法有時可能很容易受到取得的權限
設定檔。 通常，需要使用較大的數值參數的演算法
最多嘗試錯誤來尋找正確的組合。

另外，還有 [參數
sweeping](machine-learning-algorithm-parameters-optimize.md)
所有的參數會自動嘗試使用的 Azure Machine Learning 中的模組區塊
無論您選擇的資料粒度的組合。 雖然這是很大的
如何確定您已合併參數空間中，所需的時間
訓練模型以指數方式增加的參數數目。

其優點是，通常具有許多參數表示，
演算法會有更大的彈性。 它通常可以很理想達到
精確度。 提供您可以找到正確的參數組合
設定檔。

### 特徵數目

對於特定類型的資料，一些功能可能會非常大
相較於資料點數目。 這通常是這樣的關聯
遺傳學或文字資料。 大量的功能可以陷入部分
學習演算法，使訓練 unfeasibly 長的時間。 支援
向量機器是也非常適合此情況下 (請參閱下文)。

### 特殊案例

某些學習演算法做出特定假設結構
資料或想要的結果。 如果您可以找到適合您
需求，可讓您更有用的結果，更精確的預測，
或更快的定型時間。

| **演算法**| **精確度**| **定型時間**| **線性**| **參數**| **注意事項**|
|---|:---:|:---:|:---:|:---:|---|
| **雙類別分類**| | | | | |
| [羅吉斯迴歸](https://msdn.microsoft.com/library/azure/dn905994.aspx)| | ●| ●| 5| |
| [決策樹系](https://msdn.microsoft.com/library/azure/dn906008.aspx)| ●| ○| | 6| |
| [決策叢林](https://msdn.microsoft.com/library/azure/dn905976.aspx)| ●| ○| | 6| 低記憶體使用量|
| [推進式的決策樹](https://msdn.microsoft.com/library/azure/dn906025.aspx)| ●| ○| | 6| 高記憶體使用量|
| [類神經網路](https://msdn.microsoft.com/library/azure/dn905947.aspx)| ●| | | 9| [可供其他自訂](http://go.microsoft.com/fwlink/?LinkId=402867)|
| [平均感知器](https://msdn.microsoft.com/library/azure/dn906036.aspx)| ○| ○| ●| 4| |
| [支援向量機器](https://msdn.microsoft.com/library/azure/dn905835.aspx)| | ○| ●| 5| 適用於大型特徵集|
| [局部深度支援向量機器](https://msdn.microsoft.com/library/azure/dn913070.aspx)| ○| | | 8| 適用於大型特徵集|
| [貝氏點機器](https://msdn.microsoft.com/library/azure/dn905930.aspx)| | ○| ●| 3| |
| **多類別分類**| | | | | |
| [羅吉斯迴歸](https://msdn.microsoft.com/library/azure/dn905853.aspx)| | ●| ●| 5| |
| [決策樹系](https://msdn.microsoft.com/library/azure/dn906015.aspx)| ●| ○| | 6| |
| [決策叢林 ](https://msdn.microsoft.com/library/azure/dn905963.aspx)| ●| ○| | 6| 低記憶體使用量|
| [類神經網路](https://msdn.microsoft.com/library/azure/dn906030.aspx)| ●| | | 9| [可供其他自訂](http://go.microsoft.com/fwlink/?LinkId=402867)|
| [一個 v 全部](https://msdn.microsoft.com/library/azure/dn905887.aspx)| -| -| -| -| 請參閱選取的兩個類別方法的內容|
| **迴歸**| | | | | |
| [線性 ](https://msdn.microsoft.com/library/azure/dn905978.aspx)| | ●| ●| 4| |
| [貝氏線性](https://msdn.microsoft.com/library/azure/dn906022.aspx)| | ○| ●| 2| |
| [決策樹系](https://msdn.microsoft.com/library/azure/dn905862.aspx)| ●| ○| | 6| |
| [推進式的決策樹](https://msdn.microsoft.com/library/azure/dn905801.aspx)| ●| ○| | 5| 高記憶體使用量|
| [快速樹系的分位數](https://msdn.microsoft.com/library/azure/dn913093.aspx)| ●| ○| | 9| 散發套件，而不是點預測|
| [類神經網路](https://msdn.microsoft.com/library/azure/dn905924.aspx)| ●| | | 9| [可供其他自訂](http://go.microsoft.com/fwlink/?LinkId=402867)|
| [波氏 ](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | | ●| 5| 技術上來說對數線性。預測計算|
| [序數](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | | 0| 為了預測順位排序|
| **異常偵測**| | | | | |
| [支援向量機器](https://msdn.microsoft.com/library/azure/dn913103.aspx)| ○| ○| | 2| 尤其是適用於大型的功能集|
| [PCA 型異常偵測 ](https://msdn.microsoft.com/library/azure/dn913102.aspx)| | ○| ●| 3| |
| [方法](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| | ○| ●| 4| 群集演算法|


**演算法屬性：**

**●**：顯示優異的精確度、快速定型時間及使用線性

**○**：顯示不錯的精確度和適度的定型時間

## 演算法備註

### 線性迴歸

前面提過， [線性迴歸](https://msdn.microsoft.com/library/azure/dn905978.aspx)
資料集符合列 (或平面上或超平面)。 它是主要位置，
簡單且快速，但可能過於簡單部分問題。
這裡有 [線性迴歸
tutorial](machine-learning-linear-regression-in-azure.md)。

![具有線性趨勢的資料][3]

*** 使用線性趨勢 * 資料**

### 羅吉斯迴歸

雖然它會在名稱中，羅吉斯困惑包含 「 迴歸 」
迴歸是實際的強大工具
[兩個類別](https://msdn.microsoft.com/library/azure/dn905994.aspx)
和
[多級](https://msdn.microsoft.com/library/azure/dn905853.aspx)
分類。 它既快速又簡單。 它會使用的事實
'-而不是直線造形的曲線會讓自然
將資料分成群組。 羅吉斯迴歸提供線性類別
界限，因此當您使用它，請確定線性近似值
您能夠忍受的東西。

![羅吉斯迴歸與只有一項特徵的雙類別資料][4]

*** 雙類別資料只是一個功能 * 羅吉斯迴歸** *-
類別界限是只接近這兩個類別 * 羅吉斯曲線點

### 樹、樹系和叢林

決策樹系
([regression](https://msdn.microsoft.com/library/azure/dn905862.aspx),
[two-class](https://msdn.microsoft.com/library/azure/dn906008.aspx),
和
[multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)),
決策叢林
([二級](https://msdn.microsoft.com/library/azure/dn905976.aspx)
和
[multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)),
和促進式的決策樹
([迴歸](https://msdn.microsoft.com/library/azure/dn905801.aspx)
和
[兩個類別](https://msdn.microsoft.com/library/azure/dn906025.aspx))
都是根據決策樹，基本的機器學習
概念。 有許多的決策樹，但是用途都
相同的作業 — 特徵空間細分大部分相同的區域
標籤。 這些可以是常數值，或一致的類別目錄的區域
取決於是否進行分類或迴歸。

![細分特徵空間的決策樹][5]

***決策樹細分特徵空間為區域的大約
統一值***

因為特徵空間可以再細分為任意小
很容易想像不夠精確有一個資料分割區，
每個區域點 — 過度配適的極端範例。 為了避免
這一大組的樹狀結構會使用特殊數學建構
負責執行樹狀結構並沒有關聯。 這個平均
[決策樹系] 是一個樹狀結構，可避免過度配適。 決策樹系
可以使用大量記憶體。 決策叢林是消耗的 variant
但會犧牲久一點的訓練時間較少的記憶體。

促進式的決策樹藉由限制多少次他們避免過度學習
可以細分，每個區域中允許使用方式的幾個資料點。 Auch die Eigenschaften
演算法建構一連串的樹狀結構，其中每個學習
補償留下的樹狀目錄中之前的錯誤。 結果是非常
傾向於使用大量記憶體的精確度學習模組。 如需完整
技術說明，請查看 [Friedman 的原始
紙張] (http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf)。

[快速樹系的分位數
迴歸] (https://msdn.microsoft.com/library/azure/dn913093.aspx)
是一種特殊案例中您要的決策樹
知道不僅典型 (中間) 的值區內的資料
但也分位數的形式散發。

### 類神經網路和感知器

類神經網路的大腦-啟發學習涵蓋的演算法
[multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx),
[two-class](https://msdn.microsoft.com/library/azure/dn905947.aspx),
和
[迴歸](https://msdn.microsoft.com/library/azure/dn905924.aspx)
發生問題。 有無限的各種不同，但是類神經網路
Azure Machine Learning 中都有向非循環圖的表單。 ,
表示輸入功能都會通過向前 (永遠不會向後)
之前被轉換成輸出層的順序。 在每個圖層，
輸入各種組合加權、 加總，以及將傳送到
下一層。 這個簡單的計算值的組合會產生
若要了解複雜的類別界限以及資料趨勢的能力
表面上由變魔術一樣。 多層級的網路，這種執行 「 深層
學習 」 fuels 這麼技術報告和科幻小說的產物。

可惜這種高效能並非隨手可得。 類神經網路可以
需要較長的時間來定型，特別是對於具有許多大型資料集
功能。 他們也擁有更多的參數，比大多數的演算法，其中
表示參數掃掠許多展開定型時間。
並對這些 overachievers 誰想要 [指定自己的網路
結構] (http://go.microsoft.com/fwlink/?LinkId=402867)，
inexhaustible 豐富的可能性。

![類神經網路所學習的界限][6]
-----------------

***類神經網路所學習的界限可能會很複雜，
不規則***

[二級平均
認知] (https://msdn.microsoft.com/library/azure/dn906036.aspx)
為類神經網路答案急速定型時間。 它會使用
提供線性類別界限的網路架構。 幾乎
基本型別，今日的標準，但是有很長的工作歷程記錄
穩固且夠小，無法快速了解。

### SVM

支援向量機器 (Svm) 尋找用來分隔類別界限
透過為以最寬的邊界。 當兩個類別不可以是清楚
分隔，演算法會尋找最佳的界限，他們可以。 寫入中，
Azure 機器學習中，[雙類別
沒有 SVM] (https://msdn.microsoft.com/library/azure/dn905835.aspx)
這與只直線。 (但以 SVM 的說法，應該是使用線性核心)。
因為它會使這個線性近似值，是能夠執行相當
快速。 其中搭配是功能密集的資料，例如文字
或 genomic。 在這些情況下，Svm 是能夠區隔多個類別
快速且較少的過度膨脹，比其他大部分的演算法，在
除了需要只數量較少的記憶體。

![支援向量機器類別界限][7]

***典型的支援向量機器類別界限發揮最大的邊界
分隔兩個類別***

另一個產品的 Microsoft Research [二級局部深度
SVM] (https://msdn.microsoft.com/library/azure/dn913070.aspx) 是
非線性變體能夠保留大部分的速度和記憶體的 SVM
線性版本的效率。 它是理想的情況下，線性
方法不會讓不夠精確回應。 開發人員可以保留它
快速的問題細分成一大堆小型線性 SVM
發生問題。 閱讀 [完整
描述] (http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf)
如需有關如何它們拉這個技巧。

使用聰明非線性 Svm，[一級的延伸模組
SVM] (https://msdn.microsoft.com/library/azure/dn913103.aspx) 繪製
界限緊密概述整個資料集。 它可用於
異常偵測。 到目前為止外，任何新資料點
界限是不尋常夠是那麼明顯。

### 貝氏方法

貝氏方法有都會令人滿意的品質: 避免它們
過度配適。 方法是做出一些假設事先
答案可能散發。 另一個的副產品這個
方法是，他們擁有極少的參數。 Azure Machine Learning 同時具有
這兩個分類 ([二級貝氏點的貝氏演算法
電腦] (https://msdn.microsoft.com/library/azure/dn905930.aspx))
和 ([貝氏線性迴歸
迴歸] (https://msdn.microsoft.com/library/azure/dn906022.aspx))。
請注意，這些假設資料可以分割或配合直線
上方。

在歷程記錄的附註，在 Microsoft 開發貝氏點機器
參考資料。 它們有背後一些格外美麗理論工作
VM。 有興趣的學生導向至 [原始發行項中
JMLR] (http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) 和
[具洞察力的部落格 Chris
銘] (http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)。

### 專門的演算法

如果您有非常特定的目標，那麼您的可能運氣特別好。 在 Azure Machine Learning
集合是專門排名的預測演算法
([序數
迴歸] (https://msdn.microsoft.com/library/azure/dn906029.aspx))，
計數預測 ([波氏
迴歸] (https://msdn.microsoft.com/library/azure/dn905988.aspx))，
異常偵測 (一個 [主要元件為基礎，
分析] (https://msdn.microsoft.com/library/azure/dn913102.aspx)
另一個 [支援向量為基礎
電腦] (https://msdn.microsoft.com/library/azure/dn913103.aspx) s)。
而且沒有單獨群集演算法也
([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![以 PCA 為基礎的異常偵測][8]

*** PCA 型異常偵測 *** *-落大部分的資料
到 stereotypical 的散發。在違背大幅的點
發佈的可疑 *

![使用 K-means 分組的資料集][9]

*** 資料集分成多 5 個叢集使用 K-表示 ***

另外還有一整團 [一 v 全部多級
分類器] (https://msdn.microsoft.com/library/azure/dn905887.aspx)，
其中分成 N 類別分類問題 N-1 雙類別
分類的問題。 精確度、 定型時間和線性
屬性是由使用雙類別分類器決定。

![雙類別分類器結合成三個類別分類器][10]

***一組雙類別分類器合併成三個類別
分類器***

Azure Machine Learning 也包含功能強大的機器學習架構的存取
標題下的 [Vowpal
Wabbit] (https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf)。
VW 頑強，分類，因為它可以了解這兩個分類
和迴歸問題，並甚至可以了解從部分加上標籤
資料。 您可以將它設定為使用任何一個數字的學習
演算法、 遺失函式，以及最佳化演算法。 其設計
從一開始能夠有效率，平行，和極為快速。 此程式碼
處理非常大功能集明顯輕鬆。
啟動由 Microsoft Research 的自己 John Langford，VW 時，
公式股票汽車演算法的欄位中的一個項目。 並非所有問題
符合 VW，但如果有的話，它可能是值得爬
其介面上的學習曲線。 它也是 [獨立
開啟原始碼] (https://github.com/JohnLangford/vowpal_wabbit) 中
有幾種語言。





[1]: ./media/machine-learning-algorithm-choice/image1.png 
[2]: ./media/machine-learning-algorithm-choice/image2.png 
[3]: ./media/machine-learning-algorithm-choice/image3.png 
[4]: ./media/machine-learning-algorithm-choice/image4.png 
[5]: ./media/machine-learning-algorithm-choice/image5.png 
[6]: ./media/machine-learning-algorithm-choice/image6.png 
[7]: ./media/machine-learning-algorithm-choice/image7.png 
[8]: ./media/machine-learning-algorithm-choice/image8.png 
[9]: ./media/machine-learning-algorithm-choice/image9.png 
[10]: ./media/machine-learning-algorithm-choice/image10.png 

