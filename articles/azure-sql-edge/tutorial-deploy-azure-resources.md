---
title: Erőforrások beállítása egy ML-modell üzembe helyezéséhez az Azure SQL Edge-ben
description: Az alábbi, a három részből álló Azure SQL Edge-oktatóanyag első részében a vasérc-szennyeződések előrejelzéséhez telepíteni kell az előfeltételként szükséges szoftvereket, és be kell állítania egy gépi tanulási modell üzembe helyezéséhez szükséges Azure-erőforrásokat az Azure SQL Edge-ben.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c74e402fa1faa883b1e456f11a8d9d7b1e750d27
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772828"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>Szoftver telepítése és erőforrások beállítása az oktatóanyaghoz

Ebben a háromrészes oktatóanyagban egy gépi tanulási modellt hoz létre, amellyel előre jelezheti az Iron Ore-szennyeződéseket a szilícium százalékában, majd üzembe helyezheti a modellt az Azure SQL Edge-ben. Az első részben telepítenie kell a szükséges szoftvereket, és telepítenie kell az Azure-erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. Telepítse a [Python-3.6.8](https://www.python.org/downloads/release/python-368/).
      * A Windows x86-x64 végrehajtható telepítő használata
      * Adja hozzá `python.exe` a következőt a PATH környezeti változóhoz: letöltések/). A letöltést a "Tools for Visual Studio 2019" alatt találja.
3. Telepítse [a Microsoft ODBC-illesztőt (17) a SQL Serverhoz](https://www.microsoft.com/download/details.aspx?id=56567).
4. [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/) telepítése
5. Nyissa meg Azure Data Studio és konfigurálja a Pythont jegyzetfüzetekhez.Részletekért lásd: [a Python konfigurálása jegyzetfüzetekhez](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks). Ez a lépés több percet is igénybe vehet.
6. Telepítse az [Azure CLI](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020)legújabb verzióját. A következő szkriptek megkövetelik, hogy az AZ PowerShell legyen a legújabb verzió (3.5.0, Feb 2020).

## <a name="deploy-azure-resources-using-powershell-script"></a>Azure-erőforrások üzembe helyezése PowerShell-parancsfájl használatával

Az Azure SQL Edge-oktatóanyaghoz szükséges Azure-erőforrások üzembe helyezése. Ezek a PowerShell-parancsfájl vagy a Azure Portal használatával is üzembe helyezhetők. Ez az oktatóanyag egy PowerShell-szkriptet használ.

1. Importálja a PowerShell-szkript futtatásához szükséges modulokat ebben az oktatóanyagban.

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. Deklarálja a PowerShell-parancsfájlhoz szükséges változókat.

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. Deklarálja a változók többi részét.

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. Az eszközök létrehozásának megkezdéséhez jelentkezzen be az Azure-ba.

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. Állítsa be az Azure-előfizetés AZONOSÍTÓját.

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. Ha még nem létezik, hozza létre az erőforráscsoportot.

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. Hozza létre a Storage-fiók és a Storage-fiók tárolóját az erőforráscsoporthoz.

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. Töltse fel az adatbázisfájlt `dacpac` a Storage-fiókba, és állítson be egy sas URL-címet a blobhoz. **Jegyezze fel az adatbázis-blob SAS URL-címét `dacpac` .**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. Hozzon létre egy Azure Container registryt ezen az erőforráscsoporton belül.

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. Küldje le a ARM/AMD Docker-rendszerképeket a tároló-beállításjegyzékbe.

    ```powershell
    $containerRegistryCredentials = Get-AzContainerRegistryCredential -ResourceGroupName $ResourceGroup -Name $containerRegistryName
    
    $amddockerimageFile = Read-Host "Please Enter the location to the amd docker tar file:"
    $armdockerimageFile = Read-Host "Please Enter the location to the arm docker tar file:"
    $amddockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":amd64"
    $armdockertag = $containerRegistry.LoginServer + "/silicaprediction" + ":arm64"
    
    docker login $containerRegistry.LoginServer --username $containerRegistryCredentials.Username --password $containerRegistryCredentials.Password
    
    docker import $amddockerimageFile $amddockertag
    docker push $amddockertag
    
    docker import $armdockerimageFile $armdockertag
    docker push $armdockertag
    ```

11. Hozza létre a hálózati biztonsági csoportot az erőforráscsoporthoz.

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

12. SQL Edge-sel rendelkező Azure-beli virtuális gép létrehozása. Ez a virtuális gép peremhálózati eszközként fog működni.

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

13. Hozzon létre egy IoT hubot az erőforráscsoporthoz.

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

14. Vegyen fel egy peremhálózati eszközt az IoT hubhoz. Ez a lépés csak az eszköz digitális identitását hozza létre.

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

15. Az eszköz elsődleges kapcsolatainak karakterláncának beolvasása. Erre később szükség lesz a virtuális gép számára. A következő parancs az Azure CLI-t használja az üzembe helyezésekhez.

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

16. Frissítse a IoT Edge konfigurációs fájlban lévő kapcsolatok karakterláncát a peremhálózati eszközön. Az alábbi parancsok az Azure CLI-t használják az üzembe helyezésekhez.

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

17. Hozzon létre egy Azure Machine Learning munkaterületet az erőforráscsoporthoz.

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```

## <a name="next-steps"></a>Következő lépések

* [IoT Edge modulok és kapcsolatok beállítása](tutorial-set-up-iot-edge-modules.md)
