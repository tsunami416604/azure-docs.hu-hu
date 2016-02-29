<properties 
    pageTitle="教學課程：Azure Active Directory 與 Rally Software 整合 | Microsoft Azure" 
    description="了解如何使用 Rally Software 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Rally Software 整合
  
本教學課程的目的是要示範 Azure 與 Rally Software 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Rally Software 租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Rally Software 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")
##啟用 Rally Software 的應用程式整合
  
本節的目的是要說明如何啟用 Rally Software 的應用程式整合。

###若要啟用 Rally Software 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **集結**。

    ![應用程式資源庫](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Rally Software**, ，然後按一下 [ **完成** 加入應用程式。

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Rally Software。  
在此程序中，您必須將憑證上傳至 Rally Software。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站中，在 * * Rally Software * * 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Rally** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **集結軟體租用戶 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>.rally.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")

4.  在 **集結在設定單一登入** 頁面上，按一下 [下載中繼資料]，然後將它儲存在電腦上。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")

5.  登入您 **Rally Software** 租用戶。

6.  在頂端工具列中，按一下 [ **安裝**, ，然後選取 **訂閱**。

    ![訂用帳戶](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")

7.  按一下 [ **動作** 右側頂端的工具列按鈕，然後選取 **編輯訂閱**。

8.  在 **訂閱** 對話方塊頁面上，執行下列步驟，以及 [ **儲存並關閉**:

    ![驗證](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")

    1.  選取 **Rally 或 SSO 驗證** 從 [驗證] 下拉式清單
    2.  在 Azure 網站中，在 **在 Rally Software 設定單一登入** 對話方塊頁面中，複製 **身分識別提供者識別碼** 值，並接著將它貼入 **身分識別提供者 URL** 文字方塊
    3.  在 Azure 網站中，在 **在 Rally Software 設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值。

9.  在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
##設定使用者佈建
  
若要讓 AAD 使用者可以登入，則必須使用他們的 Azure Active Directory 使用者名稱將其佈建至 Rally Software 應用程式。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您的 Rally Software 租用戶。

2.  移至 **安裝 \ > 使用者**, ，然後按一下 [ **+ 加入新**。

    ![使用者](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")

3.  在新的使用者] 文字方塊中輸入名稱，然後按一下 **的詳細資料加入**。

4.  在 **Create User** 區段中，執行下列步驟:

    ![建立使用者](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")

    1.  在 **使用者名稱** 文字方塊中，輸入您想要佈建的 Azure AD 使用者的名稱。
    2.  在 **電子郵件地址** 文字方塊中，輸入您想要佈建的 Azure AD 使用者的電子郵件地址。
    3.  按一下 [ **儲存並關閉**。

>[AZURE.NOTE]您可以使用任何其他 Rally Software 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Rally Software 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Rally Software，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Rally Software** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。





