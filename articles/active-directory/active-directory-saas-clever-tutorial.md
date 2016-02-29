<properties 
    pageTitle="教學課程：Azure Active Directory 與 Clever 整合 | Microsoft Azure" 
    description="了解如何使用 Clever 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Clever 整合

本教學課程的目的是要示範 Azure 與 Clever 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Clever 租用戶

完成本教學課程之後, 您已指派給 Clever 的 Azure AD 使用者將能夠登入您聰明的公司網站 (服務提供者起始登入)，在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Clever 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")
##啟用 Clever 的應用程式整合

本節的目的是要說明如何啟用 Clever 的應用程式整合。

###若要啟用 Clever 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Clever**。

    ![應用程式庫](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Clever**, ，然後按一下 [ **完成** 加入應用程式。

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Clever。  
聰明的應用程式需要以特定格式將會要求您新增自訂屬性對應到 SAML 判斷提示您 **saml 權杖屬性** 組態。  
以下螢幕擷取畫面顯示上述的範例。

![屬性](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Clever** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Clever** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **聰明登入 URL** 文字方塊中，輸入您的使用者用於登入您聰明的應用程式使用的 URL (例如: *https://clever.com/in/azsandbox*)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")

4.  在 **Clever 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Clever 公司網站。

6.  在工具列中，按一下 [ **立即登入**。

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")

7.  在 **立即登入** 頁面上，執行下列步驟:

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")

    1.  型別 **登入 URL**。  

        >[AZURE.NOTE]  **登入 URL** 是自訂的值。
         您可以向 Clever 支援小組取得實際值。

    2.  做為 **身份識別系統**, ，請選取 **ADFS**。
    3.  按一下 [ **儲存**。

8.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")

9.  在頂端功能表中，按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")

10. 若要加入必要的屬性對應，請執行下列步驟：

    ![SAML Token 屬性](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")

    |屬性名稱|屬性值|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  每個資料列上表中，按一下 [ **新增使用者屬性**。
    2.  在 **屬性名稱** 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 **屬性值** 文字方塊中，選取該資料列所顯示的屬性值。
    4.  按一下 [ **完整**。

11. 按一下 [ **套用變更**。

##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Clever，必須將他們佈建到 Clever。  
Clever 的佈建工作必須由 Clever 支援小組以手動方式執行。

>[AZURE.NOTE] 您可以使用任何其他聰明的使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Clever。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Clever，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Clever * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

