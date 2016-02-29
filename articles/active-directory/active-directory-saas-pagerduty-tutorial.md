<properties 
    pageTitle="教學課程：Azure Active Directory 與 Pagerduty 整合 | Microsoft Azure" 
    description="了解如何使用 Pagerduty 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Pagerduty 整合
  
本教學課程的目的是要示範 Azure 與 Pagerduty 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Pagerduty 租用戶
  
完成本教學課程之後, 您已指派給 Pagerduty 的 Azure AD 使用者將能夠登入位於您 Pagerduty 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Pagerduty 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario")
##啟用 Pagerduty 的應用程式整合
  
本節的目的是要說明如何啟用 Pagerduty 的應用程式整合。

###若要啟用 Pagerduty 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Pagerduty**。

    ![應用程式資源庫](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Pagerduty**, ，然後按一下 [ **完成** 加入應用程式。

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Pagerduty 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Pagerduty** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Pagerduty** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Pagerduty 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。Pagerduty.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configure app url")

4.  在 **Pagerduty 在設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Pagerduty 公司網站。

6.  在頂端功能表中，按一下 [ **帳戶設定**。

    ![帳戶設定](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Account Settings")

7.  按一下 [ **單一登入**。

    ![單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Single sign-on")

8.  在 [啟用單一登入 (SSO)] 頁面上執行下列步驟：

    ![啟用單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Enable single sign-on")

    1.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    2.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **X.509 憑證** 文字方塊
    3.  在 Azure 網站中，在 **Pagerduty 在設定單一登入** ] 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    4.  在 Azure 網站中，在 **Pagerduty 在設定單一登入** ] 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    5.  選取 **開啟單一登入**。
    6.  按一下 [ **儲存變更**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configure single sign-on")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 Pagerduty，必須將他們佈建到 Pagerduty 中。  
在 Pagerduty 的情況下，需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Pagerduty** 租用戶。

2.  在頂端功能表中，按一下 [ **使用者**。

3.  按一下 [ **將使用者新增**。

    ![新增使用者](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Add Users")

4.  在 **邀請團隊成員** 對話方塊中，輸入 **名字和姓氏** 和 **電子郵件** Azure AD 使用者想要佈建，請按一下 [地址 **新增**, ，然後按一下 [ **傳送邀請**。

    ![邀請您的小組](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invite your team")

    >[AZURE.NOTE] 所有加入的使用者會收到建立 PagerDuty 帳戶的邀請。

>[AZURE.NOTE] 您可以使用任何其他 Pagerduty 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Pagerduty。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 Pagerduty，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Pagerduty * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
