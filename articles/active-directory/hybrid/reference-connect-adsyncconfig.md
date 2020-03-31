---
title: 'Azure AD Connect: ADSyncConfig PowerShell-hivatkozás | Microsoft dokumentumok'
description: Ez a dokumentum az ADSyncConfig.psm1 PowerShell modul referenciainformációit tartalmazza.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381195"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig PowerShell-referencia
A következő dokumentáció az Azure AD Connect részét képező ADSyncConfig.psm1 PowerShell-modul referenciainformációit tartalmazza.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorSzámla

### <a name="synopsis"></a>SZINOPSZIS
Az egyes AD-összekötőkben konfigurált fióknév és tartomány lekért

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>LEÍRÁS
Ez a függvény az AAD Connectben található "Get-ADSyncConnector" parancsmag használatával kéri le a kapcsolódási paraméterekből az AD-összekötő(k) fiókját megjelenítő táblát.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-AdsyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SZINOPSZIS
AD-objektumok bekerülése engedélyörökléssel letiltva

### <a name="syntax"></a>SZINTAXIS

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Az AD-ben végzett keresések a SearchBase paraméterből indulnak ki, és az ObjectClass paraméter szerint szűrt összes olyan objektumot visszaadják, amelyeka jelenleg le van tiltva az ACL örökléssel.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>2. PÉLDA
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>3. PÉLDA
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-searchbase"></a>-SearchBase
Az LDAP-lekérdezés SearchBase-je, amely lehet AD tartomány megkülönböztető neve vagy teljes tartományneve

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
A keresendő objektumok osztálya, amely lehet '*' (bármely objektumosztályesetében), "felhasználó", "csoport", "tároló", stb. Alapértelmezés szerint ez a függvény a "organizationalUnit" objektumosztályra fog keresni.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdő és tartomány inicializálása alapvető olvasási engedélyekhez.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncBasicReadPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Tulajdonsághozzáférés olvasása az összes leszármazott számítógép-objektum összes attribútumán
2.
Tulajdonsághozzáférés olvasása az összes leszármazott eszközobjektum összes attribútumán
3.
Tulajdonsághozzáférés olvasása az összes leszármazott foreignsecurityprincipal objektum összes attribútumán
5.
Tulajdonsághozzáférés olvasása az összes leszármazott felhasználói objektum összes attribútumán
6.
Tulajdonsághozzáférés olvasása az összes leszármazott inetorgperson objektum összes attribútumán
7.
Tulajdonsághozzáférés olvasása az összes leszármazott csoportobjektum összes attribútumán
8.
Tulajdonsághozzáférés olvasása az összes leszármazott érintkezési objektum összes attribútumán

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHibridpermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdő és az Exchange Hybrid tartomány szolgáltatás ának inicializálása.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeHybridPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Olvasási/írási tulajdonság-hozzáférés az összes leszármazott felhasználói objektum összes attribútumához
2.
Olvasási/írási tulajdonság-hozzáférés az összes leszármazott inetorgperson objektum összes attribútumához
3.
Olvasási/írási tulajdonság-hozzáférés az összes leszármazott csoportobjektum összes attribútumához
4.
Olvasási/írási tulajdonság-hozzáférés az összes leszármazott érintkezési objektum összes attribútumához

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdő és tartomány inicializálása az Exchange Mail nyilvános mappájához szolgáltatás.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncExchangeMailPublicFolderPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Tulajdonsághozzáférés olvasása az összes leszármazott nyilvános mappa objektum összes attribútumán

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja az Active Directory erdőt és tartományt az mS-DS-ConsistencyGuid szolgáltatáshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncMsDsConsistencyGuidPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Olvasási/írási tulajdonság-hozzáférés az mS-DS-ConsistencyGuid attribútumon az összes leszármazott felhasználói objektumhoz

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SZINOPSZIS
Az Active Directory erdő és tartomány inicializálása a jelszókivonat-szinkronizáláshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordHashSyncPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Címtármódosítások replikálása
2.
A címtár replikálása az összeset módosítja

Ezek az engedélyek az erdő összes tartományához tartoznak.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által a címtárban lévő objektumok kezeléséhez használt Active Directory-fiók neve.

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
Az Azure AD Connect Sync által a címtárban lévő objektumok kezeléséhez használt Active Directory-fiók tartománya.

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
Az Azure AD Connect Sync által a címtárban lévő objektumok kezeléséhez használt Active Directory-fiók Megkülönböztető neve.

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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackEngedélyek

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja az Active Directory erdőt és tartományt az Azure AD-ből történő jelszó-visszaíráshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncPasswordWritebackPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Jelszó alaphelyzetbe állítása leszármazott felhasználói objektumokon
2.
Tulajdonsághozzáférés írása lockoutTime attribútumon az összes leszármazott felhasználói objektumhoz
3.
Tulajdonsághozzáférés írása a pwdLastSet attribútumra az összes leszármazott felhasználói objektumhoz

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SZINOPSZIS
Szigorítsa az engedélyeket egy olyan AD-objektumon, amely egyébként nem szerepel az AD védett biztonsági csoportjában.
Egy tipikus példa az AAD Connect által automatikusan létrehozott AD Connect fiók (MSOL).
Ez a fiók replikálási engedélyekkel rendelkezik az összes tartományban, azonban könnyen felsérülhet, mivel nem védett.

### <a name="syntax"></a>SZINTAXIS

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncRestrictedPermissions függvény szigorítja a fiók által biztosított engedélyeket.
Az engedélyek szigorítása a következő lépéseket foglalja magában:
1.
Öröklődés letiltása a megadott objektumon
2.
Távolítsa el az adott objektum összes aces-ét, kivéve a SELF-re jellemző ACEs-eket.
Azt akarjuk, hogy az alapértelmezett engedélyek ép, amikor a SELF.
3.
Rendelje hozzá a következő speciális engedélyeket:

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
Annak az Active Directory-fióknak a megkülönböztető neve, amelynek engedélyeit meg kell szigorítani.
Ez általában a MSOL_nnnnnnnnnn-fiók vagy az AD-összekötőben konfigurált egyéni tartományi fiók.

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
Rendszergazdai hitelesítő adatok, amely rendelkezik az ADConnectorAccountDN-fiók engedélyeinek korlátozásához szükséges jogosultságokkal. Ez általában a vállalati vagy tartományi rendszergazda. A fiókfelkutatási hibák elkerülése érdekében használja a rendszergazdai fiók teljesen minősített tartománynevét.
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
A DisableCredentialValidation használat esetén a függvény nem ellenőrzi, hogy a -Credential címben megadott hitelesítő adatok érvényesek-e az AD-ben, és hogy a megadott fiók rendelkezik-e az ADConnectorAccountDN-fiók engedélyeinek korlátozásához szükséges jogosultságokkal.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackEngedélyek

### <a name="synopsis"></a>SZINOPSZIS
Inicializálja az Active Directory erdőt és tartományt az Azure AD-ből történő csoport-visszaíráshoz.

### <a name="syntax"></a>SZINTAXIS

#### <a name="userdomain"></a>UserDomain (Felhasználói tartomány)
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Megkülönböztető név
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
A Set-ADSyncUnifiedGroupWritebackPermissions függvény megadja a szükséges engedélyeket az AD szinkronizálási fiókhoz, amelyek a következőket tartalmazzák:
1.
Általános olvasási/írási, Törlési, Fa törlése és Gyermek törlése az összes csoportobjektum-típushoz és alobjektumhoz

Ezek az engedélyek az erdő összes tartományára vonatkoznak.
Tetszés szerint megadhat egy Megkülönböztető nevet az ADobjectDN paraméterben, hogy ezeket az engedélyeket csak az adott AD objektumra állítsa be (beleértve az alobjektumok öröklését is).
Ebben az esetben az ADobjectDN lesz a csoportvisszaírással kapcsolatban a GroupWriteback funkcióval kapcsolatban kívánt tároló megkülönböztető neve.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>2. PÉLDA
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

#### <a name="-adconnectoraccountname"></a>-ADConnectorszámlanév
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók neve a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók tartománya a címtárban lévő objektumok kezeléséhez.

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
Az Azure AD Connect Sync által használt vagy használni kívánt Active Directory-fiók Megkülönböztetőneve a címtárban lévő objektumok kezeléséhez.

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
A cél AD objektum megkülönböztető neve az engedélyek beállításához (nem kötelező)

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
Választható paraméter, amely jelzi, hogy az AdminSDHolder tárolót nem kell-e frissíteni ezekkel az engedélyekkel

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADobjectPermissions

### <a name="synopsis"></a>SZINOPSZIS
Egy megadott AD objektum engedélyeit jeleníti meg.

### <a name="syntax"></a>SZINTAXIS

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS
Ez a függvény az -ADobjectDN paraméterben megadott adott AD-objektumhoz jelenleg beállított összes AD-engedélyt adja vissza.
Az ADobjectDN-t DistinguishedName formátumban kell megadni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN Description}}

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .
