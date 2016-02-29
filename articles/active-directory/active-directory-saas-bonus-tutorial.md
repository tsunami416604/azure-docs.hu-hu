<properties 
    pageTitle="教學課程：Azure Active Directory 與 Bonus.ly 整合 | Microsoft Azure" 
    description="了解如何使用 Bonus.ly 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Bonus.ly 整合

本教學課程的目的是要示範 Azure 與 Bonus.ly 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Bonus.ly 中的測試租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Bonus.ly 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")
##啟用 Bonus.ly 的應用程式整合

本節的目的是要說明如何啟用 Bonus.ly 的應用程式整合。

###若要啟用 Bonus.ly 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![啟用單一登入](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Bonus.ly**。

    ![應用程式資源庫](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Bonus.ly**, ，然後按一下 [ **完成** 加入應用程式。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Bonus.ly。  
您必須從憑證擷取指紋值，才能設定 Bonus.ly 的單一登入。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Bonus.ly** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Bonus.ly** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Bonus.ly 租用戶 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。Bonus.ly*」，然後按一下 [ **下一步**: 

    ![設定應用程式 URL](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")

4.  在 **設定單一登入在 Bonus.ly** 頁面上，按一下 **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\Bonusly.cer**。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")

5.  在不同的瀏覽器視窗中，登入您 **Bonus.ly** 租用戶。

6.  在頂端工具列中，按一下 [ **設定**, ，然後選取 **整合和應用程式**。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  在 **單一登入**, ，請選取 **SAML**。

8.  在 **SAML** 對話方塊頁面上，執行下列步驟:

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  在 Azure 網站中，在 **設定單一登入在 Bonus.ly** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼到 **IdP SSO 目標 URL** 文字方塊。
    2.  在 Azure 網站中，在 **Bonus.ly 在設定單一登入** 對話方塊頁面中，複製 **發行者識別碼** 值，並接著將它貼入 **IdP 簽發者** 文字方塊。
    3.  在 Azure 網站中，在 **Bonus.ly 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **IdP 登入 URL** 文字方塊。
    4.  複製 **指紋** 值匯出的憑證，並接著將它貼入 **憑證指紋** 文字方塊。

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

9.  按一下 [ **儲存**。

10. 在 Microsoft Azure AD 入口網站中，選取設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Bonus.ly，必須將他們佈建到 Bonus.ly。  
Bonus.ly 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在網頁瀏覽器視窗中，登入您的 Bonus.ly 租用戶。

2.  按一下 [ **設定**

    ![設定](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")

3.  按一下 [ **使用者和加分** ] 索引標籤。

    ![使用者和獎勵](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")

4.  按一下 [ **管理使用者**。

    ![管理使用者](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")

5.  按一下 [ **將使用者加入**。

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")

6.  在 **新增使用者** ] 對話方塊中，執行下列步驟:

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")

    1.  類型"**電子郵件**, ，**名字**, ，**姓氏**「 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **儲存**。

    >[AZURE.NOTE] AAD 帳戶持有者將會收到電子郵件，其中包含連結，以確認其帳戶之前就會生效。

>[AZURE.NOTE] 您可以使用任何其他 Bonus.ly 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Bonus.ly。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Bonus.ly，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 Bonus.ly 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

