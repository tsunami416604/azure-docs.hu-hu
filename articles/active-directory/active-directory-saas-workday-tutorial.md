<properties 
    pageTitle="教學課程：Azure Active Directory 與 Workday 整合 | Microsoft Azure" 
    description="了解如何使用 Workday 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Workday 整合
  
本教學課程的目的是要示範 Azure 與 Workday 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Workday 中的租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Workday 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  設定使用者佈建

![案例](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

##啟用 Workday 的應用程式整合
  
本節的目的是要說明如何啟用 Salesforce 的應用程式整合。

###若要啟用 Workday 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4.  若要開啟 **應用程式庫**, ，按一下 [ **新增應用程式**, ，然後按一下 [ **加入我的組織使用的應用程式**。

    ![欲執行動作](./media/active-directory-saas-workday-tutorial/IC700995.png "What do you want to do?")

5.  在 **搜尋方塊**, ，型別 **Workday**。

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  在 [結果] 窗格中，選取 **Workday**, ，然後按一下 [ **完成** 加入應用程式。

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Workday 中進行驗證。  
在這個程序中，您必須建立 base-64 編碼的憑證。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 **Workday** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-workday-tutorial/IC782920.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Workday** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-workday-tutorial/IC782921.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟，然後按 **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-workday-tutorial/IC782957.png "Configure App URL")

    1.  在 **登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 Workday (例如: *https://impl.workday.com/ \<tenant\>/login-saml2.htmld*)
    2.  在 **Workday 回覆 URL** 文字方塊中，輸入 Workday 回覆 URL (例如: *https://impl.workday.com/ \<tenant\>/login-saml.htmld*)。

        >[AZURE.NOTE] 回覆 URL 必須有子網域 (例如: www，wd2，wd3，wd3 impl、 wd5、 wd5 impl)。 
        >使用像是 「*http://www.myworkday.com*」 的運作方式，但"*http://myworkday.com*」 並不會。 
 
4.  在 **在工作日設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-workday-tutorial/IC782922.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Workday 公司網站。

6.  移至 **功能表 \ > Workbench**。

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  移至 **帳戶管理**。

    ![帳戶管理](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8.  移至 **編輯租用戶設定-安全性**。

    ![編輯租用戶安全性](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9.  在 **重新導向 Url** 區段中，執行下列步驟:

    ![重新導向 URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")

     9.1. 按一下 [ **加入資料列**。

     9.2. 在 **登入重新導向 URL** 文字方塊和 **行動重新導向 URL** 文字方塊中，輸入 **Workday 租用戶 URL** 您所輸入的 **設定應用程式 URL** Azure 入口網站頁面。
    
     9.3. 在 Azure 網站中，在 **在工作日設定單一登入** 對話方塊頁面中，複製 **單一登出服務 URL**, ，然後將它貼入 **登出重新導向 URL** 文字方塊。

     9.4.  在 **環境** 文字方塊中，輸入環境名稱。  


       >[AZURE.NOTE] The value of the Environment attribute is tied to the value of the tenant URL:
        >
        >-   If the domain name of the Workday tenant URL starts with impl (e.g.: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), the **Environment** attribute must be set to Implementation.
        >-   If the domain name starts with something else, you need to contact Workday to get the matching **Environment** value.

10. 在 **SAML 設定** 區段中，執行下列步驟:

    ![SAML 設定](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")

     10.1.  選取 **啟用 SAML 驗證**。

     10.2.  按一下 [ **加入資料列**。

11. 在 [SAML 身分識別提供者] 區段中，執行下列步驟：

    ![SAML 身分識別提供者](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")

     11.1.  在 [身分識別提供者名稱] 文字方塊中，輸入提供者名稱 (例如: *SPInitiatedSSO*)。

     11.2.  在 Azure 網站中，在 **在工作日設定單一登入** 對話方塊頁面中，複製 **身分識別提供者 ID** 值，並接著將它貼入 **簽發者** 文字方塊。

     11.3.  選取 **啟用 Workday Initialted 登出**。

     11.4. 在 Azure 網站中，在 **在工作日設定單一登入** 對話方塊頁面中，複製 **單一登出服務 URL** 值，並接著將它貼入 **登出要求 URL** 文字方塊。


     11.3.  Click **Identity Provider Public Key Certificate**, and then click **Create**. <br><br>
        ![Create](./media/active-directory-saas-workday-tutorial/IC782928.png "Create")

     11.4.  Click **Create x509 Public Key**. <br><br>
        ![Create](./media/active-directory-saas-workday-tutorial/IC782929.png "Create")

     11.5.  In the **View x509 Public Key** section, perform the following steps: <br><br>
        ![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key") <br>

      1.  In the **Name** textbox, type a name for your certificate (e.g.: *PPE\_SP*).
      2.  In the **Valid From** textbox, type the valid from attribute value of your certificate.
      3.  In the **Valid To** textbox, type the valid to attribute value of your certificate.
        
           >[AZURE.NOTE] You can get the valid from date and the valid to date from the downloaded certificate by double-clicking it. The dates are listed under the **Details** tab.

      4.  Create a **Base-64 encoded** file from your downloaded certificate.  

        >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)

      5.  Open your base-64 encoded certificate in notepad, and then copy the content of it.
      6.  In the **Certificate** textbox, paste the content of your clipboard.
      7.  Click **OK**.

12.  執行下列步驟： <br><br>
        ![SSO 組態](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")

     12.1.  啟用 **x509 私密金鑰組**。

     12.2.  在 **服務提供者識別碼** 文字方塊中，輸入 **http://www.workday.com**。

     12.3.  選取 **啟用 SP 初始化 SAML 驗證**。

     12.4.  在 Azure 網站中，在 **在工作日設定單一登入** 對話方塊頁面中，複製 **單一登入服務 URL** 值，並接著將它貼入 **IdP SSO 服務 URL** 文字方塊。
     
     12.5 選取 **不 Deflate SP 初始化驗證要求**。

     12.6. 做為 **驗證要求簽章方法**, ，請選取 **SHA256**。 <br><br>
        ![驗證要求簽章方法](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method") <br><br>
 
     12.7 按一下 [ **確定**。 <br><br>
        ![[確定]](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. 在 Azure AD 入口網站上 **在工作日設定單一登入** 頁面上，按一下 **下一步**。 <br><br>

    ![設定單一登入](./media/active-directory-saas-workday-tutorial/IC782934.png "Configure single sign-on")

13. 在 **單一登入確認** 頁面上，按一下 **完成**。 <br><br>

    ![設定單一登入](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configure single sign-on")



##設定使用者佈建
  
若要讓測試使用者佈建到 Workday 中，您需要聯絡 Workday 支援小組。  
Workday 支援小組會為您建立此使用者。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Workday，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Workday * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-workday-tutorial/IC782935.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-workday-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
