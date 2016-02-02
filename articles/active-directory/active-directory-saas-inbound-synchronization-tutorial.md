<properties 
    pageTitle="教學課程：針對輸入同步設定 Workday| Microsoft Azure" 
    description="了解如何使用 Azure Active Directory 中的輸入同步處理，來啟用單一登入、 自動化佈建和更多功能!" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />


# 教學課程：針對輸入同步設定 Workday

>[AZURE.NOTE]Azure Active Directory (AD) Premium 適用於使用 Azure AD 全球執行個體的中國客戶。    
由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure AD Premium。    

本教學課程的目的是說明將連絡人從 Workday 匯入 Microsoft Azure AD 時，在 Workday 和 Microsoft Azure AD 中需要執行的步驟。    
 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Workday 中的租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Workday 的應用程式整合
2.  建立整合系統使用者
3.  建立安全性群組
4.  將整合系統使用者指派到安全性群組
5.  設定安全性群組選項
6.  啟用安全性原則變更
7.  在 Microsoft Azure AD 中設定匯入使用者

## 啟用 Workday 的應用程式整合

本節的目的是要說明如何啟用 Salesforce 的應用程式整合。

### 若要啟用 Workday 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")

4.  若要開啟 [應用程式庫]****，請按一下 [新增應用程式]****，然後按一下 [新增應用程式讓我的組織使用]****。

    ![欲執行動作](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "What do you want to do?")

5.  在**搜尋方塊**中，輸入 **Workday**。

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")

6.  在結果窗格中，選取 [Workday]****，然後按一下 [完成]**** 以加入應用程式。

    ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")

## 建立整合系統使用者

1.  在 **Workday Workbench**, ，輸入 **建立使用者** 搜尋] 方塊中的連結，然後按一下 [ **建立整合系統使用者**。

    ![建立使用者](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "create user")

2.  為新的「整合系統使用者」 提供使用者名稱和密碼來完成「建立整合系統使用者」工作。 讓需要新密碼在下次登入時選項未選取，因為此使用者會登入以程式設計的方式。    
    預設值為 0，會防止使用者工作階段逾時提前離開工作階段逾時分鐘數。

    ![建立整合系統使用者](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Create Integration System User")

## 建立安全性群組

對於本教學課程中所述的案例，您需要建立未受限制的整合系統安全性群組，並將使用者指派至該群組。

1.  輸入 [搜尋] 方塊中建立安全性群組，然後按一下 [建立安全性群組] 連結。

    ![建立安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity Group")

2.  完成「建立安全性群組」工作。 選取 [整合系統安全性群組 — 未受限類型租用戶安全性群組的下拉式清單中，從建立安全性群組的成員將明確新增。

    ![建立安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity Group")

## 將整合系統使用者指派到安全性群組

1.  在 [搜尋] 方塊中輸入編輯安全性群組，然後按一下該連結，  **編輯安全性群組**。

    ![編輯安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Edit Security Group")

2.  依名稱搜尋新的整合安全性群組，並選取該群組。

    ![編輯安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Edit Security Group")

3.  將新的整合系統使用者指派到安全性群組。
 

![系統安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System Security Group")

## 設定安全性群組選項

在此步驟中，您授與下列網域安全性原則所保護的物件上的 Get 和 Put 作業的新安全性群組權限:

-   外部帳戶佈建
-   人員資料：公用人員報告
-   人員資料：所有職位
-   人員資料：目前人員配置資訊
-   人員資料：人員個人檔案的職稱

&nbsp;

1.  在 [搜尋] 方塊中輸入網域安全性原則，然後按一下 [功能區域的網域安全性原則] 連結。

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domain Security Policies")

2.  搜尋系統，並選取 [系統] 功能區域。 按一下標示為 [確定] 按鈕。

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domain Security Policies")

3.  在清單中的 [系統] 功能區域的安全性原則，展開 [安全性系統管理，並選取 [網域安全性原則，外部帳戶佈建。

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domain Security Policies")

4.  按一下 [編輯權限] 按鈕，並將，在 [編輯權限] 畫面中，加入新的安全性群組具有 Get 和 Put 整合權限的安全性群組的清單。

    ![編輯權限](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Edit Permission")

5.  重複上述步驟 1，返回選取功能區域，畫面和搜尋人員配置，這次選取 [人員配置] 功能區域，然後按一下標示 [確定] 的按鈕。

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domain Security Policies")

6.  在 [人員配置] 功能區域的安全性原則清單中，展開 [工作者資料: 人員配置，並重複上述步驟 4 的每一種剩餘的安全性原則:

    -   人員資料：公用人員報告
    -   人員資料：所有職位
    -   人員資料：目前人員配置資訊
    -   人員資料：人員個人檔案的職稱

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domain Security Policies")

## 啟用安全性原則變更

1.  輸入 [搜尋] 方塊中啟動，然後按一下 [啟動擱置安全性原則變更] 連結。

    ![啟動](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activate")

2.   作為稽核用途，輸入註解，然後按一下標示為 [確定] 按鈕，開始啟動擱置安全性原則變更的工作。


    ![啟用擱置的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activate Pending Security")

3.  完成下一個畫面上的工作，藉由檢查標示為確認，然後按一下標示為 [確定] 按鈕上的核取方塊。

    ![啟用擱置的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activate Pending Security")

## 在 Microsoft Azure AD 中設定匯入使用者

本節的主要目的是說明如何設定 Microsoft Azure AD 以從 Workday 匯入人員。

### 若要在 Microsoft Azure AD 中設定匯入使用者，請執行下列步驟：

1.  在 [Workday]**** 應用程式整合頁面上，按一下 [設定使用者匯入]**** 以開啟 [設定佈建]**** 對話方塊。

2.  在 [設定及管理員認證]**** 頁面上，執行以下步驟，然後按一下 [下一步]：

    ![設定及管理員認證](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Settings and admin credentials")

    1.  在 **Workday 管理員使用者名稱** 文字方塊中，輸入您在中建立的使用者名稱 [建立整合系統使用者](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) 一節。
    2.  在 **Workday 管理員密碼** 文字方塊中，輸入您在中建立之使用者的密碼 [建立整合系統使用者](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) 一節。
    3.  在 [Workday 租用戶 URL]**** 文字方塊中，輸入您 Workday 租用戶的 URL。

3.  在 [測試連接]**** 頁面上，按一下 [開始測試]**** 來確認連接，然後按一下 [下一步]****。

    ![測試連接](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Test connection")

4.  在 [佈建選項]**** 頁面上，按一下 [下一步]****。

    ![佈建選項](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Provisioning options")

5.  在 [開始佈建]**** 對話方塊中，按一下 [完成]****。

    ![開始佈建](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Start provisioning")

您現在可以移至 [使用者]**** 區段，並檢查是否已匯入您的 Workday 使用者。





