<properties 
    pageTitle="教學課程：Azure Active Directory 與 Zoho Mail 整合 | Microsoft Azure" 
    description="了解如何使用 Zoho Mail 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Zoho Mail 整合
  
本教學課程的目的是要示範 Azure 與 Zoho Mail 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Zoho Mail 租用戶
  
完成本教學課程之後, 您指派給 Zoho 郵件 Azure 的 AD 使用者都能夠單一登入您的 Zoho 郵件公司網站 (服務提供者初始化登入)，在應用程式，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Zoho Mail 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario")

##啟用 Zoho Mail 的應用程式整合
  
本節的目的是要說明如何啟用 Zoho Mail 的應用程式整合。

###若要啟用 Zoho Mail 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋] 方塊中**, ，型別 **Zoho 郵件**。

    ![應用程式庫](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Application Gallery")

7.  在 [結果] 窗格中，選取 [ **Zoho 郵件**, ，然後按一下 [ **完成** 新增應用程式。

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Zoho Mail 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案的憑證轉換成文字檔案](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure 的 AD 入口網站，在 **Zoho 郵件** 應用程式整合] 頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configure Single Sign-On")

2.  在 **您要如何才能登入 Zoho 郵件的使用者** ] 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configure Single Sign-On")

3.  上 **設定應用程式 URL** 頁，在 **Zoho 郵件登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL"*http://company.ZohoMail.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configure App URL")

4.  在 **Zoho 封郵件設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Zoho Mail 公司網站。

6.  移至 **控制台**。

    ![控制台](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Control Panel")

7.  按一下 [ **SAML 驗證** ] 索引標籤。

    ![SAML 驗證](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML Authentication")

8.  在 **SAML 驗證詳細資料** 區段中，執行下列步驟:

    ![SAML 驗證詳細資料](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML Authentication Details")

    1.  在 Azure 的入口網站，在 **Zoho 封郵件設定單一登入** 對話方塊的頁、 複製 **遠端登入 URL** 值，而且再將其貼入 **登入 URL** 文字方塊。
    2.  在 Azure 的入口網站，在 **Zoho 封郵件設定單一登入** 對話方塊的頁、 複製 **遠端登出 URL** 值，而且再將其貼入 **登出 URL** 文字方塊。
    3.  在 Azure 的入口網站，在 **Zoho 封郵件設定單一登入** 對話方塊的頁、 複製 **變更密碼 URL** 值，而且再將它貼到 **變更密碼 URL** 文字方塊。
    4.  建立 **base 64 編碼** 檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何將二進位檔案的憑證轉換成文字檔案](http://youtu.be/PlgrzUZ-Y1o)

    5.  在記事本中開啟您 base 64 編碼的憑證，將它的內容複製到剪貼簿，然後貼至 **PublicKey** 文字方塊。
    6.  為 **演算法**, ，請選取 **RSA**。
    7.  按一下 [ **確定**。

9.  在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Zoho Mail，必須將他們佈建到 Zoho Mail。  
Zoho Mail 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Zoho 郵件** 公司網站系統管理員的身分。

2.  移至 **控制台 \ > 郵件與文件**。

3.  移至 **使用者詳細資料 \ > 將使用者新增**。

    ![新增使用者](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Add User")

4.  在 **將使用者新增** ] 對話方塊中，執行下列步驟:

    ![新增使用者](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Add User")

    1.  型別 **名字**, ，**姓氏**, ，**電子郵件識別碼**, ，**密碼** 的有效的 Azure Active Directory 帳戶，您想要在相關的文字方塊的佈建。
    2.  按一下 [ **確定**。  

        >[AZURE.NOTE] Azure Active Directory 帳戶持有人會收到一封電子郵件，其中提供連至它成為使用中之前，請確認該帳戶。

>[AZURE.NOTE] 您可以使用任何其他 Zoho 郵件使用者帳戶建立工具或 Api 提供透過 Zoho 郵件佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Zoho Mail，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Zoho 郵件 * * 應用程式整合] 頁面上，按一下 [ **將使用者指派**。

    ![指派使用者](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assign Users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
