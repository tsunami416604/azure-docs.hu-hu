<properties 
    pageTitle="教學課程：Azure Active Directory 與 Veracode 整合 | Microsoft Azure" 
    description="了解如何使用 Veracode 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Veracode 整合
  
本教學課程的目的是要示範 Azure 與 Veracode 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Veracode 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Veracode 的 Azure AD 使用者將能夠登入應用程式使用的單一登入 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Veracode 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario")

##啟用 Veracode 的應用程式整合
  
本節的目的是要說明如何啟用 Veracode 的應用程式整合。

###若要啟用 Veracode 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-veracode-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-veracode-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Veracode**。

    ![應用程式庫](./media/active-directory-saas-veracode-tutorial/IC802904.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Veracode**, ，然後按一下 [ **完成** 加入應用程式。

    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Veracode。  
Veracode 應用程式需要特定的格式，會要求您新增自訂屬性對應到 SAML 判斷提示您 **saml 權杖屬性** 組態。  
以下螢幕擷取畫面顯示上述的範例。

![屬性](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Veracode** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Veracode** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configure Single Sign-On")

3.  在 **設定應用程式設定** 頁面上，按一下 **下一步**。

    ![設定 App 設定](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configure App Settings")

4.  在 **Veracode 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Veracode 公司網站。

6.  在頂端功能表中，按一下 [ **設定**, ，然後按一下 [ **管理員**。

    ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  按一下 [ **SAML** ] 索引標籤。

8.  在 **組織 SAML 設定** 區段中，執行下列步驟 ︰

    ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  在 Azure 網站中，在 **Veracode 在設定單一登入** 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者** 文字方塊
    2.  若要上傳您下載的憑證，請按一下 [ **選擇檔案**。
    3.  選取 **啟用自動註冊**。

9.  在 **自我登錄設定** 區段中，執行下列步驟，並按 [ **儲存**:

    ![系統管理](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  做為 **新的使用者啟動**, ，請選取 **不啟用需要**。
    2.  做為 **使用者資料更新**, ，請選取 **喜好設定 Veracode 使用者資料**。
    3.  如 **SAML 屬性的詳細資料**, ，選取下列 ︰
        -   **[使用者角色]**
        -   **[原則系統管理員]**
        -   **[檢閱者]**
        -   **[安全性負責人]**
        -   **[行政人員]**
        -   **[傳送者]**
        -   **[建立者]**
        -   **[所有掃描類型]**
        -   **[小組成員資格]**
        -   **[預設小組]**

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configure Single Sign-On")

11. 在頂端功能表中，按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributes")

12. 若要加入必要的屬性對應，請執行下列步驟：

    ![屬性](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributes")

  	| 屬性名稱 | 屬性值 |
  	|:---------------|:----------------|
  	| firstname      | User.givenname  |
  	| lastname       | User.surname    |
  	| 電子郵件          | User.mail       |

    1.  每個資料列上表中，按一下 [ **新增使用者屬性**。
    
    2.  在 **屬性名稱** 文字方塊中，輸入該資料列所顯示的屬性名稱。

    3.  在 **屬性值** 文字方塊中，選取該資料列所顯示的屬性值。

    4.  按一下 [ **完整**。

13. 按一下 [ **套用變更**。

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Veracode，則必須將他們佈建到 Veracode。  
若是 Veracode 的情況 來佈建是自動化的工作。  
沒有您適用的動作項目。
  
在第一次單一登入嘗試時會自動建立使用者 (如有必要)。

>[AZURE.NOTE] 您可以使用任何其他 Veracode 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Veracode。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Veracode，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Veracode * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-veracode-tutorial/IC802915.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-veracode-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

