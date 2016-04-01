<properties 
    pageTitle="教學課程：Azure Active Directory 與 Citrix ShareFile 整合 | Microsoft Azure" 
    description="了解如何使用 Citrix ShareFile 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Citrix ShareFile 整合

本教學課程的目的是要示範 Azure 與 Citrix ShareFile 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Citrix ShareFile 租用戶

完成本教學課程之後, 您已指派給 Citrix ShareFile 的 Azure AD 使用者將能夠登入位於您 Citrix ShareFile 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Citrix ShareFile 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")
##啟用 Citrix ShareFile 的應用程式整合

本節的目的是要說明如何啟用 Citrix ShareFile 的應用程式整合。

###若要啟用 Citrix ShareFile 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Citrix ShareFile**。

    ![應用程式資源庫](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Citrix ShareFile**, ，然後按一下 [ **完成** 加入應用程式。

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Citrix ShareFile。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Citrix ShareFile** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Enable single sign-on")

2.  在 **您希望使用者如何登入 Citrix ShareFile** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Citrix ShareFile 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 `https://<tenant-name>.shareFile.com`, ，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")

4.  在 **設定單一登入在 Citrix ShareFile** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")

5.  在不同的網頁瀏覽器視窗中，登入您 **Citrix ShareFile** 系統管理員身分的公司網站。

6.  在頂端工具列中，按一下 [ **管理員**。

7.  在左的導覽窗格中，選取 **設定單一登入**。

    ![帳戶管理](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")

8.  在 **單一登入 / SAML 2.0 設定** ] 對話方塊頁面上，在 **基本設定**, ，執行下列步驟 ︰

    ![單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Single sign-on")

    1.  按一下 [ **啟用 SAML**。
    2.  在 Azure 網站中，在 **Citrix ShareFile 在設定單一登入** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **您 IDP 簽發者 / 實體識別碼** 文字方塊。
    3.  在 Azure 網站中，在 **Citrix ShareFile 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    4.  在 Azure 網站中，在 **設定單一登入在 Citrix ShareFile** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    5.  按一下 [ **變更** 旁 **X.509 憑證** 欄位，然後再上傳您下載來自 Azure AD 入口網站的憑證。
        ![基本設定](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")

9.  按一下 [ **儲存** Citrix ShareFile 管理入口網站上。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Citrix ShareFile，必須將他們佈建到 Citrix ShareFile。  
Citrix ShareFile 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Citrix ShareFile** 租用戶。

2.  按一下 [ **管理使用者 \ > 管理使用者首頁 \ > + 建立員工**。

    ![建立員工](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")

3.  輸入 **電子郵件**, ，**名字** 和 **姓氏** 的有效 azure AD 帳戶，您想要佈建。

    ![基本資訊](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")

4.  按一下 [ **將使用者加入**。

    >[AZURE.NOTE] AAD 帳戶持有者將收到的電子郵件，並依循連結來確認其帳戶，然後就會生效。

>[AZURE.NOTE] 您可以使用任何其他 Citrix ShareFile 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Citrix ShareFile 所提供。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Citrix ShareFile，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Citrix ShareFile * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


