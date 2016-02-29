<properties 
    pageTitle="教學課程：Azure Active Directory 與 TalentLMS 整合 | Microsoft Azure" 
    description="了解如何使用 TalentLMS 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 TalentLMS 整合
  
本教學課程的目的是要示範 Azure 與 TalentLMS 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   TalentLMS 租用戶
  
完成本教學課程之後, 您已指派給 TalentLMS 的 Azure AD 使用者將能夠登入位於您 TalentLMS 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 TalentLMS 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

##啟用 TalentLMS 的應用程式整合
  
本節的目的是要說明如何啟用 TalentLMS 的應用程式整合。

###若要啟用 TalentLMS 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **TalentLMS**。

    ![應用程式資源庫](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Application gallery")

7.  在 [結果] 窗格中，選取 **TalentLMS**, ，然後按一下 [ **完成** 加入應用程式。

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##設定單一登入
  
本節的目標在於概述如何讓使用者透過他們的帳戶驗證至 TalentLMS SAML 通訊協定為基礎的同盟，使用 Azure AD 中。 .  
設定 TalentLMS 的單一登入需要您從憑證擷取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **TalentLMS** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 TalentLMS** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **TalentLMS 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。TalentLMSapp.com*」，然後按一下 [ **下一步**。

    ![登入 URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Sign on URL")

4.  在 **TalentLMS 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\TalentLMS.cer**。

    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 TalentLMS 公司網站。

6.  在 **帳戶和設定** 區段中，按一下 **使用者** ] 索引標籤。

    ![帳戶和設定](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account & Settings")

7.  按一下 [ **單一登入 (SSO)**,，

8.  在 [單一登入] 區段中，執行下列步驟：

    ![單一登入](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")

    1.  從 **SSO 整合類型** 清單中，選取 **SAML 2.0**。
    2.  在 Azure 網站中，在 **TalentLMS 在設定單一登入** 對話方塊頁面中，複製 **身分識別提供者 ID** 值，並接著將它貼入 **身分識別提供者 (IdP)** 文字方塊。
    3.  複製 **指紋** 值匯出的憑證，並接著將它貼入 **憑證指紋** 文字方塊。

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    4.  在 Azure 網站中，在 **TalentLMS 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **遠端登入 URL** 文字方塊。
    5.  在 Azure 網站中，在 **TalentLMS 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **遠端登出 URL** 文字方塊。
    6.  在 **TargetedID** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
    7.  在 **名字** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    8.  在 **姓氏** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    9.  在 **電子郵件** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
    10. 按一下 [ **儲存**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 TalentLMS，則必須將他們佈建到 TalentLMS。  
TalentLMS 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **TalentLMS** 租用戶。

2.  按一下 [ **使用者**, ，然後按一下 [ **新增使用者**。

3.  在 **加入使用者** 對話方塊頁面上，執行下列步驟:

    ![新增使用者](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Add User")

    1.  在下列文字方塊中輸入的 Azure AD 使用者帳戶相關聯的屬性值: **名字**, ，**姓氏**, ，**電子郵件地址**。
    2.  按一下 [ **將使用者加入**。

>[AZURE.NOTE] 您可以使用任何其他 TalentLMS 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 TalentLMS。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 TalentLMS，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * TalentLMS * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
