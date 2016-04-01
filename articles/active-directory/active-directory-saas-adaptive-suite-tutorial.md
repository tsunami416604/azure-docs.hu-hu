<properties 
    pageTitle="教學課程：Azure Active Directory 與 Adaptive Suite 整合 | Microsoft Azure"
    description="了解如何使用 Adaptive Suite 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Adaptive Suite 整合

本教學課程的目的是要示範 Azure 與 Adaptive Suite 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Adaptive Suite 租用戶

完成此教學課程之後, 您已指派給自動調整的套件的 Azure AD 使用者將能夠登入您的自動調整的套件公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Adaptive Suite 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenario")
##啟用 Adaptive Suite 的應用程式整合

本節的目的是概述如何啟用 Adaptive Suite 的應用程式整合。

###若要啟用 Adaptive Suite 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，類型 **自動調整的套件**。

    ![應用程式庫](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **自動調整的套件**, ，然後按一下 [ **完成** 加入應用程式。

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Adaptive Suite 中進行驗證。  
您必須從憑證擷取指紋值，才能設定 Adaptive Suite 的單一登入。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **自動調整的套件** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入自動調整的套件** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configure Single Sign-On")

3.  在 **設定應用程式設定** 頁面上，於 **回覆 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE =*」，然後按一下 [ **下一步**。

    >[AZURE.NOTE] 您可以從自動調整的套件中取得此值 **SAML SSO 設定** 頁面。

    ![設定 App 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configure App Settings")

4.  在 **自動調整的套件在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Adaptive Suite 公司網站。

6.  移至 **Admin**。

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  在 **使用者和角色** 區段中，按一下 **管理 SAML SSO 設定**。

    ![管理 SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")

8.  在 **SAML SSO 設定** 頁面上，執行下列步驟 ︰

    ![SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")

    1.  在 **身分識別提供者名稱** 文字方塊中，輸入您的組態名稱。
    2.  在 Azure 網站中，在 **自動調整的套件在設定單一登入** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **身分識別提供者實體識別碼** 文字方塊。
    3.  在 Azure 網站中，在 **自動調整的套件在設定單一登入** 對話方塊頁面中，複製 **SAML SSO URL** 值，並接著將它貼入 **身分識別提供者 SSO URL** 文字方塊。
    4.  在 Azure 網站中，在 **自動調整的套件在設定單一登入** 對話方塊頁面中，複製 **SAML SSO URL** 值，並接著將它貼入 **自訂登出 URL** 文字方塊。
    5.  若要上傳您下載的憑證，請按一下 [ **選擇檔案**。
    6.  做為 **SAML 使用者識別碼**, ，請選取 **調適性 Insights 使用者名稱**。
    7.  做為 **SAML 使用者識別碼位置**, ，請選取 **NameID 的主體中的使用者識別碼**。
    8.  做為 **SAML NameID 格式**, ，請選取 **電子郵件地址**。
    9.  做為 **啟用 SAML**, ，請選取 **允許 SAML SSO 和直接調適性 Insights 登入**。
    10. 按一下 [ **儲存**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Adaptive Suite，必須將他們佈建到 Adaptive Suite。  
Adaptive Suite 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **自動調整的套件** 系統管理員身分的公司網站。

2.  移至 **Admin**。

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  在 **使用者和角色** 區段中，按一下 **新增使用者**。

    ![新增使用者](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Add User")

4.  在 **新使用者** 區段中，執行下列步驟 ︰

    ![提交](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Submit")

    1.  型別 **名稱**, ，**登入**, ，**電子郵件**, ，**密碼** 您想要佈建到相關文字方塊之有效 Azure Active Directory 使用者。
    2.  選取 **角色**。
    3.  按一下 [ **提交**。

>[AZURE.NOTE] 您可以使用任何其他自動調整的套件使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供自動調整的套件。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Adaptive Suite，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * 自動調整的套件 * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


