<properties 
    pageTitle="教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Azure" 
    description="了解如何使用 ServiceNow 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 ServiceNow 整合
  
本教學課程的目的是要示範 Azure 與 ServiceNow 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ServiceNow 中的租用戶
  
完成本教學課程之後, 您已指派給 ServiceNow 的 Azure AD 使用者將能夠登入您的 ServiceNow 公司網站 (服務提供者起始登入)，在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ServiceNow 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scenario")
##啟用 ServiceNow 的應用程式整合
  
本節的目的是概述如何啟用 ServiceNow 的應用程式整合。

###若要啟用 ServiceNow 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **ServiceNow**。

    ![應用程式資源庫](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Application gallery")

7.  在 [結果] 窗格中，選取 **ServiceNow**, ，然後按一下 [ **完成** 加入應用程式。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 ServiceNow 中進行驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。 如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ServiceNow** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 ServiceNow** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3.  在 **設定應用程式設定** 頁面上，執行下列步驟:

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")

    a. 在 **ServiceNow 登入 URL** 文字方塊中，輸入您的使用者用於登入您的 ServiceNow 應用程式使用您的 URL (例如: *https://\<InstanceName\>.service-now.com*)。

    b. 在 **簽發者 URL** 文字方塊中，輸入您的 URL，讓使用者中用來登入您的 ServiceNow 應用程式 (例如: *https://\<InstanceName\>.service-now.com*)。

    c. 按一下 [ **下一步**

4.  在 **自動設定單一登入** 頁面上，按一下 **手動設定單一登入的應用程式**, ，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")



4.  在 **在 ServiceNow 設定單一登入** 頁面上，按一下 **下載憑證**, ，儲存在本機電腦上的憑證檔案，然後按一下 **下一步**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

1. 以系統管理員身分登入您的 ServiceNow 應用程式。

2. 在左側導覽窗格中按一下 **屬性**。  

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configure app URL")


3. 在 **多個提供者 SSO 屬性** ] 對話方塊中，執行下列步驟:

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")

    a. 做為 **啟用多個提供者 SSO**, ，請選取 **是**。

    b. 做為 **啟用偵錯記錄中有多個提供者 SSO 整合**, ，請選取 **是**。

    c. 在 **上使用者欄位資料表...** 文字方塊中，輸入 **user_name**。

    d. 按一下 [ **儲存**。



1. 在左側導覽窗格中按一下 [ **x509 憑證**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configure single sign-on")


1. 在 **X.509 憑證** ] 對話方塊中，按一下 [ **新增**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")


1. 在 **X.509 憑證** ] 對話方塊中，執行下列步驟:

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")

    a. 按一下 [ **新**。

    b. 在 **名稱** 文字方塊中，輸入您的組態名稱 (例如: **TestSAML2.0**)。

    c. 選取 **Active**。

    d. 做為 **格式**, ，請選取 **PEM**。

    e. 做為 **類型**, ，請選取 **信任存放區 Cert**。

    f. 從您下載的憑證中建立 Base-64 編碼的檔案。
    > [AZURE.NOTE] 如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。
    
    g. 在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **PEM 憑證** 文字方塊。

    h. 按一下 [ **更新**。


1. 在左側導覽窗格中按一下 [ **身分識別提供者**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configure single sign-on")

1. 在 **身分識別提供者** ] 對話方塊中，按一下 [ **新增**:

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")


1. 在 **身分識別提供者** ] 對話方塊中，按一下 [ **SAML2 更新 1?**:

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")


1. 在 [SAML2 Update1 內容] 對話方塊上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")


    a. in the **Name** textbox, type a name for your configuration (e.g.: **SAML 2.0**).

    b. In the **User Field** textbox, type **email**. 

    c. In the Azure AD portal, copy the **Identity Provider ID** value, and then paste it into the **Identity Provider URL** textbox.

    d. In the Azure AD portal, copy the **Authentication Request URL** value, and then paste it into the **Identity Provider's AuthnRequest** textbox.

    e. In the Azure AD portal, copy the **Single Sign-Out Service URL** value, and then paste it into the **Identity Provider's SingleLogoutRequest** textbox.

    f. In the **ServiceNow Homepage** textbox, type the URL of your ServiceNow instance homepage.

    > [AZURE.NOTE] The ServiceNow instance homepage is a concatenation of your **ServieNow tenant URL** and **/navpage.do** (e.g.: *https://fabrikam.service-now.com/navpage.do*).
 

    g. In the **Entity ID / Issuer** textbox, type the URL of your ServiceNow tenant.

    h. In the **Audience URL** textbox, type the URL of your ServiceNow tenant. 

    i. In the **Protocol Binding for the IDP's SingleLogoutRequest** textbox, type **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. In the NameID Policy textbox, type **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Deselect **Create an AuthnContextClass**.

    l. In the **AuthnContextClassRef Method**, type **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. In **Clock Skew** textbox, type **60**.

    n. As **Single Sign On Script**, select **MultiSSO_SAML2_Update1**.

    o. As **x509 Certificate**, select the certificate you have created in the previous step.

    p. Click **Submit**. 



6. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **下一步**。 

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

7. 在 **單一登入確認** 頁面上，按一下 **完成**。
 
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")



##設定使用者佈建
  
本節的目的是要說明如何對 ServiceNow 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 管理入口網站中，在 **ServiceNow** 應用程式整合頁面上，按一下 [ **設定使用者佈建**。 <br><br> ![使用者佈建](./media/active-directory-saas-servicenow-tutorial/IC769498.png "User provisioning")


2. 在 **輸入您的 ServiceNow 認證以啟用自動使用者佈建** 頁面上，提供下列組態設定:
設定使用者佈建 

     2.1. 在 **ServiceNow 執行個體名稱** 文字方塊中，輸入 ServiceNow 執行個體名稱。

     2.2. 在 **ServiceNow 管理使用者名稱** 文字方塊中，輸入 ServiceNow 管理員帳戶的名稱。

     2.3. 在 **ServiceNow 管理員密碼** 文字方塊中，輸入此帳戶的密碼。

     2.4. 按一下 [ **驗證** 來驗證您的組態。

     2.5. 按一下 [ **下一步** 按鈕來開啟 **後續步驟** 頁面。

     2.6. 如果您想要佈建到此應用程式的所有使用者，選取 「**自動佈都建到此應用程式目錄中的所有使用者帳戶**」。 <br><br> ![後續步驟](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Next Steps")

     2.7. 在 **後續步驟** 頁面上，按一下 **完成** 來儲存組態。











##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ServiceNow，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * ServiceNow * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
