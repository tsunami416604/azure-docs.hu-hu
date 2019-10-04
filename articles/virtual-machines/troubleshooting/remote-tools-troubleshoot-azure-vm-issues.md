---
title: Távoli eszközök használata az Azure-beli virtuális gépekkel kapcsolatos problémák elhárításához | Microsoft Docs
description: ''
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
ms.openlocfilehash: 70d777fe2e939c1871bc318eed439214fd3e3f60
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155736"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Távoli eszközök használata az Azure-beli virtuális gépekkel kapcsolatos problémák elhárításához

Ha egy Azure-beli virtuális gép (VM) hibáit elhárítja, akkor a RDP protokoll (RDP) használata helyett a cikkben tárgyalt távoli eszközök használatával csatlakozhat a virtuális géphez.

## <a name="serial-console"></a>Soros konzol

A [virtuális gép soros konzoljának](serial-console-windows.md) használatával parancsokat futtathat a távoli Azure-beli virtuális gépen.

## <a name="remote-cmd"></a>Távoli CMD

Töltse le a [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Kapcsolódjon a virtuális géphez a következő parancs futtatásával:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* A parancsot egy olyan számítógépen kell futtatni, amely ugyanabban a VNET található.
>* A DIP vagy a hostname használható a számítógép \<> lecserélésére.
>* A-s paraméter gondoskodik arról, hogy a parancs a rendszerfiók (rendszergazdai engedély) használatával legyen meghívva.
>* A PsExec a 135-es és 445-es TCP-portot használja. Ezért a két portot meg kell nyitni a tűzfalon.

## <a name="run-commands"></a>Parancsok futtatása

A parancsfájloknak a virtuális gépen való futtatásával kapcsolatos további információkért lásd: [PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal](../windows/run-command.md) .

## <a name="customer-script-extension"></a>Customer script bővítmény

Az egyéni szkriptek bővítményének használatával egyéni parancsfájlokat futtathat a cél virtuális gépen. A szolgáltatás használatához a következő feltételeknek kell teljesülniük:

* A virtuális gép rendelkezik kapcsolattal.

* Az Azure-ügynök telepítve van, és a várt módon működik a virtuális gépen.

* A bővítmény korábban nem lett telepítve a virtuális gépre.
 
  A bővítmény csak az első használat során adja meg a parancsfájlt. Ha később ezt a szolgáltatást használja, a bővítmény felismeri, hogy már használatban van, és nem tölti fel az új parancsfájlt.

Fel kell töltenie a szkriptet egy Storage-fiókba, és saját tárolót kell előállítania. Ezután futtassa a következő parancsfájlt Azure PowerShell egy olyan számítógépen, amely kapcsolódik a virtuális géphez.

### <a name="for-v1-vms"></a>V1-es virtuális gépek esetén

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>V2-es virtuális gépek esetén

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Távoli PowerShell

>[!Note]
>Az 5986-as (HTTPS) TCP-portnak nyitva kell lennie, hogy használhassa ezt a lehetőséget.
>
>ARM virtuális gépek esetén a hálózati biztonsági csoport (NSG) 5986-es portját kell megnyitnia. További információ: biztonsági csoportok. 
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

A klasszikus virtuális gépek esetében az egyéni szkriptek bővítmény használatával futtassa a következő parancsfájlt:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

ARM virtuális gépek esetén a portálon futtassa a futtatási parancsokat a EnableRemotePS parancsfájl futtatásához:

![Parancs futtatása](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Futtassa az alábbi parancsot az ügyfélszámítógép helyétől függően:

* A VNET vagy az üzemelő példányon kívül

  * Klasszikus virtuális gép esetén futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * ARM virtuális gép esetén először adjon hozzá egy DNS-nevet a nyilvános IP-címhez. A részletes lépésekért lásd: [teljes tartománynév létrehozása a Azure Portal a Windows rendszerű virtuális](../windows/portal-create-fqdn.md)gépekhez. Ezután futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Futtassa a következő parancsot a VNET vagy az üzemelő példányon belül:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>A SkipCaCheck jelző beállítása megkerüli a tanúsítványnak a virtuális gépre való importálásának követelményét a munkamenet indításakor.

A Meghívási parancs parancsmag használatával távolról is futtathat parancsfájlokat a virtuális gépen:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Távoli beállításjegyzék

>[!Note]
>A beállítás használatához a 135-es vagy a 445-es TCP-portnak nyitva kell lennie.
>
>ARM-alapú virtuális gépek esetén az 5986-es portot kell megnyitnia a NSG. További információ: biztonsági csoportok. 
>
>RDFE virtuális gépek esetén olyan végponttal kell rendelkeznie, amely rendelkezik 5986-es privát porttal és egy nyilvános porttal. Meg kell nyitnia a nyilvános elérésű portot is a NSG.

1. Az azonos VNET lévő másik virtuális gépről nyissa meg a Beállításszerkesztőt (Regedit. exe).

2. Válassza a **fájl** >**összekötése hálózati beállításjegyzék**lehetőséget.

   ![Távoli beállítás](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Keresse meg a cél virtuális gépet az **állomásnév** vagy a **dinamikus IP-cím** (lehetőleg) megadásával az "adja meg a kijelölendő objektum nevét" mezőben.

   ![Távoli beállítás](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Adja meg a cél virtuális gép hitelesítő adatait.

5. Végezze el a beállításjegyzék szükséges módosításait.

## <a name="remote-services-console"></a>Távoli szolgáltatások konzolja

>[!Note]
>A beállítás használatához a 135-es vagy a 445-es TCP-portnak nyitva kell lennie.
>
>ARM-alapú virtuális gépek esetén az 5986-es portot kell megnyitnia a NSG. További információ: biztonsági csoportok. 
>
>RDFE virtuális gépek esetén olyan végponttal kell rendelkeznie, amely rendelkezik 5986-es privát porttal és egy nyilvános porttal. Ezután meg kell nyitnia a nyilvános elérésű portot is a NSG.

1. Egy másik virtuális gépről ugyanazon a VNET nyissa meg a **Services. msc**egy példányát.

2. Kattintson a jobb gombbal a **szolgáltatások (helyi)** elemre.

3. Válassza **a Kapcsolódás másik számítógéphez**lehetőséget.

   ![Távoli szolgáltatás](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Adja meg a cél virtuális gép dinamikus IP-címét.

   ![Bemeneti DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Végezze el a szükséges módosításokat a szolgáltatásokban.

## <a name="next-steps"></a>További lépések

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Egyéni parancsfájl-bővítmény a Windows rendszerhez a klasszikus üzemi modell használatával](../extensions/custom-script-classic.md)

A PsExec a [PsTools csomag](https://download.sysinternals.com/files/PSTools.zip)része.

További információ az PSTools Suite-ról: [PsTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


