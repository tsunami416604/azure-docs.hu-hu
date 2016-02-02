<properties 
    pageTitle="教學課程：Azure AD 與 Sciforma 整合 | Microsoft Azure" 
    description="了解如何使用 Sciforma 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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


# 教學課程：Azure AD 與 Sciforma 整合

本教學課程的目的是要示範 Azure 與 Sciforma 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Sciforma 租用戶



本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Sciforma 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")
## 啟用 Sciforma 的應用程式整合

本節的目的是要說明如何啟用 Sciforma 的應用程式整合。

### 若要啟用 Sciforma 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]****中，輸入 **Sciforma**。

    ![應用程式資源庫](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")

7.  在結果窗格中，選取 [Sciforma]****，然後按一下 [完成]**** 來新增應用程式。

    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Sciforma。

### 若要設定單一登入，請執行下列步驟：

1.  

    ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")

2.  

    ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")

3.  

    ![設定應用程式 URL](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")

4.  

    ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")

5.  將該中繼資料檔轉寄給 Sciforma 支援小組。 支援小組需要為您設定單一登入。

6.  

    ![設定單一登入](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
## 設定使用者佈建

沒有動作項目可讓您設定 Sciforma 使用者佈建。  
當指派的使用者透過存取面板嘗試登入 Sciforma 時，Sciforma 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Sciforma 會自動予以建立。
## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 Sciforma，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  

    ![指派使用者](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。




