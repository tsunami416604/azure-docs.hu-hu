## 檢閱 API 應用程式中繼資料

可讓 Web API 專案部署為 API 應用程式中包含的中繼資料 *apiapp.json* 檔案和 *中繼資料* 資料夾。

![](./media/app-service-api-review-metadata/metadatainse.png)

預設內容 *apiapp.json* 檔類似下列的範例:

        {
            "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
            "id": "ContactsList",
            "namespace": "microsoft.com",
            "gateway": "2015-01-14",
            "version": "1.0.0",
            "title": "ContactsList",
            "summary": "",
            "author": "",
            "endpoints": {
                "apiDefinition": "/swagger/docs/v1",
                "status": null
            }
        }

請注意 `apiDefinition` 端點 `/swagger/docs/v1`: 根據預設，API 應用程式專案會使用 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 封裝提供自動 [Swagger](http://swagger.io/) 中繼資料產生。 

在本教學課程中，您可以接受預設值。  [API 應用程式中繼資料](#api-app-metadata) 教學課程稍後的章節將說明如何自訂此中繼資料。

