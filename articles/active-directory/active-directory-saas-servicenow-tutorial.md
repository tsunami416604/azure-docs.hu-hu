<properties 
    pageTitle="教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Azure" 
    description="" 
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
    ms.date="11/02/2015" 
    ms.author="markvi" />


# 教學課程：Azure Active Directory 與 ServiceNow 整合

本教學課程的目的是要示範 Azure 與 ServiceNow 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ServiceNow 中的租用戶



本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ServiceNow 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scenario")
## 啟用 ServiceNow 的應用程式整合

本節的目的是概述如何啟用 ServiceNow 的應用程式整合。

### 若要啟用 ServiceNow 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋]**** 對話方塊中，輸入 **ServiceNow**。

    ![應用程式資源庫](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Application gallery")

7.  在結果窗格中，選取 [ServiceNow]****，然後按一下 [完成]**** 來加入應用程式。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
## 設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 ServiceNow 中進行驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。

### 若要設定單一登入，請執行下列步驟：

1.  

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2.  

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3.  在 [設定應用程式設定]**** 頁面上，執行下列步驟：

![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")



b.

c. 按 [**下一步**]

4.  

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")

4.  

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

1. 以系統管理員身分登入您的 ServiceNow 應用程式。

2. 

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configure app URL")

3. 在 [多個提供者 SSO 內容]**** 對話方塊上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")

    a. 針對 [啟用多個提供者 SSO]****，選取 [是]****。

    b. 針對 [啟用偵錯記錄有多個提供者 SSO 整合]****，選取 [是]****。

    c. 在 [使用者資料表上的欄位...]**** 文字方塊中，輸入 **user_name**。

    d. 按一下 [儲存]****。

1. 

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")

1. 在 [X.509 憑證]**** 對話方塊中，按一下 [新增]****。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")

1. 在 [X.509 憑證]**** 對話方塊中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")

    a. 按一下 [新增]****。

    b.

    c. 選取 [使用中]****。

    d. 針對 [格式]****，選取 [PEM]****。

    e. 針對 [類型****，選取 [信任存放區憑證]****。

    f.
    > [AZURE.NOTE] 

    g.

    h. 按一下 [更新]****。

1. 

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")

1. 在 [身分識別提供者]**** 對話方塊中，按一下 [新增]****：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")

1. 在 [身分識別提供者]**** 對話方塊中，按一下 [SAML2 更新 1]****：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")

1. 在 [SAML2 Update1 內容] 對話方塊上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")

    a.

    b. 在 [使用者欄位]**** 文字方塊中，輸入 **email**。

    c. 在 Azure AD 入口網站中，複製**身分識別提供者 ID** 值，然後貼到 [身分識別提供者 URL]**** 文字方塊中。

    d. 在 Azure AD 入口網站中，複製**驗證要求 URL** 值，然後貼到 [身分識別提供者的 AuthnRequest]**** 文字方塊中。

    e. 在 Azure AD 入口網站中，複製**單一登出服務 URL** 值，然後貼到 [身分識別別提供者的 SingleLogoutRequest]**** 文字方塊中。

    f. 在 [ServiceNow 首頁]**** 文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。
    > [AZURE.NOTE] 

    g. 在 [對象識別碼/核發者]**** 文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    h. 在 [對象 URL]**** 文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    i.

    

    取消選取 [建立 AuthnContextClass]****。

    

    在 [時鐘誤差]**** 文字方塊中，輸入 **60**。

    針對**單一登入指令碼**，選取 **MultiSSO_SAML2_Update1**。

    針對 **x509 憑證**，選取您在上一個步驟中建立的憑證。

    按一下 [提交]****。

6. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]****。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

7. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")



## 設定使用者佈建

本節的目的是要說明如何對 ServiceNow 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 

2. 
設定使用者佈建

     2.1. 在 [ServiceNow 執行個體名稱]**** 文字方塊中，輸入 ServiceNow 執行個體名稱。

     2.2. 在 [ServiceNow 管理使用者名稱]**** 文字方塊中，輸入 ServiceNow 管理員帳戶的名稱。

     2.3. 在 [ServiceNow 管理員密碼]**** 文字方塊中，輸入這個帳戶的密碼。

     2.4. 按一下 [驗證]**** 來驗證您的組態。

     2.5. 按 [下一步]**** 按鈕以開啟 [後續步驟]**** 頁面。

     2.6. 

     2.7. 在 [後續步驟]**** 頁面上，按一下 [完成]**** 來儲存您的組態。











## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 ServiceNow，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  

    ![指派使用者](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。


## 其他資源

* 
* 




