<properties 
    pageTitle="部署連結至 GitHub 儲存機制的 Web 應用程式" 
    description="使用 Azure 資源管理員範本來部署 Web 應用程式，其中包含 GitHub 儲存機制的專案。" 
    services="app-service" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/16/2015" 
    ms.author="tomfitz"/>

# 部署連結至 GitHub 儲存機制的 Web 應用程式

在本主題中，您將學習如何建立 Azure 資源管理員範本，以部署連結至 GitHub 儲存機制中之專案的 Web 應用程式。 您將學習如何定義要部署哪些資源， 
如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需建立範本的詳細資訊，請參閱 [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需完整的範本，請參閱 [Web 應用程式連結至 GitHub 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## 部署內容

使用此範本時，您將會部署 Web 應用程式，其中包含 GitHub 中之專案的程式碼。

若要自動執行部署，請按一下下列按鈕：

[![Deploy 到 Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## 參數

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL

包含要部署之專案的 GitHub 儲存機制 URL。 此參數包含預設值，但是這個值只用來示範如何提供儲存機制 URL。 測試範本時，您可以使用此值，但您在使用範本時將會想要提供專屬儲存機制的 URL。

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### 分支

要在部署應用程式時使用之儲存機制的分支。 預設值是 master，但是您可以提供想要部署之儲存機制中任何分支的名稱。

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## 要部署的資源

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Web 應用程式

建立連結至 GitHub 中之專案的 Web 應用程式。 

您指定的 web 應用程式名稱 **siteName** 參數，以及透過 web 應用程式的位置 **siteLocation** 參數。 在 **dependsOn** 項目，範本會定義 web 應用程式 
為依存於服務主控方案。 因為它依存於主控方案，所以在建立好主控方案前將不會建立 Web 應用程式。  **DependsOn** 項目只用來指定部署 
順序。 如果您不要將標示為依存於主控方案的 web 應用程式，Azure 資源管理程式會嘗試同時建立這兩個資源，如果 web 應用程式會建立在主控之前，可能會收到錯誤 
計劃。

Web 應用程式也有子資源，其定義在 **資源** 下一節。 此子資源會定義使用 Web 應用程式部署之專案的原始檔控制。 在此範本中，原始檔控制 
連結到特定的 GitHub 儲存機制。 GitHub 儲存機制以程式碼定義 **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** 您將硬式編碼可能的儲存機制 URL，當您想要建立的範本，重複部署單一專案參數的最小數目。
硬式編碼的儲存機制 URL，您可以將參數加入儲存機制 url，並使用該值 **RepoUrl** 屬性。 您可以看到在儲存機制 URL 參數的範例 
[Web 應用程式與 Web 工作範本](../app-service-web-deploy-web-app-with-webjobs.md)。

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 

