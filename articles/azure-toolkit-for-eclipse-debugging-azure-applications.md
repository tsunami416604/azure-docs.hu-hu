<properties
    pageTitle="在 Eclipse 中偵錯 Azure 應用程式"
    description="使用 Eclipse 的 Azure 工具組深入了解偵錯 Azure 應用程式。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# 在 Eclipse 中偵錯 Azure 應用程式 #

使用 Azure Toolkit for Eclipse，無論您的應用程式是在 Azure 或計算模擬器 (如果您使用 Windows 做為作業系統) 中執行，您都可以進行偵錯。 下圖顯示 **偵錯** 用來啟用遠端偵錯的 [內容] 對話方塊 ︰

![][ic719504]

本教學課程假設您已成功建立應用程式，而且熟悉計算模擬器和部署到 Azure 的操作。

我們將使用應用程式從 [使用 Azure 服務執行階段程式庫在 JSP 中][] 教學課程為本主題的起始點。 如果您尚未建立該應用程式，請先建立應用程式後再繼續。

## 在應用程式於 Azure 中執行時偵錯 ##

>[AZURE.WARNING] 此工具組目前支援遠端 Java 偵錯主要被供 Azure 計算模擬器中執行的部署。 由於偵錯連線不安全，您不應在生產環境部署中啟用遠端偵錯。 如果您需要偵錯在 Azure 雲端中執行的應用程式，請使用預備環境部署，但您必須了解如果有人知道您的雲端部署的 IP 位址，可能會在未經授權的情況下存取您的偵錯工作階段，即使是預備環境部署也一樣。

1. 建置在模擬器中測試專案 ︰ 在 Eclipse 的專案總管] 中，以滑鼠右鍵按一下 **{1>myazureproject**, ，按一下 **屬性**, ，按一下 **Azure**, ，並設定 **建置適用於** 至 **部署至雲端**。
1. 重建您的專案 ︰ 在 Eclipse] 功能表中，按一下 **專案**, ，然後按一下 [ **建置所有**。
1. 應用程式部署至 *預備* 在 Azure 中。
    >[AZURE.IMPORTANT] 如前所述，強烈建議您在大部分情況下，計算模擬器中偵錯，則需要額外的偵錯時，才在預備環境中偵錯。 建議您不要在生產環境中偵錯。
1. 在您的部署準備在 Azure 中，取得從部署的 DNS 名稱 [Azure 管理入口網站][]。 預備部署具有 DNS 名稱中的格式為 http://*& lt; guid （& s) gt;*。cloudapp.net，其中 *& lt; guid （& s) gt;* 是由 Azure 指派的 GUID 值。
1. 在 Eclipse 的專案總管] 中，以滑鼠右鍵按一下 **WorkerRole1**, ，按一下 [ **Azure**, ，然後按一下 [ **偵錯**。
1. 在 **WorkerRole1 偵錯屬性** 對話方塊 ︰
    1. 檢查 **啟用這個角色的遠端偵錯。**
    1. 如 **輸入端點，以使用**, ，使用 **偵錯 （公用 ︰ 8090、 私用 ︰ 8090）**。
    1. 請確定 **等候偵錯工具連接暫停模式啟動 JVM** 為未選取狀態。
        >[AZURE.IMPORTANT]  **等候偵錯工具連接暫停模式啟動 JVM** 選項適用於進階偵錯在計算模擬器中的案例 （不適用於雲端部署）。 如果 **等候偵錯工具連接暫停模式啟動 JVM** 選項時，就會擱置在伺服器啟動程序，直到 Eclipse 偵錯工具連接到其 JVM。 雖然您可以在使用計算模擬器的偵錯工作階段使用此選項，但請勿將它使用在雲端部署的偵錯工作階段。 伺服器的初始化是在 Azure 啟動工作中進行，而 Azure 雲端在啟動工作完成之前不會提供公用端點。 因此，如果在雲端部署中啟用此選項，啟動程序將無法順利完成，因為它無法接收來自外部 Eclipse 用戶端的連線。
    1.按一下 [ **建立偵錯組態**。
1. 在 **Azure 偵錯組態** 對話方塊 ︰
    1. 如 **Java 專案偵錯**, ，請選取 **MyHelloWorld** 專案。
    1. 如 **設定偵錯的**, ，檢查 **Azure 雲端 （臨時）**。
    1. 請確定 **Azure 計算模擬器** 為未選取狀態。
    1. 如 **主機**, ，輸入 DNS 名稱將預備部署，但是沒有上述 **http://**。 例如 （使用您的 GUID 取代以下顯示的 GUID） ︰ **4e616d65-6f6e-6d 65 6973 526f62657274.cloudapp.net**
1. 按一下 [ **確定** 關閉 **Azure 偵錯組態** ] 對話方塊。
1. 按一下 [ **確定** 關閉 **WorkerRole1 偵錯屬性** ] 對話方塊。
1. 如果您尚未在 index.jsp 中設定中斷點，請設定一個：
    1. 在 Eclipse 的專案總管中，展開 **MyHelloWorld**, ，依序展開 **WebContent**, ，然後按兩下 **index.jsp**。
    1. 在 index.jsp 中，以滑鼠右鍵按一下藍色列左側的 Java 程式碼中，按一下 [ **切換中斷點**, ，如下列所示 ︰
        ![][ic551537]
1. 在 Eclipse 的功能表上，按一下 [ **執行** 然後按一下 [ **偵錯組態**。
1. 在 **偵錯組態** ] 對話方塊中，展開 **遠端 Java 應用程式** 在左窗格中，選取 **Azure 定域機組 (WorkerRole1)**, ，然後按一下 **偵錯**。
1. 在瀏覽器中執行預備應用程式， **http://***（& s) lt; guid （& s) gt;***.cloudapp.net/MyHelloWorld**, ，以取代您的 DNS 名稱中的 GUID *& lt; guid （& s) gt;*。 如果出現提示 **確認檢視方塊參數** 對話方塊中，按一下 [ **是**。 您的偵錯工作階段現在應該執行到設定中斷點的程式碼。

>[AZURE.NOTE] 如果您嘗試啟動遠端偵錯連接有多個執行的角色執行個體，您目前尚無法控制哪一個執行個體的部署偵錯工具會先連接，因為 Azure 負載平衡器會隨機選取執行個體。 不過，一旦您與該執行個體連線後，您就會繼續偵錯相同的執行個體。 另外請注意，如果閒置時間超過 4 分鐘 (例如，當您停在中斷點的時間太長時)，Azure 可能會關閉連線。

## 在多個執行個體的部署中偵錯特定的角色執行個體 ##

當您的部署在雲端中執行時，您很有可能在多個計算或角色執行個體中執行它。 這可讓您充分利用 Azure 99.95% 的可用性保證，並擴充您的應用程式。

在這種情況下，您可能需要在特定的角色執行個體中遠端偵錯您的 Java 應用程式。 不過，如果您只啟用一個一般輸入端點來偵錯，Azure 負載平衡器會讓您幾乎無法將偵錯工具連線到特定的角色執行個體。 它會改將您連線至其隨機挑選的角色執行個體。

這是利用執行個體輸入端點來讓您更輕鬆地偵錯特定角色執行個體的案例類型。

例如，假設您計劃在您的部署中執行最多 5 個角色執行個體。 使用 **端點** 屬性頁，在 [角色屬性] 對話方塊中，建立執行個體輸入的端點，並將其指派公用連接埠，而不是單一通訊埠編號的範圍。 例如，在 **公用連接埠** 輸入方塊中，指定 **81 85**。

使用這個執行個體端點部署您的應用程式之後，Azure 會從這個範圍指派一個唯一的連接埠號碼到每個角色執行個體。 然後，為了找出哪一個執行個體指派給哪個連接埠號碼，您可以使用 *{1>instanceendpointname***_PUBLICPORT** 環境變數 (其中 *{1>instanceendpointname* 是建立執行個體端點時所指派的名稱) 與工具組 （例如，藉由網頁，尾中傳回其值，所以當您瀏覽至它讀取） 部署中自動設定。

一旦您知道該執行個體被指派的公用連接埠號碼，您可以將它加到服務的主機名稱，以在 Eclipse 的偵錯組態中參考它。 這會讓 Eclipse 偵錯工具連線至該特定執行個體，而不是任何其他的執行個體。

## 僅限 Windows：在計算模擬器中執行應用程式時偵錯 ##

>[AZURE.NOTE] 在 Windows 上，才可用 Azure 模擬器。 如果您使用 Windows 以外的作業系統，請略過本節。 

1. 建置在模擬器中測試專案 ︰ 在 Eclipse 的專案總管] 中，以滑鼠右鍵按一下 **{1>myazureproject**, ，按一下 **屬性**, ，按一下 **Azure**, ，並設定 **建置適用於** 至 **在模擬器中測試**。
1. 重建您的專案 ︰ 在 Eclipse] 功能表中，按一下 **專案**, ，然後按一下 [ **建置所有**。
1. 在 Eclipse 的專案總管] 中，以滑鼠右鍵按一下 **WorkerRole1**, ，按一下 [ **Azure**, ，然後按一下 [ **偵錯**。
1. 在 **WorkerRole1 偵錯屬性** 對話方塊 ︰
    1. 檢查 **啟用這個角色的遠端偵錯。**
    1. 如 **輸入端點，以使用**, ，使用預設的端點時自動產生的工具組，列為 **偵錯 （公用 ︰ 8090、 私用 ︰ 8090）**。
    1. 請確定 **等候偵錯工具連接暫停模式啟動 JVM** 未選取選項。
        >[AZURE.IMPORTANT]  **等候偵錯工具連接暫停模式啟動 JVM** 選項適用於進階偵錯在計算模擬器中的案例 （不適用於雲端部署）。 如果 **等候偵錯工具連接暫停模式啟動 JVM** 選項時，就會擱置在伺服器啟動程序，直到 Eclipse 偵錯工具連接到其 JVM。 雖然您可以在使用計算模擬器的偵錯工作階段使用此選項，但請勿將它使用在雲端部署的偵錯工作階段。 伺服器的初始化是在 Azure 啟動工作中進行，而 Azure 雲端在啟動工作完成之前不會提供公用端點。 因此，如果在雲端部署中啟用此選項，啟動程序將無法順利完成，因為它無法接收來自外部 Eclipse 用戶端的連線。
    1.按一下 [ **建立偵錯組態**。
1. 在 **Azure 偵錯組態** 對話方塊 ︰
    1. 如 **Java 專案偵錯**, ，請選取 **MyHelloWorld** 專案。
    1. 如 **設定偵錯的**, ，檢查 **Azure 計算模擬器**。
1. 按一下 [ **確定** 關閉 **Azure 偵錯組態** ] 對話方塊。
1. 按一下 [ **確定** 關閉 **WorkerRole1 偵錯屬性** ] 對話方塊。
1. 在 index.jsp 中設定中斷點：
    1. 在 Eclipse 的專案總管中，展開 **MyHelloWorld**, ，依序展開 **WebContent**, ，然後按兩下 **index.jsp**。
    1. 在 index.jsp 中，以滑鼠右鍵按一下藍色列左側的 Java 程式碼中，按一下 [ **切換中斷點**, ，如下列所示 ︰
        ![][ic551537]
       如果您看到一個藍色列內的中斷點圖示左邊的 Java 程式碼，設定中斷點。
1. 在計算模擬器中啟動應用程式，藉以 **Run in Azure Emulator** Azure 工具列上的按鈕。
1. 在 Eclipse 的功能表上，按一下 [ **執行** 然後按一下 [ **偵錯組態**。
1. 在 **偵錯組態** ] 對話方塊中，展開 **遠端 Java 應用程式** 在左窗格中，選取 **Azure 模擬器 (WorkerRole1)**, ，然後按一下 **偵錯**。
1. 在計算模擬器指出應用程式正在執行，在瀏覽器中之後，執行 **http://localhost:8080//MyHelloWorld**。
    如果出現提示 **確認檢視方塊參數** 對話方塊中，按一下 [ **是**。
    您的偵錯工作階段現在應該執行到設定中斷點的程式碼。

這會告訴您如何在計算模擬器中偵錯；下一節說明如何偵錯部署至 Azure 的應用程式。

## 偵錯注意事項 ##

* 偵錯之後，您可以切換的檢視方塊 **偵錯** 至 **Java** 透過按一下 Eclipse 的功能表上，依序按一下 **視窗**, ，**開啟的觀點來看**, ，然後選取您想要使用的檢視方塊。
* 若要在 GlassFish 中啟用遠端偵錯，請勿使用 Eclipse 的 Azure 工具組的遠端偵錯組態功能。 請改為手動設定 GlassFish。 由於 GlassFish 處理預先定義在環境變數中的 Java 選項的方式，此工具組的遠端偵錯組態功能無法正確搭配 GlassFish 使用。 如果工具組的遠端偵錯組態功能已啟用，可能造成 GlassFish 無法啟動。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][] 

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Using the Azure Service Runtime Library in JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png


