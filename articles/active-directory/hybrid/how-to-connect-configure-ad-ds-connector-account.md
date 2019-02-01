---
title: 'Azure AD Connect: Az AD DS-összekötő fiók engedélyeinek konfigurálása |} A Microsoft Docs'
description: Ez a dokumentum részletesen bemutatja az AD DS-összekötő fiók konfigurálásához az új ADSyncConfig PowerShell-modullal
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9d58751bc9379d6d9a5a492bee35e349c36087c7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497886"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Az AD DS-összekötő fiók engedélyeinek konfigurálása 

A PowerShell-modul nevű [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) jelent meg a build 1.1.880.0 (2018 augusztus jelent meg), amely segít a megfelelő Active Directory-engedélyek konfigurálása az Azure AD-parancsmagok egy gyűjteményét tartalmazza. Csatlakozás a központi telepítés. 

## <a name="overview"></a>Áttekintés 
A következő PowerShell-parancsmagok segítségével az AD DS-összekötő fiók Active Directory-engedélyek beállítása az egyes szolgáltatásokhoz, az Azure AD Connect engedélyezéséhez kiszűrésére. Esetleges problémák megelőzése érdekében meg kell előkészítenie, Active Directory-engedélyek előre, amikor az Azure AD Connect egyéni tartományi fiókkal az erdőhöz való csatlakozáshoz telepíteni kívánja. Ez a modul ADSyncConfig is használható engedélyek konfigurálása az Azure AD Connect telepítése után.

![](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Az Azure AD Connect Expressz telepítés esetén egy automatikusan létrehozott fiókot (MSOL_nnnnnnnnnn) jön létre az Active Directory minden a szükséges engedélyekkel, így nem kell a ADSyncConfig modult használja a letiltott engedélyek öröklés a szervezeti egységek vagy az adott Azure ad-val szinkronizálni kívánt Active Directory-objektumok. 
 
### <a name="permissions-summary"></a>Engedélyek összegzése 
Az alábbi táblázatban egy AD-objektumok szükséges engedélyek összefoglalása: 

| Szolgáltatás | Engedélyek |
| --- | --- |
| MS-DS-ConsistencyGuid funkció |Írási engedélyekkel az ms-DS-ConsistencyGuid attribútum dokumentált [tervezési alapelvei – az ms-DS-ConsistencyGuid használata sourceanchorként](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Jelszókivonat-szinkronizálás |<li>Címtárváltozások replikálása</li>  <li>Replikálás könyvtár összes változik |
| Hibrid Exchange-telepítés |Írási engedélyekkel a dokumentált attribútumok [Exchange hibrid visszaírási](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) felhasználók, csoportok és partnerek számára. |
| Exchange-levelezés nyilvános mappa |Olvasási engedéllyel a dokumentált attribútumok [Exchange-levelezés nyilvános mappa](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) nyilvános mappákhoz. | 
| Jelszóvisszaíró |Írási engedélyekkel a dokumentált attribútumok [a jelszókezelés első lépései](../authentication/howto-sspr-writeback.md) a felhasználók számára. |
| Eszközvisszaíró |Írási engedélyekkel eszköz objektumok és tárolók dokumentált [eszközvisszaírás](how-to-connect-device-writeback.md). |
| Group writeback (Csoportvisszaíró) |Olvassa el, létrehozási, frissítési és törlési csoport objektumok szinkronizálva a **Office 365-csoportok**.  További információ: [Csoportvisszaírás](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>A ADSyncConfig PowerShell-modullal 
A ADSyncConfig modulhoz szükséges a [távoli kiszolgálófelügyelet eszközei (RSAT) az AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) óta attól függ, az AD DS PowerShell-modult és eszközöket. Az RSAT telepítése az AD DS Szolgáltatáshoz, nyissa meg a "Futtatás mint Administrator" egy Windows PowerShell-ablakot, és hajtsa végre: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurálás](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>A fájl is másolhatja **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** egy olyan tartományvezérlőre, amely már rendelkezik az RSAT, az AD DS telepítve van, és ott a PowerShell-modult használja.

A ADSyncConfig használatának megkezdéséhez szüksége betölteni a modult a Windows PowerShell-ablakban: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Ez a modul beírhatja minden parancsmagok ellenőrzése:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Jelölőnégyzet](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Minden parancsmag ugyanazokat a paramétereket adjon meg az AD DS-összekötő fiók és a egy AdminSDHolder kapcsoló rendelkezik. Az AD DS-összekötő fiókot ad meg, hogy a fiók nevét és a tartományhoz, vagy csak a fiók megadhat megkülönböztető név (DN),

például: 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName ADaccount -ADConnectorAccountDomain Contoso`

Vagy; 

`Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADaccount,OU=AADconnect,DC=Contoso,DC=com'`

Abban az esetben, ha nem szeretné a AdminSDHolder tároló engedélyeit, használja a kapcsolót `-SkipAdminSdHolders`. 

Alapértelmezés szerint a beállított engedélyeket parancsmagok megpróbálja AD DS-engedélyek beállítására, ami azt jelenti, hogy a PowerShell-munkamenetet futtató felhasználó igényel minden egyes tartományhoz, az erdő tartományi rendszergazdai jogosultságok az erdőben lévő minden tartomány gyökeréhez.  Ez a követelmény miatt ajánlott használni az erdő gyökértartományában vállalati rendszergazdaként. Ha az Azure AD Connect üzemelő példány több AD DS-összekötők, azt kell futtassa ugyanezt a parancsmagot minden olyan erdőben, amely egy AD DS-összekötőt. 

Beállíthat engedélyeket adott szervezeti Egységben vagy az AD DS-objektum a paraméter használatával `-ADobjectDN` megkülönböztető nevét a célobjektum követ, ahol szeretné engedélyek beállítása. A cél ADobjectDN használatakor a parancsmag fog engedélyek tohoto objektu nastavit csak, és nem a tartomány gyökerében vagy AdminSDHolder tároló. Ezt a paramétert akkor hasznos, ha rendelkezik bizonyos szervezeti egységek vagy Active Directory tartományi szolgáltatások objektumokat, hogy az engedélyek öröklődésének letiltotta (lásd: keresse meg az AD DS objektum le van tiltva az engedélyek öröklődésének) 

Ezeket a gyakori paramétereket a kivételek a `Set-ADSyncRestrictedPermissions` parancsmag, amely segítségével állítsa be az AD DS-összekötő fiók magát, és a `Set-ADSyncPasswordHashSyncPermissions` óta a Jelszókivonat-szinkronizálás szükséges engedélyek csak állítsa be a tartomány gyökerében, ezért a parancsmag Ez a parancsmag nem tartalmazza a `-ObjectDN` vagy `-SkipAdminSdHolders` paramétereket.

### <a name="determine-your-ad-ds-connector-account"></a>Határozza meg az AD DS-összekötő fiók 
Abban az esetben az Azure AD Connect már telepítve van, és mi az az AD DS-összekötő fiókot az Azure AD Connect jelenleg használatban ellenőrizni kívánja, a parancsmag hajthat végre: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Keresse meg az AD DS-objektumok az engedélyek öröklődésének le van tiltva 
Abban az esetben, ha azt szeretné, ellenőrizze, hogy van-e bármely AD DS objektum le van tiltva az engedélyek öröklődésének, futtathatja: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Alapértelmezés szerint ez a parancsmag csak megkeresi a szervezeti egységek az öröklés letiltva, de megadhat más AD DS objektum osztályok a `-ObjectClass` paramétert, vagy használjon ' *' az összes objektum osztályok, a következő: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Az objektum Active Directory tartományi szolgáltatások engedélyeinek megtekintése 
Az alábbi parancsmagot használhatja a DistinguishedName azáltal, hogy egy Active Directory-objektum jelenleg beállított engedélyek listájának megtekintéséhez: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Az AD DS-összekötő fiók engedélyeinek konfigurálása 
 
### <a name="configure-basic-read-only-permissions"></a>Alapszintű csak olvasható engedélyek konfigurálása 
Bármilyen Azure AD Connect funkciót nem használja az AD DS-összekötő fiókhoz tartozó alapvető csak olvasható engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Ez a parancsmag állítja a következő engedélyekkel: 
 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik| 
|-----|-----|-----|-----|
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |Eszközobjektumok Leszármazottak| 
|Engedélyezés |AD DS-összekötő fiók|Az összes tulajdonság olvasása |A gyermekobjektumokat InetOrgPerson| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |A gyermekobjektumokat számítógép| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |Leszármazott foreignSecurityPrincipal objektumok| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |Leszármazott csoportobjektumokhoz| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |Felhasználói a gyermekobjektumokat| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |Forduljon a gyermekobjektumokat| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-konzisztencia-Guid-engedélyek konfigurálása 
Az ms-Ds-konzisztencia-Guid attribútumot használja forráshorgony-(más néven "Let me a forráshorgony kezelését az Azure" beállítás) az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Vagy; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés|AD DS-összekötő fiók|Olvasási/írási tulajdonság|MS-DS-Consistency-Guid|Felhasználói a gyermekobjektumokat|

### <a name="permissions-for-password-hash-synchronization"></a>Jelszókivonat-szinkronizálási engedélyekkel 
A Jelszókivonat-szinkronizálás használata esetén az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Vagy; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiók |Címtárváltozások replikálása |Ez az objektum csak (tartománygyökér)| 
|Engedélyezés |AD DS-összekötő fiók |Címtárváltozások replikálása összes |Ez az objektum csak (tartománygyökér)| 
  
### <a name="permissions-for-password-writeback"></a>A Jelszóvisszaíró engedélyei 
A Jelszóvisszaíró használata esetén az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiók |Jelszó alaphelyzetbe állítása |Felhasználói a gyermekobjektumokat| 
|Engedélyezés |AD DS-összekötő fiók |Vlastnost lockoutTime írása |Felhasználói a gyermekobjektumokat| 
|Engedélyezés |AD DS-összekötő fiók |Vlastnost pwdLastSet írása |Felhasználói a gyermekobjektumokat| 

### <a name="permissions-for-group-writeback"></a>A csoportok visszaírásához engedélyek 
Csoportvisszaírás használata esetén az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Vagy; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiók |Általános olvasási/írási |Az összes objektum attribútumainak írja be a csoport és alobjektumaihoz| 
|Engedélyezés |AD DS-összekötő fiók |Gyermek-objektum létrehozása/törlése |Az összes objektum attribútumainak írja be a csoport és alobjektumaihoz| 
|Engedélyezés |AD DS-összekötő fiók |Fa objektumok törlése vagy törlése|Az összes objektum attribútumainak írja be a csoport és alobjektumaihoz|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Hibrid Exchange-telepítés vonatkozó engedélyek 
Az Exchange hibrid telepítés használata esetén az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag állítja a következő engedélyekkel:  
 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiók |Olvasási/írási az összes tulajdonság |Felhasználói a gyermekobjektumokat| 
|Engedélyezés |AD DS-összekötő fiók |Olvasási/írási az összes tulajdonság |A gyermekobjektumokat InetOrgPerson| 
|Engedélyezés |AD DS-összekötő fiók |Olvasási/írási az összes tulajdonság |Leszármazott csoportobjektumokhoz| 
|Engedélyezés |AD DS-összekötő fiók |Olvasási/írási az összes tulajdonság |Forduljon a gyermekobjektumokat| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Engedélyek az Exchange-levelezés nyilvános mappái (előzetes verzió) 
Az Exchange-levelezés nyilvános mappái funkció használata az AD DS-összekötő fiókhoz tartozó engedélyek beállításához futtassa: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Vagy; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő fiók |Az összes tulajdonság olvasása |A gyermekobjektumokat PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Az AD DS-összekötő jogosultságok korlátozása fiók 
A PowerShell-szkript fog elősegíti a megerősítését a paraméterként AD-összekötő fiók engedélyeit. Engedélyek szigorítása az alábbi lépésekből áll: 

- A megadott objektum öröklődés letiltása 
- Távolítsa el az adott objektumra, kivéve az adott ÖNKISZOLGÁLÓ hozzáférés-vezérlési bejegyzés vonatkozó összes ACE, szeretnénk az alapértelmezett engedélyek ép esetén, a saját MAGA. 
 
 A paraméter - ADConnectorAccountDN az AD-fiókot, amelynek engedélyeket kell megfeszíteni kell. Ez általában az a MSOL_nnnnnnnnnnnn tartományi fiók, amely az AD DS-összekötő konfigurálva van (lásd a állapítsa meg az AD DS-összekötő fiókot). A - Credential paraméter adja meg a rendszergazdai fiók, amely rendelkezik a szükséges jogosultságokkal a célobjektum AD az Active Directory-engedélyek korlátozása szükség. Ez általában az a vállalati vagy a tartományi rendszergazda.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Példa: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Ez a parancsmag állítja a következő engedélyekkel: 

|Typo |Name (Név) |Hozzáférés |Erre vonatkozik|
|-----|-----|-----|-----| 
|Engedélyezés |RENDSZER |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Domain Admins |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Tartalom listázása |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Olvasási engedélyek |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Tartalom listázása |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Olvasási engedélyek |Ez az objektum 

## <a name="next-steps"></a>További lépések
- [Azure AD Connect: Fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Az Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [ADSyncConfig referencia](reference-connect-adsyncconfig.md)

