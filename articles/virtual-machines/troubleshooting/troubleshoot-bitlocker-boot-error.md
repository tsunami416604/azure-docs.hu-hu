---
title: BitLocker rendszerindítási hibák elhárítása Azure-beli virtuális gépen | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a BitLocker rendszerindítási hibáit egy Azure-beli virtuális gépen
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: f69d81656358b8ee9a5fa51cb8261e3115729714
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511559"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker rendszerindítási hibák egy Azure-beli virtuális gépen

 Ez a cikk azokat a BitLocker-hibákat ismerteti, amelyek akkor jelentkezhetnek, ha a Windows rendszerű virtuális gépet (VM) elindítja Microsoft Azureban.

 

## <a name="symptom"></a>Hibajelenség

 Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a képernyőképeket a [rendszerindítási diagnosztika](./boot-diagnostics.md) ablakban, a következő hibaüzenetek egyike jelenik meg:

- Csatlakoztassa a BitLocker-kulcsot tartalmazó USB-illesztőprogramot

- Ki van zárva! Adja meg a helyreállítási kulcsot a kezdéshez (billentyűzetkiosztás: US) a helytelen bejelentkezési adatok túl sokszor lettek megadva, így a számítógép zárolva van az adatok védelme érdekében. A helyreállítási kulcs lekéréséhez ugorjon https://windows.microsoft.com/recoverykeyfaq egy másik számítógép vagy mobil eszközről. Ha szüksége van rá, a kulcs azonosítója XXXXXXX. Vagy alaphelyzetbe állíthatja a számítógépet.

- Adja meg a meghajtó zárolásának feloldásához szükséges jelszót [] a beíráskor nyomja le az INSERT billentyűt a jelszó megjelenítéséhez.
- Adja meg a helyreállítási kulcs betöltését egy USB-eszközről.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a virtuális gép nem találta meg a BitLocker helyreállítási kulcs (BEK) fájlját a titkosított lemez visszafejtéséhez.

## <a name="solution"></a>Megoldás

A probléma megoldásához állítsa le és szabadítsa fel a virtuális gépet, majd indítsa el. Ez a művelet arra kényszeríti a virtuális gépet, hogy lekérje a BEK-fájlt a Azure Key Vaultból, majd helyezze a titkosított lemezre. 

Ha ez a módszer nem oldja meg a problémát, kövesse az alábbi lépéseket a BEK-fájl manuális visszaállításához:

1. Készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).
2. [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez](troubleshoot-recovery-disks-portal-windows.md). A 7. lépésben a [manage-bde](/windows-server/administration/windows-commands/manage-bde) parancs futtatásához a **BitLocker meghajtótitkosítás** funkciót engedélyeznie kell a helyreállítási virtuális gépen.

    Felügyelt lemez csatlakoztatásakor előfordulhat, hogy a "titkosítási beállításokat tartalmaz, ezért nem használható adatlemezként" hibaüzenet jelenik meg. Ebben az esetben futtassa a következő parancsfájlt a lemez csatlakoztatásához:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Nem csatolhat felügyelt lemezt egy blob-lemezképből visszaállított virtuális géphez.

3. Ha a lemez csatlakoztatva van, hozzon egy távoli asztali kapcsolattal a helyreállítási virtuális gépet, hogy néhány Azure PowerShell szkriptet futtasson. Győződjön meg arról, hogy a helyreállítási virtuális gépen a [Azure PowerShell legújabb verziója](/powershell/azure/) van telepítve.

4. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). A következő parancsok futtatásával jelentkezzen be az Azure-előfizetésbe:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Futtassa a következő szkriptet a BEK-fájl nevének a megadásához:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Az alábbiakban a kimenet mintája látható. Ebben az esetben feltételezzük, hogy a fájl neve EF7B2F5A-50C6-4637-0001-7F599C12F85C. BEK.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Ha két duplikált kötet jelenik meg, akkor az újabb időbélyeggel rendelkező kötet a jelenlegi BEK-fájl, amelyet a helyreállítási virtuális gép használ.

    Ha a **tartalomtípus** értéke **becsomagolt BEK**, lépjen a [Key encryption Key (KEK) forgatókönyvekhez](#key-encryption-key-scenario).

    Most, hogy már rendelkezik a meghajtóhoz tartozó BEK-fájl nevével, létre kell hoznia a titkos fájl nevét. BEK-fájl a meghajtó zárolásának feloldásához.

6.  Töltse le a BEK-fájlt a helyreállítási lemezre. A következő minta menti a BEK-fájlt a C:\BEK mappába. Győződjön meg arról, hogy az `C:\BEK\` elérési út létezik a parancsfájlok futtatása előtt.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  A csatolt lemez a BEK-fájllal való feloldásához futtassa a következő parancsot.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez a meghajtó F. Ellenőrizze, hogy a megfelelő meghajtóbetűjelet használja-e. 

8. Miután a lemez sikeresen fel lett oldva a BEK kulccsal, válassza le a lemezt a helyreállítási virtuális gépről, majd hozza létre újra a virtuális gépet az új operációsrendszer-lemez használatával.

    > [!NOTE]
    > A lemezes titkosítást használó virtuális gépek nem támogatják az operációsrendszer-lemez cseréjét.

9. Ha az új virtuális gép továbbra sem indul el rendesen, próbálja meg a következő lépések egyikét a meghajtó zárolásának feloldása után:

    - A védelem felfüggesztésével ideiglenesen kapcsolja ki a BitLockert a következő futtatásával:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Teljes mértékben visszafejtheti a meghajtót. Ehhez futtassa az alábbi parancsot:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Kulcs titkosítási kulcsának forgatókönyve

A kulcs titkosítási kulcsainak forgatókönyvéhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a bejelentkezett felhasználói fiók "nincs becsomagolt" engedélyre van szüksége a felhasználó Key Vault hozzáférési házirendjében **| Kulcs engedélyei | Titkosítási műveletek | Kulcs kicsomagolása**.
2. Mentse a következő szkriptet a-be. PS1-fájl:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Agit pgit ccounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Állítsa be a paramétereket. A szkript feldolgozza a KEK titkát a BEK-kulcs létrehozásához, majd a helyreállítási virtuális gép helyi mappájába menti azt. Ha a parancsfájl futtatásakor hibaüzenetet kap, tekintse meg a [parancsfájl hibaelhárítása](#script-troubleshooting) szakaszt.

4. A szkript elkezdése után a következő kimenet jelenik meg:

    A GAC verziójának helye                                                                              
    ---    -------        --------                                                                              
    Hamis v 4.0.30319 elemet C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  Hamis v 4.0.30319 elemet C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    A szkript befejezésekor a következő kimenet jelenik meg:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. A csatolt lemez a BEK-fájllal való feloldásához futtassa a következő parancsot:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez a meghajtó F. Ellenőrizze, hogy a megfelelő meghajtóbetűjelet használja-e. 

6. Miután a lemez sikeresen fel lett oldva a BEK kulccsal, válassza le a lemezt a helyreállítási virtuális gépről, majd hozza létre újra a virtuális gépet az új operációsrendszer-lemez használatával. 

    > [!NOTE]
    > A lemezes titkosítást használó virtuális gépek nem támogatják az operációsrendszer-lemez cseréjét.

7. Ha az új virtuális gép továbbra sem indul el rendesen, próbálja meg a következő lépések egyikét a meghajtó zárolásának feloldása után:

    - A védelem felfüggesztésével ideiglenesen kapcsolja ki a BitLockert a következő parancs futtatásával:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Teljes mértékben visszafejtheti a meghajtót. Ehhez futtassa az alábbi parancsot:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Parancsfájlok hibaelhárítása

**Hiba: nem sikerült betölteni a fájlt vagy a szerelvényt**

Ez a hiba azért fordul elő, mert a ADAL-szerelvények elérési útjai helytelenek. A megfelelő elérési út megkereséséhez keresse meg a `Az.Accounts` mappát.

**Hiba: Get-AzKeyVaultSecret vagy Get-AzKeyVaultSecret nem ismerhető fel a parancsmag neveként**

Ha a régi AZ PowerShell-modult használja, a és a két parancsot kell módosítania `Get-AzureKeyVaultSecret` `Get-AzureKeyVaultSecret` .

**Paraméterek mintái**

| Paraméterek  | Érték minta  |Megjegyzések   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | A kulcsot tároló kulcstároló neve |
|$kekName   |Mykey   | A virtuális gép titkosításához használt kulcs neve|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | A VM-kulcs titkának neve|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |A BEK-fájl írásához használt elérési út.|
|$adTenant  |contoso.onmicrosoft.com   | A Key vaultot tároló Azure Active Directory teljes tartományneve vagy GUID-azonosítója |
