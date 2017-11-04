### <a name="app-service-plan"></a>App Service-csomag
A service-csomag a webalkalmazás üzemeltetéséhez létrehoz. Itt megadni a segítségével a terv a **hostingPlanName** paraméter. A terv helye ugyanazon a helyen használt ahhoz az erőforráscsoporthoz. Az árképzési szint és a feldolgozói mérete vannak megadva a **sku** és **workerSize** paraméterek

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

