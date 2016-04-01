<properties 
    pageTitle="教學課程：Azure Active Directory 與 Onit 整合 | Microsoft Azure" 
    description="了解如何使用 Onit 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Onit 整合
  
本教學課程的目的是要示範 Azure 與 Onit 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Onit 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Onit 的 Azure AD 使用者將能夠登入您的 Onit 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Onit 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario")
##啟用 Onit 的應用程式整合
  
本節的目的是要說明如何啟用 Onit 的應用程式整合。

###若要啟用 Onit 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-onit-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Onit**。

    ![應用程式庫](./media/active-directory-saas-onit-tutorial/IC791167.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Onit**, ，然後按一下 [ **完成** 加入應用程式。

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Onit 中進行驗證。  
設定 Onit 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。
  
您的 Onit 應用程式特定的格式，會要求您新增自訂屬性對應至預期 SAML 判斷提示您 **saml 權杖屬性** 組態。  
以下螢幕擷取畫面顯示上述的範例。

![單一登入](./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Onit** 應用程式整合頁面上，在最上層功能表中按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-onit-tutorial/IC791169.png "Attributes")

2.  若要加入必要的屬性對應，請執行下列步驟：

    
  	|Attribute Name|Attribute Value|
  	|---|---|
  	|name|User.userprincipalname|
  	|email|User.mail|

    1.  For each data row in the table above, click **add user attribute**.
    2.  In the **Attribute Name** textbox, type the attribute name shown for that row.
    3.  From the **Attribute Value** list, select the attribute value shown for that row.
    4.  Click **Complete**.

3.  按一下 [ **套用變更**。

4.  在瀏覽器中，按一下 [ **回** 開啟 **快速入門** 對話方塊一次。

5.  按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791170.png "Configure Single Sign-On")

6.  在 **您希望使用者如何登入 Onit** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")

7.  在 **設定應用程式 URL** 頁面上，於 **Onit 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您的 Onit 應用程式 (例如: 「*https://ms-sso-test.onit.com*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-onit-tutorial/IC791172.png "Configure App URL")

8.  在 **Onit 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")

9.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Onit 公司網站。

10. 在頂端功能表中，按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")

11. 按一下 [ **編輯公司**。

    ![編輯公司](./media/active-directory-saas-onit-tutorial/IC791175.png "Edit Corporation")

12. 按一下 [ **安全性** ] 索引標籤。

    ![編輯公司資訊](./media/active-directory-saas-onit-tutorial/IC791176.png "Edit Company Information")

13. 在 **安全性** 索引標籤上，執行下列步驟 ︰

    ![單一登入](./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")

    1.  做為 **驗證策略**, ，請選取 **單一登入和密碼**。
    2.  在 Azure 網站中，在 **Onit 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **Idp 目標 URL** 文字方塊。
    3.  在 Azure 網站中，在 **Onit 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **Idp 登出 URL** 文字方塊。
    4.  複製 **指紋** 值匯出的憑證，並接著將它貼入 **Idp 憑證指紋 (SHA1)** 文字方塊。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    5.  做為 **SSO 類型**, ，請選取 **SAML**。
    6.  在 **SSO 登入按鈕文字** 文字方塊中，輸入您喜歡的按鈕文字。
    7.  選取 **SSO 登入 ︰ 所需的下列網域/使用者**, ，在相關的文字方塊中，輸入測試使用者電子郵件地址，然後按一下 **更新**。
        ![編輯公司](./media/active-directory-saas-onit-tutorial/IC791178.png "Edit Corporation")

14. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 Onit，必須將他們佈建到 Onit。  
在 Onit 的情況下，需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **Onit** 系統管理員身分的公司網站。

2.  按一下 [ **將使用者加入**。

    ![系統管理](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")

3.  在 **新增使用者** 對話方塊頁面上，執行下列步驟 ︰

    ![新增使用者](./media/active-directory-saas-onit-tutorial/IC791181.png "Add User")

    1.  型別 **名稱** 和 **電子郵件地址** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **建立**。  

        >[AZURE.NOTE] 帳戶擁有者會收到電子郵件，其中包含的連結，確認其帳戶之前就會生效。

>[AZURE.NOTE] 您可以使用任何其他 Onit 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Onit。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Onit，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Onit * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-onit-tutorial/IC791182.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-onit-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

