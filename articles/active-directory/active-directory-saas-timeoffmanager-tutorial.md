<properties 
    pageTitle="教學課程：Azure Active Directory 與 TimeOffManager 整合 | Microsoft Azure" 
    description="了解如何使用 TimeOffManager 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 TimeOffManager 整合
  
本教學課程的目的是要示範 Azure 與 TimeOffManager 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 TimeOffManager 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 TimeOffManager 的 Azure AD 使用者將能夠登入位於您 TimeOffManager 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 TimeOffManager 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

##啟用 TimeOffManager 的應用程式整合
  
本節的目的是要說明如何啟用 TimeOffManager 的應用程式整合。

###若要啟用 TimeOffManager 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **TimeOffManager**。

    ![應用程式庫](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **TimeOffManager**, ，然後按一下 [ **完成** 加入應用程式。

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TimeOffManager。  
在此程序中，您需要上傳 base-64 編碼憑證到您的 TimeOffManager 租用戶。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **TimeOffManager** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 TimeOffManager** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **TimeOffManager 回覆 URL** 文字方塊中，輸入您的 TimeOffManager AssertionConsumerService URL (例如: 「*範例: https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company \_id=IC34216*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configure App URL")

    您可以從 [TimeOffManager 單一登入設定] 頁面取得回覆 URL。

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")

4.  在 **TimeOffManager 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 TimeOffManager 公司網站。

6.  移至 **帳戶 \ > 帳戶選項 \ > 單一登入設定**。

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")

7.  在 **單一登入設定** 區段中，執行下列步驟:

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")

    1.  建立 **Base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    2.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後貼上整個憑證到 **X.509 憑證** 文字方塊。
    3.  在 Azure 網站中，在 **TimeOffManager 在設定單一登入** 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **Idp 簽發者** 文字方塊。
    4.  在 Azure 網站中，在 **TimeOffManager 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **IdP 端點 URL** 文字方塊。
    5.  做為 **強制 SAML**, ，請選取 **否**。
    6.  做為 **自動建立使用者**, ，請選取 **是**。
    7.  在 Azure 網站中，在 **TimeOffManager 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    8.  按一下 [ **儲存變更**。

8.  在 Azure 網站中，在 **TimeOffManager 在設定單一登入** 對話方塊頁面上，選取 [單一登入設定確認，然後再按 **完成**。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configure Single Sign-On")

9.  在頂端功能表中，按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributes")

10. 若要加入必要的屬性對應，請執行下列步驟：

    ![SAML Token 屬性](./media/active-directory-saas-timeoffmanager-tutorial/IC795921.png "saml token attributes")

    |屬性名稱|屬性值|
    |---|---|
    |Firstname|User.givenname|
    |lastname|User.surname|

    1.  每個資料列上表中，按一下 [ **新增使用者屬性**。
    2.  在 **屬性名稱** 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 **屬性值** 文字方塊中，選取該資料列所顯示的屬性值。
    4.  按一下 [ **完整**。

11. 按一下 [ **套用變更**。

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 TimeOffManager，則必須將他們佈建到 TimeOffManager。  
TimeOffManager 支援即時使用者佈建。 沒有您適用的動作項目。  
在第一次登入時使用單一登入，便會自動加入使用者。

>[AZURE.NOTE] 您可以使用任何其他 TimeOffManager 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 TimeOffManager。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 TimeOffManager，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **TimeOffManager** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

