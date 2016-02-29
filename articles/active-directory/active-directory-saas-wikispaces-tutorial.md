<properties 
    pageTitle="教學課程：Azure Active Directory 與 Wikispaces 整合 | Microsoft Azure" 
    description="了解如何使用 Wikispaces 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Wikispaces 整合
  
本教學課程的目的是要示範 Azure 與 Wikispaces 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Wikispaces 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Wikispaces 的 Azure AD 使用者將能夠登入位於您 Wikispaces 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Wikispaces 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##啟用 Wikispaces 的應用程式整合
  
本節的目的是要說明如何啟用 Wikispaces 的應用程式整合。

###若要啟用 Wikispaces 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Wikispaces**。

    ![應用程式庫](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Wikispaces**, ，然後按一下 [ **完成** 加入應用程式。

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 Wikispaces。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Wikispaces** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Wikispaces** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Wikispaces 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*http://company.wikispaces.net*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")

4.  在 **Wikispaces 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後儲存您的電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")

5.  將中繼資料檔傳送給 Wikispaces 支援小組。

    >[AZURE.NOTE] 單一登入組態必須 Wikispaces 支援小組必須執行。 設定完成後，您將會收到通知。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Wikispaces，則必須將他們佈建到 Wikispaces。  
Wikispaces 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Wikispaces** 系統管理員身分的公司網站。

2.  移至 **成員**。

    ![成員](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")

3.  按一下 [ **邀請的人**。

    ![邀請人員](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")

4.  在 **邀請的人** 區段中，執行下列步驟:

    ![邀請人員](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")

    1.  型別 **使用者名稱或電子郵件地址** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **傳送**。  

        >[AZURE.NOTE] Azure Active Directory 帳戶持有者會收到包含連結就會生效，先確認其帳戶的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Wikispaces 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Wikispaces。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Wikispaces，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Wikispaces * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
