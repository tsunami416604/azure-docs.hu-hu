<properties 
    pageTitle="教學課程：針對輸入同步設定 Workday| Microsoft Azure" 
    description="了解如何使用 Workday 做為 Azure Active Directory 身分識別資料的來源。" 
    services="active-directory" 
    authors="MarkusVi"  
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

本教學課程的目的是說明將連絡人從 Workday 匯入 Azure AD 時，在 Workday 和 Azure AD 中需要執行的步驟。

本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure AD Premium 訂用帳戶
-   Workday 中的租用戶

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 Workday 的應用程式整合

2. 建立整合系統使用者

3. 建立安全性群組

4. 將整合系統使用者指派到安全性群組

5. 設定安全性群組選項

6. 啟用安全性原則變更

7. 在 Azure AD 中設定使用者匯入



## 啟用 Workday 的應用程式整合

本節的目的是概述如何啟用 Workday 的應用程式整合。

### 若要啟用 Workday 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Add application")

5. 在搜尋方塊中，輸入 **Workday**。

    ![從組件庫新增應用程式](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Add an application from gallerry")

6. 在結果窗格中，選取 [Workday]，然後按一下 [完成] 以加入應用程式。

    ![應用程式資源庫](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Application gallery")





## 建立整合系統使用者

1. 在 **Workday Workbench**, ，輸入 [搜尋] 方塊中，在建立使用者並按一下 [ **建立整合系統使用者**。<br><br>   ![建立使用者](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Create user")

2. 為新的「整合系統使用者」 提供使用者名稱和密碼來完成「建立整合系統使用者」****工作。讓需要新密碼在下次登入時選項未選取，因為此使用者會登入以程式設計的方式。 <br>
保持 [Session Timeout Minutes] 為預設值 [0]，這會防止使用者的工作階段提早逾時。<br><br>   ![建立整合系統使用者](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Create Integration System User")





## 建立安全性群組

對於本教學課程中所述的案例，您需要建立未受限制的整合系統安全性群組，並將使用者指派至該群組。


1. 輸入 [搜尋] 方塊中建立安全性群組，然後按一下 [ **建立安全性群組**。<br><br>   ![CreateSecurity 群組](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity Group")

2. 完成「建立安全性群組」工作。從 [租用安全性群組類型] 下拉式清單選取 [整合系統安全性群組—未受限制]，以建立需要明確地加入使用者的安全性群組。<br><br>   ![CreateSecurity 群組](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity Group")




## 將整合系統使用者指派到安全性群組

1. 在 [搜尋] 方塊中，輸入編輯安全性群組，然後按一下 **編輯安全性群組**。<br><br>   ![編輯安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edit Security Group")

2. 依名稱搜尋新的整合安全性群組，並選取該群組。<br><br> ![編輯安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Edit Security Group")
編輯安全性群組

3. 將新的整合系統使用者指派到安全性群組。<br><br>   ![系統安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System Security Group")




## 設定安全性群組選項

在此步驟中，您授予新的安全性群組，對受下列網域安全性原則保護之物件進行 **Get** 和 **Put** 作業：

- 外部帳戶佈建

- 人員資料：公用人員報告

- 人員資料：所有職位

- 人員資料：目前人員配置資訊

- 人員資料：人員個人檔案的職稱


1. 在搜尋方塊中輸入 domain security policies，然後按一下 [功能區域的網域安全性原則]。<br><br>   ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domain Security Policies")

2. 搜尋 system 並選取 [系統]**** 功能區域。按一下 [確定]****。<br><br>   ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domain Security Policies")

3. 在 [系統] 功能區域的安全性原則清單中，展開 [安全性管理]，並選取 [外部帳戶佈建] 網域安全性原則。<br><br>   ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domain Security Policies")

4. 按一下 [ **編輯權限**, ，然後在 **編輯權限**對話方塊頁面上，將新的安全性群組新增至安全性群組與清單 **取得** 和 **放** 整合權限。<br><br>   ![編輯權限](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Edit Permission")

5. 重複上述步驟 1，返回選取功能區域，畫面，這次，搜尋人員配置，請選取 [人員配置] 功能區域，然後按一下 **確定**。<br><br> ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domain Security Policies")

6. 在 [人員配置] 功能區域的安全性原則清單中，展開 [人員資料：人員配置]，並對其餘的各安全性原則重複上述步驟 4：

     - 人員資料：公用人員報告

     - 人員資料：所有職位

     - 人員資料：目前人員配置資訊

     - 人員資料：人員個人檔案的職稱


<br><br>   ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domain Security Policies")







## 啟用安全性原則變更

1. 在搜尋方塊中輸入 activate，然後按一下 [啟用擱置的安全性原則變更] 連結。<br><br>   ![啟動](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activate")

2. 輸入註解作為稽核用途，一開始先啟動擱置安全性原則變更的工作，然後按一下 [ **確定**。<br><br>   ![啟動擱置安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activate Pending Security")

3. 選取核取方塊，確認，標示為完成下一個畫面上的工作，然後按一下 [ **確定**。<br><br>   ![啟動擱置安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activate Pending Security")





## 在 Azure AD 中設定使用者匯入

本節的主要目的是說明如何設定 Azure AD 以從 Workday 匯入人員。


### 若要在 Azure AD 中設定匯入使用者，請執行下列步驟：

1. 在 [Workday]**** 應用程式整合頁面上，按一下 [設定使用者匯入]**** 以開啟 [設定佈建]**** 對話方塊。

2.在 **設定和管理員認證** 頁面上，執行下列步驟，然後按 **下一步**: <br><br>   ![設定和管理員認證](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Settings and admin credentials")

     2.1. 在 [Workday 管理員使用者名稱] 文字方塊中，輸入您在 「 建立中建立的使用者名稱整合系統使用者一節。

     2.2. 在 [Workday 管理員密碼] 文字方塊中，輸入您在 「 建立中建立之使用者的密碼整合系統使用者一節。

     2.3. 在 [Workday 租用戶 URL] 文字方塊中，輸入 URL 或您的 Workday 租用戶。

3. 在 [測試連接]**** 頁面上，按一下 [開始測試]**** 來確認連接，然後按一下 [下一步]****。<br><br>   ![測試連接](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test connection")

4. 在 **佈建** 選項] 頁面上，按一下 [ **下一步**。<br><br>   ![佈建選項](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Provisioning options")

5. 在 [開始佈建]**** 對話方塊中，按一下 [完成]****。<br><br>   ![開始佈建](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Start provisioning")


您現在可以移至 [使用者]**** 區段，並檢查是否已匯入您的 Workday 使用者。



## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)





