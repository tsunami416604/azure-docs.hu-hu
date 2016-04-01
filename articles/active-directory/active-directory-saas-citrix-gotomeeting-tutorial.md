<properties 
    pageTitle="教學課程：Azure Active Directory 與 Citrix GoToMeeting 整合 | Microsoft Azure" 
    description="了解如何使用 Citrix GoToMeeting 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/01/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Citrix GoToMeeting 整合  
適用對象：Azure

>[AZURE.TIP]意見反應，按一下 [ [這裡](http://go.microsoft.com/fwlink/?LinkId=522412)。

本教學課程的目的是要示範 Azure 與 Citrix GoToMeeting 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Citrix GoToMeeting 租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Citrix GoToMeeting 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![組態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")
##啟用 Citrix GoToMeeting 的應用程式整合

本節的目的是要說明如何啟用 Citrix GoToMeeting 的應用程式整合。

###若要啟用 Citrix GoToMeeting 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6.  在 **搜尋方塊**, ，型別 **Citrix GoToMeeting**。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  在 [結果] 窗格中，選取 **Citrix GoToMeeting**, ，然後按一下 [ **完成** 加入應用程式。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Citrix GoToMeeting。  
在這個程序中，您需要上傳 base-64 編碼憑證到您的 Citrix GoToMeeting 租用戶。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 **Citrix GoToMeeting** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登 ON** ] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2.  在 **您希望使用者如何登入 Citrix GoToMeeting** 頁面上，選取 **Microsoft Azure AD 單一登入**。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")


3. 在 **設定應用程式設定** 頁面上，按一下 **下一步**。 <br><br>![啟用單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4.  在 **在 Citrix GoToMeeting 設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5.  在不同的瀏覽器視窗中，登入您 Citrix 組織中心 ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/))。

6. 按一下 [ **身分識別提供者** 索引標籤，然後再執行下列步驟 ︰  <br><br> ![SAML 設定](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")

     6.1. 選取 **手冊**

     6.2. 在 Azure 網站中，在 **在 Citrix GoToMeeting 設定單一登入** 對話方塊頁面中，複製 **登入頁面 URL** 值，並接著將它貼入 **登入頁面 URL** 文字方塊。 

     6.3. 在 Azure 網站中，在 **在 Citrix GoToMeeting 設定單一登入** 對話方塊頁面中，複製 **登出頁面 URL** 值，並接著將它貼入 **登出頁面 URL** 文字方塊。

     6.4. 在 Azure 網站中，在 **在 Citrix GoToMeeting 設定單一登入** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **身分識別提供者的實體識別碼** 文字方塊。

     6.5. 若要上傳您下載的憑證，請按一下 [ **上傳憑證**。

     6.6. 按一下 [ **儲存**。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")


7. 在 **單一登入確認** 頁面上，按一下 **完成**。<br><br> ![SAML 設定](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")





##設定使用者佈建

本節的目的是要說明如何對 Citrix GoToMeeting 啟用 Active Directory 使用者帳戶的佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Azure 管理入口網站中，在 **Citrix GoToMeeting** 應用程式整合頁面上，按一下 [ **設定使用者佈建** 開啟 **設定使用者佈建** ] 對話方塊。

    ![設定使用者佈建](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2.  在 **設定和管理員認證** 頁面上，執行下列步驟 ︰

    ![設定使用者佈建](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")

     2.1. 在 **Citrix GoToMeeting 管理使用者名稱** 文字方塊中，輸入系統管理員的使用者名稱。

     2.2. 在 **Citrix GoToMeeting 管理員密碼** 文字方塊中，系統管理員的密碼。

     2.3. 按一下 [ **下一步**。

3.  在 **確認** 頁面上，按一下核取記號以儲存您的設定。

4.  按一下 [ **驗證** ] 按鈕，以驗證您的組態。
##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Citrix GoToMeeting，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Citrix GoToMeeting** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

請等候 10 分鐘並確認帳戶已同步至企業適用的 Dropbox。

第一個驗證步驟中，您可以檢查佈建狀態中，按一下儀表板中的 D **Citrix GoToMeeting** Azure 管理入口網站上的應用程式整合頁面。

![儀表板](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

成功完成的使用者佈建週期會以相關狀態表示：

![整合狀態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

如果要測試您的單一登入設定，請開啟存取面板。

如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](https://msdn.microsoft.com/library/dn308586)。


