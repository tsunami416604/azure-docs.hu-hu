<properties 
   pageTitle="編譯 Azure 自動化 DSC 中的組態 | Microsoft Azure" 
   description="「期望的狀態設定 (DSC)」組態兩種編譯方式的概觀：在 Azure 入口網站中，以及使用 Windows PowerShell。 " 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="10/15/2015"
   ms.author="coreyp"/>
   
#編譯 Azure 自動化 DSC 中的組態#

使用 Azure 自動化時有兩種方式可以編譯「期望的狀態設定 (DSC)」組態：在 Azure 入口網站中，以及使用 Windows PowerShell。 下表將協助您根據方法的特性判斷何時應使用哪種方法： 

###Azure Preview 入口網站###
- 互動式使用者介面的最簡單方法
- 提供簡單參數值的表單
- 輕鬆追蹤工作狀態
- 使用 Azure 登入資訊驗證存取

###Windows PowerShell###
- 使用 Windows PowerShell Cmdlet 從命令列呼叫
- 可以包含在具有多個步驟的自動化解決方案中
- 提供簡單和複雜的參數值
- 追蹤工作狀態
- 支援 PowerShell Cmdlet 所需的用戶端
- 傳遞 ConfigurationData
- 編譯使用認證的組態

在您決定編譯方法後，您可以依照下列個別的程序開始編譯。

##使用 Azure Preview 入口網站編譯 DSC 組態##

1.  從您的自動化帳戶，按一下 [ **組態**。
2.  按一下組態以開啟其刀鋒視窗。
3.  按一下 [ **編譯**。
4.  如果組態沒有參數，系統會提示您確認是否要加以編譯。 如果組態中有參數， **編譯組態** 會開啟分頁，因此您可以提供參數值。 請參閱 <a href="#basic-parameters">**基本參數**</a> 下面章節以取得參數的詳細資料。
5.   **編譯工作** ] 分頁隨即開啟，讓您可以追蹤編譯作業的狀態，以及節點組態 (MOF 組態文件)，則要放在 Azure 自動化 DSC 提取伺服器上造成問題。

##使用 Windows PowerShell 編譯 DSC 組態##

您可以使用 [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) 開始使用 Windows PowerShell 進行編譯。 下列範例程式碼會啟動編譯 DSC 組態稱為 **SampleConfig**。

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` 會傳回可用來追蹤工作狀態的編譯工作物件。 然後，您可以使用此編譯作業物件搭配 [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) 判斷編譯作業的狀態和 [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) 若要檢視其資料流 (輸出)。 下列範例程式碼會啟動編譯 **SampleConfig** 組態中，等待它完成，並接著會顯示其資料流。
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)           
    {$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
        Start-Sleep -Seconds 3
    
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##基本參數##

DSC 組態中的參數宣告 (包括參數類型和屬性) 的運作方式與 Azure 自動化 Runbook 中相同。 請參閱 [Azure 自動化中啟動 runbook](automation-starting-a-runbook.md) 若要深入了解 runbook 參數。

下列範例會使用兩個參數呼叫 **FeatureName** 和 **IsPresent**, ，以決定中的屬性值 **ParametersExample.sample** 節點組態，在編譯期間產生。

    Configuration ParametersExample {
        param(
        [Parameter(Mandatory=$true)]
    
        [string] $FeatureName,
    
        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    
    )
    
    $EnsureString = "Present"
    if($IsPresent -eq $false) {
        $EnsureString = "Absent"
    
    }
    
    Node "sample" {
    
        WindowsFeature ($FeatureName + "Feature") {
            Ensure = $EnsureString
            Name = $FeatureName
            }
        }
    }

您可以在 Azure 自動化 DSC 入口網站或 Azure PowerShell 中編譯使用基本參數的 DSC 組態：

###入口網站###

在入口網站中，您可以輸入參數值之後按一下 **編譯**。

![替代文字](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

PowerShell 需要參數 [雜湊表](http://technet.microsoft.com/library/hh847780.aspx) 其中索引鍵符合參數名稱，且值等於參數值。

    $Parameters = @{
            "FeatureName" = "Web-Server"
            "IsPresent" = $False
    }
    
    
    Start-AzureRMAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

如需傳遞 PSCredentials 做為參數的資訊，請參閱 <a href="#credential-assets">**認證資產**</a> 下方。

##ConfigurationData##

**ConfigurationData** 可讓您分隔結構時使用 PowerShell DSC 任何環境特定組態的組態。 請參閱 [「 什麼 」 分離 PowerShell DSC 中的"Where"](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) 若要深入了解 **ConfigurationData**。

>[AZURE.NOTE] 您可以使用 **ConfigurationData** 編譯中使用 Azure PowerShell 的 Azure 自動化 DSC，但不是在 Azure 入口網站時。

下列範例 DSC 組態使用 **ConfigurationData** 透過 **$ConfigurationData** 和 **$AllNodes** 關鍵字。 您也需要 [**xWebAdministration** 模組](https://www.powershellgallery.com/packages/xWebAdministration/) 此範例中:

     Configuration ConfigurationDataSample {
        Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    
        Write-Verbose $ConfigurationData.NonNodeData.SomeMessage 
    
        Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
        {
    
            xWebsite Site
            {
    
                Name = $Node.SiteName
                PhysicalPath = $Node.SiteContents
                Ensure   = "Present"
            }
    
        }
 
    }

您可以編譯 DSC 組態上面使用 PowerShell，在 Azure 自動化 DSC 提取伺服器會將兩個節點組態: **ConfigurationDataSample.MyVM1** 和 **ConfigurationDataSample.MyVM3**:

    $ConfigData = @{
        AllNodes = @(
            @{
                NodeName = "MyVM1"
                Role = "WebServer"
            },
            @{
                NodeName = "MyVM2"
                Role = "SQLServer"
            },
            @{
                NodeName = "MyVM3"
                Role = "WebServer"
    
            }
    
        )
    
        NonNodeData = @{
            SomeMessage = "I love Azure Automation DSC!"
    
        }
    
    } 
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData


##Assets##

Azure 自動化 DSC 組態和 Runbook 中的資產參考是相同的。 如需詳細資訊，請參閱下列主題：

- [憑證](automation-certificates.md)
- [連線](automation-connections.md)
- [認證](automation-credentials.md)
- [變數](automation-variables.md)

###認證資產###
雖然在 Azure 自動化 DSC 組態可以參考使用的認證資產 **Get-automationpscredential**, ，認證資產，也可以傳遞中透過參數，如有需要。 如果設定採用參數的 **PSCredential** 型別，則您需要將 Azure 自動化認證資產的字串名稱傳遞為該參數的值，而不是 PSCredential 物件。 具有該名稱的 Azure 自動化認證資產會在背景中被擷取，並傳遞至組態。

要在節點組態 (MOF 組態文件) 中保持認證的安全性，需要在節點組態 MOF 檔案中為認證加密。 Azure 自動化會進一步執行此步驟，而加密整個 MOF 檔案。 不過，目前您必須告知 PowerShell DSC 在節點組態 MOF 產生期間以純文字形式輸出認證是可行的，因為 PowerShell DSC 並不知道在透過編譯工作產生 MOF 檔案之後 Azure 自動化會加密整個檔案。

您可以分辨沒關係認證，以輸出以純文字產生的節點組態 Mof 的 PowerShell DSC 使用 <a href="#configurationdata">**ConfigurationData**</a>。 您應該傳遞 `PSDscAllowPlainTextPassword = $true` 透過 **ConfigurationData** DSC 組態中會出現，並使用認證的每個節點區塊的名稱。

下列範例說明使用自動化認證資產的 DSC 組態。

    Configuration CredentialSample {
    
       $Cred = Get-AutomationPSCredential -Name "SomeCredentialAsset"
    
        Node $AllNodes.NodeName { 
    
            File ExampleFile { 
                SourcePath = "\\Server\share\path\file.ext" 
                DestinationPath = "C:\destinationPath" 
                Credential = $Cred 
    
            }
    
        }
    
    }

您可以編譯 DSC 組態上面使用 PowerShell，在 Azure 自動化 DSC 提取伺服器會將兩個節點組態:  **CredentialSample.MyVM1** 和 **CredentialSample.MyVM2**。


    $ConfigData = @{
        AllNodes = @(
             @{
                NodeName = "*"
                PSDscAllowPlainTextPassword = $True
            },
    
            @{
                NodeName = "MyVM1"
            },
    
            @{
                NodeName = "MyVM2"
            }
        )
    } 
    
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData

