<properties 
    pageTitle="教學課程：Azure Active Directory 與 AirWatch 整合 | Microsoft Azure" 
    description="了解如何使用 AirWatch 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 AirWatch 整合

本教學課程的目的是要示範 Azure 與 AirWatch 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 AirWatch 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 AirWatch 的 Azure AD 使用者將能夠登入位於您 AirWatch 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 AirWatch 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##啟用 AirWatch 的應用程式整合

本節的目的是概述如何啟用 AirWatch 的應用程式整合。

###若要啟用 AirWatch 的應用程式整合，請執行下列步驟：

1.  在 [Azure 管理上 「 入口 」，左邊的瀏覽] 窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，在 [目錄] 檢視中，按一下 [ **應用程式** 在最上層的功能表。

    ![應用程式](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 在頁面底部。

    ![新增應用程式](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從圖庫中新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **AirWatch**。

    ![應用程式庫](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **AirWatch**, ，然後按一下 [ **完成** 加入應用程式。

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 AirWatch 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **AirWatch** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 AirWatch** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **AirWatch 登入 URL** 文字方塊中，輸入您的 URL，讓使用者中用來登入 AirWatch 的應用程式 (例如: 「*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configure App URL")

4.  在 **設定單一登入在 AirWatch** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 AirWatch 公司網站。

6.  在左的導覽窗格中，按一下 [ **帳戶**, ，然後按一下 [ **管理員**。

    ![系統管理員](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrators")

7.  展開 **設定** 功能表，然後再按一下 **目錄服務**。

    ![設定](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Settings")

8.  按一下 [ **使用者** 索引標籤的 **Base DN** textfield，輸入您的網域名稱，然後按一下 **儲存**。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791922.png "User")

9.  按一下 [ **伺服器** ] 索引標籤。

    ![伺服器](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. 執行下列步驟：

    ![上傳](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Upload")

    1.  做為 **目錄類型**, ，請選取 **無**。
    2.  選取 **使用 SAML 進行驗證**。
    3.  若要上傳已下載的憑證，請按一下 [ **上載**。

11. 在 **要求** 區段中，執行下列步驟:

    ![要求](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Request")

    1.  做為 **要求繫結類型**, ，請選取 **POST**。
    2.  在 Azure 網站中，在 **Airwatch 在設定單一登入** 對話方塊頁面中，複製 **單一登入服務 URL** 值，並接著將它貼入 **身分識別提供者單一登入 URL** 文字方塊。
    3.  做為 **NameID 格式**, ，請選取 **電子郵件地址**。
    4.  按一下 [ **儲存**。

12. 按一下 [ **使用者** ] 索引標籤。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791926.png "User")

13. 在 **屬性** 區段中，執行下列步驟:

    ![屬性](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribute")

    1.  在 **物件識別元** 文字方塊中，輸入 **http://schemas.microsoft.com/identity/claims/objectidentifier**。
    2.  在 **Username** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    3.  在 **顯示名稱** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    4.  在 **名字** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    5.  在 **姓氏** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    6.  在 **電子郵件** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    7.  按一下 [ **儲存**。

14. 在 Azure 的 AD 入口網站中，選取單一的登入設定確認，然後按一下 [ **完成** 關閉 **設定單一登入** 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 AirWatch，必須將他們佈建到 AirWatch。  
AirWatch 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **AirWatch** 系統管理員身分的公司網站。

2.  在左側導覽窗格中按一下 [ **帳戶**, ，然後按一下 [ **使用者**。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Users")

3.  在 **使用者** ] 功能表上，按一下 [ **清單檢視**, ，然後按一下 [ **新增 \ > 新增使用者**。

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Add User")

4.  在 **新增 / 編輯使用者** ] 對話方塊中，執行下列步驟:

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Add User")

    1.  型別 **Username**, ，**密碼**, ，**確認密碼**, ，**名字**, ，**姓氏**, ，**電子郵件地址** 有效的 Azure Active Directory 帳戶，您想要佈建相關的文字方塊。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 AirWatch 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 AirWatch。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 AirWatch，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * AirWatch * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Assign Users")

3.  選取您的測試使用者，請按一下 [ **指派**, ，然後按一下 [ **是** 以確認您的工作分派。

    ![是](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

