<properties 
    pageTitle="如何建立 Azure RemoteApp 的雲端收藏 | Microsoft Azure" 
    description="了解如何建立將資料儲存在 Azure 雲端中的 Azure RemoteApp 部署。" 
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

# 如何建立 Azure RemoteApp 的雲端收藏

有兩種類型的 [Azure RemoteApp 集合](remoteapp-collections.md): 

- 雲端：完全位於 Azure 中。 您可以選擇在雲端儲存所有資料 (也就是僅限雲端的集合) 或將您的集合連線到 VNET，並於該處儲存資料。   
- 混合式：包含可內部存取的虛擬網路，這需要使用 Azure AD 和內部部署的 Active Directory 環境。

本教學課程將逐步引導您完成建立雲端收藏的程序。 共有四個步驟： 

1.  建立 Azure RemoteApp 集合。
2.  選擇性地設定目錄同步處理。 如果您使用 Azure AD + Active Directory，就必須將使用者、連絡人和密碼從您的內部部署 Active Directory 同步處理至 Azure AD 租用戶。
5.  發佈應用程式。
6.  設定使用者存取。


**開始之前**

在建立收藏之前，您必須執行下列作業：

- [註冊](http://azure.microsoft.com/services/remoteapp/) Azure remoteapp。 
- 收集您想授與存取權之使用者的相關資訊。 這可以是使用者 Microsoft 帳戶資訊或 Active Directory 工作帳戶資訊。
- 此程序假設您將使用您的訂用帳戶隨附的範本映像之一，或是您已上傳所要使用的範本映像。 如果您需要上傳不同的範本映像，您可以從 [範本映像] 頁面執行此作業。 只要按一下 **上傳範本映像** 並遵循精靈中的步驟。 
- 想要使用 Office 365 ProPlus 的映像嗎？ 簽出資訊 [這裡](remoteapp-officesubscription.md)。
- 想提供自訂應用程式或 LOB 程式？ 建立新 [映像](remoteapp-imageoptions.md) ，並使用您的雲端集合中。
- 了解您是否需要連接到 VNET。 如果您選擇連接到 VNET，請確定它符合 [大小的指導方針](remoteapp-vnetsizing.md) 並且它 [可以連線到 RemoteApp](remoteapp-vnet.md)。 簽出 [VNET 規劃文章 ](remoteapp-planvnet.md)如需詳細資訊。
- 如果您使用 VNET，請決定是否要將它聯結至本機 Active Directory 網域。

## 步驟 1：建立雲端集合 - 不論是否有 VNET##


使用下列步驟以 **建立僅限雲端集合**:

1. 在 Azure 管理入口網站中，移至 RemoteApp 頁面。
2. 按一下 [ **新 > [快速建立**。
3. 輸入收藏的名稱，然後選取區域。
4. 選擇您要使用的方案 - 標準或基本。
5. 選擇要用於此收藏的範本。 

    **提示 ︰** 隨附於您的 RemoteApp 訂閱 [範本映像](remoteapp-images.md) 包含 Office 365 或 Office 2013 （供試用） 程式，有些程式已發佈 （例如 Word)，有些即將發佈。 您也可以建立新 [映像](remoteapp-imageoptions.md) ，並使用您的雲端集合中。


1. 按一下 [ **建立 RemoteApp 收藏**。
    
    **重要事項 ︰** 可能需要 30 分鐘的時間來佈建您的集合。

建立了 Azure RemoteApp 集合之後，請按兩下集合的名稱。 將會出現 **快速入門** 頁面-這是您完成設定集合。

使用下列步驟來建立 **cloud + VNET 集合**:

1. 在管理入口網站中，前往 [Azure RemoteApp] 頁面。
2. 按一下 [ **新** > **使用 VNET 建立**。
3. 輸入收藏的名稱。
4. 選擇您要使用的方案 - 標準或基本。
5. 選擇您已經建立的 VNET。 不知道該怎麼做？ 現在，這些步驟都在 [混合式](remoteapp-create-hybrid-deployment.md) 主題。
6. 決定您是否要將集合聯結至網域。 如果是，您需要使用 AD Connect 來整合 Azure AD 及 Active Directory 環境。 底下有涵蓋在 **步驟 2**。
6. 按一下 [ **建立 RemoteApp 收藏**。


## 步驟 2：設定 Active Directory 目錄同步處理 (選用) ##

如果您想要使用 Active Directory，Azure RemoteApp 需要 Azure Active Directory 與您的內部部署 Active Directory 使用者、 連絡人和密碼同步處理至 Azure Active Directory 租用戶之間的目錄同步處理。 請參閱 [設定 Azure remoteapp 的 Active Directory](remoteapp-ad.md) 的規劃資訊。 您也可以直接跳 [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) 資訊。

## 步驟 3：發佈應用程式 ##

Azure RemoteApp 應用程式就是您提供給使用者的應用程式或程式。 此程式位於您為收藏上傳的範本映像中。 當使用者存取應用程式時，應用程式看起來會像是在本機環境中執行，但其實是在 Azure 中執行。 

您必須先將應用程式發佈至使用者摘要，您的使用者才能存取這些應用程式；使用者摘要是您的使用者可透過遠端桌面用戶端存取的可用應用程式清單。
 
您可以將多個應用程式發佈到自己的 Azure RemoteApp 集合。 在發佈頁面中，按一下 [ **發行** 新增程式。 您可以從發行 **啟動** ] 功能表上的範本映像，或藉由指定應用程式在範本映像的路徑。 如果您選擇從 [開始] 功能表新增，請選擇要發佈的應用程式。 如果您選擇提供應用程式的路徑，請提供應用程式的名稱，以及應用程式在範本映像上的安裝路徑。

## 步驟 4：設定使用者存取 ##

您已經建立集合，現在您必須新增可使用您遠端資源的使用者。 如果您使用 Active Directory，則您要給予存取權的使用者，必須存在於與您用來建立此集合的訂用帳戶相關聯的 Active Directory 租用戶中。

1.  從 [快速入門] 頁面中，按一下 [ **設定使用者存取**。 
2.  輸入工作帳戶 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

    **注意：** 

    確定您使用的是 “user@domain.com” 格式。

    如果您的收藏中使用 Office 365 ProPlus，您必須為使用者使用 Active Directory 身分識別。 這有助於驗證授權。 

3.  驗證使用者之後，請按一下 **儲存**。


## 後續步驟 ##

您已經成功建立並部署了自己的 Azure RemoteApp 雲端收藏。 下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。 您可以在 Azure RemoteApp 的 [快速啟動] 頁面上找到用戶端的 URL。 接著，請讓使用者登入用戶端，並存取您所發佈的應用程式。

### 幫我們來協助您 
您知道除了評比這篇文章以及在下面留言以外，您可以變更文件本身嗎？ 有所遺漏？ 有所錯誤？ 我是否撰寫了令人混淆的內容？ 向上捲動，然後按一下 [ **GitHub 上的編輯** 若要變更-供檢閱，這些會變成給我們，接著，我們登入它們，就會看到您的變更和改進這裡。

