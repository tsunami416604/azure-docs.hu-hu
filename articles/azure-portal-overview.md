<properties
    pageTitle="Microsoft Azure 入口網站概觀"
    description="了解如何使用 Microsoft Azure 入口網站。"
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# Microsoft Azure 入口網站概觀

Microsoft Azure 入口網站是一個可以佈建和管理 Azure 資源的中央位置。  本教學課程將您熟悉入口網站，並示範如何使用某些重要功能 ︰
- A **完整 marketplace** ，可讓您瀏覽數千個來自 Microsoft 及其他廠商可以購買，及/或佈建的項目。
- A **統一且可擴充的瀏覽經驗** ，可輕鬆尋找您重視，而且執行各種管理操作的資源。
- **一致的管理頁面** （或刀鋒視窗），可讓您管理 Azure 的各種不同的服務，透過一致的方式來設定、 動作、 帳單資訊、 健全狀況監視與使用量資料，以及許多公開更多。
- A **個人的經驗** ，可讓您建立自訂的啟動螢幕顯示的資訊，您想要看到每次您登入。  您也可以自訂任何包含磚的管理刀鋒視窗。

 ![Azure 入口網站 UI 定位][UIOrientation]

## 開始之前

您需要一個有效的 Azure 訂用帳戶才能完成本教學課程。  如果您沒有帳戶，然後 [申請免費試用版](http://azure.microsoft.com/pricing/free-trial/) 今天。  擁有訂用帳戶之後，您便可以存取入口網站，網址是 [https://portal.azure.com]。

## 如何建立資源

Azure 擁有一個包含數千個項目的 Marketplace，您可以在一個位置即可建立這些項目。  例如，假設您想要建立新的 Windows Server 2012 VM。  [+ 新增] 中心是您進入 Marketplace 精選分類策劃組合的進入點。  每個類別都會有一小組的精選項目，以及一個可顯示所有類別和搜尋之完整 Marketplace 的連結。 若要建立新的 Windows Server 2012 VM，請執行下列動作：  

1.  採用 Windows Server 2012，因此您可以在 [計算] 類別中加以選取。  
2.  填寫表單上的某些基本輸入。
3.  按一下 [建立]，您的 VM 便會立即開始佈建。

通知中心會在完成建立資源時提醒您，且管理刀鋒視窗會隨即開啟 (您之後仍可隨時瀏覽至資源)。

![入口網站的類別目錄][PortalCategories]


## 如何尋找您的資源

您可以隨時將經常存取的資源固定到您的儀表板，但有時您可能需要瀏覽至不常存取的項目。  如下所示的瀏覽中樞是您取得所有資源的方式。  您可以依訂用帳戶進行篩選、選擇/調整資料行大小，以及按一下個別項目以瀏覽至管理刀鋒視窗。

![Browse Hub][BrowseHub]

## 如何管理和委派資源存取

從這個刀鋒視窗中，您可以使用遠端桌面連接虛擬機器、監視關鍵效能度量、使用角色型存取 (RBAC) 來控制此 VM 的存取、設定 VM，以及執行其他重要管理工作。  根據角色委派存取對於大規模管理相當重要。  按一下 [ [這裡](role-based-access-control-configure.md) 若要深入了解。 若要委派資源存取，請執行下列動作：

1.  瀏覽至您的資源。
2.  按一下 [Essentials] 區段中的 [所有設定]。
3.  按一下設定清單中的 [使用者]。
4.  按一下命令列中的 [新增]。
5.  選擇使用者和角色。

![管理資源][ManageResource]

## 如何自訂資源刀鋒視窗

Azure 會預先設定資源的刀鋒視窗，但您可以控制這些刀鋒視窗上的磚。  您可以輕鬆地進入自訂模式，以新增、移除、調整大小，或重新排列磚。 若要自訂刀鋒視窗，請執行下列動作：

1.  瀏覽至您的資源。
2.  按一下 [...] 分頁頂端，您想要自訂。
3.  按一下 [新增組件]。
4.  開始拖放組件。  

![自訂刀鋒視窗][CustomizeBlades]

## 如何取得協助

如果您有任何疑問，我們會隨時提供協助。  入口網站具備可協助您作業的說明及支援頁面。  取決於您 [支援計劃](http://azure.microsoft.com/support/plans/), ，您也可以直接在入口網站中建立支援票證。  建立支援票證之後，您便可以從入口網站中管理票證的生命週期。 您可以瀏覽至 [瀏覽] -> [說明 + 支援] 以取得說明及支援頁面。  

![說明及支援][HelpSupport]

## 摘要

讓我們來檢視您在本教學課程中了解 ︰
- 您了解如何註冊、 取得訂用帳戶，並瀏覽至入口網站
- 您有方向與入口網站 UI，學到如何建立和瀏覽資源
- 您學到如何建立資源，並瀏覽資源
- 您已了解相關的結構或管理刀鋒視窗和如何持續管理不同類型的資源
- 您了解如何自訂入口網站，以將資訊您關心的前端和中心
- 您學到如何控制存取資源使用角色型存取 (RBAC)
- 您了解如何取得說明及支援

Microsoft Azure 入口網站會徹底簡化建置及管理您在雲端中的應用程式。  看看 [管理部落格](http://azure.microsoft.com/blog/topics/management/) 像我們持續維持最新狀態 [聆聽意見](http://feedback.azure.com/forums/223579-azure-preview-portal) 並加以改善。  [ScottGu 的部落格](http://weblogs.asp.net/scottgu) 是另一個很棒的地方尋找所有 Azure 更新。

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png


