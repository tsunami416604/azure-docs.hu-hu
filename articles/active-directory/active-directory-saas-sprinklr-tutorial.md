<properties 
    pageTitle="教學課程：Azure Active Directory 與 Sprinklr 整合 | Microsoft Azure" 
    description="了解如何使用 Sprinklr 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Sprinklr 整合
  
本教學課程的目的是要示範 Azure 與 Sprinklr 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Sprinklr 租用戶
  
完成本教學課程之後, 您已指派給 Sprinklr 的 Azure AD 使用者將能夠登入您的 Sprinklr 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Sprinklr 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

##啟用 Sprinklr 的應用程式整合
  
本節的目的是概述如何啟用 Sprinklr 的應用程式整合。

###若要啟用 Sprinklr 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Sprinklr**。

    ![應用程式庫](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Sprinklr**, ，然後按一下 [ **完成** 加入應用程式。

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Sprinklr 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Sprinklr** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Sprinklr** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Sprinklr 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>.sprinklr.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configure App URL")

4.  在 **Sprinklr 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Sprinklr 公司網站。

6.  移至 **管理 \ > 設定**。

    ![系統管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  移至 **管理夥伴 \ > 單一登** 上從左窗格中。

    ![管理夥伴](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Manage Partner")

8.  按一下 [ **+ 新增單一登入**。

    ![單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Single Sign-Ons")

9.  在 **單一登入** 頁面上，執行下列步驟 ︰

    ![單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Single Sign-Ons")

    1.  在 **名稱** 文字方塊中，輸入您的組態名稱 (例如 ︰ *WAADSSOTest*)。
    2.  選取 **啟用**。
    3.  選取 **使用新的 SSO 憑證**。
    4.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    5.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **身分識別提供者憑證** 文字方塊中，
    6.  在 Azure 網站中，在 **Sprinklr 在設定單一登入** 對話方塊頁面中，複製 **身分識別提供者 ID** 值，並接著將它貼入 **實體識別碼** 文字方塊。
    7.  在 Azure 網站中，在 **Sprinklr 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **身分識別提供者登入 URL** 文字方塊。
    8.  在 Azure 網站中，在 **Sprinklr 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **身分識別提供者登出 URL** 文字方塊。
    9.  做為 **SAML 使用者 ID 型別**, ，請選取 **判斷提示包含使用者 「 s sprinklr.com username**。
    10. 做為 **SAML 使用者識別碼位置**, ，請選取 **主體陳述式的名稱識別碼項目中的使用者識別碼是**。
    11. 關閉 **儲存**。

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configure single sign-on")

##設定使用者佈建
  
必須先針對 Sprinklr 應用程式內的存取佈建 AAD 使用者，才可以讓他們登入。  
本章節描述如何建立 Sprinklr 內的 AAD 使用者帳戶。

###若要佈建使用者帳戶到 Sprinklr，請執行下列步驟：

1.  以系統管理員身分登入您的 Sprinklr 公司網站。

2.  移至 **管理 \ > 設定**。

    ![系統管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  移至 **管理用戶端 \ > 使用者** 從左窗格中。

    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4.  按一下 [ **將使用者加入**。

    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Settings")

5.  在 **編輯使用者** ] 對話方塊中，執行下列步驟 ︰

    ![編輯使用者](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Edit user")

    1.  在 **電子郵件**, ，**名字** 和 **姓氏** 文字方塊，輸入您想要佈建 Azure AD 使用者帳戶的資訊。
    2.  選取 **密碼停用**。
    3.  選取 **語言**。
    4.  選取 **使用者類型**。
    5.  按一下 [ **更新**。

    >[AZURE.IMPORTANT] **密碼停用** 必須選取要讓使用者透過身分識別提供者登入。

6.  移至 **角色**, ，然後執行下列步驟 ︰

    ![夥伴角色](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Partner Roles")

    1.  從 **Global** 清單中，選取 **ALL\_Permissions**。
    2.  按一下 [ **更新**。

>[AZURE.NOTE] 您可以使用任何其他 Sprinklr 使用者帳戶建立工具或 Api Sprinklr 所提供的佈建 Azure AD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Sprinklr，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Sprinklr * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

