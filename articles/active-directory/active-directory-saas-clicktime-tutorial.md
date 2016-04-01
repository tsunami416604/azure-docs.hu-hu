<properties 
    pageTitle="教學課程：Azure Active Directory 與 ClickTime 整合 | Microsoft Azure" 
    description="了解如何使用 ClickTime 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 ClickTime 整合

本教學課程的目的是要示範 Azure 與 ClickTime 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ClickTime 租用戶

完成本教學課程之後, 您已指派給 ClickTime 的 Azure AD 使用者將能夠登入位於您 ClickTime 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ClickTime 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Scenario")
##啟用 ClickTime 的應用程式整合

本節的目的是要說明如何啟用 ClickTime 的應用程式整合。

###若要啟用 ClickTime 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **ClickTime**。

    ![應用程式資源庫](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Application gallery")

7.  在 [結果] 窗格中，選取 **ClickTime**, ，然後按一下 [ **完成** 加入應用程式。

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 ClickTime。  
在這個程序中，您需要上傳 base-64 編碼憑證到您的 ClickTime 租用戶。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

>[AZURE.IMPORTANT] 若要能在 ClickTime 租用戶上設定單一登入，您必須先連絡 ClickTime 技術支援部門將此功能啟用。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ClickTime** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Enable single sign-on")

2.  在 **您希望使用者如何登入 ClickTime** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configure single sign-on")

3.  在 **設定單一登入在 ClickTime** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configure single sign-on")

4.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ClickTime 公司網站。

5.  在頂端工具列中，按一下 [ **喜好設定**, ，然後按一下 [ **安全性設定**。

6.  在 **單一登入喜好設定** 組態區段中，執行下列步驟 ︰

    ![安全性設定](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Security Settings")

    1.  選取 **允許** 登入使用單一登入 (SSO) 與 **OneLogin**。
    2.  在 Azure 網站中，在 **ClickTime 在設定單一登入** 對話方塊頁面中，複製 **單一登入服務 URL** 值，並接著將它貼入 **身分識別提供者端點** 文字方塊。
    3.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  開啟中的 base 64 編碼的憑證 **記事本**, ，複製內容，然後將它貼入 **X.509 憑證** 文字方塊。
    5.  按一下 [ **儲存**。

7.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configure single sign-on")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 ClickTime，必須將他們佈建到 ClickTime。  
ClickTime 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **ClickTime** 租用戶。

2.  在頂端工具列中，按一下 [ **公司**, ，然後按一下 [ **人**。

    ![人員](./media/active-directory-saas-clicktime-tutorial/IC777282.png "People")

3.  按一下 [ **將人**。

    ![新增人員](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Add Person")

4.  在 [新人員] 區段中，執行下列步驟：

    ![人員](./media/active-directory-saas-clicktime-tutorial/IC777284.png "People")

    1.  在 **電子郵件地址** 文字方塊中，輸入您的 Azure AD 帳戶的電子郵件地址。
    2.  在 **全名** 文字方塊中，輸入您的 Azure AD 帳戶的名稱。  

        >[AZURE.NOTE] 如果您想要您可以設定新人員物件的其他屬性。

    3.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 ClickTime 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 ClickTime。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 ClickTime，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * ClickTime * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


