7. 以滑鼠右鍵按一下 [API 應用程式專案，在 **方案總管] 中** ，然後選取 **發行** 若要開啟 [發行] 對話方塊。 您稍早建立的發佈設定檔應該預先選取。 

9. 按一下 [ **發行** 開始部署程序。 

    ![部署 API 應用程式](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

     **Azure App Service 活動** ] 視窗會顯示部署進度。 

    ![Azure App Service 活動視窗的狀態通知](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

    在部署過程中，Visual Studio 會自動嘗試重新啟動 *閘道*。 閘道處理資源群組中的所有 API 應用程式的管理功能的 web 應用程式，且必須重新啟動，才能辨識 API 應用程式的 API 定義中的變更或 *apiapp.json* 檔案。 
 
    如果您使用另一個方法來部署 API 應用程式，或如果 Visual Studio 無法重新啟動閘道，您可能必須手動重新啟動閘道。 下列步驟說明如何執行該動作。

1. 在瀏覽器中，移至 [Azure 預覽入口網站](https://portal.azure.com)。 

2. 瀏覽至 **API 應用程式** 您部署的 API 應用程式的刀鋒視窗。

    如需有關資訊 **API 應用程式** 刀鋒視窗中，以及如何尋找它，請參閱 [管理 API 應用程式](../articles/app-service-api/app-service-api-manage-in-portal.md)。

4. 按一下 [ **閘道** 連結。

3. 在 **閘道** 刀鋒視窗中，按一下 [ **重新啟動**。

    ![](./media/app-service-api-pub-web-deploy/restartgateway.png)

