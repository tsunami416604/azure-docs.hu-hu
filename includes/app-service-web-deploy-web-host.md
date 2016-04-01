### App Service 方案

建立主控 Web 應用程式的服務方案。 提供透過計劃名稱 **hostingPlanName** 參數。 計劃的位置是 
web 應用程式所使用的相同位置。 定價層和背景工作大小指定於 **sku** 和 **workerSize** 參數

    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "numberOfWorkers": 1
      }
    },


