---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálása | Microsoft Docs
description: A hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálásának ismertetése
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: a3c35057af883eb790c44b3547072031eaf4ad2f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962010"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök manuális konfigurálása 

Az Azure Active Directory (Azure AD) eszközfelügyeletével biztosítható, hogy a felhasználók csak olyan eszközökről férjenek hozzá a vállalat erőforrásaihoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. További részletek: [Az Azure Active Directory eszközkezelésének alapjai](overview.md).


> [!TIP]
> Ha lehetősége van az Azure AD Connect használatára, tekintse meg a [Válasszon forgatókönyvet](hybrid-azuread-join-plan.md#select-your-scenario) című részt. Az Azure AD Connect használatával lényegesen egyszerűbbé válik a hibrid Azure AD-csatlakozás konfigurálása.



Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Ezen oktatóanyagból megtudhatja, hogyan konfigurálhatja manuálisan az eszközök hibrid Azure AD-csatlakozását.

> [!div class="checklist"]
> * Előfeltételek
> * Konfigurációs lépések
> * A szolgáltatáskapcsolódási pont konfigurálása
> * Jogcímek kiállításának beállítása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * A megvalósítás hibaelhárítása
 




## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy Ön járatos az alábbi témakörökben:
    
-  [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)
    
-  [A hibrid Azure Active Directory-csatlakozás megvalósításának tervezése](hybrid-azuread-join-plan.md)

-  [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)


Mielőtt engedélyezi a vállalatán belül a hibrid Azure AD-csatlakoztatott eszközöket, a következő feltételeknek kell teljesülnie:

- Eszközein az Azure AD Connect legújabb verziója fut.

- Az Azure AD Connect szinkronizálta a számítógép-objektumokat az Azure AD-hez hibrid módon csatlakoztatni kívánt eszközök közül. Ha a számítógép-objektumok bizonyos szervezeti egységekhez (OU) tartoznak, akkor az OU-k szinkronizálását is konfigurálni kell az Azure AD Connectben.

  

Azure AD Connect:

- Társítja a helyszíni Active Directory (AD) számítógépfiókját és az Azure AD-ban lévő eszköz-objektumot. 
- Más, eszközökhöz kapcsolódó funkciókat is engedélyez, mint például a Vállalati Windows Hellót.

Győződjön meg arról, hogy a következő URL-ek elérhetők a vállalati hálózat számítógépeiről a számítógépek Azure AD-be való regisztrálásához:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Engedélyezés
- https://device.login.microsoftonline.com

- A szervezet biztonsági jegykiadó szolgáltatása (összevont tartományok esetén)

Ellenőrizze, hogy a felhasználó helyi intranet-beállításai tartalmazzák a vállalat biztonsági jegykiadó szolgáltatását (összevont tartományok esetén).

Ha a vállalata közvetlen egyszeri bejelentkezés használatát tervezi, a következő URL-eknek elérhetőknek kell lenniük a vállalati számítógépekről, továbbá a felhasználó helyi intranetes zónájához is hozzá kell adni őket:

- https://autologon.microsoftazuread-sso.com

- Emellett a következő beállításokat kell engedélyezni a felhasználó intranetes zónájában: „Az állapotsor szkriptekkel való frissítése.”

Ha a vállalata felügyelt (nem összevont) beállításokat használ a helyi AD-hez, és nem használja az ADFS-t az Azure AD-val való összevonáshoz, akkor a hibrid Azure AD-csatlakozás a Windows 10 rendszeren az AD-ban lévő számítógép-objektumok Azure AD-val való szinkronizálására támaszkodik. Győződjön meg arról, hogy minden olyan Szervezeti egység (OU) számára engedélyezve legyen a szinkronizáció az Azure AD Connect szinkronizálási konfigurációban, amely az Azure AD-hez hibrid módon csatlakoztatni kívánt számítógép-objektumot tartalmaz.

Ha a vállalatának az internethez való hozzáféréshez kimenő proxyra van szüksége, az olyan eszközök esetében, amelyeken a Windows 10 1703-as vagy annál régebbi verziója fut, implementálni kell az Automatikus webproxykeresőt (WPAD), hogy a Windows 10 rendszerű számítógépek regisztrálhassanak az Azure AD-be. 

A Windows 10 1803-as verziójától kezdve, ha egy AD FS-t használó összevont tartományban egy eszköz hibrid Azure AD-csatlakozásra tett kísérlete meghiúsul, és az Azure AD Connect úgy van konfigurálva, hogy a számítógépet/eszközobjektumokat szinkronizálja az Azure AD-vel, akkor az eszköz a szinkronizált számítógéppel/eszközzel próbálja majd befejezni a hibrid Azure AD-csatlakozást.

## <a name="configuration-steps"></a>Konfigurációs lépések

A hibrid Azure AD-csatlakoztatott eszközöket számos típusú Windows-alapú eszközplatformhoz konfigurálhatja. Ez a témakör az összes jellemző konfigurációs forgatókönyv szükséges lépéseit tartalmazza:  

Az alábbi táblázatban áttekintheti a forgatókönyvéhez szükséges lépéseket:  



| Lépések                                      | Jelenlegi Windows és a jelszókivonat szinkronizálása | Jelenlegi Windows és összevonás | Korábbi verziójú Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| A szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| Jogcímek kiállításának beállítása           |                                        | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| Nem Windows 10 operációs rendszerű eszközök engedélyezése      |                                        |                                | ![Jelölőnégyzet][1]        |
| Csatlakoztatott eszközök ellenőrzése          | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |



## <a name="configure-service-connection-point"></a>A szolgáltatáskapcsolódási pont konfigurálása

A szolgáltatáskapcsolódási pont (SCP) objektumát az eszközök a regisztráció során az Azure AD-bérlő adatainak felderítésére használják. A helyszíni Active Directoryban (AD) a hibrid Azure AD-csatlakoztatott eszközök SCP-objektumának a konfigurációs névhasználati környezet partíciójában kell léteznie a számítógép erdőjében. Erdőnként egy konfigurációs névhasználati környezet létezik. Egy többerdős Active Directory-konfigurációban a szolgáltatáskapcsolódási pontnak minden olyan erdőben léteznie kell, amely tartományokhoz csatlakoztatott számítógépeket tartalmaz.

Használja a [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) parancsmagot az erdő konfigurációs névhasználati környezetének lekéréséhez.  

A *fabrikam.com* Active Directory-tartománynevű erdő konfigurációs névhasználati környezete a következő:

`CN=Configuration,DC=fabrikam,DC=com`

A tartományokhoz csatlakoztatott eszközök automatikus regisztrációjának SCP-objektuma a következő helyen található a saját erdőjében:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Az Azure AD Connect üzembehelyezési módjától függően előfordulhat, hogy az SCP-objektum már konfigurálva van.
Az alábbi Windows PowerShell-szkript használatával ellenőrizheti az objektum meglétét és kérheti le a felderítési értékeket: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

A **$scp.Keywords** kimenet megmutatja az Azure AD-bérlő adatait, például:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Ha a szolgáltatáskapcsolódási pont nem létezik, a létrehozásához futtassa az `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálóján. A parancsmag futtatásához vállalati rendszergazdai hitelesítő adatok szükségesek.  
A parancsmag:

- Létrehozza a szolgáltatáskapcsolódási pontot abban az Active Directory-erdőben, amihez az Azure AD Connect csatlakoztatva van. 
- Az `AdConnectorAccount` paraméter megadása szükséges hozzá. Ez az Azure AD Connectben az Active Directory-összekötőként konfigurált fiók. 


Az alábbi szkriptben egy példa látható a parancsmag használatára. A szkript `$aadAdminCred = Get-Credential` eleme esetében egy felhasználónév megadása szükséges. A felhasználónevet egyszerű felhasználónév (UPN) formátumban kell megadni (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Az `Initialize-ADSyncDomainJoinedComputerSync` parancsmag:

- Az Active Directory PowerShell modult és AD DS-eszközöket használ, amelyek tartományvezérlőn futtatott Active Directory webszolgáltatásokra támaszkodnak. Az Active Directory webszolgáltatások Windows Server 2008 R2 vagy újabb rendszerű tartományvezérlőkön támogatott.
- Kizárólag az **MSOnline PowerShell modul 1.1.166.0-s verziója** támogatja. A modul letöltéséhez kövesse ezt a [hivatkozást](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Ha az AD DS-eszközök nincsenek telepítve, az `Initialize-ADSyncDomainJoinedComputerSync` sikertelen lesz.  Az AD DS-eszközöket a Kiszolgálókezelő Szolgáltatások - Távoli kiszolgálófelügyelet eszközei - Szerepkör-felügyeleti eszközök területén telepítheti.

A Windows Server 2008 vagy régebbi verzióját futtató tartományvezérlők esetében használja az alábbi szkriptet szolgáltatáskapcsolódási pont létrehozásához.

Egy többerdős konfigurációban használja a következő szkriptet a szolgáltatáskapcsolódási pontok létrehozásához minden olyan erdőben, amelyben számítógép található:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

A fenti szkriptben a

- `$verifiedDomain = "contoso.com"` egy helyőrző, amelyet az egyik ellenőrzött Azure AD-tartománynevével kell helyettesítenie. A használatához az adott tartomány tulajdonosának kell lennie.

Az ellenőrzött tartománynevekkel kapcsolatos további részleteket az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory-domains-add-azure-portal.md) című részben találja.  
Az ellenőrzött vállalati tartományok listáját a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) parancsmaggal kaphatja meg. 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Jogcímek kiállításának beállítása

Összevont Azure AD-konfiguráció esetén az eszközök az Active Directory összevonási szolgáltatásokra (AD FS) vagy harmadik fél helyszíni összevonási szolgáltatásaira támaszkodnak az Azure AD-val való hitelesítéshez. Az eszközök a hitelesítés révén kapnak hozzáférési jogkivonatokat, amelyekkel regisztrálhatnak az Azure Active Directory eszközregisztrációs szolgáltatásába (Azure DRS).

A jelenleg Windows-eszközök az integrált Windows-hitelesítés használatával végzik el a hitelesítést egy aktív WS-Trust végponttal (1.3-as vagy 2005-ös verzió), amelyet a helyszíni összevonási szolgáltatás futtat.

> [!NOTE]
> AD FS használatakor a következők egyikének engedélyezve kell lennie: **adfs/services/trust/13/windowstransport** vagy **adfs/services/trust/2005/windowstransport**. Ha a webes hitelesítési proxyt használja, győződjön meg róla, hogy a végpont is közzé van téve a proxyn keresztül. Az AD FS felügyeleti konzolon keresztül engedélyezett végpontokat a **Szolgáltatás > Végpontok** területen találja.
>
>Ha helyszíni összevont szolgáltatásként nem az AD FS-t használja, a szállító utasításait követve győződjön meg arról, hogy támogatják a WS-Trust 1.3-as vagy 2005-ös végpontokat, valamint hogy ezek a metaadat-átviteli fájlon (MEX) keresztül vannak közzétéve.

Az eszközregisztráció befejezéséhez elengedhetetlen, hogy a következő jogcímek meglegyenek az Azure DRS által fogadott jogkivonatban. Az Azure DRS egy eszközobjektumot hoz létre az Azure AD-ben ezen információk alapján, amelyet ezután az Azure AD Connect az újonnan készített eszközobjektumok és a helyszíni számítógépfiók társításához használ fel.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartománynévvel rendelkezik, a következő jogcímeket kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már rendelkezik ImmutableID-jogcímmel (pl. alternatív bejelentkezési azonosító), egy kapcsolódó jogcímet kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban az alábbiakról talál információt:
 
- Az értékek, amelyekkel a jogcímeknek rendelkezniük kell
- Meghatározás megjelenése az AD FS-ben

A meghatározás segít ellenőrizni, hogy az értékek léteznek-e már, vagy még létre kell őket hozni.

> [!NOTE]
> Ha helyszíni összevonási kiszolgálóként nem az AD FS-t használja, kövesse a szállítója utasításait a jogcímek kiállításához megfelelő konfiguráció létrehozásához.

### <a name="issue-account-type-claim"></a>Fióktípusra vonatkozó jogcím kiállítása

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – A jogcímnek tartalmaznia kell a **DJ** értéket, amely az eszközt tartományhoz csatolt számítógépként azonosítja. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectGUID értékének kiállítása

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – A jogcímnek tartalmaznia kell a helyszíni számítógépfiók **objectGUID** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectSID értékének kiállítása

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – A jogcímnek tartalmaznia kell a helyszíni számítógépfiók **objectSid** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>A számítógép issuerID értékének kiállítása, ha több ellenőrzött tartománynév is van az Azure AD-ban

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – A jogcímnek tartalmaznia kell a jogkivonatot kiállító helyszíni összevonási szolgáltatáshoz (AD FS vagy harmadik féltől származó) csatlakozó ellenőrzött tartománynevek bármelyikének Uniform Resource Identifier (URI) azonosítóját. Az AD FS-ben az alábbiakhoz hasonló kiállítási átalakítószabályokat a megadott sorrendben, a fentiek után adhatja hozzá. Ne feledje, hogy a szabály a felhasználókra való explicit alkalmazásához szükség van egy szabályra. Az alábbi szabályok esetében hozzáadtunk egy első szabályt, amely azonosítja, hogy felhasználói vagy számítógép-hitelesítésről van-e szó.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


A fenti jogcímben a

- `<verified-domain-name>` egy helyőrző, amelyet az egyik ellenőrzött Azure AD-tartománynevével kell helyettesítenie. Például: Value = "http://contoso.com/adfs/services/trust/"



Az ellenőrzött tartománynevekkel kapcsolatos további részleteket az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory-domains-add-azure-portal.md) című részben találja.  

Az ellenőrzött vállalati tartományok listáját a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) parancsmaggal kaphatja meg. 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Az ImmutableID kiállítása számítógéphez, ha már létezik egy a felhasználókhoz (pl. alternatív bejelentkezési azonosító van beállítva)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – A jogcímnek számítógépekre érvényes értéket kell tartalmaznia. Az AD FS-ben a következő módon hozhat létre kiállítási átalakítószabályt:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Az AD FS kiállítási átalakítószabályainak létrehozását segítő szkript

A következő szkript segít a fent leírt kiállítási átalakítószabályok létrehozásában.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Megjegyzések 

- A szkript hozzáfűzi a szabályokat a meglévő szabályokhoz. Ne futtassa kétszer a szkriptet, mert azzal kétszer adná hozzá a szabálykészletet. A szkript újbóli futtatása előtt ellenőrizze, hogy a jogcímekre nem vonatkoznak kapcsolódó szabályok (a kapcsolódó feltételek mellett).

- Ha több ellenőrzött tartománynévvel is rendelkezik (ahogy az Azure AD portálon vagy a Get-MsolDomains parancsmagon keresztül látható), állítsa a **$multipleVerifiedDomainNames** értékét a **$true** értékre a szkriptben. Emellett távolítson el minden meglévő issuerID jogcímet, amelyek az Azure AD Connect vagy más szolgáltatások révén jöhettek létre. Példa a szabályra:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Ha a felhasználói fiókokhoz már kiállított egy **ImmutableID** jogcímet, állítsa az **$immutableIDAlreadyIssuedforUsers** értékét a **$true** értékre a szkriptben.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

- Állítson be egy szabályzatot az Azure AD-ben, amellyel a felhasználók eszközeinek regisztrációját engedélyezheti.
 
- Konfigurálja a helyszíni összevonási szolgáltatást arra, hogy jogcímek kiállításával támogassa az **integrált Windows-hitelesítést (IWA)** az eszközök regisztrációjához.
 
- Adja hozzá az Azure AD eszközhitelesítési végpontot a helyi intranetes zónákhoz az eszközök hitelesítésekor megjelenő tanúsítványkérések elkerüléséhez.

- A korábbi verziójú Windows-eszközök vezérlése 


### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Szabályzat beállítása az Azure AD-ben a felhasználói eszközregisztráció engedélyezéséhez

A korábbi verziójú Windows-eszközök regisztrálásának feltétele, hogy az eszközbeállításokban be legyen állítva, hogy a felhasználók regisztrálhassák az eszközöket az Azure AD-ben. Ez a beállítás az Azure Portalon a következő helyen található:

`Azure Active Directory > Users and groups > Device settings`
    
A **szabályzatnál, amely lehetővé teszi, hogy a felhasználók regisztrálják a saját eszközeiket az Azure AD-ben**, az **Összes** értéket kell beállítani.

![Eszközök regisztrálása](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>Helyszíni összevonási szolgáltatás konfigurálása 

A helyszíni összevonási szolgáltatásnak támogatnia kell az **authenticationmethod** és a **wiaormultiauthn** jogcímek kiállítását, amikor hitelesítési kérelem érkezik az Azure AD függő entitáshoz, amely a resource_params paramétert és az abban kódolt értéket tartalmazza az alábbiak szerint:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Egy ilyen kérelem érkezésekor a helyszíni összevonási szolgáltatásnak hitelesítenie kell a felhasználót az integrált Windows-hitelesítésen keresztül, és sikeres hitelesítés esetén a következő két jogcímet kell kiállítania:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

AD FS-ben hozzá kell adnia egy kiállítási átalakítószabályt, amely áthalad a hitelesítési módszeren.  

**A szabály hozzáadásához:**

1. Az AD FS felügyeleti konzoljában lépjen a következő helyre:`AD FS > Trust Relationships > Relying Party Trusts`.
2. Kattintson a jobb gombbal a Microsoft Office 365 Identity Platform megbízható függő entitás elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.
3. A **Kiállítási átalakítószabályok** lapon válassza a **Szabály hozzáadása** lehetőséget.
4. A **Jogcímszabály** sablonlistában válassza a **Jogcímek küldése egyéni szabállyal** lehetőséget.
5. Kattintson a **Tovább** gombra.
6. A **Jogcímszabály neve** mezőbe írja be a következőt: **Auth Method Claim Rule**.
7. Az **Jogcímszabály** mezőbe írja be az alábbi szabályt:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Az összevonási kiszolgálón írja be az alábbi PowerShell-parancsot, miután lecserélte az **\<RPObjectName\>** értéket az Azure AD megbízható függő entitás objektum függőentitás-objektumának nevére. Ezen objektum neve általában **Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Az Azure AD eszközhitelesítési végpont hozzáadása a helyi intranetes zónákhoz

A felhasználók regisztráló eszközeinek Azure AD-hitelesítésekor megjelenő tanúsítványkérések elkerüléséhez leküldhet egy szabályzatot a tartományhoz csatlakozó eszközökre, amely hozzáadja az alábbi URL-címeket a helyi intranetes zónához az Internet Explorerben:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése 

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információért kattintson [ide](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

A vállalatában sikeresen csatlakoztatott eszközöket a [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) parancsmaggal ellenőrizheti az [Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ezen parancsmag kimenete megjeleníti az Azure AD-be regisztrált és az ahhoz csatlakoztatott eszközöket. Az összes eszköz megjelenítéséhez használja az **-All** paramétert, majd szűrje őket a **deviceTrustType** tulajdonság szerint. A tartományhoz csatlakoztatott eszközök a **Domain Joined** értékkel rendelkeznek.



## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásával kapcsolatban, tekintse át a következő cikkeket:

- [Jelenlegi Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Korábbi verziójú Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory eszközkezelésének alapjai](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
