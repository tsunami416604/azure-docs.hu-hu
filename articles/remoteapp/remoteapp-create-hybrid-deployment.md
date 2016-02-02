<properties 
    pageTitle="如何建立 Azure RemoteApp 的混合式收藏 | Microsoft Azure" 
    description="了解如何建立連接內部網路的 RemoteApp 部署。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="elizapo"/>


# 如何建立 Azure RemoteApp 的混合式收藏

Azure RemoteApp 收藏分成兩種：

- 雲端：完全位於 Azure 中。 您可以選擇在雲端儲存所有資料 (也就是僅限雲端的集合) 或將您的集合連線到 VNET，並於該處儲存資料。
- 混合式：包含可內部存取的虛擬網路，這需要使用 Azure AD 和內部部署的 Active Directory 環境。

不知道您需要什麼嗎? 簽出 [您需要 Azure RemoteApp 的哪一種集合](remoteapp-collections.md)。

本教學課程將逐步引導您完成建立混合式收藏的程序。 有八個步驟：

1.  決定 [映像](remoteapp-imageoptions.md) 使用於您的集合。 您可以建立自訂映像，或使用您的訂用帳戶隨附的其中一個 Microsoft 映像。
2. 設定虛擬網路。 簽出 [VNET 規劃](remoteapp-planvpn.md) 和 [sizing](remoteapp-vnetsizing.md) 資訊。
2.  建立收藏。
2.  將您的集合加入本機網域。
3.  將範本映像新增到您的收藏。
4.  設定目錄同步處理。 RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 Azure Active Directory 同步作業，或 2) 設定不具 [密碼同步] 選項的 Azure Active Directory 同步作業，但使用同盟至 AD FS 的網域。 簽出 [Active Directory 搭配 RemoteApp 的組態資訊](remoteapp-ad.md)。
5.  發佈 RemoteApp 應用程式。
6.  設定使用者存取。

**開始之前**

在建立收藏之前，您必須執行下列作業：

- [註冊](http://azure.microsoft.com/services/remoteapp/) Azure remoteapp。
- 要做為 Azure RemoteApp 服務帳戶的 Active Directory 中建立使用者帳戶。 限制此帳戶的權限，使其只能將機器加入網域中。
- 收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
- 安裝 [PowerShell](../install-configure-powershell.md) 模組。
- 收集您想授與存取權之使用者的相關資訊。 您必須為每個使用者的 Azure Active Directory 使用者主體名稱 (例如 name@contoso.com)。 請確定 UPN 符合 Azure AD 和 Active Directory。
- 選擇範本映像。 Azure RemoteApp 範本映像包含的應用程式與您要發佈您的使用者的程式。 請參閱 [Azure RemoteApp 映像選項](remoteapp-imageoptions.md) 如需詳細資訊。
- 想要使用 Office 365 ProPlus 的映像嗎？ 簽出資訊 [這裡](remoteapp-officesubscription.md)。
- [設定 RemoteApp 的 Active Directory](remoteapp-ad.md)。



## 步驟 1：設定虛擬網路

您可以部署混合式集合，會使用現有的 Azure 虛擬網路，或您可以建立新的虛擬網路。 虛擬網路可讓您的使用者透過 RemoteApp 遠端資源存取您本機網路上的資料。 使用 Azure 虛擬網路可以讓您的收藏直接從網路存取其他 Azure 服務和部署到該虛擬網路的虛擬機器。

請確定您檢閱 [VNET 規劃](remoteapp-planvnet.md) 和 [VNET 大小](remoteapp-vnetsizing.md) 資訊之前建立 VNET。

### 建立 Azure VNET 並將它加入您的 Active Directory 部署

藉由建立啟動 [虛擬網路](../virtual-network/virtual-networks-create-vnet.md)。 您可以在 Azure 管理入口網站的 [網路]**** 索引標籤上進行。 您必須將虛擬網路連線到同步到您 Azure Active Directory 租用戶的 Active Directory 部署。

請參閱 [關於虛擬網路設定管理入口網站中](../virtual-network/virtual-networks-settings.md) 如需詳細資訊。

### 請確定您的虛擬網路可供 Azure RemoteApp

在建立收藏之前，讓我們來確定新的虛擬網路已準備好。 您可以執行下列動作來驗證：

1. 在您剛才為 RemoteApp 建立的虛擬網路子網路內建立 Azure 虛擬機器。
2. 使用遠端桌面連線到虛擬機器。 (按一下 [連線]****)。
3. 將虛擬機器加入您要用於 RemoteApp 的同一個 Active Directory 部署。

有作用嗎？ 您的虛擬網路和子網路已針對 Azure RemoteApp 準備就緒！

您可以找到有關建立 Azure 虛擬機器和連接的詳細資訊，使用遠端桌面 [這裡](https://msdn.microsoft.com/library/azure/jj156003.aspx)。

## 步驟 2: 建立 Azure RemoteApp 集合

1. 在 [Azure 入口網站](http://manage.windowsazure.com), ，請移至 [Azure RemoteApp] 頁面上。
2. 按一下 [新增] > [使用 VNET 建立]****。
3. 輸入收藏的名稱。
4. 選擇您要使用的方案 - 標準或基本。
5. 從下拉式清單中依序選擇您的 VNET 和子網路。
6. 選擇加入至您的網域。
5. 按一下 [建立 RemoteApp 收藏]****。

建立 Azure RemoteApp 收藏之後，按兩下集合的名稱。 這時會顯示 [快速入門]**** 頁面，您可以在這裡完成設定集合。

發生錯誤了嗎？ 簽出 [疑難排解資訊的混合式收藏](remoteapp-hybridtrouble.md)。

## 步驟 3：將您的集合連結到本機網域

1. 在 [快速入門]**** 頁面上，按一下 [加入本機網域]****。
2. Azure RemoteApp 服務帳戶加入本機的 Active Directory 網域。 您將需要網域名稱、組織單位、服務帳戶的使用者名稱和密碼。

    這是如果您已遵循步驟中的您所收集的資訊 [設定 Azure RemoteApp 的 Active Directory](remoteapp-ad.md)。


## 步驟 4: 連結至 Azure RemoteApp 映像

Azure RemoteApp 範本映像包含您想要與使用者共用的程式。 您可以建立新 [範本映像](remoteapp-imageoptions.md) 或連結至現有的映像 (已匯入或上傳至 Azure RemoteApp)。 您也可以連結到其中一個 Azure RemoteApp [範本映像](remoteapp-images.md) 包含 Office 365 或 Office 2013 (供試用) 程式。

如果您要上傳新映像，您必須輸入名稱，並選擇映像的位置。 在精靈的下一頁，您會看見一組 PowerShell Cmdlet - 從提高權限的 Windows PowerShell 提示複製並執行這些 Cmdlet，可上傳指定的映像。

如果您要連結至現有的範本映像，請直接指定映像名稱、位置和相關聯的 Azure 訂閱。



## 步驟 5：設定 Active Directory 目錄同步處理

RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 Azure Active Directory 同步作業，或 2) 設定不具 [密碼同步] 選項的 Azure Active Directory 同步作業，但使用同盟至 AD FS 的網域。

簽出 [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) -這個文章可協助您設定 4 個步驟中的目錄整合。

請參閱 [目錄同步作業藍圖](http://msdn.microsoft.com//library/azure/hh967642.aspx) 規劃資訊和詳細的步驟。

## 步驟 6: 發佈應用程式

Azure RemoteApp 應用程式是應用程式或提供給使用者的程式。 此程式位於您為收藏上傳的範本映像中。 當使用者存取應用程式時，應用程式看似會在其本機環境中執行，但實際上是在 Azure 中執行。

您必須先將應用程式發佈至使用者摘要，您的使用者才能存取這些應用程式；使用者摘要是您的使用者可透過遠端桌面用戶端存取的可用應用程式清單。

您可以發佈多個應用程式到您的集合。 在發佈頁面中，按一下 [ **發行** 來新增應用程式。 您可以從範本映像的 [開始]**** 功能表發佈，或藉由為 App 指定範本映像的路徑來發佈。 如果您選擇從 [開始]**** 功能表新增，請選擇要加入的程式。 如果您選擇提供應用程式的路徑，請提供應用程式的名稱，以及應用程式在範本映像上的安裝路徑。

## 步驟 7：設定使用者存取

既然您已建立您的集合，您需要新增您想要能夠使用遠端資源的使用者。 您提供存取權必須存在於 Active Directory 租用戶訂用帳戶相關聯的使用者，您用來建立此 Azure RemoteApp 收藏。

1.  在 [快速入門] 頁面上，按一下 [Configure user access]****。
2.  輸入工作帳戶 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

    **注意：**

    確定您使用的是 “user@domain.com” 格式。

    如果您的收藏中使用 Office 365 ProPlus，您必須為使用者使用 Active Directory 身分識別。 這有助於驗證授權。

3.  在驗證使用者之後，按一下 [儲存]****。


## 後續步驟

這樣就大功告成了-您已成功建立和部署 Azure RemoteApp 混合式收藏。 下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。 您可以尋找 Azure RemoteApp 快速入門] 頁面上的用戶端的 URL。 接著，請讓使用者登入用戶端，並存取您所發佈的應用程式。



### 幫我們來協助您

您知道除了評比這篇文章以及在下面留言以外，您可以變更文件本身嗎？ 有所遺漏？ 有所錯誤？ 我是否撰寫了令人混淆的內容？ 向上捲動並按一下 [在 GitHub 上編輯]**** 以進行變更 - 系統會顯示這些變更以供我們檢閱，而我們簽核後，您就會在這裡看到您所進行的變更和改良。




