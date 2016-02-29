<properties
    pageTitle="使用 DocumentDB 指令碼總管檢視預存程序、觸發程序和使用者定義函數 | Microsoft Azure"
    description="了解 DocumentDB [指令碼總管]，它是讓您檢視 DocumentDB 伺服器端程式設計成品 (包括預存程序、觸發程序和使用者定義函數) 的 Azure 入口網站工具。"
    services="documentdb"
    authors="AndrewHoh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="12/01/2015"
    ms.author="anhoh"/>

# 使用 DocumentDB 指令碼總管檢視、編輯和建立預存程序、觸發程序和使用者定義函式。

這篇文章概述 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 指令碼總管] 中，Azure 入口網站工具，可讓您檢視 DocumentDB 伺服器端程式設計成品包括預存程序、 觸發程序和使用者定義函式。  深入了解 DocumentDB 伺服器端程式設計 [這裡](documentdb-programming.md)。

完成本教學課程後，您將能夠回答下列問題：  

-   如何輕鬆地透過網頁瀏覽器檢視 DocumentDB 預存程序？
-   如何輕鬆地透過網頁瀏覽器檢視 DocumentDB 觸發程序？
-   如何輕鬆地透過網頁瀏覽器檢視 DocumentDB 使用者定義函數？

## 啟動及瀏覽指令碼總管

您可以從任何 DocumentDB 帳戶、資料庫和集合刀鋒視窗啟動指令碼總管。  

1. 在 DocumentDB 帳戶或資料庫刀鋒視窗頂端，只要按一下 **指令碼總管** 命令。

    ![[指令碼總管] 命令的螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
2. 此外，每個刀鋒視窗底部附近看到是 **開發人員工具** ] 透鏡，其中包含 **指令碼總管** 組件。

    ![[指令碼總管] 組件的螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerpart.png)

2. 只要按一下命令或部件即可啟動 [指令碼總管]。

    <p> **資料庫** 和 **集合** 下拉式清單方塊會根據您在其中啟動指令碼總管] 的內容預先填入。  例如，如果您要從資料庫刀鋒視窗啟動，則系統會預先填入目前資料庫。  如果您要從集合刀鋒視窗啟動，則系統會預先填入目前集合。

    ![[指令碼總管] 的螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerinitial.png)


3.  **資料庫** 和 **集合** 下拉式清單方塊可用來輕鬆地變更的指令碼目前檢視而不需要關閉並重新啟動指令碼總管] 的集合。  

4. [指令碼總管] 還支援根據 id 屬性篩選目前載入的指令碼集。  您只需在篩選方塊中輸入即可。

    ![反白顯示篩選條件的 [指令碼總管] 螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerfilter.png)

    [指令碼總管] 清單中的結果便會根據您所提供的準則進行篩選。

    ![[指令碼總管] 的螢幕擷取畫面和篩選結果](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


    > [AZURE.IMPORTANT] The Script Explorer filter functionality only filters from the ***currently*** loaded set of scripts and does not automatically refresh the currently selected collection.

5. 若要重新整理載入的指令碼總管] 的指令碼的清單，只要按一下 **重新整理** 分頁頂端的命令。

    ![[指令碼總管] 重新整理命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## 檢視、 編輯、 建立和刪除預存程序、 觸發程序和使用者定義函數與指令碼總管]

[指令碼總管] 可讓您輕鬆地在 DocumentDB 伺服器端程式設計成品上執行 CRUD 作業。  

- 若要建立指令碼，只要按一下適用於建立指令碼總管] 中的命令，提供 id、 輸入的指令碼內容，按一下 **儲存** 命令。

    ![[指令碼總管] 建立選項螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- 在建立觸發程序時，您也必須指定觸發程序類型和觸發程序作業。

    ![[指令碼總管] 觸發程序選項螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- 若要檢視指令碼，只要按一下您想要的指令碼即可。

    ![[指令碼總管] 檢視指令碼體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- 若要編輯指令碼，只進行所需的變更，然後按一下 [ **儲存** 命令。

    ![[指令碼總管] 檢視指令碼體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- 若要捨棄任何暫止的變更指令碼，只要按一下 **捨棄** 命令。

    ![[指令碼總管] 捨棄變更體驗螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- 指令碼總管] 也可讓您輕鬆地檢視目前載入的指令碼的系統屬性，即可 **屬性** 命令。

    ![[指令碼總管] 指令碼屬性檢視螢幕擷取畫面](./media/documentdb-view-scripts/scriptproperties.png)

    > [AZURE.NOTE] 時間戳記 (_ts) 屬性內部會以 epoch 時間表示，但是指令碼總管] 以一般人可讀的 GMT 格式顯示值。

- 若要刪除指令碼，在指令碼總管] 中選取它，按一下 **刪除** 命令。

    ![[指令碼總管] 刪除命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- 按一下 [確認刪除動作 **是** 或按一下 [取消刪除動作 **否**。

    ![[指令碼總管] 刪除命令螢幕擷取畫面](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## 後續步驟

若要深入了解 DocumentDB，請按一下 [這裡](http://azure.com/docdb)。
 

