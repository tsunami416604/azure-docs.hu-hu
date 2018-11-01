---
title: Táveszközök használata Azure virtuális gép hibáinak elhárítása |} A Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: d9bb13b24a16cc38f738d9a654789d4410225c09
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634052"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Távoli eszközök használata az Azure virtuális gép hibáinak elhárítása

Egy Azure virtuális gépen (VM) problémák elhárításakor csatlakozhat a virtuális Géphez a távoli asztal protokoll (RDP) használata helyett ebben a cikkben ismertetett eszközök segítségével.

## <a name="serial-console"></a>Soros konzol

Használat [virtuális gépek soros konzolja](serial-console-windows.md) parancsokat futtatni a távoli Azure virtuális gépen.

## <a name="remote-cmd"></a>Távoli CMD

Töltse le [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Csatlakozzon a virtuális Gépet a következő parancs futtatásával:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* A parancs, amely ugyanabban a vnetben számítógépen kell futtatni.
>* Dedikált IP-CÍMMEL vagy állomásnév helyett használható <computer>.
>* Az -s paraméter gondoskodik arról, hogy a parancs hív (rendszergazdának) rendszer fiók használatával.
>* PsExec 135-ös és a 445-ös TCP-portot használja. Ezért a két portokat kell nyitva a tűzfalon.

## <a name="run-commands"></a>Parancsok futtatása

Lásd: [futtatása PowerShell-parancsfájlokat a a Windows virtuális gép futtatása paranccsal](../windows/run-command.md) parancsfájlok futtatásához a virtuális gépen a parancsok futtatása a szolgáltatás használatával kapcsolatos további információt.

## <a name="customer-script-extension"></a>Ügyfél parancsprogramok futtatására szolgáló bővítmény

Az egyéni szkriptek futtatására szolgáló bővítmény funkció segítségével egyéni parancsfájl futtatása a cél virtuális Gépen. Ez a funkció használatához a következő feltételeknek kell teljesülniük:

* A virtuális gép kapcsolódik.

* Az Azure-ügynök telepítve van, és a virtuális gép a vártnak megfelelően működik.

* A bővítményt korábban nem telepítette a virtuális gépen.
 
  A bővítmény csak az első alkalommal használt azt fogja tölteni a parancsfájl. Ha később használja ezt a szolgáltatást, a bővítmény felismeri, hogy már használta azt, és nem tölti fel az új parancsfájl.

A szkript feltöltése a storage-fiókba, és hozzon létre saját tárolót kell. Ezután futtassa a következő szkriptet az Azure PowerShell egy számítógépen, amely kapcsolódik a virtuális géphez.

### <a name="for-v1-vms"></a>A V1-beli virtuális gépek

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

### <a name="for-v2-vms"></a>A V2 virtuális gép

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
Import-Module AzureRM
Login-AzureRmAccount #Ensure Login with account associated with subscription ID
Get-AzureRmSubscription -SubscriptionId $subscriptionID | Select-AzureRmSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzureRmVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Távoli PowerShell

>[!Note]
>TCP-Port 5986-os (HTTPS) nyitva kell lennie, hogy ezt a lehetőséget is használhatja.
>
>ARM-beli virtuális gépek a hálózati biztonsági csoport (NSG) az 5986-os portot kell megnyitni. További információkért tekintse meg a biztonsági csoportokat. 
>
>Az RDFE-beli virtuális gépek, rendelkeznie kell egy végpontot, amely rendelkezik egy magánhálózati portot (5986-os) és a egy nyilvános port. Majd hogy az NSG-t a nyilvános irányuló-portot is megnyithatja.

### <a name="set-up-the-client-computer"></a>Az ügyfélszámítógép beállítása

A PowerShell használatával távolról csatlakozhat a virtuális Gépet, először akkor állítsa be az ügyfélszámítógépen a kapcsolat engedélyezéséhez. Ehhez a szükség szerint a következő parancs futtatásával adja hozzá a virtuális Gépet a PowerShell megbízható gazdagépek listájához.

Egy virtuális gép hozzáadása a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Több virtuális gép hozzáadása a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Minden számítógép felvétele a megbízható gazdagépek listájához:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>A virtuális gép RemotePS engedélyezése

Klasszikus virtuális gépeket, az egyéni szkriptek futtatására szolgáló bővítmény használatával futtassa a következő szkriptet:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

ARM-beli virtuális gépek parancsok futtatása a portálról parancsfájl futtatásához használhatja a EnableRemotePS:

![Parancs futtatása](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Futtassa a következő parancsot, az ügyfél függően számítógép helye:

* A virtuális hálózat vagy a központi telepítési kívül

    * Egy klasszikus virtuális gép futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

    * Egy ARM virtuális Géphez tartozó első DNS-név hozzáadása a nyilvános IP-címre. Részletes lépéseiért lásd: [teljesen minősített tartománynév létrehozása az Azure Portalon Windows virtuális gép](../windows/portal-create-fqdn.md). Ezután futtassa a következő parancsot:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Belül a virtuális hálózatok közötti vagy az üzembe helyezés futtassa a következő parancsot:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>A SkipCaCheck jelző megkerüli a vonatkozó követelményt a tanúsítvány importálása a virtuális géphez, ha a munkamenet elindításához.

Parancsfájl futtatása a virtuális gép távolról is használhatja az Invoke-Command parancsmagot:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Távoli beállításjegyzék

>[!Note]
>135-ös vagy a 445-ös TCP-portot meg kell nyitni annak érdekében, hogy ezt a beállítást használja.
>
>ARM-beli virtuális gépek akkor nyissa meg az NSG-t az 5986-os portot. További információkért tekintse meg a biztonsági csoportokat. 
>
>Az RDFE-beli virtuális gépek rendelkeznie kell egy végpontot, amely rendelkezik egy privát 5986-os portot és a egy nyilvános port. Akkor is megnyithatja az NSG-t a nyilvános portot.

1. Egy másik virtuális Géphez ugyanazon a VNETEN nyissa meg a Beállításszerkesztőt (regedit.exe).

2. Válassza ki **fájl** >**csatlakozás hálózati beállításjegyzék**.

   ![Távoli lehetőség](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Keresse meg a cél virtuális gép által **állomásnév** vagy **dinamikus IP** (előnyösebb), a "Írja be a kijelölendő objektum nevét" beírásával.

   ![Távoli lehetőség](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Adja meg a hitelesítő adatokat a céloldali virtuális Gépet.

5. Végezze el a szükséges módosításokat.

## <a name="remote-services-console"></a>Távoli szolgáltatások konzol

>[!Note]
>A 135-ös vagy a 445-ös TCP-portok nyitva annak érdekében, hogy ezzel a beállítással kell lennie.
>
>ARM-beli virtuális gépek akkor nyissa meg az NSG-t az 5986-os portot. További információkért tekintse meg a biztonsági csoportokat. 
>
>Az RDFE-beli virtuális gépek rendelkeznie kell egy végpontot, amely rendelkezik egy privát 5986-os portot és a egy nyilvános port. Ezt követően kell az NSG-t a nyilvános portot is megnyithatja.

1. Az azonos virtuális hálózaton egy másik virtuális gépről nyissa meg a **Services.msc**.

2. Kattintson a jobb gombbal **szolgáltatások (helyi)**.

3. Válassza ki **Csatlakozás másik számítógéphez**.

   ![A távoli szolgáltatás](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Adja meg a dinamikus IP-címét a cél virtuális Gépen.

   ![Bemeneti dedikált IP-CÍMMEL](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Végezze el a szükséges módosításokat a szolgáltatásokhoz.

## <a name="next-steps"></a>További lépések

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Egyéni parancsfájl kiterjesztése a Windows a klasszikus üzemi modell használatával](../extensions/custom-script-classic.md)

PsExec része a [PSTools Suite](https://download.sysinternals.com/files/PSTools.zip).

A PSTools csomaggal kapcsolatos további információkért lásd: [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


