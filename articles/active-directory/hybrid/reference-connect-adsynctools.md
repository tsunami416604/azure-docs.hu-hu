---
title: 'Azure AD Connect: AdsyncTools PowerShell-hivatkozás | Microsoft dokumentumok'
description: Ez a dokumentum az ADSyncTools.psm1 PowerShell-modul referenciainformációit tartalmazza.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60454659"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: AdsyncTools PowerShell-referencia
A következő dokumentáció az Azure AD Connect részét képező ADSyncTools.psm1 PowerShell-modul referenciainformációit tartalmazza.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid (Törlés-ADSyncToolsConsistencyGuid)

### <a name="synopsis"></a>SZINOPSZIS
Törölje az mS-Ds-konzisztenciaGuid az AD-felhasználó

### <a name="syntax"></a>SZINTAXIS

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Törölje az mS-Ds-ConsistencyGuid értékét a cél AD-felhasználó számára

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
Az AD-ben beállított célfelhasználó

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-AdsyncToolsADModuleLoaded

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-database"></a>-Adatbázis
{{Adatbázis leírása}}

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
{{Fill instance description}}

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
{{Jelszó-leírás kitöltése}}

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
{{Fill Server Description}}

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
{{Felhasználónév kitöltése leírás}}

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyInDMigration

### <a name="synopsis"></a>SZINOPSZIS
KonzisztenciaGuid jelentés exportálása

### <a name="syntax"></a>SZINTAXIS

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
KonzisztenciaGuid jelentés létrehozása az Import-ADSyncToolsImmutableIdMigration importálási CSV-fájlja alapján

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

#### <a name="-alternativeloginid"></a>-AlternatívLoginId
Alternatív bejelentkezési azonosító használata (e-mail)

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
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
CsV- és LOG-fájlok kimeneti fájlneve

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-állomásnév
{{Fill hostName Description}}

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
Felhasználó beszereznie az AD-ből

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Eredményül egy AD-objektum, amely et do: Több erdő támogatása

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
Az AD célfelhasználója a konzisztenciaGuid beállításához

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SZINOPSZIS
Az mS-Ds-ConsistencyGuid beszereznie az AD-felhasználótól

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A cél AD-felhasználó mS-Ds-ConsistencyGuid attribútumában megadott értéket adja eredményül GUID formátumban.

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
Az AD-ben beállított célfelhasználó

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SZINOPSZIS
Az ObjectGuid beszereznie az AD-felhasználótól

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A cél AD-felhasználó ObjektumGUID attribútumában megadott értéket adja eredményül GUID formátumban.

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
Az AD-ben beállított célfelhasználó

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsrunHistory

### <a name="synopsis"></a>SZINOPSZIS
Az AAD Connect futtatási előzményei

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az AAD-csatlakozási futtatási előzményeit XML formátumban visszaadó függvény

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

#### <a name="-days"></a>-Napok
{{Fill days leírás}}

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SZINOPSZIS
A SourceAnchor módosított hibáival rendelkező felhasználók beszerezni

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A függvény lekérdezi az AAD Connect futtatási előzményeit, és exportálja a hibát jelző összes felhasználót: "SourceAnchor attribútum megváltozott."

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Adja meg a\<naplófájl elérési útját fájlnévvel" Source_Path "\>Source_Path " $outputPath\<=\>Read-Host -Prompt "Adja meg a fájl elérési útját fájlnévvel" #" Out_Path "
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>2. PÉLDA
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath Description}}

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
{{Fill outputPath Description}}

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SZINOPSZIS
Nem módosítható azonosító importálása AAD-ból

### <a name="syntax"></a>SZINTAXIS

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Létrehoz egy fájlt az összes Azure AD szinkronizált felhasználóval, amely az ImmutableID értéket GUID formátumban Követelmények: MSOnline PowerShell-modul

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludesyncUsersFromRecyclebin
Szinkronizált felhasználók beszereznie az Azure AD Lomtárból

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-query"></a>-Lekérdezés
{{Fill Query Description}}

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
{{Fill SqlConnection Description}}

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
Lejárt tanúsítványok eltávolítása a UserCertificate attribútumból

### <a name="syntax"></a>SZINTAXIS

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a parancsfájl az Active Directory tartomány célszervezeti egységéből származó összes objektumot átveszi – objektumosztály (felhasználó/számítógép) szerint szűrve, és törli a UserCertificate attribútumban található összes lejárt tanúsítványt.
Alapértelmezés szerint (BackupOnly mód) csak a lejárt tanúsítványokat biztonsági másolatot készít egy fájlba, és nem hajt végre módosításokat az AD-ben.
Ha a -BackupOnly $false-t használja, akkor az objektumok UserCertificate attribútumában található lejárt tanúsítvány a fájlba másolásután törlődik az AD-ből.
A rendszer minden tanúsítványról külön fájlnévre készül: ObjectClass_ObjectGUID_CertThumprint.cer A parancsfájl csv formátumú naplófájlt is létrehoz, amely az összes érvényes vagy lejárt tanúsítvánnyal rendelkező felhasználót tartalmazza, beleértve a tényleges műveletet is (Kihagyott/exportált/törölt).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass felhasználó

#### <a name="example-2"></a>2. PÉLDA
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-targetou"></a>-TargetOU
Célszervezeti egység AD-objektumok felkéséséhez

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
A BackupOnly nem törli a tanúsítványokat az AD-ből

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
Objektumosztály szűrője

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

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
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-állomásnév
{{Fill hostName Description}}

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

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SZINOPSZIS
(Teendő) Az AD UserCertificate attribútum visszaállítása tanúsítványfájlból

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
MS-DS-ConsistencyGuid beállítása AD-felhasználón

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Érték beállítása mS-Ds-ConsistencyGuid attribútumban a cél AD-felhasználó számára

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
Az AD célfelhasználója a konzisztenciaGuid beállításához

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
Nem módosítható azonosító (bájttömb, GUID, GUID karakterlánc vagy Base64 karakterlánc)

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="test-adsyncnetworkport"></a>Teszt-ADSyncNetworkPort

### <a name="synopsis"></a>SZINOPSZIS
{{Töltse ki a Szinopszis}}

### <a name="syntax"></a>SZINTAXIS

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>LEÍRÁS
{{A leírás kitöltése}}

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. példa
```powershell
PS C:\> {{ Add example code here }}
```

{{ Példaleírás hozzáadása itt }}

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-hostname"></a>-állomásnév
{{Fill hostName Description}}

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
{{Fill port description}}

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

## <a name="trace-adsynctoolsadimport"></a>Trace-AdsyncToolsADImport

### <a name="synopsis"></a>SZINOPSZIS
Nyomkövetési fájl létrehozása a fájlból és az AD importálási lépésből

### <a name="syntax"></a>SZINTAXIS

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az AAD Connect AD importálása összes ldap-lekérdezésének nyomon követése egy adott AD vízjel-ellenőrzőpontról (partíciócookie) fut. Az aktuális mappában ".\ADimportTrace_yyyyMMddHHmmss.log" nyomkövetési fájlt hoz létre.

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
{{Fill ADConnectorXML Description}}

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

#### <a name="-dc"></a>-dc
Az AD összekötő exportálásának XML-fájlja

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
Céltartományvezérlő

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
Erdőgyökér DN

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
A nyomon követendő \> AD objektumok típusai * = az összes objektumtípus

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

#### <a name="-adwatermark"></a>-ADvízjel
Ha már fut tartományi rendszergazdaként, nincs szükség AD-hitelesítő adatok megadására.
A vízjel kézi bevitele AZ XML-fájl helyett, pl. $ADwatermark = "TVNEUwMAAAAAXyK9ir1zSAQAAAAAAAAAAA(...)"

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

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

#### <a name="-context"></a>-Kontextus
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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SZINOPSZIS
Frissíti a felhasználókat az új Konzisztenciaguid (ImmutableId)

### <a name="syntax"></a>SZINTAXIS

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Frissíti a felhasználókat a KonzisztenciaGuid jelentésből vett új KonzisztenciaGuid (ImmutableId) értékkel Ez a funkció támogatja a WhatIf kapcsolót Megjegyzés: A KonzisztenciaGuid jelentést tabulátólaliterel kell importálni

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

#### <a name="-distinguishedname"></a>-Megkülönböztető név
Megkülönböztető név

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

#### <a name="-action"></a>-Akció
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
Naplófájlok kimeneti fájlneve

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

#### <a name="-whatif"></a>-Milenne
Bemutatja, mi történne a parancsmag futtatásakor.
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

#### <a name="-confirm"></a>-Confirm
Jóváhagyást kér a parancsmag futtatása előtt.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .
