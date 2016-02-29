<properties 
    pageTitle="教學課程：Azure Active Directory 與 Cherwell 整合 | Microsoft Azure" 
    description="了解如何使用 Cherwell 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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
    ms.date="10/20/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Cherwell 整合

本教學課程的目的是要示範 Azure 與 Cherwell 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Cherwell 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Cherwell 的 Azure AD 使用者將能夠登入位於您 Cherwell 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Cherwell 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario")
##啟用 Cherwell 的應用程式整合

本節的目的是要說明如何啟用 Cherwell 的應用程式整合。

###若要啟用 Cherwell 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-cherwell-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-cherwell-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Cherwell**。

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  在 [結果] 窗格中，選取 **Cherwell**, ，然後按一下 [ **完成** 加入應用程式。
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Cherwell。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Cherwell** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Cherwell** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟:

    ![設定應用程式 URL](./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configure App URL")

    1.  在 **登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您 **Cherwell** (例如: *https://pictdev.cherwellondemand.com/cherwellclient*)。
    2.  按一下 [ **下一步**

4.  在 **在 Cherwell 單一登入設定** 頁面上，執行下列步驟:

    ![設定單一登入](./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configure Single Sign-On")

    1.  按一下 [ **下載憑證**, ，並在本機電腦上儲存憑證。
    2.  複製 **身分識別提供者 URL**。
    3.  複製 **單一登入服務 URL**。
    4.  按一下 [ **下一步**。

5.  提交已下載的憑證， **身分識別提供者 URL** 和 **單一登入服務 URL** 給您的 Cherwell 支援小組。

    >[AZURE.NOTE] Cherwell 支援小組必須執行實際的 SSO 設定。
    當您的訂用帳戶啟用 SSO 之後，您會收到通知。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Cherwell，必須將他們佈建到 Cherwell。  
Cherwell 的使用者帳戶必須由 Cherwell 支援小組建立。

>[AZURE.NOTE] 您可以使用任何其他 Cherwell 使用者帳戶建立工具或 Api Cherwell 所提供的佈建 Azure Active Directory 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Cherwell，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Cherwell * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-cherwell-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

