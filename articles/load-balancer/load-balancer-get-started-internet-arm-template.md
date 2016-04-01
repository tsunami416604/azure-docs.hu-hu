<properties 
   pageTitle="使用範本在資源管理員中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 ARM 範本在資源管理員中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="joaoma" />

# 開始使用 ARM 範本建立網際網路面向的負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [了解如何建立網際網路面向的負載平衡器使用傳統部署模型](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## 使用按一下即部署來部署 ARM 範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 若要部署使用按一下要部署此範本，請遵循 [此連結](http://go.microsoft.com/fwlink/?LinkId=544801), ，按一下 [ **部署至 Azure**, 、 取代預設參數值，如有必要，並依照入口網站中的指示。

## 使用 PowerShell 部署 ARM 範本

若要使用 PowerShell 部署您下載的 ARM 範本，請依照下列步驟執行。

1. 如果您從未使用 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 並遵循指示以登入 Azure，並選取您的訂閱結束。
2. 執行 **Switch-azuremode** 指令程式可切換至資源管理員模式，如下所示。

        Switch-AzureMode AzureResourceManager

    此為上述命令的預期輸出內容：

        WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

    >[AZURE.WARNING] Switch-azuremode cmdlet 很快就會被取代。 屆時將重新命名所有的資源管理員 Cmdlet。

3. 執行 **New-azureresourcegroup** 指令程式來建立資源群組範本中使用。

        New-AzureResourceGroup -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json'  

## 使用 Azure CLI 部署 ARM 範本

若要使用 Azure CLI 部署 ARM 範本，請依照下列步驟執行。

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
2. 執行 **azure 組態模式** 命令以切換至資源管理員模式，如下所示。

        azure config mode arm

    此為上述命令的預期輸出內容：

        info:    New mode is arm

3. 從瀏覽器中瀏覽至 **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json**, 、 json 檔案的內容複製並貼到新檔案中，您的電腦。 此案例中，您會將複製以下的值為檔案 **c:\lb\azuredeploy.parameters.json**。
4. 執行 **azure 群組部署建立** 指令程式來部署新的負載平衡器使用的範本和參數檔案下載並修改上面。 輸出後顯示的清單可說明所使用的參數。

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)


