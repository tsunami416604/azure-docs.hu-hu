<properties 
    pageTitle="教學課程：Azure Active Directory 與 Adobe EchoSign 整合 | Microsoft Azure" 
    description="了解如何使用 Adobe EchoSign 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" authors="markusvi"  
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

#教學課程：Azure Active Directory 與 Adobe EchoSign 整合

本教學課程的目的是要示範 Azure 與 Adobe EchoSign 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Adobe EchoSign 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Adobe EchoSign 的 Azure AD 使用者將能夠登入位於您 Adobe EchoSign 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Adobe EchoSign 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenario")
##啟用 Adobe EchoSign 的應用程式整合

本節的目的是概述如何啟用 Adobe EchoSign 的應用程式整合。

###若要啟用 Adobe EchoSign 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Adobe EchoSign**。

    ![應用程式庫](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Adobe EchoSign**, ，然後按一下 [ **完成** 加入應用程式。

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Adobe EchoSign 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Adobe EchoSign** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Adobe EchoSign** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Adobe EchoSign 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.echosign.com/*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configure App URL")

4.  在 **設定單一登入，在 Adobe EchoSign** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Adobe EchoSign 公司網站。

6.  在頂端功能表中，按一下 [ **帳戶**, ，然後在左邊的骰子上瀏覽窗格中按一下 [ **SAML 設定** 下 **帳戶設定**。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Account")

7.  在 [SAML 設定] 區段中，執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML Settings")

    1.  做為 **SAML 模式**, ，請選取 **SAML 強制**。
    2.  選取 **允許 EchoSign 帳戶系統管理員使用其 EchoSign 認證登入**。
    3.  做為 **使用者建立**, ，請選取 **自動新增使用者經過 SAML**。

8.  繼續執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML Settings")

    1.  在 Azure 網站中上, **設定單一登入，在 Adobe EchoSign** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **IdP 實體識別碼** 文字方塊。
    2.  在 Azure 網站中上, **設定單一登入，在 Adobe EchoSign** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **IdP 登入 URL** 文字方塊。
    3.  在 Azure 網站中上, **設定單一登入，在 Adobe EchoSign** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **IdP 登出 URL** 文字方塊。
    4.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)
    5.在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **IdP 憑證** 文字方塊
    6.按一下 [ **儲存變更**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Adobe EchoSign，必須將他們佈建到 Adobe EchoSign。  
Adobe EchoSign 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Adobe EchoSign** 系統管理員身分的公司網站。

2.  在頂端功能表中，按一下 [ **帳戶**, ，然後在左邊的骰子上瀏覽窗格中按一下 [ **使用者和群組**, ，然後按一下 [ **建立新的使用者**。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Account")

3.  在 **建立新的使用者** 區段中，執行下列步驟 ︰

    ![建立使用者](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")

    1.  型別 **電子郵件地址**, ，**名字** 和 **姓氏** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **建立使用者**。

        >[AZURE.NOTE] Azure Active Directory 帳戶持有者將會收到電子郵件，其中包含連結，以確認其帳戶之前就會生效。

>[AZURE.NOTE] 您可以使用任何其他 Adobe EchoSign 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Adobe EchoSign 所提供。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Adobe EchoSign，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Adobe EchoSign * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


