<properties 
    pageTitle="教學課程：Azure Active Directory 與 Projectplace 整合 | Microsoft Azure" 
    description="了解如何使用 Projectplace 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Projectplace 整合
  
本教學課程的目的是要示範 Azure 與 Projectplace 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Projectplace 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Projectplace 的 Azure AD 使用者將能夠登入位於您 Projectplace 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Projectplace 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")
##啟用 Projectplace 的應用程式整合
  
本節的目的是要說明如何啟用 Projectplace 的應用程式整合。

###若要啟用 Projectplace 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Projectplace**。

    ![應用程式庫](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Projectplace**, ，然後按一下 [ **完成** 加入應用程式。

    ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Projectplace 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Projectplace** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")

2.  在 **您希望使用者如何登入 Projectplace** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")

3.  在 **設定應用程式 URL** 頁面上，於 **Projectplace 登入 URL** 文字方塊中，輸入您 ProjectPlace 的租用戶 URL (例如: 「*http://company.projectplace.com*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")

4.  在 **Projectplace 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後儲存您的電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")

5.  將中繼資料檔傳送給 Projectplace 支援小組。

    >[AZURE.NOTE] 單一登入組態必須由 Projectplace 支援小組執行。 設定完成後，您將會收到通知。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 Projectplace，必須將他們佈建到 Projectplace。  
Projectplace 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Projectplace** 系統管理員身分的公司網站。

2.  移至 **人**, ，然後按一下 [ **成員**。

    ![人員](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")

3.  按一下 [ **將成員加入**。

    ![新增成員](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")

4.  在 **加入成員** 區段中，執行下列步驟 ︰

    ![新成員](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")

    1.  在 **新成員** 文字方塊中，輸入您想要佈建到相關文字方塊的有效 AAD 帳戶的電子郵件地址。
    2.  按一下 [ **傳送**

        >[AZURE.NOTE] 包含的連結，就會生效，先確認其帳戶的電子郵件會傳送至 Azure Active Directory 帳戶持有者。
    
>[AZURE.NOTE]您可以使用任何其他 Projectplace 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Projectplace。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Projectplace，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Projectplace * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

