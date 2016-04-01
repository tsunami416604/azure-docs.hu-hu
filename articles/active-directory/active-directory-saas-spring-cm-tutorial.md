<properties 
    pageTitle="教學課程：Azure Active Directory 與 SpringCM 整合 | Microsoft Azure" 
    description="了解如何使用 SpringCM 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Spring CM 整合
  
本教學課程的目的是示範如何設定 Azure Active Directory 與 SpringCM 之間的單一登入。
  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 SpringCM 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 SpringCM 的 Azure Active Directory 使用者將能夠使用 [AAD 存取面板] 單一登入。

1.  啟用 SpringCM 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario")

##啟用 SpringCM 的應用程式整合
  
本節的目的是概述如何啟用 SpringCM 的應用程式整合。

###若要啟用 SpringCM 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **SpringCM**。

    ![應用程式庫](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **SpringCM**, ，然後按一下 [ **完成** 加入應用程式。

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##設定單一登入
  
本節概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure Active Directory 帳戶在 SpringCM 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **SpringCM** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configure single Sign-On")

2.  在 **您希望使用者如何登入 SpringCM** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configure single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **SpringCM 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您 SpringCM 的應用程式，然後按一下 **下一步**。 

    應用程式 URL 是您 SpringCM 租用戶 URL (例如 ︰ *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![設定應用程式 URL](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configure App URL")

4.  在 **SpringCM 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將憑證檔案儲存在本機電腦。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configure Single SignOn")

5.  在不同的網頁瀏覽器視窗中，登入您 **SpringCM** 系統管理員身分的公司網站。

6.  在頂端功能表中，按一下 [ **移至**, ，按一下 [ **喜好設定**, ，然後在 **帳戶喜好設定** 區段中，按一下 **SAML SSO**。

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  在 [識別提供者組態] 區段執行下列步驟：

    ![識別提供者組態](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Identity Provider Configuration")

    1.  若要上傳您下載的 Azure Active Directory 憑證，請按一下 [ **選取簽發者憑證** 或 **變更簽發者憑證**。
    2.  在 Microsoft Azure 入口網站上 **SpringCM 在設定單一登入** 頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者** 文字方塊。
    3.  在 Microsoft Azure 入口網站上 **SpringCM 在設定單一登入** 頁面中，複製 **單一登入服務 URL** 值，並接著將它貼入 **服務提供者 (SP) 起始端點** 文字方塊。
    4.  做為 **SAML 啟用**, ，請選取 **啟用**。
    5.  按一下 [ **儲存**。

8.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configure Single SignOn")

##設定使用者佈建
  
若要讓 Azure Active Directory 使用者能夠登入 SpringCM，必須將他們佈建到 SpringCM。  
SpringCM 需以手動方式佈建。

>[AZURE.NOTE] 如需詳細資訊，請參閱 [建立和編輯 SpringCM 使用者](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###若要佈建使用者帳戶到 SpringCM，請執行下列步驟：

1.  登入您 **SpringCM** 系統管理員身分的公司網站。

2.  按一下 [ **GOTO**, ，然後按一下 [ **通訊錄**。

    ![建立使用者](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Create User")

3.  按一下 [ **建立使用者**。

4.  選取 **使用者角色**。

5.  選取 **傳送啟用電子郵件**。

6.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 使用者帳戶的名字、姓氏和電子郵件地址。

7.  新增使用者至 **安全性群組**。

8.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 SpringCM 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 SpringCM 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 SpringCM，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **SpringCM** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。






