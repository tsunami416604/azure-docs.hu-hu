<properties 
   pageTitle="對無法啟動的角色進行疑難排解 |Microsoft Azure"
   description="以下是雲端服務角色無法啟動的一些常見原因。 此外也提供這些問題的解決方案。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# 對無法啟動的雲端服務角色進行疑難排解的一般步驟

以下是與無法啟動的 Azure 雲端服務角色相關的一些常見問題和解決方案。

## 連絡 Azure 客戶支援

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。

或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。 使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 遺失 Dll 或相依性
沒有回應的角色和角色之間循環 **初始化**, ，**忙碌**, ，和 **停止** 狀態可能因遺失 Dll 或組件。

**徵兆 ︰**  遺失 Dll 或組件的徵兆可能是 ︰

- 您的角色執行個體狀態之間循環 **初始化** / **忙碌** / **停止**
- 您的角色執行個體已移至 **準備** 但瀏覽至您的 web 應用程式頁面不會顯示

解決方式 ︰ 有三種調查這些問題的建議的方法。

## 診斷 Web 角色中遺失 DLL 的問題

當您瀏覽至在 Web 角色中部署的網站時，瀏覽器顯示如下的伺服器錯誤：

!['/' 應用程式中有伺服器錯誤。](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## 關閉自訂錯誤以診斷問題

設定 Web 角色的 web.config 以將自訂錯誤模式設為關閉，並重新部署服務，可以檢視更完整的錯誤。

若要檢視更完整的錯誤而不使用遠端桌面：

1. 在 Visual Studio 中開啟解決方案。

2. 在 **方案總管] 中**, ，找出 web.config 檔案，並開啟它。

3. 在 web.config 檔案中找出 system.web 區段，並加入下面這一行：

    ```xml
    <customErrors mode="Off" />
    ```

4. 儲存檔案。

5. 重新封裝並重新部署服務。

重新部署服務之後，您會看到下列錯誤，以及遺失組件或 DLL 的名稱。

## 從遠端檢視錯誤以診斷問題

您可以使用遠端桌面存取角色，並從遠端檢視更完整的錯誤。 使用下列步驟，可使用遠端桌面檢視錯誤：

1. 確定已安裝 Azure SDK 1.3 或更新版本。

2. 在使用 Visual Studio 方案的部署，期間選擇 「 設定遠端桌面連線...」 如需有關如何設定遠端桌面連線的詳細資訊，請參閱 [與 Azure 角色使用遠端桌面](https://msdn.microsoft.com/library/gg443832.aspx)。

3. 在 Microsoft Azure 傳統入口網站，一旦執行個體顯示狀態為 **準備**, ，按一下其中一個角色執行個體。

4. 按一下 [ **連接** 圖示 **遠端存取** 功能區

5. 使用在遠端桌面設定期間指定的認證登入虛擬機器。

6. 開啟命令提示字元。

7. 輸入 `IPconfig`。

8. 記下 IPV4 位址值。

9. 開啟 Internet Explorer。

10. 輸入 Web 應用程式的位址和名稱。 例如，`http://<IPV4 Address>/default.aspx`。

瀏覽至網站會傳回更明確的錯誤訊息。

* '/' 應用程式中有伺服器錯誤

* 說明：執行目前的 Web 要求期間發生未處理的例外狀況。 請檢閱堆疊追蹤，以進一步了解錯誤以及它產生於程式碼中的何處。

* 例外狀況詳細資料：System.IO.FIleNotFoundException：無法載入檔案或組件 ‘Microsoft.WindowsAzure.StorageClient，Version=1.1.0.0，Culture=neutral，PublicKeyToken=31bf856ad364e35’ 或其相依性之一。 系統找不到指定的檔案。

例如：

!['/' 應用程式中有明確的伺服器錯誤。](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## 使用計算模擬器診斷問題

您可以使用 Azure Microsoft Azure 計算模擬器來診斷和排解遺失相依性與 web.config 錯誤的問題。

若要讓這種診斷方法獲得最佳結果，您應該使用具有 Windows 全新安裝的電腦或虛擬機器。 若要達到模擬 Azure 環境的最佳效果，您應使用 Windows Server 2008 R2 x64。

1. 安裝獨立版的 [Azure SDK](https://azure.microsoft.com/downloads)

2. 在開發電腦上建置雲端服務專案。

3. 在 Windows 檔案總管中，瀏覽至雲端服務專案的 bin\debug 資料夾。

4. 將 .csx 資料夾及 .cscfg 檔案複製到您用來偵錯問題的電腦。

5. 在初始狀態的電腦上開啟 Azure SDK 命令提示字元，並輸入 `csrun.exe /devstore:start`。

6. 在命令提示字元中輸入 `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`。

7. 在角色啟動時，您會在 Internet Explorer 中看到詳細的錯誤資訊。  您也可以使用標準 Windows 疑難排解工具進一步診斷問題。

## 使用 IntelliTrace 診斷問題

對於背景工作和 web 角色使用.NET Framework 4，您可以使用 [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx) 中所提供 [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs)。

請遵循下列步驟，部署已啟用 IntelliTrace 的服務：

1. 確認已安裝 Azure SDK 1.3 或更新版本。

2. 使用 Visual Studio 部署解決方案。 在部署期間，檢查 **為.NET 4 角色啟用 IntelliTrace** 核取方塊。

3. 執行個體啟動後，開啟 **伺服器總管**。

4. 展開 **Azure\\Cloud 服務** 節點並找出部署。

5. 展開部署，直到您看見角色執行個體。 以滑鼠右鍵按一下其中一個執行個體。

6. 選擇 **檢視 IntelliTrace 記錄檔**。  **IntelliTrace 摘要** 隨即開啟。

7. 找出摘要的例外狀況區段。 如果有例外狀況會標示 **例外狀況資料**。

8. 展開 **例外狀況資料** ，並尋找 **System.IO.FileNotFoundException** 類似下列的錯誤 ︰

![例外狀況資料、遺失檔案或組件](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## 解決遺失 Dll 和組件的問題

若要解決遺失 DLL 和組件錯誤，請遵循下列步驟：

1. 在 Visual Studio 中開啟解決方案。

2. 在 **方案總管] 中**, ，開啟 **參考** 資料夾。

3. 按一下錯誤中識別的組件。

4. 在 **屬性** 窗格然後找出 [複製本機] 屬性，並將值設為 **True**。

5. 重新部署託管服務。

一旦系統已驗證已更正所有錯誤，服務可能會部署而不需要 **為.NET 4 角色啟用 IntelliTrace** 設定檢查。

## 後續步驟

檢視更多 [疑難排解文章](..\?tag=top-support-issue&service=cloud-services) 雲端服務。



