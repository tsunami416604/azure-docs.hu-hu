<properties 
   pageTitle="使用資源管理員中的範本建立內部負載平衡器| Microsoft Azure"
   description="了解如何使用資源管理員中的範本建立內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma" />


# 開始使用範本建立內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](load-balancer-get-started-ilb-classic-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## 使用按一下即部署來部署範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 若要部署使用按一下要部署此範本，請 [連結] ((https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer)，按一下 [ **部署至 Azure**, 、 取代預設參數值，如有必要，並依照入口網站中的指示。

## 使用 PowerShell 部署範本

若要使用 PowerShell 部署您下載的範本，請依照下列步驟執行。

1. 如果您從未使用 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 並遵循指示以登入 Azure，並選取您的訂閱結束。
2. 執行 **Switch-AzureMode** Cmdlet，以切換為資源管理員模式，如下所示。

        Switch-AzureMode AzureResourceManager

    此為上述命令的預期輸出內容：

        WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING] Switch-AzureMode Cmdlet 即將被汰除。 屆時將重新命名所有的資源管理員 Cmdlet。

3.下載 [參數](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json) 到本機磁碟的檔案。<BR>
4. 編輯檔案並加以儲存。<BR>
5. 執行 **New-AzureResourceGroup** Cmdlet，以使用範本建立資源群組。

        New-AzureResourceGroup -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'




## 使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請依照下列步驟執行。

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

        azure config mode arm

    此為上述命令的預期輸出內容：

        info:    New mode is arm

3. 開啟 [參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json), 、 選取它的內容，並將它儲存到您的電腦中的檔案。 在此範例中，我們將參數檔案儲存為 *parameters.json*。

4. 執行 **azure group deployment create** Cmdlet，以使用先前所下載及修改的範本和參數檔案部署新的 VNet。 輸出後顯示的清單可說明所使用的參數。

        azure group create -n TestRG -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json -e parameters.json




## 後續步驟

[設定負載平衡器分配模式使用來源 IP 同質性](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)








