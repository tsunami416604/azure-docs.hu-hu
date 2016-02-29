<properties 
    pageTitle="教學課程：Azure Active Directory 與 SumoLogic 整合 | Microsoft Azure" 
    description="了解如何使用 SumoLogic 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
    services="active-directory" 
    authors="MarkusVi"  
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

#教學課程：Azure Active Directory 與 SumoLogic 整合
  
本教學課程的目的是要示範 Azure 與 SumoLogic 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SumoLogic 租用戶
  
完成本教學課程之後, 您已指派給 Sumologic 是能夠使用單一登入位於您 SumoLogic 的應用程式的 Azure AD 使用者和公司網站 (服務提供者起始登入)，或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SumoLogic 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario")

##啟用 SumoLogic 的應用程式整合
  
本節的目的是概述如何啟用 SumoLogic 的應用程式整合。

###若要啟用 SumoLogic 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sumologic-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-sumologic-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **sumologic**。

    ![應用程式資源庫](./media/active-directory-saas-sumologic-tutorial/IC778550.png "Application gallery")

7.  在 [結果] 窗格中，選取 **SumoLogic**, ，然後按一下 [ **完成** 加入應用程式。

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 SumoLogic 中進行驗證。  
在此程序中，您需要將 base-64 編碼憑證上傳到您的 SumoLogic 租用戶。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **SumoLogic** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 SumoLogic** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **SumoLogic 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。SumoLogic.com*」，然後按一下 [ **下一步**。

    ![設定 aoo URL](./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configure aoo URL")

4.  在 **SumoLogic 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 SumoLogic 公司網站。

6.  移至 **管理 \ > 安全性**。

    ![管理](./media/active-directory-saas-sumologic-tutorial/IC778556.png "Manage")

7.  按一下 [ **SAML**。

    ![全域安全性設定](./media/active-directory-saas-sumologic-tutorial/IC778557.png "Global security settings")

8.  從 **選取組態或建立一個新** 清單中，選取 **Azure AD**, ，然後按一下 [ **設定**。

    ![設定 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configure SAML 2.0")

9.  在 **設定 SAML 2.0** ] 對話方塊中，執行下列步驟:

    ![設定 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configure SAML 2.0")

    1.  在 **組態名稱** 文字方塊中，輸入 **Azure AD**。
    2.  選取 **偵錯模式**。
    3.  在 Azure 網站中，在 **SumoLogic 在設定單一登入** ] 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者** 文字方塊。
    4.  在 Azure 網站中，在 **SumoLogic 在設定單一登入** ] 對話方塊頁面中，複製 **驗證要求的 URL** 值，並接著將它貼到 **驗證要求的 URL** 文字方塊。
    5.  建立 **Base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後貼上整個憑證到 **X.509 憑證** 文字方塊。
    7.  做為 **電子郵件屬性**, ，請選取 **使用 SAML 主體**。
    8.  選取 **SP 初始化登入組態**。
    9.  在 **登入路徑** 文字方塊中，輸入 **Azure**。
    10. 按一下 [ **儲存**。

10. 在 Azure 網站中，在 **SumoLogic 在設定單一登入** 對話方塊頁面上，選取單一登入設定確認，然後按 **完成**。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configure single sign-on")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 SumoLogic，必須將他們佈建到 SumoLogic。  
SumoLogic 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **SumoLogic** 租用戶。

2.  移至 **管理 \ > 使用者**。

    ![使用者](./media/active-directory-saas-sumologic-tutorial/IC778561.png "Users")

3.  按一下 [ **新增**。

    ![使用者](./media/active-directory-saas-sumologic-tutorial/IC778562.png "Users")

4.  在 **新使用者** ] 對話方塊中，執行下列步驟:

    ![新使用者](./media/active-directory-saas-sumologic-tutorial/IC778563.png "New User")

    1.  輸入您想要佈建的 Azure AD 帳戶的相關的資訊 **名字**, ，**姓氏** 和 **電子郵件** 文字方塊。
    2.  選取角色。
    3.  做為 **狀態**, ，請選取 **Active**。
    4.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 SumoLogic 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 SumoLogic。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 SumoLogic，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **SumoLogic** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-sumologic-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
