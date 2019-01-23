---
title: 'Azure AD Connect: ADSyncConfig PowerShell-referencia |} A Microsoft Docs'
description: Ebben a dokumentumban részletes információ a ADSyncConfig.psm1 PowerShell-modult.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 015823ee9674c2c704cdea13e44ced06e5b9644c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463524"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect:  ADSyncConfig PowerShell-referencia
A következő dokumentáció arról nyújt a ADSyncConfig.psm1 PowerShell-modult, amely tartalmazza az Azure AD Connecttel kapcsolatos referenciainformációk.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SZINOPSZIS
Lekéri a fiók neve és a tartományhoz, amely minden AD-összekötő konfigurálva van

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>LEÍRÁS
Ez a funkció a 'Get-ADSyncConnector' parancsmagot, amely szerepel az AAD Connect kapcsolódási paraméterek lekérése a AD összekötő(k) fiókot megjelenítő táblázat.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SZINOPSZIS
Az engedélyek öröklődésének le van tiltva az AD-objektumok beolvasása

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az ad-ben a SearchBase paraméter kezdve átvizsgálja, és adja vissza, ObjectClass paraméter alapján szűrt rendelkező objektumok az ACL-öröklés jelenleg le van tiltva.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase "Contoso"

#### <a name="example-2"></a>EXAMPLE 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase "Contoso" - ObjectClass "felhasználó"

#### <a name="example-3"></a>3. PÉLDA
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled - SearchBase OU AzureAD, DC = Contoso, DC = com - ObjectClass = ' *'

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-searchbase"></a>-SearchBase
Az LDAP-lekérdezést, amely egy AD-tartomány DistinguishedName vagy teljes tartománynév lehet a SearchBase

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

#### <a name="-objectclass"></a>-ObjectClass
Az osztály az objektumok keresése, amely lehet "*" (az összes objektum osztálya), "felhasználó", "csoport", "container", stb. Alapértelmezés szerint ez a függvény meg fogja keresni objektumosztály "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdőben és tartományban, alapvető olvasási engedélyek inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncBasicReadPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Olvasási hozzáféréshez az összes attribútum minden gyermekobjektumra számítógép-objektumok
2.
Az összes leszármazott eszköz objektum tulajdonság olvasása az összes attribútum hozzáférést
3.
Az összes leszármazott foreignsecurityprincipal objektum tulajdonság olvasása az összes attribútum hozzáférést
5.
Az összes leszármazott felhasználó objektum tulajdonság olvasása az összes attribútum hozzáférést
6.
Az összes leszármazott inetorgperson objektum tulajdonság olvasása az összes attribútum hozzáférést
7.
Minden gyermekobjektumra csoport objektumainak tulajdonság olvasása az összes attribútum hozzáférést
8.
Kapcsolattartási gyermekobjektumok tulajdonság olvasása az összes attribútum hozzáférést

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory-erdő és a hibrid Exchange szolgáltatáshoz tartozó tartományi inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeHybridPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Olvasási/írási hozzáféréshez az összes attribútum az összes leszármazott felhasználó objektumok
2.
Olvasási/írási hozzáféréshez az összes attribútum az összes leszármazott inetorgperson objektum
3.
Olvasási/írási hozzáféréshez az összes attribútum minden gyermekobjektumra csoport objektum
4.
Tulajdonság olvasási/írási hozzáférés az összes attribútum kapcsolattartási gyermekobjektumok

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory-erdő és tartomány Exchange-levelezés nyilvános mappa funkció inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeMailPublicFolderPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Az összes leszármazott publicfolder objektum tulajdonság olvasása az összes attribútum hozzáférést

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdőben és tartományban, mS-DS-ConsistencyGuid funkció inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncMsDsConsistencyGuidPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Olvasási/írási hozzáféréshez az mS-DS-ConsistencyGuid attribútum az összes leszármazott felhasználó objektum

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory-erdőben és tartományban a Jelszókivonat-szinkronizálás inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordHashSyncPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Címtárváltozások replikálása
2.
Címtárváltozások replikálása összes

Ezeket az engedélyeket az erdőben lévő összes tartományban vannak megadva.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók, amely a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdőben és tartományban, a jelszóvisszaírást az Azure ad-ből inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordWritebackPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Jelszó alaphelyzetbe állítása a leszármazott felhasználó objektumai
2.
Az írási hozzáféréshez elérhető összes leszármazott felhasználó objektum attribútuma lockoutTime
3.
Az írási hozzáféréshez elérhető összes leszármazott felhasználó objektum attribútuma pwdLastSet

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SZINOPSZIS
Ellenkező esetben nem szereplő bármely AD védett biztonsági csoporthoz AD objektumra vonatkozó engedélyek elősegíti megerősítését.
Egy tipikus példája az AD Connect-fiók (MSOL) automatikusan létrehozza az AAD Connect.
Ez a fiók replikálása engedélyekkel rendelkezik az összes tartományt, azonban is támadásoknak kitett szerint nem védett.

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncRestrictedPermissions függvény fog elősegíti a megerősítését engedélyek fájlmegosztásain a megadott fiók.
Engedélyek szigorítása az alábbi lépésekből áll:
1.
A megadott objektum öröklődés letiltása
2.
Távolítsa el az adott objektumra, kivéve az adott ÖNKISZOLGÁLÓ hozzáférés-vezérlési bejegyzés vonatkozó összes ACE.
Szeretnénk az alapértelmezett engedélyek ép esetén, a saját MAGA.
3.
Ezek az engedélyek hozzárendelése:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiókot, amelynek engedélyeket kell megfeszíteni kell a DistinguishedName.
Ez általában az a MSOL_nnnnnnnnnn vagy egyéni tartományi fiók, amely konfigurálva van az AD Connectoron.

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

#### <a name="-credential"></a>-Credential
Rendszergazdai hitelesítő adatok, amely rendelkezik a szükséges jogosultságokkal a ADConnectorAccountDN fiók az engedélyek korlátozása. Ez általában az a vállalati vagy tartományi rendszergazdával. A rendszergazdai fiók a teljesen minősített tartománynevét használja a fiók keresési hibák elkerülése érdekében.
Példa: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
DisableCredentialValidation használata esetén a függvény nem ellenőrzi a megadott hitelesítő adatok a - Credential-e érvényes AD-ben, és ha a megadott fiók rendelkezik-e a szükséges jogosultságokkal a ADConnectorAccountDN fiók az engedélyek korlátozása.

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory-erdő és a csoportok visszaírásához az Azure AD tartományi inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncUnifiedGroupWritebackPermissions funkciót biztosít az AD szinkronizálási szolgáltatásfiók szükséges többek között a következő engedélyekkel:
1.
Általános olvasási/írási, törlési, fa törlése és Create\Delete gyermek minden objektumtípus és alobjektumaihoz

Ezeket az engedélyeket az erdőben lévő összes tartományban érvényesek.
Opcionálisan megadhat egy DistinguishedName ADobjectDN paraméterben ezek az engedélyek beállítása az AD-objektum csak (beleértve az öröklési sub objektumok).
Ebben az esetben ADobjectDN lesz, amely megfelel a GroupWriteback szolgáltatás összekapcsolása a tároló megkülönböztető nevét.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>EXAMPLE 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>3. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>4. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó neve.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Az Active Directory-fiókot, vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó tartományát.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Az Active Directory-fiók vagy a címtárban található objektumokhoz kezelése az Azure AD Connect szinkronizálási szolgáltatás által használandó a DistinguishedName.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
A célobjektum AD DistinguishedName engedélyek beállítása (nem kötelező)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Nem kötelező paraméter jelzi, ha az AdminSDHolder tároló nem frissül az ezeket az engedélyeket

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

#### <a name="-confirm"></a>-Confirm
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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SZINOPSZIS
Megjeleníti egy megadott AD-objektum engedélyekkel.

### <a name="syntax"></a>SZINTAXIS

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a funkció retuns az AD-engedélyekről jelenleg beállítása egy adott AD-objektum, paraméterben megadott – ADobjectDN.
A ADobjectDN DistinguishedName formátumban kell megadni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN leírás}}

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

#### <a name="commonparameters"></a>CommonParameters
Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).