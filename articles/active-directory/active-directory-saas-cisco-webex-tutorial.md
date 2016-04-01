<properties 
    pageTitle="教學課程：Azure Active Directory 與 Cisco Webex 整合 | Microsoft Azure" 
    description="了解如何使用 Cisco Webex 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Cisco Webex 整合

本教學課程的目的是要示範 Azure 與 Cisco Webex 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Cisco Webex 租用戶

完成本教學課程之後, 您已指派給 Cisco Webex 的 Azure AD 使用者將能夠登入您的 Cisco Webex 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Cisco Webex 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")
##啟用 Cisco Webex 的應用程式整合

本節的目的是要說明如何啟用 Cisco Webex 的應用程式整合。

###若要啟用 Cisco Webex 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Cisco Webex**。

    ![應用程式庫](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Cisco Webex**, ，然後按一下 [ **完成** 加入應用程式。

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Cisco Webex 中進行驗證。  
在這個程序中，您必須建立 base-64 編碼的憑證。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Cisco Webex** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Cisco Webex** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟，然後按 **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configure app URL")

    1.  在 **登入 URL** 文字方塊中，輸入您的 Cisco Webex 租用戶 URL (例如 ︰ *http://contoso.webex.com*)。
    2.  在 **Cisco Webex 回覆 URL** 文字方塊中，輸入您 **Cisco Webex AssertionConsumerService URL** (例如 ︰ *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*)。

4.  在 **設定單一登入，在 Cisco Webex** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configure single sign-on")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Cisco Webex 公司網站。

6.  在頂端功能表中，按一下 [ **網站管理**。

    ![網站管理](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")

7.  在 **管理站台** 區段中，按一下 **SSO 組態**。

    ![SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")

8.  在 [同盟網頁 SSO 組態] 區段中，執行下列步驟：

    ![同盟 SSO 組態](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")

    1.  從 **同盟通訊協定** 清單中，選取 **SAML 2.0**。
    2.  建立 **Base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    3.  在記事本中開啟 base-64 編碼的憑證，然後複製其內容。
    4.  按一下 [ **匯入 SAML 中繼資料**, ，然後貼上您的 base-64 編碼的憑證。
    5.  在 Azure 網站中，在 **Cisco Webex 在設定單一登入** 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **saml (IdP ID) 的簽發者** 文字方塊。
    6.  在 Azure 網站中，在 **Cisco Webex 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **客戶 SSO 服務登入 URL** 文字方塊。
    7.  從 **NameID 格式** 清單中，選取 **電子郵件地址**。
    8.  在 **AuthnContextClassRef** 文字方塊中，輸入 **urn: oasis ︰ 名稱 ︰ tc: SAML:2.0:ac:classes:Password**。
    9.  在 Azure 網站中，在 **Cisco Webex 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **客戶 SSO 服務登出 URL** 文字方塊。
    10. 按一下 [ **更新**。

9.  在 Azure 網站中，在 **設定單一登入，在 Cisco Webex** 對話方塊頁面上，選取 [單一登入設定確認，然後再按 **完成**。

    ![設定單一登入](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configure single sign-on")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Cisco Webex，則必須將他們佈建到 Cisco Webex。  
Cisco Webex 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Cisco Webex** 租用戶。

2.  移至 **管理使用者 \ > 將使用者加入**。

    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Add users")

3.  在 [加入使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Add user")

    1.  做為 **帳戶類型**, ，請選取 **主機**。
    2.  在下列文字方塊中輸入現有的 Azure AD 使用者的資訊 ︰ **名字、 姓氏**, ，**使用者名稱**, ，**電子郵件**, ，**密碼**, ，**確認密碼**。
    3.  按一下 [ **新增**。

>[AZURE.NOTE] 您可以使用任何其他 Cisco Webex 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Cisco Webex 所提供。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Cisco Webex，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Cisco Webex * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


