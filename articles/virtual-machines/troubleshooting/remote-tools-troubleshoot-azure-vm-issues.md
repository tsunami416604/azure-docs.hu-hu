---
title: Távoli eszközök használata az Azure virtuális gépekkel kapcsolatos problémák elhárításához | Microsoft dokumentumok
description: Ismerje meg a PsExec, a PowerShell-parancsfájlok és más távoli eszközök segítségével a távoli Azure virtuális gép kel kapcsolatos problémák rdp használata nélkül.
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
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422610"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Távoli eszközök használata az Azure virtuális gépekkel kapcsolatos problémák elhárításához

Ha egy Azure virtuális gépen (VM) kapcsolatos problémákelhárításakor a távoli, a jelen cikkben tárgyalt eszközök használatával csatlakozhat a virtuális géphez a Távoli asztali protokoll (RDP) használata helyett.

## <a name="serial-console"></a>Soros konzol

Az [Azure virtuális gépek hez egy soros konzol](serial-console-windows.md) használatával parancsokat futtatanak a távoli Azure virtuális gépen.

## <a name="remote-cmd"></a>Távoli CMD

Letöltés [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Csatlakozzon a virtuális géphez a következő parancs futtatásával:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* A parancsot olyan számítógépen kell futtatni, amely ugyanabban a virtuális hálózatban található.
>* A DIP vagy a HostName a számítógép> cseréjére \<használható.
>* A -s paraméter gondoskodik arról, hogy a parancs meghívása a Rendszerfiók (rendszergazdai engedély) használatával történik.
>* A PsExec a 135-ös és 445-ös TCP-portokat használja. Ennek eredményeképpen a két portnak nyitva kell lennie a tűzfalon.

## <a name="run-command"></a>Futtatás parancs

Ha többet szeretne tudni arról, hogy miként futtathatja a parancsfuttatási szolgáltatást a virtuális gép parancsfájljainak futtatásához, olvassa el a [PowerShell-parancsfájlok futtatása a Windows virtuális gépben a futtatás paranccsal című témakört.](../windows/run-command.md)

## <a name="custom-script-extension"></a>Egyéni szkriptbővítmény

Az egyéni parancsfájl-bővítmény szolgáltatás sal egyéni parancsfájlfuttatásához a cél virtuális gép. A szolgáltatás használatához a következő feltételeknek kell teljesülniük:

* A virtuális gép rendelkezik kapcsolattal.
* Az Azure virtuálisgép-ügynök telepítve van, és a várt módon működik a virtuális gépen.
* A bővítmény korábban nem volt telepítve a virtuális gépre.
 
  A bővítmény csak az első használatkor adja be a parancsfájlt. Ha később használja ezt a funkciót, a bővítmény felismeri, hogy már használták, és nem tölti fel az új parancsfájlt.

Töltse fel a parancsfájlt egy tárfiókba, és hozzon létre saját tárolót. Ezután futtassa a következő parancsfájlt az Azure PowerShellben egy olyan számítógépen, amely kapcsolattal rendelkezik a virtuális géphez.

### <a name="for-classic-deployment-model-vms"></a>Klasszikus üzembe helyezési modell virtuális gépekhez

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

### <a name="for-azure-resource-manager-vms"></a>Azure Resource Manager virtuális gépekhez

 

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
>Az 5986-os (HTTPS) TCP-portnak nyitva kell lennie, hogy használhassa ezt a beállítást.
>
>Az Azure Resource Manager virtuális gépek, meg kell nyitnia az 5986-os port a hálózati biztonsági csoport (NSG). További információ: Biztonsági csoportok. 
>
>RdFE-virtuális gépek esetén rendelkeznie kell egy olyan végponttal, amely rendelkezik egy privát porttal (5986) és egy nyilvános porttal. Ezután meg kell nyitnia azt a nyilvános portot az NSG-n.

### <a name="set-up-the-client-computer"></a>Az ügyfélszámítógép beállítása

A PowerShell segítségével távolról csatlakozhat a virtuális géphez, először be kell állítania az ügyfélszámítógépet a kapcsolat engedélyezéséhez. Ehhez adja hozzá a virtuális gép a PowerShell megbízható állomások listáját a következő parancs futtatásával, szükség szerint.

Egy virtuális gép hozzáadása a megbízható állomások listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Több virtuális gép hozzáadása a megbízható állomások listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Az összes számítógép felvétele a megbízható állomások listájára:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>RemotePS engedélyezése a virtuális gépen

A klasszikus központi telepítési modell használatával létrehozott virtuális gépek esetén az egyéni parancsfájl-bővítmény segítségével futtassa a következő parancsfájlt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Az Azure Resource Manager virtuális gépek esetén a portálról származó futtatási parancsok használatával futtassa az EnableRemotePS-parancsfájlt:

![Futtatás parancs](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Futtassa a következő parancsot az ügyfélszámítógép helye alapján:

* A virtuális hálózaton vagy a központi telepítésen kívül

  * A klasszikus központi telepítési modell használatával létrehozott virtuális gép esetén futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Az Azure Resource Manager virtuális gép, először adjon hozzá egy DNS-nevet a nyilvános IP-címhez. Részletes lépésekért [olvassa el A teljesen minősített tartománynév létrehozása az Azure Portalon egy Windows virtuális géphez című témakört.](../windows/portal-create-fqdn.md) Ezután futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* A virtuális hálózaton vagy a központi telepítésen belül futtassa a következő parancsot:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>A SkipCaCheck jelző beállítása megkerüli a tanúsítvány virtuális gépre való importálásának követelményét a munkamenet indításakor.

Az Invoke-Command parancsmag használatával távoli módon futtathat egy parancsfájlt a virtuális gépen.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Távoli beállításjegyzék

>[!NOTE]
>A 135-ös vagy 445-ös TCP-portnak nyitva kell lennie ahhoz, hogy használni lehessen ezt a beállítást.
>
>Az Azure Resource Manager virtuális gépek, meg kell nyitnia az 5986-os port ot az NSG-n. További információ: Biztonsági csoportok. 
>
>RdFE-virtuális gépek esetén olyan végpontnak kell lennie, amely 5986-os magánporttal és nyilvános porttal rendelkezik. Azt is meg kell nyitnia, hogy a nyilvános oldali port az NSG.You have to open that public-facing port on the NSG.

1. Egy másik virtuális gépről ugyanazon a virtuális hálózaton nyissa meg a rendszerleíró adatbázis szerkesztőjét (regedit.exe).

2. Válassza **a Fájlcsatlakozási** > **hálózati beállításjegyzék lehetőséget.**

   ![Rendszerleíró adatbázis szerkesztője](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Keresse meg a cél virtuális gép **állomásnév** vagy **dinamikus IP** (előnyösebb) írja be az Adja meg az objektum nevét **a kijelöléshez** mezőbe.

   ![Írja be a kijelölendő objektum nevét](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Adja meg a cél virtuális gép hitelesítő adatait.

5. Hajtsa végre a szükséges rendszerleíró adatbázis-módosításokat.

## <a name="remote-services-console"></a>Távoli szolgáltatások konzolja

>[!NOTE]
>A 135-ös vagy 445-ös TCP-portnak nyitva kell lennie ahhoz, hogy használni lehessen ezt a beállítást.
>
>Az Azure Resource Manager virtuális gépek, meg kell nyitnia az 5986-os port ot az NSG-n. További információ: Biztonsági csoportok. 
>
>RdFE-virtuális gépek esetén olyan végpontnak kell lennie, amely 5986-os magánporttal és nyilvános porttal rendelkezik. Azt is meg kell nyitnia, hogy a nyilvános oldali port az NSG.You have to open that public-facing port on the NSG.

1. Egy másik virtuális gépről ugyanazon a virtuális hálózaton nyissa meg a **Services.msc egy példányát.**

2. Kattintson a jobb gombbal **a Szolgáltatások (helyi)** elemre.

3. Válassza **a Csatlakozás másik számítógépre**lehetőséget.

   ![Távoli szolgáltatás](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Adja meg a cél virtuális gép dinamikus IP-címét.

   ![Bemeneti dinamikus IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. A szükséges módosításokat a szolgáltatásokon hajtsa végre.

## <a name="next-steps"></a>További lépések

- Az Enter-PSSession parancsmagról további információt az [Enter-PSSession című](https://technet.microsoft.com/library/hh849707.aspx)témakörben talál.
- A klasszikus telepítési modellt használó Windows egyéni parancsfájl-bővítményéről további információt a [Windows egyéni parancsfájl-bővítménye](../extensions/custom-script-classic.md)című témakörben talál.
- PsExec része a [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).
- A PSTools Suite programcsomagról további információt a [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


