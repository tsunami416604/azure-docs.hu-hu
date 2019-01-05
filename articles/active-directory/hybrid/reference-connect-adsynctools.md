---
title: 'Azure AD Connect: ADSyncTools PowerShell-referencia |} A Microsoft Docs'
description: Ebben a dokumentumban részletes információ a ADSyncTools.psm1 PowerShell-modult.
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 54d23bca29c38f258334d7b641b8c1f480da55e1
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054962"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect:  ADSyncTools PowerShell-referencia
A következő dokumentáció arról nyújt a ADSyncTools.psm1 PowerShell-modult, amely tartalmazza az Azure AD Connecttel kapcsolatos referenciainformációk.

## <a name="clear-adsynctoolsconsistencyguid"></a>CLEAR-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
Az mS-Ds-ConsistencyGuid az AD-felhasználó törlése

### <a name="syntax"></a>SZINTAXIS

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Törölje az értéket az mS-Ds-ConsistencyGuid a cél AD-felhasználó

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
Megcélzott felhasználó ad beállítása

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Győződjön meg róla ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

## <a name="connect-adsyncdatabase"></a>Csatlakozás AdSyncDatabase

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-database"></a>-Adatbázis
{{Fill adatbázis leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Példány
{{Fill példány leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill jelszó leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Kiszolgáló
{{Fill kiszolgáló leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill felhasználónév leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Exportálás – ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SZINOPSZIS
ConsistencyGuid jelentés exportálása

### <a name="syntax"></a>SZINTAXIS

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az Importálás CSV-fájlt a Import-ADSyncToolsImmutableIdMigration alapján ConsistencyGuid jelentést hoz létre

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Használja az alternatív bejelentkezési Azonosítóval (levelezés)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>UserPrincipalName –
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Kimenet
Kimeneti fájl nevét, a fürt megosztott kötetei szolgáltatás és a NAPLÓFÁJLOK

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-Állomásnév
{{Fill állomásnév leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SZINOPSZIS
Felhasználó beolvasása az AD-ből

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
TEGYE AD objektumot adja vissza: Több erdő támogatása

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
Az AD-ConsistencyGuid beállítása célfelhasználó

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
Az mS-Ds-ConsistencyGuid le AD-felhasználó

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A cél az AD felhasználó GUID formátumú mS-Ds-ConsistencyGuid attribútum értékét adja vissza

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
Megcélzott felhasználó ad beállítása

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SZINOPSZIS
Első az ObjectGuid AD-felhasználó

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az értéket adja vissza az ObjectGUID attribútum GUID formátumú cél AD-felhasználó

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
Megcélzott felhasználó ad beállítása

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SZINOPSZIS
Get AAD Connect futtatási előzmények

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Függvény, amely XML formátumban adja vissza az AAD Connect futtatási előzmények

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>2. PÉLDA
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-days"></a>– Nap
{{Adja meg a napok leírás}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SZINOPSZIS
Felhasználók beolvasása megváltozott SourceAnchor hibákkal

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Lekérdezések AAD Connect futtatási előzmények működik, és exportálja a hibát jelentő összes felhasználója: "SourceAnchor attribútum módosult."

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
#Required Parameters
```

$sourcePath = Read-Host - parancssorból "adja meg a naplófájl elérési útja a fájl neve" #"\<forrás_elérési_útja\>" $outputPath = Read-Host-Prompt "adja meg a kibővített fájl elérési útja fájlnévvel rendelkező" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged - sourcePath $sourcePath - outputPath $outputPath

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath leírás}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fill outputPath leírás}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Importálás – ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SZINOPSZIS
Importálás ImmutableID az aad-ből

### <a name="syntax"></a>SZINTAXIS

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Létrehoz egy fájlt az összes Azure AD-Synchronized felhasználóval GUID formátumú követelmények ImmutableID értéket tartalmazza: MSOnline PowerShell modul

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-output"></a>-Kimenet
Kimeneti CSV-fájl

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Szinkronizálandó felhasználók Azure AD-ből Lomtár

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-query"></a>-Lekérdezés
{{Fill lekérdezés leírásának}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection leírás}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
Távolítsa el a lejárt tanúsítványokat UserCertificate attribútum-szkript

### <a name="syntax"></a>SZINTAXIS

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a szkript veszi a szervezeti egységbe az összes objektum az Active Directory-tartomány - objektumosztály (felhasználó/számítógép) alapján szűri, és szerepel a UserCertificate attribútum lejárt tanúsítványok törlése.
Fogja csak biztonsági mentése (BackupOnly mód) alapértelmezés szerint lejárt tanúsítványok egy fájlba, és nem hajtsa végre a módosításokat az ad-ben.
Ha - BackupOnly $false használja, akkor ezen objektumok UserCertificate attribútum található bármely lejárt tanúsítvány törlődni fognak a másolását követően AD fájlba.
Mindegyik tanúsítvány egy elkülönített filename készül biztonsági másolat: A parancsfájl ObjectClass_ObjectGUID_CertThumprint.cer CSV formátumban, vagy amelyek érvénytelen vagy lejárt, beleértve a tényleges megtett tanúsítványok rendelkező összes felhasználó megjelenítése (kimaradnak/exportált/törölt) is létrehoz egy naplófájlt.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates - TargetOU "szervezeti egység felhasználók, OU = = Corp, DC = Contoso, DC = com" - ObjectClass felhasználó

#### <a name="example-2"></a>2. PÉLDA
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates - TargetOU "OU = számítógépek, OU = Corp, DC = Contoso, DC = com" - ObjectClass számítógép - BackupOnly $false

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-targetou"></a>-TargetOU
Cél szervezeti Egységet az AD-objektumok keresése

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly nem törli a tanúsítványok az AD-ből

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Osztály szűrővel

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Javítás-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SZINOPSZIS
Rövid leírás

### <a name="syntax"></a>SZINTAXIS

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-Állomásnév
{{Fill állomásnév leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Visszaállítás – ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
(EHHEZ) Visszaállítja a AD UserCertificate attribútum certicate-fájlból

### <a name="syntax"></a>SZINTAXIS

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
MS-Ds-ConsistencyGuid beállítása az AD-felhasználó

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Adja meg a cél AD-felhasználó mS-Ds-ConsistencyGuid attribútum

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-user"></a>-Felhasználó
Az AD-ConsistencyGuid beállítása célfelhasználó

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Érték
ImmutableId (Bajtového Pole, GUID, GUID karakterlánc vagy Base64-karakterlánc)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszist}}

### <a name="syntax"></a>SZINTAXIS

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>LEÍRÁS
{{Töltse ki a leírást}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{Hozzáadása példa leírása itt}}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-Állomásnév
{{Fill állomásnév leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Fill port leírása}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Nyomkövetés-ADSyncToolsADImport

### <a name="synopsis"></a>SZINOPSZIS
Létrehoz egy nyomkövetési fájl és az AD importálás lépés

### <a name="syntax"></a>SZINTAXIS

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Nyomkövetések minden, az AAD Connect AD importálás ldap-lekérdezéseket futtatni egy adott AD vízjel ellenőrzőpontot (partíció cookie-k). Az aktuális mappa egy nyomkövetési fájl ".\ADimportTrace_yyyyMMddHHmmss.log" hoz létre.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML leírás}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-tartományvezérlő
Az AD-összekötő exportálása XML-fájl

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Cél-tartományvezérlő

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-szűrő
Erdő gyökerének DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
A nyomkövetési objektumok AD típusú \> * = összes objektumtípusa

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Ha már fut a tartományi rendszergazdaként esetén nem kell AD hitelesítő adatait.
Manuális bevitel vízjel helyett XML fájlt például $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Nyomkövetés-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SZINOPSZIS
Rövid leírás

### <a name="syntax"></a>SZINTAXIS

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Hosszú leírás

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-context"></a>-Környezet
Param1 súgó leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Kiszolgáló
Param2 súgó leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Param2 súgó leírása

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Szűrő
Param2 súgó leírása

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Frissítés-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SZINOPSZIS
Felhasználók frissíti az új ConsistencyGuid (immutableid azonosítója)

### <a name="syntax"></a>SZINTAXIS

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Frissíti a felhasználók az új ConsistencyGuid (immutableid azonosítója) értéket a ConsistencyGuid jelentés Ez a funkció támogatja a WhatIf kapcsolóval vegye figyelembe venni: ConsistencyGuid jelentés lap azokat a kell importálni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>2. PÉLDA
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Művelet
Műveletek

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Kimenet
Kimeneti fájl nevét, a naplófájlok

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Megmutatja, hogy mi történne a parancsmag futtatásakor.
A parancsmag nem fut.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Megerősítése
A parancsmag futtatása előtt megerősítést fog kérni.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
