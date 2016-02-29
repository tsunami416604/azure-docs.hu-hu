<properties 
    pageTitle="教學課程：Azure Active Directory 與 SugarCRM 整合 | Microsoft Azure" 
    description="了解如何使用 SugarCRM 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 SugarCRM 整合
  
本教學課程的目的是要示範 Azure 與 SugarCRM 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Sugar CRM 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Sugar CRM 的 Azure AD 使用者將能夠登入位於您 Sugar CRM 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Sugar CRM 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario")

##啟用 Sugar CRM 的應用程式整合
  
本節的目的是概述如何啟用 Sugar CRM 的應用程式整合。

###若要啟用 Sugar CRM 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Sugar CRM**。

    ![應用程式庫](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Sugar CRM**, ，然後按一下 [ **完成** 加入應用程式。

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Sugar CRM 中進行驗證。  
在此程序中，您需要上傳 base-64 編碼憑證到您的 Sugar CRM 租用戶。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Sugar CRM** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Sugar CRM** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Sugar CRM 登入 URL** 文字方塊中，輸入您的使用者用於登入 Sugar CRM 應用程式使用的 URL (例如: 「*http://company.sugarondemand.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configure App URL")

4.  在 **Sugar CRM 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Sugar CRM 公司網站。

6.  移至 **Admin**。

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

7.  在 **管理** 區段中，按一下 **密碼管理**。

    ![系統管理](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8.  選取 **啟用 SAML 驗證**。

    ![系統管理](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9.  在 **SAML 驗證** 區段中，執行下列步驟:

    ![SAML 驗證](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML Authentication")

    1.  在 Azure 網站中，在 **Sugar CRM 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    2.  在 Azure 網站中，在 **Sugar CRM 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **SLO URL** 文字方塊。
    3.  建立 **Base 64 編碼** 檔案下載的憑證。

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後貼上整個憑證到 **X.509 憑證** 文字方塊。
    5.  按一下 [ **儲存**。

10. 在 Azure 網站中，在 **Sugar CRM 在設定單一登入** 對話方塊頁面上，選取 [單一登入設定確認，然後再按 **完成**。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Sugar CRM，必須將他們佈建到 Sugar CRM。  
Sugar CRM 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Sugar CRM** 系統管理員身分的公司網站。

2.  移至 **Admin**。

    ![Admin](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

3.  在 **管理** 區段中，按一下 **使用者管理**。

    ![系統管理](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4.  移至 **使用者 \ > 建立新的使用者**。

    ![建立新的使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Create New User")

5.  在 **使用者設定檔** 索引標籤上，執行下列步驟:

    ![新使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "New User")

    1.  在相關的文字方塊中，輸入有效 Azure Active Directory 使用者的使用者名稱、姓氏和電子郵件地址。

6.  做為 **狀態**, ，請選取 **Active**。

7.  在 [密碼] 索引標籤上，執行下列步驟：

    ![新使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "New User")

    1.  在相關的文字方塊中輸入密碼。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 Sugar CRM 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Sugar CRM 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Sugar CRM，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Sugar CRM** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
