---
title: BitLocker rendszerindítási hibák elhárítása Azure-beli virtuális gépen | Microsoft dokumentumok
description: A BitLocker rendszerindítási hibáinak elhárítása Azure-beli virtuális gépben
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250009"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker rendszerindítási hibák egy Azure virtuális gépen

 Ez a cikk azokat a BitLocker-hibákat ismerteti, amelyek a Windows virtuális gép (VM) Microsoft Azure-beli indításakor jelentkezhetnek.

 

## <a name="symptom"></a>Hibajelenség

 A Windows virtuális gép nem indul el. Amikor a [Rendszerindítás diagnosztika](../windows/boot-diagnostics.md) ablakban ellenőrzi a képernyőképeket, a következő hibaüzenetek egyike jelenik meg:

- Csatlakoztassa a BitLocker-billentyűvel rendelkező USB-illesztőprogramot

- Ki vagy zárva! Adja meg a helyreállítási kulcsot, hogy újra induljon (Billentyűzetkiosztás: US) A helytelen bejelentkezési adatok túl sokszor lettek megadva, így a számítógép zárolva volt a személyes adatok védelme érdekében. A helyreállítási kulcs lekéréséhez lépjen https://windows.microsoft.com/recoverykeyfaq másik számítógépről vagy mobileszközről. Abban az esetben ön szükség ez, a kulcs ID van XXXXXXX. Vagy alaphelyzetbe állíthatja a számítógépet.

- Írja be a jelszót a meghajtó zárolásának feloldásához [ ] Nyomja le a Beszúrás billentyűt a jelszó írás közbeni megtekintéséhez.
- Adja meg a helyreállítási kulcsot A helyreállítási kulcs betöltése USB-eszközről.

## <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a virtuális gép nem találja a BitLocker helyreállítási kulcs (BEK) fájlt a titkosított lemez visszafejtéséhez.

## <a name="solution"></a>Megoldás

A probléma megoldásához állítsa le és szabadítsa fel a virtuális gép, majd indítsa újra. Ez a művelet kényszeríti a virtuális gép lekérni a BEK-fájlt az Azure Key Vault, majd tegye a titkosított lemezre. 

Ha ez a módszer nem oldja meg a problémát, kövesse az alábbi lépéseket a BEK fájl manuális visszaállításához:

1. Készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.
2. [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md) A [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) parancs futtatásához a **BitLocker Drive Encryption** 7.

    Felügyelt lemez csatlakoztatásakor előfordulhat, hogy "titkosítási beállításokat tartalmaz, ezért nem használható adatlemezként" hibaüzenet jelenik meg. Ebben az esetben futtassa a következő parancsfájlt a lemez csatolásához való ismételt csatlakoztatáshoz:

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
     Felügyelt lemezt nem csatolhat egy blobrendszerről visszaállított virtuális géphez.

3. A lemez csatlakoztatása után, hogy egy távoli asztali kapcsolat a helyreállítási virtuális gép, így futtathat néhány Azure PowerShell-parancsfájlok. Győződjön meg arról, hogy az [Azure PowerShell legújabb verziója](https://docs.microsoft.com/powershell/azure/overview) telepítve van a helyreállítási virtuális gép.

4. Nyisson meg egy emelt szintű Azure PowerShell-munkamenetet (futtatás rendszergazdaként). Futtassa a következő parancsokat az Azure-előfizetésbe való bejelentkezéshez:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Futtassa a következő parancsfájlt a BEK-fájl nevének ellenőrzéséhez:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    A következő minta a kimenet. Keresse meg a csatolt lemez BEK fájlnevét. Ebben az esetben feltételezzük, hogy a csatlakoztatott lemez meghajtóbetűjele F, és a BEK fájl EF7B2F5A-50C6-4637-9F13-7F599C12F85C. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Ha két duplikált kötetet lát, az újabb időbélyeggel rendelkező kötet a helyreállítási virtuális gép által használt aktuális BEK-fájl.

    Ha a **Tartalomtípus** értéke **Becsomagolt BEK,** lépjen a [kulcstitkosítási kulcs (KEK) forgatókönyvekre.](#key-encryption-key-scenario)

    Most, hogy már megvan a meghajtó BEK fájljának neve, létre kell hoznia a titkos fájlnevet. BEK fájlt a meghajtó feloldásához.

6.  Töltse le a BEK-fájlt a helyreállítási lemezre. A következő minta a BEK-fájlt a C:\BEK mappába menti. Győződjön meg `C:\BEK\` arról, hogy az elérési út létezik a parancsfájlok futtatása előtt.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  A csatolt lemez FELoldásához a BEK fájl használatával futtassa a következő parancsot.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez az F meghajtó. 

8. Miután a lemez zárolása sikeresen feloldva lett a BEK kulccsal, válassza le a lemezt a helyreállítási virtuális gépről, majd hozza létre újra a virtuális gép et ezzel az új operációsrendszer-lemezzel.

    > [!NOTE]
    > Az operációsrendszer-lemez cseréje nem támogatott a lemeztitkosítást használó virtuális gépek számára.

9. Ha az új virtuális gép továbbra sem tudja a szokásos módon elindítani, próbálkozzon az alábbi lépésekkel a meghajtó zárolásának feloldása után:

    - A védelem felfüggesztése a BitLocker ideiglenes kikapcsolására az alábbi futtatásával:

                    manage-bde -protectors -disable F: -rc 0
           
    - Teljesen visszafejti a meghajtót. Ehhez futtassa az alábbi parancsot:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Kulcstitkosítási kulcs forgatókönyve

Kulcstitkosítási kulcs esetén kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a bejelentkezett felhasználói fiókhoz "kicsomagolt" engedély szükséges a **USER| Kulcsengedélyek| Kriptográfiai műveletek| Kulcs kicsomagolása**.
2. Mentse a következő parancsfájlt egy be. PS1 fájl:

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
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
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
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
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
    ```
3. Állítsa be a paramétereket. A parancsfájl feldolgozza a KEK titkos kulcsot a BEK-kulcs létrehozásához, majd menti azt a helyreállítási virtuális gép egy helyi mappájába. Ha a parancsfájl futtatásakor hibaüzenetet kap, olvassa el a [parancsfájl hibaelhárítási szakaszát.](#script-troubleshooting)

4. A parancsfájl megkezdésekor a következő kimenet jelenik meg:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Amikor a parancsfájl befejeződik, a következő kimenet jelenik meg:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. A csatolt lemez BEK fájllal történő zárolásának feloldásához futtassa a következő parancsot:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Ebben a példában a csatlakoztatott operációsrendszer-lemez az F meghajtó. 

6. Miután a lemez zárolása sikeresen feloldva lett a BEK kulccsal, válassza le a lemezt a helyreállítási virtuális gépről, majd hozza létre újra a virtuális gép et ezzel az új operációsrendszer-lemezzel. 

    > [!NOTE]
    > Az operációsrendszer-lemez cseréje nem támogatott a lemeztitkosítást használó virtuális gépek számára.

7. Ha az új virtuális gép továbbra sem tudja a szokásos módon elindítani, próbálkozzon az alábbi lépésekkel a meghajtó zárolásának feloldása után:

    - A védelem felfüggesztése a BitLocker ideiglenes kikapcsolására a következő parancs futtatásával:

             manage-bde -protectors -disable F: -rc 0
           
    - Teljesen visszafejti a meghajtót. Ehhez futtassa az alábbi parancsot:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Parancsfájlokkal kapcsolatos hibaelhárítás

**Hiba: Nem lehetett betölteni a fájlt vagy a szerelvényt**

Ez a hiba azért fordul elő, mert az ADAL-szerelvények elérési útjai hibásak. Ha az AZ modul csak az aktuális felhasználó számára van telepítve, `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`az ADAL-szerelvények a rendszerben lesznek elhelyezve.

A `Az.Accounts` megfelelő elérési út megkereséséhez mappát is kereshet.

**Hiba: A Get-AzKeyVaultSecret vagy a Get-AzKeyVaultSecret nem ismeri fel parancsmag neveként**

Ha a régi AZ PowerShell-modult használja, a `Get-AzureKeyVaultSecret` `Get-AzureKeyVaultSecret`két parancsot a és a számára kell módosítania.

**Paraméterminták**

| Paraméterek  | Értékminta  |Megjegyzések   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | A kulcsot tároló kulcstároló neve |
|$kekName   |mykey (kulcs   | A virtuális gép titkosításához használt kulcs neve|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | A virtuális gép kulcsa titkoskulcsának neve|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |A BEK-fájl írásának elérési útja.|
|$adTenant  |contoso.onmicrosoft.com   | A key vaultot tároló Azure Active Directory teljes tartományszáma vagy GUID azonosítója |
