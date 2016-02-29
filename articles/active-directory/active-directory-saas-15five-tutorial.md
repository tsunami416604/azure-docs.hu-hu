<properties 
    pageTitle="教學課程：Azure Active Directory 與 15Five 整合 | Microsoft Azure" 
    description="了解如何使用 15Five 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 15Five 整合

本教學課程的目的是要示範 Azure 與 15Five 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 15Five 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 15Five 的 Azure AD 使用者將能夠登入位於您 15Five 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 15Five 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")
##啟用 15Five 的應用程式整合

本節的目的是概述如何啟用 15Five 的應用程式整合。

###若要啟用 15Five 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-15five-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-15five-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **15Five**。

    ![應用程式庫](./media/active-directory-saas-15five-tutorial/IC784668.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **15Five**, ，然後按一下 [ **完成** 加入應用程式。

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶對 15Five 進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **15Five** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 15Five** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **15Five 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.15Five.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")

4.  在 **15Five 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後轉寄給 15Five 支援小組的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")

    >[AZURE.NOTE] 單一登入必須由 15Five 支援小組啟用。

5.  在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 15Five，必須將他們佈建到 15Five。  
15Five 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **15Five** 系統管理員身分的公司網站。

2.  移至 **管理公司**。

    ![管理公司](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")

3.  移至 **人 \ > 新增人員**。

    ![人員](./media/active-directory-saas-15five-tutorial/IC784676.png "People")

4.  在 [新增人員] 區段中，執行下列步驟：

    ![新增人員](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")

    1.  型別 **名字**, ，**姓氏**, ，**標題**, ，**電子郵件地址** 有效的 Azure Active Directory 帳戶，您想要佈建相關的文字方塊。
    2.  按一下 [ **完成**。

    >[AZURE.NOTE] Azure AD 帳戶持有者將會收到包含連結就會生效，先確認其帳戶的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 15Five 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶的 15Five 所提供。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 15Five，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * 15Five * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-15five-tutorial/IC784678.png "Assign users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

