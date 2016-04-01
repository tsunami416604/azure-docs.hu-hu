<properties 
    pageTitle="教學課程：Azure Active Directory 與 Clarizen 整合 | Microsoft Azure" 
    description="了解如何使用 Clarizen 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Clarizen 整合

本教學課程的目的是要示範 Azure 與 Clarizen 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Clarizen 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Clarizen 的 Azure AD 使用者將能夠登入位於您 Clarizen 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Clarizen 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")
##啟用 Clarizen 的應用程式整合

本節的目的是要說明如何啟用 Clarizen 的應用程式整合。

###若要啟用 Clarizen 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Clarizen**。

    ![應用程式庫](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Clarizen**, ，然後按一下 [ **完成** 加入應用程式。

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Clarizen。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Clarizen** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Clarizen** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")

3.  在 **設定單一登入在 Clarizen** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")

4.  在不同的網頁瀏覽器視窗中，登入您 **Clarizen** 公司網站，以系統管理員身分 (例如 ︰ *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*)。

5.  按一下您的使用者名稱，然後按一下 **設定**。

    ![設定](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")

6.  按一下 [ **通用設定** ] 索引標籤，然後下, 一步將 **同盟驗證**, ，按一下 [ **編輯**。

    ![全域設定](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")

7.  在 **同盟驗證** ] 對話方塊中，執行下列步驟 ︰

    ![同盟驗證](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")

    1.  按一下 [ **上載** 上傳您下載的憑證。
    2.  在 Azure 網站中，在 **在 Clarizen 中設定單一登入** 對話方塊頁面中，複製 **單一登入服務 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    3.  在 Azure 網站中，在 **設定單一登出在 Clarizen** 對話方塊頁面中，複製 **單一登出服務 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    4.  選取 **使用 POST**。
    5.  按一下 [ **儲存**。

8.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Clarizen，必須將他們佈建到 Clarizen。  
Clarizen 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Clarizen** 系統管理員身分的公司網站。

2.  按一下 [ **人**。

    ![人員](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")

3.  按一下 [ **邀請使用者**。

    ![邀請使用者](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")

4.  在 [邀請人員] 對話頁面上，執行下列步驟：

    ![邀請人員](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")

    1.  在 **電子郵件** 文字方塊中，輸入您想要佈建的有效 Azure Active Directory 帳戶的電子郵件地址。
    2.  按一下 [ **邀請**。

    >[AZURE.NOTE] Azure Active Directory 帳戶持有者將收到的電子郵件，並依循連結來確認其帳戶，然後就會生效。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Clarizen，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Clarizen * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


