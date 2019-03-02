---
title: Egy Azure-beli virtuális gépen a BitLocker rendszerindítási hibák elhárítása |} A Microsoft Docs
description: Ismerje meg, egy Azure-beli virtuális gépen a BitLocker rendszerindítás kapcsolatos hibák elhárítása
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 13a45593509deb0cb9578c5ea6ed83aab3e008a4
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216560"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>A BitLocker rendszerindítási hibák-beli virtuális gépen

 Ez a cikk ismerteti a BitLocker hibák léphetnek fel egy Windows virtuális gép (VM) megnyitásakor a Microsoft Azure-ban.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptom"></a>Jelenség

 Windows virtuális gép nem indul el. Amikor ellenőrizheti a képernyőképek a a [rendszerindítási diagnosztika](../windows/boot-diagnostics.md) ablakban a következő hibaüzeneteket egyikét látja:

- Csatlakoztassa az USB-illesztőprogramot, amely rendelkezik a BitLocker-kulcs

- Már zárolva van! Adja meg a helyreállítási kulcsot az induláshoz újra (billentyűzetkiosztás módosítása: Egyesült Államok) nem a megfelelő bejelentkezési adatok a megadott túl sokszor, így a számítógép adatainak védelme zárolták. folyamatban van. A helyreállítási kulcs lekéréséhez lépjen a http://windows.microsoft.com/recoverykeyfaq egy másik számítógépen vagy mobileszközön. Abban az esetben szüksége lesz rá, a kulcs azonosítója XXXXXXX. Vagy visszaállíthatja a Számítógépet.

- Adja meg a jelszót, a meghajtó [] tekintse meg a jelszó beírása a Beszúrás billentyű lenyomásával zárolásának feloldásához.
- Adja meg a helyreállítási kulcs betöltése a helyreállítási kulcsot az USB-eszközt.

## <a name="cause"></a>Ok

A probléma akkor fordulhat elő, ha a virtuális gép visszafejteni a titkosított lemez BitLocker helyreállítási kulcs (Blokktitkosítási) fájl nem található.

## <a name="solution"></a>Megoldás

A probléma megoldásához állítsa le és felszabadítja a virtuális Gépet, és indítsa újra. Ez a művelet arra kényszeríti a virtuális Gépre, és kérje le a blokktitkosítási kulcsot fájlt az Azure Key vaultból, és csak utána, a titkosított lemez. 

Ez a módszer nem nem a hárítsa el a problémát, ha manuálisan állítsa vissza a blokktitkosítási kulcsot-fájlt az alábbi lépésekkel:

1. Pillanatkép készítése a rendszerlemezt az érintett virtuális gépek biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).
2. [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md) , amely a BitLocker titkosítja. Ez futtatásához szükséges a [kezelése-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) parancsot, amely csak a BitLocker-titkosítású virtuális gépen érhető el.

    Felügyelt lemez csatolása, amikor egy "titkosítási beállításait tartalmazza, és ezért nem használható adatlemezként" hibaüzenetet kaphat. Ebben az esetben futtassa a következő parancsfájlt, és próbálkozzon újra a lemez csatlakoztatásához:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Felügyelt lemez nem csatolható a blob rendszerképből visszaállított virtuális géphez.

3. Miután a lemez csatlakoztatva van, győződjön meg a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot, hogy az egyes Azure PowerShell-parancsfájlok futtatása. Győződjön meg arról, hogy rendelkezik-e a [az Azure PowerShell legújabb verzióját](https://docs.microsoft.com/powershell/azure/overview) telepítve a helyreállítási virtuális Gépet.

4. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (Futtatás rendszergazdaként). Jelentkezzen be Azure-előfizetés a következő parancsok futtatásával:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Futtassa a következő parancsfájl ellenőrzéséhez a blokktitkosítási kulcsot fájl neve:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    A következő egy példa a kimenetre. Keresse meg a csatlakoztatott lemez rendelkeznek BEk-KEL fájl nevét. Ebben az esetben feltételezzük, hogy a csatlakoztatott lemez meghajtóbetűjelét F, pedig a blokktitkosítási kulcsot fájl EF7B2F5A 50 - C 6-4637-9F13-7F599C12F85C. A BLOKKTITKOSÍTÁSI KULCSOT.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Két duplikált kötetet lát, a kötet, amely az újabb időbélyeg rendelkezik-e az aktuális rendelkeznek BEk-KEL fájlba, amelyet a helyreállítási virtuális Gépet.

    Ha a **tartalomtípus** érték **rendelkeznek BEk-KEL burkolt be**, nyissa meg a [kulcs titkosítási kulcs-(KEK-) forgatókönyveket](#key-encryption-key-scenario).

    Most, hogy a meghajtó a blokktitkosítási kulcsot fájl nevét, hogy a titkos kulcs-fájlnév létrehozásához. A meghajtó feloldásához fájl rendelkeznek BEk-KEL. 

6.  Töltse le a blokktitkosítási kulcsot a helyreállító lemez. Az alábbi minta a blokktitkosítási kulcsot fájlt a C:\BEK mappába menti. Győződjön meg arról, hogy a `C:\BEK\` elérési út létezik, a parancsfájlok futtatása előtt.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  A blokktitkosítási kulcsot fájl segítségével a csatlakoztatott lemez zárolásának feloldásához, futtassa a következő parancsot:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez az meghajtó F. Győződjön meg arról, hogy a helyes meghajtóbetűjelet használja. 

    - Ha a lemez sikeresen feloldotta a Blokktitkosítási kulcs használatával. azt is vegye figyelembe a BItLocker probléma fogja megoldani. 

    - A Blokktitkosítási kulcs használatával nem a lemez zárolásának feloldásához, ha átmeneti kikapcsolásához a BitLocker a következő parancs futtatásával védelem felfüggesztése
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Ha azt tervezi, a virtuális gép újraépítése a dytem lemez segítségével, teljes körűen vissza kell fejtenie a meghajtó. Ehhez futtassa az alábbi parancsot:

        ```powershell
        manage-bde -off F:
        ```
8.  Válassza le a lemezt a helyreállítási virtuális Gépet, és ezután csatlakoztassa újra a lemezt, egy rendszerlemezt az érintett virtuális géphez. További információkért lásd: [hibáinak elhárítása egy Windows virtuális Gépet az operációsrendszer-lemez egy helyreállítási virtuális Géphez való csatlakoztatásával](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Kulcsalapú titkosítás kulcsa forgatókönyv

A Kulcsalapú titkosítás kulcsa esetben kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a bejelentkezett felhasználói fiók a Key Vault hozzáférési szabályzatokat az a "burkolatlan" engedély szükséges a **felhasználói |} Kulcsengedélyek |} Titkosítási műveletek |} Kulcs kicsomagolása**.
2. Mentse az alábbi parancsfájlok a. PS1 fájlnevet:

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
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

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
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
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
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
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
    ```
3. Állítsa be a paramétereket. A parancsfájl feldolgozni a Blokktitkosítási kulcs létrehozásához a KEK titkos kódot, és ezután menti azt a helyreállítási virtuális Gépet egy helyi mappába.

4. A következő kimenet jelenik meg a parancsfájl megkezdésekor:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    A szkript befejezése a következő kimenet jelenik meg:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. A blokktitkosítási kulcsot fájl segítségével a csatlakoztatott lemez zárolásának feloldásához, futtassa a következő parancsot:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez az meghajtó F. Győződjön meg arról, hogy a helyes meghajtóbetűjelet használja. 

    - Ha a lemez sikeresen feloldotta a Blokktitkosítási kulcs használatával. azt is vegye figyelembe a BItLocker probléma fogja megoldani. 

    - A Blokktitkosítási kulcs használatával nem a lemez zárolásának feloldásához, ha átmeneti kikapcsolásához a BitLocker a következő parancs futtatásával védelem felfüggesztése
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Ha azt tervezi, a virtuális gép újraépítése a dytem lemez segítségével, teljes körűen vissza kell fejtenie a meghajtó. Ehhez futtassa az alábbi parancsot:

        ```powershell
        manage-bde -off F:
        ```

6. Válassza le a lemezt a helyreállítási virtuális Gépet, és ezután csatlakoztassa újra a lemezt, egy rendszerlemezt az érintett virtuális géphez. További információkért lásd: [hibáinak elhárítása egy Windows virtuális Gépet az operációsrendszer-lemez egy helyreállítási virtuális Géphez való csatlakoztatásával](troubleshoot-recovery-disks-windows.md).
