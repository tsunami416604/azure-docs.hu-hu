---
title: 'Azure AD Connect: AD DS-összekötőfiók-engedélyek konfigurálása | Microsoft dokumentumok'
description: Ez a dokumentum részletezi, hogyan konfigurálható az AD DS-összekötő fiók az új ADSyncConfig PowerShell-modullal
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515816"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS-összekötőfiók-engedélyek konfigurálása 

Az [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) nevű PowerShell-modul az 1.1.880.0 buildtel (2018 augusztusában jelent meg), amely parancsmagok gyűjteményét tartalmazza az Azure AD Connect telepítéséhez szükséges megfelelő Active Directory-engedélyek konfigurálásához. 

## <a name="overview"></a>Áttekintés 
A következő PowerShell-parancsmagok segítségével az Active Directory-engedélyek az Active Directory-összekötő fiók, minden egyes funkció, amely az Azure AD Connect engedélyezéséhez kiválasztott. A problémák elkerülése érdekében elő kell készítenie az Active Directory-engedélyeket előre, amikor egyéni tartományi fiókkal szeretné telepíteni az Azure AD Connectet az erdőhöz való csatlakozáshoz. Ez az ADSyncConfig modul az Azure AD Connect telepítése után is használható az engedélyek konfigurálására.

![a hirdetési ds-fiók áttekintése](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Az Azure AD Connect Express telepítéséhez automatikusan generált fiók (MSOL_nnnnnnnnnn) jön létre az Active Directoryban az összes szükséges engedéllyel, így nem kell használnia ezt az ADSyncConfig modult, kivéve, ha blokkolta az engedélyeket öröklődés szervezeti egységeken vagy adott Active Directory-objektumokon, amelyeket szinkronizálni szeretne az Azure AD-vel. 
 
### <a name="permissions-summary"></a>Engedélyek összegzése 
Az alábbi táblázat az AD-objektumokhoz szükséges engedélyek összegzését tartalmazza: 

| Szolgáltatás | Engedélyek |
| --- | --- |
| ms-DS-ConsistencyGuid szolgáltatás |Olvasási és írási engedélyek az ms-DS-ConsistencyGuid attribútum dokumentált [Tervezési fogalmak - Az ms-DS-ConsistencyGuid használata sourceAnchor .](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) | 
| Jelszókivonat-szinkronizálás |<li>Címtármódosítások replikálása</li>  <li>Könyvtármódosítás-módosítások replikálása |
| Hibrid Exchange-telepítés |Olvasási és írási engedélyek az [Exchange hibrid visszaírásában](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dokumentált attribútumokhoz felhasználók, csoportok és kapcsolattartók számára. |
| Exchange Mail nyilvános mappa |A nyilvános mappák Exchange [Mail nyilvános mappájában](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dokumentált attribútumok olvasása. | 
| Jelszóvisszaíró |Olvasási és írási engedélyek a felhasználók [jelszókezelésének első lépései](../authentication/howto-sspr-writeback.md) című részben dokumentált attribútumokhoz. |
| Eszközvisszaíró |Olvasási és írási engedélyek az [eszköz visszaírásában](how-to-connect-device-writeback.md)dokumentált eszközobjektumokhoz és tárolókhoz. |
| Group writeback (Csoportvisszaíró) |A szinkronizált **Office 365-csoportok**csoportobjektumainak olvasása, létrehozása, frissítése és törlése .  További információ: [Group Writeback](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Az ADSyncConfig PowerShell-modul használata 
Az ADSyncConfig modulhoz az [AD DS-hez](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) szükséges a Távoli kiszolgáló felügyeleti eszközei (RSAT), mivel az az AD DS PowerShell-modultól és -eszközöktől függ. Az RSAT a DD DS-hez telepítéséhez nyisson meg egy Windows PowerShell-ablakot a "Futtatás rendszergazdaként" ablakkal, és hajtsa végre a következőket: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurálás](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>A **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** fájlt is átmásolhatja egy olyan tartományvezérlőre, amelyen már telepítve van az RSAT for AD DS szolgáltatás, és onnan használhatja ezt a PowerShell-modult.

Az ADSyncConfig használatának megkezdéséhez be kell töltenie a modult egy Windows PowerShell-ablakba: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

A modulban található összes parancsmag ellenőrzéséhez írja be a következőt:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Jelölőnégyzet](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Minden parancsmag ugyanazokat a paramétereket adja meg az AD DS-összekötő fiók és egy AdminSDHolder kapcsoló bemenetéhez. Az AD DS-összekötő fiók megadásához megadhatja a fiók nevét és tartományát, vagy csak a megkülönböztető nevet (DN),

példa:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Vagy;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Győződjön meg `<ADAccountName>` `<ADDomainName>` róla, hogy cserélje ki a, és `<ADAccountDN>` a megfelelő értékeket a környezetben.

Abban az esetben, ha nem szeretné módosítani az AdminSDHolder `-SkipAdminSdHolders`tároló engedélyeit, használja a kapcsolót. 

Alapértelmezés szerint az összes beállított engedélyparancsma megpróbálja beállítani az AD DS-engedélyeket az erdő egyes tartományainak gyökerénél, ami azt jelenti, hogy a PowerShell-munkamenetet futtató felhasználónak tartományi rendszergazdai jogosultságokra van szüksége az erdő minden tartományában.  E követelmény miatt ajánlott az erdő gyökérből származó vállalati rendszergazdát használni. Ha az Azure AD Connect központi telepítése több AD DS-összekötővel rendelkezik, akkor ugyanazt a parancsmamot kell futtatnia minden olyan erdőn, amely rendelkezik egy AD DS-összekötővel. 

Egy adott szervezeti egység- vagy AD DS-objektumra `-ADobjectDN` vonatkozó engedélyeket a célobjektum dn-je által követett paraméter rel is megadhat, ahol engedélyeket szeretne beállítani. Cél ADobjectDN használataesetén a parancsmag csak erre az objektumra állítja be az engedélyeket, a tartománygyökér- vagy AdminSDHolder tárolón nem. Ez a paraméter akkor lehet hasznos, ha bizonyos olyan oUs- vagy AD DS-objektumokkal rendelkezik, amelyek engedélyöröklése le van tiltva (lásd: Az AD DS-objektumok megkeresése engedélyörökléssel letiltva) 

Ezek alól a gyakori paraméterek `Set-ADSyncRestrictedPermissions` alól kivételt képeznek a parancsmag, amely az AD `Set-ADSyncPasswordHashSyncPermissions` DS-összekötő fiók engedélyeinek beállítására szolgál, és a parancsmag, mivel a `-ObjectDN` jelszókivonat-szinkronizáláshoz szükséges engedélyek csak a tartomány gyökérén belül vannak beállítva, ezért ez a parancsmag nem tartalmazza a vagy `-SkipAdminSdHolders` a paramétereket.

### <a name="determine-your-ad-ds-connector-account"></a>Az AD DS-összekötőfiók meghatározása 
Abban az esetben, ha az Azure AD Connect már telepítve van, és szeretné ellenőrizni, hogy mi az Azure AD Connect által jelenleg használt AD DS-összekötő fiók, végrehajthatja a parancsmast: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Az Engedélyd DS-objektumok megkeresése letiltott engedélyörökléssel 
Abban az esetben, ha ellenőrizni szeretné, hogy van-e olyan AD DS-objektum, amelynek engedélyöröklődése le van tiltva, futtathatja a következőket: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Alapértelmezés szerint ez a parancsmag csak letiltott öröklődéssel rendelkező ous-okat keres, `-ObjectClass` de más AD DS-objektumosztályokat is megadhat a paraméterben, vagy használhatja a "*" értéket az összes objektumosztályhoz, az alábbiak szerint: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Objektum AD DS-engedélyeinek megtekintése 
Az alábbi parancsmag segítségével megtekintheti az Active Directory-objektumokon jelenleg beállított engedélyek listáját a Megkülönböztető név megadásával: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Az AD DS Connector-fiók engedélyeinek konfigurálása 
 
### <a name="configure-basic-read-only-permissions"></a>Alapvető írásvédett engedélyek konfigurálása 
Ha alapvető írásvédett engedélyeket szeretne beállítani az AD DS-összekötő fiókhoz, ha nem használ semmilyen Azure AD Connect funkciót, futtassa a következőket: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


vagy az; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Ez a parancsmag a következő engedélyeket állítja be: 
 

|Típus |Név |Hozzáférés |Érvényesség| 
|-----|-----|-----|-----|
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott eszköz objektumok| 
|Engedélyezés |AD DS-összekötő-fiók|Az összes tulajdonság olvasása |Leszármazott InetOrgPerson objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott számítógép-objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott foreignSecurityPrincipal objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott csoport objektumai| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Leszármazott kapcsolattartó objektumok| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-Konzisztencia-Guid engedélyek konfigurálása 
Ha az Ms-Ds-Consistency-Guid attribútum forráshorgonyként való használatakor az AD DS-összekötő fiók engedélyeit szeretné beállítani (más néven "Hagyja, hogy az Azure kezelje a forráshorgonyt számomra" beállítást), futtassa a következőket: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

vagy az; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés|AD DS-összekötő-fiók|Olvasási/írási tulajdonság|Leszármazott felhasználói objektumok|

### <a name="permissions-for-password-hash-synchronization"></a>A jelszókivonat-szinkronizálás engedélyei 
Ha a jelszókivonat-szinkronizálás használatakor be szeretné állítani az AD DS-összekötőfiók engedélyeit, futtassa a következőket: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


vagy az; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő-fiók |Címtármódosítások replikálása |Csak ez az objektum (tartománygyökér)| 
|Engedélyezés |AD DS-összekötő-fiók |A címtár replikálása az összeset módosítja |Csak ez az objektum (tartománygyökér)| 
  
### <a name="permissions-for-password-writeback"></a>A jelszó-visszaírás engedélyei 
Ha a Jelszó-visszaírás használatakor be szeretné állítani az AD DS-összekötő fiók engedélyeit, futtassa a következőket: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


vagy az;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő-fiók |Jelszó alaphelyzetbe állítása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Tulajdonságzárolás írásaTime |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |PwdLastSet tulajdonság írása |Leszármazott felhasználói objektumok| 

### <a name="permissions-for-group-writeback"></a>A csoportvisszaírás engedélyei 
Az AD DS Connector-fiók engedélyeinek beállítása a csoportvisszaírás használatakor futtassa a következőket: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
vagy az; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő-fiók |Általános olvasás/írás |Az objektumtípus-csoport és az alobjektumok összes attribútuma| 
|Engedélyezés |AD DS-összekötő-fiók |Gyermekobjektum létrehozása/törlése |Az objektumtípus-csoport és az alobjektumok összes attribútuma| 
|Engedélyezés |AD DS-összekötő-fiók |Faobjektumok törlése/törlése|Az objektumtípus-csoport és az alobjektumok összes attribútuma|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Engedélyek az Exchange hibrid telepítéshez 
Az AD DS Connector-fiók engedélyeinek beállításához az Exchange Hybrid központi telepítése kor futtassa a következőket: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


vagy az; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Ez a parancsmag a következő engedélyeket állítja be:  
 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása/írása |Leszármazott felhasználói objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása/írása |Leszármazott InetOrgPerson objektumok| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása/írása |Leszármazott csoport objektumai| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása/írása |Leszármazott kapcsolattartó objektumok| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Engedélyek az Exchange Mail nyilvános mappáihoz (előzetes verzió) 
Ha az Exchange Mail nyilvános mappák szolgáltatáshasználata kor szeretné beállítani az AD DS Connector-fiók engedélyeit, futtassa a következő parancsot: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


vagy az; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |AD DS-összekötő-fiók |Az összes tulajdonság olvasása |Levantsi nyilvános mappa objektumai| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Engedélyek korlátozása az AD DS-összekötő fiókhoz 
Ez a PowerShell-parancsfájl szigorítja a paraméterként megadott AD-összekötő fiók engedélyeit. Az engedélyek szigorítása a következő lépéseket foglalja magában: 

- Öröklődés letiltása a megadott objektumon 
- Távolítsa el az összes ACEs az adott objektum, kivéve ACEs kifejezetten SELF, mint azt akarjuk, hogy az alapértelmezett engedélyek ép, amikor a SELF. 
 
  Az -ADConnectorAccountDN paraméter az a AD-fiók, amelynek engedélyeit meg kell szigorítani. Ez általában az AD DS-összekötőben konfigurált MSOL_nnnnnnnnnnnn tartományi fiók (lásd: Az AD DS-összekötőfiók meghatározása). A -Credential paraméter szükséges a rendszergazdai fiók megadásához, amely rendelkezik a cél AD objektum Active Directory-engedélyeinek korlátozásához szükséges jogosultságokkal. Ez általában a vállalati vagy tartományi rendszergazda.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Például: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Ez a parancsmag a következő engedélyeket állítja be: 

|Típus |Név |Hozzáférés |Érvényesség|
|-----|-----|-----|-----| 
|Engedélyezés |RENDSZER |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Tartományi rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Rendszergazdák |Teljes hozzáférés |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Lista tartalma |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Vállalati tartományvezérlők |Olvasási engedélyek |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Lista tartalma |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Az összes tulajdonság olvasása |Ez az objektum 
|Engedélyezés |Hitelesített felhasználók |Olvasási engedélyek |Ez az objektum 

## <a name="next-steps"></a>Következő lépések
- [Azure AD Connect: fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [Az ADSyncConfig referenciája](reference-connect-adsyncconfig.md)

