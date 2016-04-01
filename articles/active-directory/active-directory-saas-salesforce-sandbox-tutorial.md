<properties pageTitle="教學課程：Azure Active Directory 與 Salesforce 沙箱整合 | Microsoft Azure" description="了解如何使用 Salesforce 沙箱搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="10/07/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Salesforce 沙箱整合
>[AZURE.TIP]意見反應，按一下 [ [這裡](http://go.microsoft.com/fwlink/?LinkId=521878)。
  
本教學課程的目的是要示範 Azure 與 Salesforce 沙箱的整合。  
沙箱讓您能夠針對不同用途 (例如開發、測試和訓練) 在個別環境中建立貴組織的多個複本，而不會危害 Salesforce 生產環境組織中的資料和應用程式。  
如需詳細資訊，請參閱 [沙箱概觀](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   在 Salesforce.com 中的沙箱
  
如果您在 Salesforce.com 中還沒有有效的沙箱，則您需要連絡 Salesforce。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Salesforce 沙箱的應用程式整合
2.  設定單一登入
3.  啟用您的網域
4.  設定使用者佈建
5.  指派使用者

![案例](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")
##啟用 Salesforce 沙箱的應用程式整合
  
本節的目的是要說明如何啟用 Salesforce 沙箱的應用程式整合。

###若要啟用 Salesforce 沙箱的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")

4.  若要開啟 **應用程式庫**, ，按一下 [ **新增應用程式**, ，然後按一下 [ **加入我的組織使用的應用程式**。

    ![欲執行動作](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "What do you want to do?")

5.  在 **搜尋方塊**, ，型別 **Salesforce 沙箱**。

    ![應用程式庫](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Application Gallery")

6.  在 [結果] 窗格中，選取 **Salesforce 沙箱**, ，然後按一下 [ **完成** 加入應用程式。

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Salesforce 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Salesforce 沙箱** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Salesforce 沙箱** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  在 **設定應用程式 URL** 頁面上，於 **登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 `http://company.my.salesforce.com`, ，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configure App URL")

4. 如果您已在目錄中，設定單一登入的另一個 Salesforce 沙箱執行個體，則您也必須設定 **識別碼** 有相同的值 **登入 URL**。  **識別碼** 欄位可以找到藉由檢查 **顯示進階設定** 上的核取方塊 **設定應用程式 URL** 對話方塊的頁面。

4.  在 **在 Saleforce 沙箱設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Salesforce 沙箱。

6.  在頂端功能表中，按一下 [ **安裝**。

    ![設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Setup")

7.  在左側導覽窗格中按一下 [ **安全性控制**, ，然後按一下 [ **單一登入設定**。

    ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On Settings")

8.  在 [單一登入設定] 區段中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On Settings")

    1.  選取 **已啟用 SAML**。
    2.  按一下 [ **新**。

9.  在 [SAML 單一登入設定] 區段中，執行下列步驟：

    ![SAML 單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML Single Sign-On Settings")

    1.  在 [名稱] 文字方塊中，輸入組態的名稱 (例如 ︰ *SPSSOWAAD\_Test*)。
    2.  在 Azure 網站中，在 **在 Saleforce 沙箱設定單一登入** ] 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者** 文字方塊。
    3.  在 **實體識別碼** 文字方塊中，輸入 **https://test.salesforce.com** 如果這是您要新增至您的目錄的第一個 Salesforce 沙箱執行個體。 如果您已新增的 Salesforce 沙箱執行個體然後如 **實體識別碼** 輸入 **登入 URL**, ，應該以下列格式 ︰ `http://company.my.salesforce.com`
    4.  按一下 [ **瀏覽** 上傳已下載的憑證。
    5.  做為 **SAML 身分識別類型**, ，請選取 **判斷提示包含使用者物件的同盟 ID**。
    6.  做為 **SAML 身分識別位置**, ，請選取 **身分識別位於 Subject 陳述式的 NameIdentifier 元素中**。
    7.  在 Azure 網站中，在 **在 Saleforce 沙箱設定單一登入** ] 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **身分識別提供者登入 URL** 文字方塊。
    8.  在 Azure 網站中，在 **在 Saleforce 沙箱設定單一登入** ] 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **身分識別提供者登出 URL** 文字方塊。
    9.  做為 **服務提供者起始的要求繫結**, ，請選取 **HTTP POST**。
    10. 按一下 [ **儲存**。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configure Single Sign-On")

##啟用您的網域
  
本節假設您已經建立了一個網域。  
如需詳細資訊，請參閱 [定義您的網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

###若要啟用您的網域，請執行下列步驟：

1.  在左的導覽窗格中，按一下 [ **定義域管理**, ，然後按一下 [ **我的網域。**

    ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "My Domain")

    >[AZURE.NOTE]請確定已正確設定您的網域。

2.  在 **登入頁面設定** 區段中，按一下 **編輯**, ，然後當 **驗證服務**, 前一節中，選取 [SAML 單一登入設定的名稱，最後按一下 [ **儲存**。

    ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "My Domain")
  
一旦您設定了網域，您的使用者便應使用該網域 URL 登入至 Salesforce 沙箱。  
若要取得 URL 的值，請按一下您在上一區段中所建立的 SSO 設定檔。
##設定使用者佈建
  
本節的目的是要說明如何對 Salesforce 沙箱啟用 Active Directory 使用者帳戶的使用者佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Salesforce 入口網站上方的導覽列中選取您的名稱來展開使用者功能表：

    ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "My Settings")

2.  從您的使用者功能表選取 [ **我的設定** 開啟您 **我的設定** 頁面。

3.  在左窗格中，按一下 [ **個人** 以展開 [個人] 區段，然後按一下 [ **重設我的安全性權杖**:

    ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "My Settings")

4.  在 **重設我的安全性權杖** 頁面上，按一下 **重設安全性權杖** 要求電子郵件，其中包含您的 Salesforce.com 安全性權杖。

    ![新的權杖](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "New Token")

5.  檢查來自 Salesforce.com 且電子郵件的電子郵件收件匣 」**{1>salesforce.com.com 安全性確認**」 為主題。

6.  檢閱這封電子郵件並複製安全性權杖值。

7.  在 Azure 管理入口網站中，在 **salesforce 沙箱** 應用程式整合頁面上，按一下 [ **設定使用者佈建** 開啟 **設定使用者佈建** ] 對話方塊。

    ![設定使用者佈建](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure user provisioning")

8.  在 **輸入 Salesforce 沙箱認證以啟用自動使用者佈建** 頁面上，提供下列組態設定 ︰

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    1.  在 **Salesforce 沙箱管理使用者名稱** 文字方塊中，輸入 Salesforce 沙箱帳戶名稱具有 **系統管理員** 指派 Salesforce.com 中的設定檔。

    2.  在 **Salesforce 沙箱管理員密碼** 文字方塊中，輸入此帳戶的密碼。

    3.  在 **使用者安全性語彙基元** 文字方塊中，貼上安全性 token 值。

    4.  按一下 [ **驗證** 來驗證您的組態。

    5.  按一下 [ **下一步** 按鈕來開啟 **確認** 頁面。

9.  在 **確認** 頁面上，按一下 **完成** 來儲存組態。
##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Salesforce 沙箱，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Salesforce 沙箱 * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Yes")
  
請等候 10 分鐘並確認帳戶已同步至 Salesforce 沙箱。
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

