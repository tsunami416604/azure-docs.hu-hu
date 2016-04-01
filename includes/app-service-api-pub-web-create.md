1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下專案 （而非方案），然後按一下 **發行**。 

    ![專案發佈功能表選項](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. 按一下 [ **設定檔** ] 索引標籤上，按一下 [ **Microsoft Azure API 應用程式 （預覽）**。 

    ![發佈 Web 對話方塊](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. 按一下 [ **新增** 佈建您的 Azure 訂閱中的新 API 應用程式。

    ![選取現有的 API 服務對話方塊](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. 在 **建立 API 應用程式** ] 對話方塊中，輸入下列命令 ︰

    - 如 **API 應用程式名稱**, ，輸入您在本教學課程使用的名稱。 
    - 如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶。
    - 如 **應用程式服務方案**, 中，選取 [從現有的應用程式服務方案，或選取 **建立新的應用程式服務方案** ，然後輸入新的方案名稱。 
    - 如 **資源群組**, 中，選取 [從現有的資源群組，或選取 **建立新的資源群組** 輸入的名稱。 
    - 如 **存取層級**, ，請選取 **可供任何人**。 稍後您可以透過 Azure 預覽入口網站限制存取。
    - 如 **區域**, ，選取接近您的區域。  

    ![設定 Microsoft Azure Web 應用程式對話方塊](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. 按一下 [ **確定** 在您的訂閱中建立 API 應用程式。 

    因為此程序可能花費數分鐘，所以 Visual Studio 會顯示一個確認對話方塊。  

6. 按一下 [ **確定** 在確認對話方塊。 
 
    佈建程序會在您的 Azure 訂閱中建立資源群組和 API 應用程式。 Visual Studio 中顯示進度 **Azure App Service 活動** 視窗。 

    ![透過 Azure App Service 活動視窗的狀態通知](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

