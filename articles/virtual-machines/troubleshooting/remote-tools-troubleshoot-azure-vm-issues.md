---
title: Távoli eszközök használata az Azure-beli virtuális gépekkel kapcsolatos problémák elhárításához | Microsoft Docs
description: Ismerje meg a PsExec, a PowerShell-szkripteket és az egyéb távoli eszközöket, amelyekkel a távoli Azure-beli virtuálisgép-problémák az RDP használata nélkül is elhárítható.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: d29b2b7c2b9194f20afe4c74d117847f0e343b12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422610"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Távoli eszközök használata az Azure-beli virtuális gépekkel kapcsolatos problémák elhárításához

Az Azure-beli virtuális gépek (VM) hibáinak elhárítása során a RDP protokoll (RDP) használata helyett a cikkben tárgyalt távoli eszközök használatával csatlakozhat a virtuális géphez.

## <a name="serial-console"></a>Soros konzol

Az [azure Virtual Machines soros konzoljának](serial-console-windows.md) használatával futtathat parancsokat a távoli Azure virtuális gépen.

## <a name="remote-cmd"></a>Távoli CMD

Töltse le a [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Kapcsolódjon a virtuális géphez a következő parancs futtatásával:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* A parancsot egy olyan számítógépen kell futtatni, amely ugyanabban a virtuális hálózatban található.
>* A DIP vagy a HostName használható a számítógép \<> lecserélésére.
>* A-s paraméter gondoskodik arról, hogy a parancs a rendszerfiók (rendszergazdai engedély) használatával legyen meghívva.
>* A PsExec a 135-es és 445-es TCP-portot használja. Ennek eredményeképpen a két portot meg kell nyitni a tűzfalon.

## <a name="run-command"></a>Parancs futtatása

További információ arról, hogyan használható a futtatási parancs a parancsfájlok futtatásához a virtuális gépen: [PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal](../windows/run-command.md).

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az egyéni szkriptek bővítményének használatával egyéni parancsfájlokat futtathat a cél virtuális gépen. A szolgáltatás használatához a következő feltételeknek kell teljesülniük:

* A virtuális gép rendelkezik kapcsolattal.
* Az Azure-beli virtuálisgép-ügynök telepítve van, és a várt módon működik a virtuális gépen.
* A bővítmény korábban nem lett telepítve a virtuális gépen.
 
  A bővítmény csak az első használat alkalmával adja meg a parancsfájlt. Ha később ezt a szolgáltatást használja, a bővítmény felismeri, hogy már használatban van, és nem tölti fel az új parancsfájlt.

Töltse fel a szkriptet egy Storage-fiókba, és állítson elő saját tárolót. Ezután futtassa a következő parancsfájlt Azure PowerShell egy olyan számítógépen, amely kapcsolódik a virtuális géphez.

### <a name="for-classic-deployment-model-vms"></a>Klasszikus üzembe helyezési modell virtuális gépek esetén

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Azure Resource Manager virtuális gépek esetén

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Távoli PowerShell

>[!NOTE]
>Az 5986-as (HTTPS) TCP-portnak nyitva kell lennie, hogy használhassa ezt a lehetőséget.
>
>Azure Resource Manager virtuális gépek esetében meg kell nyitnia a 5986-es portot a hálózati biztonsági csoporton (NSG). További információ: biztonsági csoportok. 
>
>RDFE virtuális gépek esetén olyan végponttal kell rendelkeznie, amely rendelkezik privát porttal (5986) és egy nyilvános porttal. Ezután meg kell nyitnia a nyilvános elérésű portot is a NSG.

### <a name="set-up-the-client-computer"></a>Az ügyfélszámítógép beállítása

Ahhoz, hogy a PowerShell használatával távolról csatlakozhasson a virtuális géphez, először be kell állítania az ügyfélszámítógépet, hogy engedélyezze a kapcsolatot. Ehhez a következő parancs futtatásával adja hozzá a virtuális gépet a PowerShell megbízható gazdagépek listájához.

Egy virtuális gép hozzáadása a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Több virtuális gép hozzáadása a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Az összes számítógép hozzáadása a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>RemotePS engedélyezése a virtuális gépen

A klasszikus üzemi modellel létrehozott virtuális gépek esetében az egyéni szkriptek bővítmény használatával futtassa a következő parancsfájlt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Azure Resource Manager virtuális gépek esetében a portálon futtatott parancsok futtatásával futtassa a EnableRemotePS parancsfájlt:

![Parancs futtatása](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Futtassa a következő parancsot az ügyfélszámítógép helye alapján:

* A virtuális hálózaton vagy az üzemelő példányon kívül

  * A klasszikus üzemi modellel létrehozott virtuális gépek esetében futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Azure Resource Manager virtuális gép esetében először adjon hozzá egy DNS-nevet a nyilvános IP-címhez. A részletes lépésekért lásd: [teljes tartománynév létrehozása a Azure Portal a Windows rendszerű virtuális](../windows/portal-create-fqdn.md)gépekhez. Ezután futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Futtassa a következő parancsot a virtuális hálózaton vagy az üzemelő példányon belül:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>A SkipCaCheck jelző beállítása megkerüli a tanúsítványnak a virtuális gépre való importálásának követelményét a munkamenet indításakor.

A Meghívási parancs parancsmag használatával távolról is futtathat parancsfájlokat a virtuális gépen.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Távoli beállításjegyzék

>[!NOTE]
>A beállítás használatához a 135-es vagy a 445-es TCP-portnak nyitva kell lennie.
>
>Azure Resource Manager virtuális gépek esetében meg kell nyitnia a 5986-as portot a NSG. További információ: biztonsági csoportok. 
>
>RDFE virtuális gépek esetén olyan végponttal kell rendelkeznie, amely rendelkezik 5986-es privát porttal és egy nyilvános porttal. Azt is meg kell nyitnia, hogy a nyilvános port a NSG.

1. Az ugyanazon a virtuális hálózaton lévő másik virtuális gépről nyissa meg a Beállításszerkesztőt (Regedit. exe).

2. Válassza a **fájl** > **összekötése hálózati beállításjegyzék**lehetőséget.

   ![Rendszerleíróadatbázis-szerkesztő](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Keresse meg a cél virtuális gépet az **állomásnév** vagy a **dinamikus IP-cím** alapján (lehetőleg), és írja be azt az **adja meg a kijelölendő objektum nevét** mezőbe.

   ![Adja meg a kijelölendő objektum nevét](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Adja meg a cél virtuális gép hitelesítő adatait.

5. Végezze el a beállításjegyzék szükséges módosításait.

## <a name="remote-services-console"></a>Távoli szolgáltatások konzolja

>[!NOTE]
>A beállítás használatához a 135-es vagy a 445-es TCP-portnak nyitva kell lennie.
>
>Azure Resource Manager virtuális gépek esetében meg kell nyitnia a 5986-as portot a NSG. További információ: biztonsági csoportok. 
>
>RDFE virtuális gépek esetén olyan végponttal kell rendelkeznie, amely rendelkezik 5986-es privát porttal és egy nyilvános porttal. Azt is meg kell nyitnia, hogy a nyilvános port a NSG.

1. Egy ugyanazon a virtuális hálózaton lévő másik virtuális gépről nyissa meg a **Services. msc**egy példányát.

2. Kattintson a jobb gombbal a **szolgáltatások (helyi)** elemre.

3. Válassza **a Kapcsolódás másik számítógéphez**lehetőséget.

   ![Távoli szolgáltatás](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Adja meg a cél virtuális gép dinamikus IP-címét.

   ![Bemeneti dinamikus IP-cím](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Végezze el a szükséges módosításokat a szolgáltatásokban.

## <a name="next-steps"></a>További lépések

- Az ENTER-PSSession parancsmaggal kapcsolatos további információkért lásd: [ENTER-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- További információ a Windowshoz készült egyéni parancsfájl-bővítményről a klasszikus üzemi modell használatával: [egyéni parancsfájl-bővítmény a Windowshoz](../extensions/custom-script-classic.md).
- A PsExec a [PsTools csomag](https://download.sysinternals.com/files/PSTools.zip)része.
- A PSTools csomaggal kapcsolatos további információkért lásd: [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


