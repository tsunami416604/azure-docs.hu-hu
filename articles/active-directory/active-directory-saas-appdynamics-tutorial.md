<properties 
    pageTitle="教學課程：Azure Active Directory 與 AppDynamics 整合 | Microsoft Azure" 
    description="了解如何使用 AppDynamics 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 AppDynamics 整合

本教學課程的目的是要示範 Azure 與 AppDynamics 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 AppDynamics 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 AppDynamics 的 Azure AD 使用者將能夠登入您的 AppDynamics 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 AppDynamics 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")
##啟用 AppDynamics 的應用程式整合

本節的目的是要說明如何啟用 AppDynamics 的應用程式整合。

###若要啟用 AppDynamics 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **AppDynamics**。

    ![應用程式庫](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **AppDynamics**, ，然後按一下 [ **完成** 加入應用程式。

    ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 AppDynamics。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **AppDynamics** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")

2.  在 **您希望使用者如何登入 AppDynamics** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")

3.  在 **設定應用程式 URL** 頁面上，於 **AppDynamics 登入 URL** 文字方塊中，輸入您的 URL 會使用您的使用者用於登入 AppDynamics (「*https://companyname.saas.appdynamics.com*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")

4.  在 **設定單一登入在 AppDynamics** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AppDynamics 公司網站。

6.  在頂端工具列中，按一下 [ **設定**, ，然後按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  按一下 [ **驗證提供者** ] 索引標籤。

    ![驗證提供者](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")

8.  在 **驗證提供者** 區段中，執行下列步驟 ︰

    ![SAML 組態](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")

    1.  做為 **驗證提供者**, ，請選取 **SAML**。
    2.  在 Azure 網站中，在 **AppDynamics 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    3.  在 Azure 網站中，在 **AppDynamics 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    4.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    5.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **憑證** 文字方塊
    6.  按一下 [ **儲存**。
        ![儲存](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 AppDynamics，必須將他們佈建到 AppDynamics。  
AppDynamics 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 AppDynamics 公司網站。

2.  移至 **使用者**, ，然後按一下 [ **+** 開啟 **Create User** ] 對話方塊。

    ![使用者](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")

3.  在 **Create User** 區段中，執行下列步驟 ︰

    ![建立使用者](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")

    1.  型別 **Username**, ，**名稱**, ，**電子郵件**, ，**新密碼**, ，**確認新密碼** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 AppDynamics 使用者帳戶建立工具或 Api 來佈建 Azure AD 使用者帳戶的 appdynamics 所提供。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 AppDynamics，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * AppDynamics * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


