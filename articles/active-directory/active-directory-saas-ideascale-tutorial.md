<properties 
    pageTitle="教學課程：Azure Active Directory 與 IdeaScale 整合 | Microsoft Azure" 
    description="了解如何使用 IdeaScale 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 IdeaScale 整合
  
本教學課程的目的是要示範 Azure 與 IdeaScale 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 IdeaScale 單一登入的訂閱
  
完成本教學課程之後, 您已指派給 IdeaScale 的 Azure AD 使用者將能夠登入應用程式使用的單一登入 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 IdeaScale 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")
##啟用 IdeaScale 的應用程式整合
  
本節的目的是要說明如何啟用 IdeaScale 的應用程式整合。

###若要啟用 IdeaScale 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **IdeaScale**。

    ![應用程式庫](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **IdeaScale**, ，然後按一下 [ **完成** 加入應用程式。

    ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 IdeaScale 中進行驗證。  
設定 IdeaScale 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **IdeaScale** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 IdeaScale** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **IdeaScale 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 IdeaScale 的應用程式 (例如: 「*https://company.IdeaScale.com*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configure App URL")

4.  在 **IdeaScale 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 IdeaScale 公司網站。

6.  移至 **社群設定**。

    ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")

7.  移至 **安全性 \ > 單一登入設定**。

    ![單一登入設定](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")

8.  做為 **單一登入類型**, ，請選取 **SAML 2.0**。

    ![單一登入類型](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")

9.  在 **單一登入設定** ] 對話方塊中，執行下列步驟:

    ![單一登入設定](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")

    1.  在 Azure 網站中，在 **IdeaScale 在設定單一登入** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **SAML IdP 實體識別碼** 文字方塊。
    2.  複製您的下載的中繼資料檔案的內容，然後將它貼入 **SAML IdP 中繼資料** 文字方塊。
    3.  在 Azure 網站中，在 **IdeaScale 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出成功 URL** 文字方塊。
    4.  按一下 [ **儲存變更**。

10. 在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 IdeaScale，則必須將他們佈建到 IdeaScale。  
在 IdeaScale 的情況下，佈建是手動工作。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **IdeaScale** 系統管理員身分的公司網站。

2.  移至 **社群設定**。

    ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")

3.  移至 **基本設定 \ > 成員管理**。

4.  按一下 [ **將成員加入**。

    ![成員管理](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")

5.  在 [加入新成員] 對話方塊中，執行下列步驟：

    ![加入新成員](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")

    1.  在 **電子郵件地址** 文字方塊中，輸入您想要佈建之有效 AAD 帳戶電子郵件地址。
    2.  按一下 [ **儲存變更**。

    >[AZURE.NOTE] Azure Active Directory 帳戶持有者將會收到電子郵件的連結，確認其帳戶之前就會生效。

>[AZURE.NOTE] 您可以使用任何其他 IdeaScale 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 IdeaScale。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 IdeaScale，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * IdeaScale * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assign Users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
