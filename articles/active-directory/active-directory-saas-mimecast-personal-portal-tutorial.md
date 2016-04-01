<properties 
    pageTitle="教學課程：Azure Active Directory 與 Mimecast Personal Portal 整合 | Microsoft Azure" 
    description="了解如何使用 Mimecast Personal Portal 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Mimecast Personal Portal 整合
  
本教學課程的目的是要示範 Azure 與 Mimecast Personal Portal 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Mimecast Personal Portal 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Mimecast 個人入口網站的 Azure AD 使用者將能夠登入位於您 Mimecast 個人入口網站公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Mimecast Personal Portal 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scenario")
##啟用 Mimecast Personal Portal 的應用程式整合
  
本節的目的是要說明如何啟用 Mimecast Personal Portal 的應用程式整合。

###若要啟用 Mimecast Personal Portal 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Mimecast 個人入口網站**。

    ![應用程式庫](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Mimecast 個人入口網站**, ，然後按一下 [ **完成** 加入應用程式。

    ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Mimecast Personal Portal 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Mimecast 個人入口網站** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Mimecast 個人入口網站** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Mimecast 個人入口網站登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 Mimecast 個人入口網站應用程式 (例如:"https://webmail-uk.mimecast.com"或"https://webmail-us.mimecast.com")，然後按一下 [ **下一步**。

    >[AZURE.NOTE] 登入 URL 是特定的區域。

    ![設定應用程式 URL](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configure App URL")

4.  在 **Mimecast 個人入口網站設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Mimecast Personal Portal 公司網站。

6.  移至 **服務 \ > 應用程式**。

    ![應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")

7.  按一下 [ **驗證設定檔**。

    ![驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentication Profiles")

8.  按一下 [ **新驗證設定檔**。

    ![新驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "New Authentication Profil")

9.  在 **驗證設定檔** 區段中，執行下列步驟 ︰

    ![驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentication Profil")

    1.  在 **描述** 文字方塊中，輸入您的組態名稱。
    2.  選取 **強制 Mimecast 個人入口網站的 SAML 驗證**。
    3.  做為 **提供者**, ，請選取 **Azure Active Directory**。
    4.  在 Azure 網站中，在 **Mimecast 個人入口網站設定單一登入** 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼到 **簽發者 URL** 文字方塊。
    5.  在 Azure 網站中，在 **Mimecast 個人入口網站設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    6.  在 Azure 網站中，在 **Mimecast 個人入口網站設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。  

        >[AZURE.NOTE] 登入 URL 值和登出 URL] 值，可供位在 Mimecast 個人入口網站相同。

    7.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP]如需詳細資訊，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

    8.  開啟您在 [記事本] 的 base 64 編碼的憑證移除的第一行 (「*-*") 和最後一行 (「*-*」)，將它的其餘內容複製到剪貼簿，然後將它貼到 **身分識別提供者憑證 （中繼資料）** 文字方塊。
    9.  選取 **允許單一登入**。
    10. 按一下 [ **儲存**。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Mimecast Personal Portal，必須將它們佈建到 Mimecast Personal Portal。  
Mimecast Personal Portal 需以手動的方式佈建。
  
您需要先註冊網域才能建立使用者。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **Mimecast 個人入口網站** 系統管理員身分。

2.  移至 **目錄 \ > 內部**。

    ![目錄](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directories")

3.  按一下 [ **註冊新網域**。

    ![註冊新網域](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Register New Domain")

4.  建立新的網域之後，按一下 [ **新位址**。

    ![新位址](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "New Address")

5.  在 [新位址] 對話方塊中，執行下列步驟：

    ![Save](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Save")

    1.  型別 **電子郵件地址**, ，**全域名稱**, ，**密碼** 和 **確認密碼** 您想要佈建到相關文字方塊之有效 AAD 帳戶的屬性。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE]您可以使用任何其他 Mimecast 個人入口網站使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Mimecast 個人入口網站。

##指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Mimecast Personal Portal，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Mimecast 個人入口網站 * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

