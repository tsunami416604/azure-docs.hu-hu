<properties 
    pageTitle="教學課程：Azure Active Directory 與 Directions on Microsoft 整合 | Microsoft Azure" 
    description="了解如何使用 Directions on Microsoft 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Directions on Microsoft 整合

本教學課程的目的是要示範 Azure Active Directory 與 Directions on Microsoft 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Directions on Microsoft 訂用帳戶

如果您沒有 Directions on Microsoft 訂閱，傳送電子郵件的要求 」*service@DirectionsOnMicrosoft.com*」。

完成本教學課程之後，您指派給 Directions on Microsoft 的 Azure Active Directory 使用者將能夠使用單一登入，單一登入到應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Directions on Microsoft 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario")
##啟用 Directions on Microsoft 的應用程式整合

本節的目的是要說明如何啟用 Directions on Microsoft 的應用程式整合。

###若要啟用 Directions on Microsoft 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Directions on Microsoft**。

    ![應用程式庫](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Directions on Microsoft**, ，然後按一下 [ **完成** 加入應用程式。

    ![案例](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Directions on Microsoft。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Directions on Microsoft** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Enable Single Sign-On")

2.  在 **您希望使用者如何登入 Directions on Microsoft** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")

3.  在 **設定應用程式 URL** ] 頁面上，在登入 URL] 文字方塊中輸入 **https://www.directionsonmicrosoft.com/user/login**, ，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configure App URL")

4.  在 **設定單一登入 Directions on Microsoft 在** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configure Single Sign-On")

5.  將中繼資料檔案傳送給 Directions on Microsoft 支援小組 (*service@DirectionsOnMicrosoft.com*)。 若要讓 Directions on Microsoft 支援小組能夠找出您的同盟站台成員資格，請在您的電子郵件中附上您公司的資訊。

    >[AZURE.NOTE] 單一登入 directions on Microsoft 必須由 Directions on Microsoft 支援小組啟用。
    單一登入啟用之後，您便會收到通知。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configure Single Sign-On")
##設定使用者佈建

沒有動作項目可讓您設定 Directions on Microsoft 使用者佈建。  
當指派的使用者嘗試使用存取面板登入 Directions on Microsoft 時，Directions on Microsoft 會檢查使用者是否存在。 如果尚無可用的使用者帳戶，Directions on Microsoft 會自動予以建立。
##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Directions on Microsoft，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Directions on Microsoft * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Yes")


