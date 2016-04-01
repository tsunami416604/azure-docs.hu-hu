<properties 
    pageTitle="教學課程：Azure Active Directory 與 LogicMonitor 整合 | Microsoft Azure" 
    description="了解如何使用 LogicMonitor 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 LogicMonitor 整合
  
本教學課程的目的是要示範 Azure 與 LogicMonitor 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   LogicMonitor 租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 LogicMonitor 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")
##啟用 LogicMonitor 的應用程式整合
  
本節的目的是要說明如何啟用 LogicMonitor 的應用程式整合。

###若要啟用 LogicMonitor 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **logicmonitor**。

    ![應用程式庫](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **LogicMonitor**, ，然後按一下 [ **完成** 加入應用程式。

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 LogicMonitor 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站中，在 * * LogicMonitor * * 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 LogicMonitor** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")

3.  上 **設定應用程式 URL** 頁面上，於 **登入 URL** 文字方塊中，輸入您的 URL，讓使用者中用來登入 LogicMonitor \ (電子、 g、: 「*http://company.logicmonitor.com*"\)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")

4.  在 **LogicMonitor 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後將它儲存在電腦上。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")

5.  登入您 **LogicMonitor** 系統管理員身分的公司網站。

6.  在頂端功能表中，按一下 [ **設定**。

    ![設定](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")

7.  在左側導覽列中，按一下 [ **單一登入**

    ![單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  在 **單一登入 (SSO) 設定** 區段中，執行下列步驟 ︰

    ![單一登入設定](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")

    1.  選取 **啟用單一登入**。
    2.  做為 **預設角色指派**, ，請選取 **readonly**。
    3.  在記事本中，開啟下載的中繼資料檔案，然後再貼上檔案的內容 **身分識別提供者中繼資料** 文字方塊。
    4.  按一下 [ **儲存變更**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 AAD 使用者能夠登入，必須使用他們的 Azure Active Directory 使用者名稱將他們佈建到 LogicMonitor 應用程式。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 LogicMonitor 公司網站。

2.  在頂端功能表中，按一下 [ **設定**, ，然後按一下 [ **角色和使用者**。

    ![角色和使用者](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")

3.  按一下 [ **新增**。

4.  在 **將帳戶加入** 區段中，執行下列步驟 ︰

    ![新增帳戶](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")

    1.  型別 **Username**, ，**電子郵件**, ，**密碼** 和 **重新輸入密碼** Azure Active Directory 使用者想要佈建到相關文字方塊的值。
    2.  選取 **角色**, ，**檢視權限** 和 **狀態**。
    3.  按一下 [ **提交**。

>[AZURE.NOTE]您可以使用任何其他 LogicMonitor 使用者帳戶建立工具或 Api 提供 LogicMonitor 所要佈建 Azure Active Directory 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 LogicMonitor，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **LogicMonitor** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。






