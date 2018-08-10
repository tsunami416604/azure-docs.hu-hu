---
title: Hibrid Azure Active Directoryhoz csatlakoztatott eszközök kézi konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja manuálisan a hibrid Azure Active Directoryhoz csatlakoztatott eszközökön.
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
ms.topic: article
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: ba47223f86005809189214f26a63b75b21449e3a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630619"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Azure Active Directoryhoz csatlakoztatott eszközök hibrid kézi konfigurálása 

Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. További részletekért tekintse meg a [bemutatása az Eszközfelügyelet az Azure Active Directory](overview.md).

Ha rendelkezik a helyszíni Active Directory-környezetben, és azt szeretné, a tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-ben, ez elvégezhető a hibrid Azure AD-csatlakoztatott eszközök konfigurálásával. A cikk ismerteti a kapcsolódó lépések. 



> [!TIP]
> Ha az Azure AD Connect használatával, lehetőség, tekintse meg [válassza ki a forgatókönyv](hybrid-azuread-join-plan.md#select-your-scenario). Az Azure AD Connect használatával egyszerűsítheti a hibrid Azure AD-csatlakozás konfigurációja jelentősen.




## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy Ön ismeri a:
    
-  [Az Eszközfelügyelet az Azure Active Directory bemutatása](../device-management-introduction.md)
    
-  [A hibrid Azure Active Directory-csatlakozás megvalósításának tervezése](hybrid-azuread-join-plan.md)

-  [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)


Hibrid Azure AD-csatlakoztatott eszközök a szervezetben engedélyezése előtt győződjön meg arról, hogy szüksége:

- Futtatja egy naprakész verzióját az Azure AD connect.

- Az Azure AD connect szinkronizálja a számítógép-objektumokat szeretné a hibrid Azure AD-hez az Azure AD-eszközök. Csatlakozás az is, ha a számítógép-objektumok tartoznak adott szervezeti egység (OU), akkor ezeket a szervezeti egységek szinkronizálása az Azure ad-ben konfigurálni kell.

  

Az Azure AD Connect:

- Megtartja a számítógépfiókot a helyszíni Active Directory (AD) és az Azure AD-ben az eszköz objektum közötti társítást. 
- Lehetővé teszi, hogy a többi eszköz kapcsolódó szolgáltatások, mint a Windows Hello for Business.

Ügyeljen arra, hogy a következő URL-címek az Azure AD-regisztrációs számítógépek számára a szervezeti hálózaton belüli számítógépek elérhető:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Engedélyezése
- https://device.login.microsoftonline.com

- A szervezet STS (összevont tartományok)

Nem tette meg, ha a szervezet STS (az összevont tartományok) szerepelnie kell a felhasználó helyi intranet beállításait.

A szervezet tervezi, hogy közvetlen egyszeri bejelentkezés használata, ha a számítógépekről a szervezeten belüli elérhetőnek kell lennie kell a következő URL-címeket, és azok is hozzá kell adni a felhasználó helyi intranetzónához:

- https://autologon.microsoftazuread-sso.com

- Ezenkívül a következő beállítást engedélyezni kell a felhasználó intranet zóna: "Állapot parancsfájllal történő frissítésének engedélyezése."

Ha a szervezete használja az felügyelt (nem összevont) beállítása a helyszíni AD- és nem használja az AD FS vonni az Azure AD hibrid Azure AD join a Windows 10-es támaszkodik a számítógép-objektumokat az ad-ben az Azure AD sync'ed kell majd. Győződjön meg arról, hogy bármely szervezeti egység (OU), amelyek tartalmazzák a számítógép-objektumokat, kell lennie a hibrid Azure AD-hez az Azure AD Connect szinkronizálási konfigurációjában a szinkronizálási szolgáltatás engedélyezve vannak-e.

1703-as vagy korábbi verziójú Windows 10 rendszerű eszközökhöz Ha a szervezet megköveteli a hozzáférést az interneten keresztül egy kimenő proxy meg kell valósítani Proxy automatikus felderítési WPAD (Web) lehetővé teszik a Windows 10-es számítógépek regisztrálása az Azure AD. 

## <a name="configuration-steps"></a>Konfigurációs lépések

Hibrid Azure AD-csatlakoztatott eszközök különféle típusú Windows platformot is konfigurálhatja. Ez a témakör a tipikus konfigurációja minden esetben szükséges lépéseket tartalmazza.  

Az alábbi táblázat segítségével áttekintheti a forgatókönyvhöz szükséges lépéseket:  



| Lépések                                      | Windows jelenlegi és a jelszót a jelszókivonatok szinkronizálása | Jelenlegi Windows és az összevonás | Régebbi verziójú Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| A telepítő kiállítási jogcímek           |                                        | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| A Windows 10-eszközök engedélyezése      |                                        |                                | ![Jelölőnégyzet][1]        |
| Csatlakoztatott eszközök ellenőrzése          | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |



## <a name="configure-service-connection-point"></a>Szolgáltatáskapcsolódási pont konfigurálása

A szolgáltatás kapcsolódási pont (SCP) objektum az eszközök a regisztráció során felderítésére szolgál az Azure AD bérlői kapcsolatos információkat. A helyszíni Active Directoryban (AD) a hibrid Azure AD-hez csatlakoztatott eszközök a szolgáltatáskapcsolódási pont objektum konfigurációs környezet partíciójára a számítógép erdő léteznie kell. Csak egy konfigurációs névhasználati környezet minden erdőre van. Többerdős Active Directory-konfiguráció esetén a szolgáltatáskapcsolódási pont minden olyan erdőben, tartományhoz csatlakoztatott számítógépeket tartalmazó léteznie kell.

Használhatja a [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) az erdő konfigurációelnevezési környezetében beolvasásához.  

Az Active Directory tartományi nevű erdő *fabrikam.com*, konfigurációelnevezési környezetében van:

`CN=Configuration,DC=fabrikam,DC=com`

Az erdő az SCP-objektumot a tartományhoz csatlakoztatott eszközök automatikus – regisztráció a következő helyen található:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Attól függően, hogy az Azure AD Connect telepített az SCP-objektum előfordulhat, hogy már be van állítva.
Ellenőrizze az objektum létezik-e, és a használatával a következő Windows PowerShell-parancsfájl felderítési értékek lekéréséhez: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

A **$scp. A kulcsszavak** megjelenítheti az Azure AD-bérlői adatok, például:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Ha a szolgáltatáskapcsolódási pont nem létezik, létrehozhatja futtatásával a `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálón. Vállalati rendszergazdai hitelesítő adatok a parancsmag futtatásához szükséges.  
A parancsmag:

- Az Active Directory-erdőben, az Azure AD Connect csatlakozik a szolgáltatáskapcsolódási pontot hoz létre. 
- Kell adnia a `AdConnectorAccount` paraméter. Ez az a fiók, az Active Directory-összekötő fiók az Azure AD connect konfigurálva van. 


Az alábbi parancsfájlt mutat be a parancsmag használatával. Ez a szkript a `$aadAdminCred = Get-Credential` megköveteli, hogy írja be a felhasználónevet. Meg kell adnia a felhasználónevet, a felhasználó egyszerű felhasználónév (UPN) formátumban (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

A `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot:

- Használja az Active Directory PowerShell-modul és az AD DS eszközök segítségével, amelyek egy tartományvezérlőn futó Active Directory webszolgáltatások támaszkodnak. Az Active Directory Web Services és újabb verziói támogatják a Windows Server 2008 R2 rendszerű tartományvezérlőkön.
- Csak a támogatja a **MSOnline PowerShell modul 1.1.166.0-s**. Ez a modul letöltéséhez használja a [hivatkozás](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Ha az Active Directory tartományi szolgáltatások eszközei nincsenek telepítve, a `Initialize-ADSyncDomainJoinedComputerSync` sikertelen lesz.  Az Active Directory tartományi szolgáltatások eszközei is telepíthető a Kiszolgálókezelő használatával – távoli kiszolgálófelügyelet eszközei - szolgáltatások szerepkör-felügyeleti eszközök alatt.

Windows Server 2008 vagy korábbi verzióit futtató tartományvezérlők használja az alábbi parancsfájlt a szolgáltatáskapcsolódási pont létrehozásához.

Többerdős konfiguráció esetén a szolgáltatáskapcsolódási pont létrehozása minden olyan erdőben, amennyiben a számítógép létezik a következő szkriptet kell használnia:
 
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

A fenti, parancsfájl

- `$verifiedDomain = "contoso.com"` pedig ki kell cserélni az egyik ellenőrzött tartománynév Azure AD-ben. A tartománynak a tulajdonosa, mielőtt használhatná azt kell.

Ellenőrzött tartomány nevét kapcsolatos további információkért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
A vállalat ellenőrzött tartományok listájának lekéréséhez használja a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) parancsmagot. 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>A telepítő kiállítási jogcímek

Az összevont Azure AD konfigurálása, a eszközök támaszkodnak az Active Directory összevonási szolgáltatások (AD FS) vagy a 3. fél a helyi összevonási szolgáltatás az Azure AD-hitelesítést. Eszközök regisztrálása a Azure Active Directory Eszközregisztrációs szolgáltatásában (Azure DRS) ellen hozzáférési jogkivonatot kapjon hitelesíteni.

A Windows jelenlegi eszközök hitelesítése az integrált Windows-hitelesítés használatával egy aktív végpontot WS-Trust (1.3-as vagy 2005-ös verzió), a helyi összevonási szolgáltatás által üzemeltetett.

> [!NOTE]
> AD FS-ben vagy használatakor **adfs/services/megbízhatósági/13/windowstransport** vagy **adfs/services/megbízhatósági/2005/windowstransport** engedélyezve kell lennie. Ha a hitelesítési Webproxyt használ, is győződjön meg arról, hogy ezt a végpontot a proxyn keresztül van közzétéve. Láthatja, milyen végpontok engedélyezve vannak a az AD FS felügyeleti konzolon keresztül **Service > végpontok**.
>
>Ha a helyi összevonási szolgáltatás AD FS nem rendelkezik, kövesse a szállító, hogy a WS-Trust 1.3-as vagy 2005 végpontok és, hogy ezeket a metaadatokat az Exchange-fájl (MEX) keresztül közzétett támogatják.

A következő jogcímek léteznie kell a jogkivonat befejezéséhez az eszköz regisztrálása az Azure DRS által fogadott. Azure DRS egy eszközobjektumot hoz létre az Azure AD Connect által az újonnan létrehozott eszköz objektum társítása a számítógép fiók a helyi majd használt információk az Azure AD-ben.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha egynél több ellenőrzött tartomány nevét, meg kell adnia a következő jogcím számítógépek esetén:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már meg vannak kiállító meg kell adnia egy megfelelő jogcím számítógépek esetén az ImmutableID jogcím (pl. másodlagos bejelentkezési azonosító):

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban, talál információkat:
 
- Az értékek egyes jogcímek kell rendelkeznie
- Hogyan definici módon jelenik meg az AD FS-ben

A definíció segítségével ellenőrizze-e az értékek találhatók, vagy ha kell létrehoznia őket.

> [!NOTE]
> Ha a helyi összevonási kiszolgáló AD FS nem használja, kövesse a gyártója által biztosított utasításokat hozza létre ezeket a jogcímeket kiadni, a megfelelő konfigurációt.

### <a name="issue-account-type-claim"></a>Probléma fiókja típusának jogcím

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Ez a jogcím tartalmaznia kell a egy értéke **DJ**, amely azonosítja, hogy az eszköz egy tartományhoz csatlakozó számítógép. Az AD FS-ben adhat hozzá egy kiállítási átalakítási szabály a következőhöz hasonló:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>A számítógép fiók helyszíni probléma objectGUID

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** – Ez a jogcím tartalmaznia kell a **objectGUID** értéket a helyi számítógépfiók. Az AD FS-ben adhat hozzá egy kiállítási átalakítási szabály a következőhöz hasonló:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>A számítógép fiók helyszíni probléma objectSID

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** – Ez a jogcím tartalmaznia kell a **objectSid** értéket a helyi számítógépfiók. Az AD FS-ben adhat hozzá egy kiállítási átalakítási szabály a következőhöz hasonló:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Számítógép issuerID kibocsátani, ha több ellenőrzött tartománynevek az Azure ad-ben

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – Ez a jogcím a egységes erőforrás-azonosító (URI) bármely, a helyszíni összevonási szolgáltatással (AD FS vagy 3. fél) csatlakozó ellenőrzött tartomány nevét kell tartalmaznia a jogkivonat kiadása. Az AD FS-ben, amelyek adott sorrendben alábbi rétegében után azokat a fenti jogcímkiadás-átalakítási szabályok is hozzáadhat. Vegye figyelembe, hogy a szabály a felhasználók explicit módon ki egy szabályt szükség. Az alábbi szabályok azonosítása a felhasználók és számítógépek hitelesítését első szabály egészül ki.

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


A fenti jogcímek

- `<verified-domain-name>` pedig ki kell cserélni az egyik ellenőrzött tartománynév Azure AD-ben. Például érték = "http://contoso.com/adfs/services/trust/"



Ellenőrzött tartomány nevét kapcsolatos további információkért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

A vállalat ellenőrzött tartományok listájának lekéréséhez használja a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) parancsmagot. 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Számítógép ImmutableID ki, amikor a felhasználók létező (pl. másodlagos bejelentkezési azonosító beállítása)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** – Ez a jogcím számítógépek egy érvényes értéket kell tartalmaznia. Az AD FS-ben a következő létrehozhat egy kiállítási átalakítási szabályt:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Az AD FS-kiállítási átalakítási szabályok létrehozását segítő parancsfájl

A következő parancsfájl segítségével létrehozni a kiállítási átalakítási szabályok a fent leírt.

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

- Ez a szkript a szabályok hozzáfűzi a meglévő szabályok. Ne futtassa a parancsfájlt kétszer, mert a szabálykészletet kétszer kell hozzáadni. Győződjön meg arról, hogy nem megfelelő szabályok tartoznak ezeket a jogcímeket (alatt a megfelelő feltételek) ismét a parancsfájl futtatása előtt.

- Ha több ellenőrzött tartomány nevét (ahogyan az az Azure AD portálon vagy a Get-MsolDomains parancsmag használatával), az értékét állítsa be **$multipleVerifiedDomainNames** a parancsfájl **$true**. Győződjön meg arról, hogy távolítsa el minden meglévő issuerid jogcím, előfordulhat, hogy létrehozott Azure AD Connect vagy más módon is. Íme egy példa, ehhez a szabályhoz:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Ha már adtak egy **ImmutableID** jogcím-felhasználói fiókok esetében, az értékét állítsa be **$immutableIDAlreadyIssuedforUsers** a parancsfájl **$true**.

## <a name="enable-windows-down-level-devices"></a>Windows régebbi verziójú eszközök engedélyezése

Ha Windows régebbi verziójú eszközök, a tartományhoz csatlakoztatott eszközök némelyike kell tennie:

- Szabályzat beállítása az Azure AD-felhasználók regisztrálhatják eszközeiket.
 
- Konfigurálja a helyszíni összevonási szolgáltatás támogatására jogcímeket kiadni, **integrált Windows-hitelesítés (IWA)** való eszközregisztrációhoz.
 
- Az Azure ad-ben eszköz hitelesítési végpont hozzáadása a helyi intranetes zóna elkerülése érdekében tanúsítványokra, ha az eszköz hitelesítéséhez.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Szabályzat beállítása az Azure AD-felhasználók regisztrálhatják eszközeiket

Windows régebbi verziójú eszközök regisztrálása, győződjön meg arról, hogy a felhasználók regisztrálhatják az eszközeiket az Azure ad-ben a beállítás értéke kell. Az Azure Portalon találhatja meg a beállítás:

`Azure Active Directory > Users and groups > Device settings`
    
A következő szabályzatot kell beállítani **összes**: **a felhasználók regisztrálhatják eszközeiket az Azure ad-vel**

![Eszközök regisztrálása](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>A helyi összevonási szolgáltatás konfigurálása 

A helyi összevonási szolgáltatás támogatnia kell a kiállító a **authenticationmethod** és **wiaormultiauthn** jogcímeket, ha a tároló az Azure AD függő entitás hitelesítési kérelmet fogad egy resouce_params paraméterhez a kódolt érték látható módon:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Ha ilyen kérést, a helyi összevonási szolgáltatás kell hitelesíteni a felhasználót az integrált Windows-hitelesítés használatával, és követően sikeres, akkor a következő két jogcímeket kell kiadni:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Az AD FS-ben hozzá kell adnia egy kiállítási átalakítási szabályt, amely pass révén a hitelesítési módszert.  

**Ez a szabály hozzáadása:**

1. Az AD FS felügyeleti konzolon lépjen a `AD FS > Trust Relationships > Relying Party Trusts`.
2. Kattintson a jobb gombbal a függő entitás megbízhatósági objektum a Microsoft Office 365 Identity Platform, és válassza **Jogcímszabályok szerkesztése**.
3. Az a **kiadás átalakítási szabályai** lapon jelölje be **szabály hozzáadása**.
4. Az a **jogcímszabály** sablon listáról válassza ki **jogcímek küldése egyéni szabállyal egy**.
5. Kattintson a **Tovább** gombra.
6. Az a **Jogcímszabály neve** mezőbe írja be **hitelesítési módszer Jogcímszabály**.
7. Az a **jogcímszabály** mezőbe írja be a következő szabálynak:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Az összevonási kiszolgálón írja be az alábbi PowerShell-parancsot a cseréje után ** \<RPObjectName\> ** az Azure AD függő entitás megbízhatósági objektum a függő entitás objektum nevére. Ez az objektum neve általában **a Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Az Azure ad-ben eszköz hitelesítési végpont hozzáadása a Helyi Intranet zónák

Tanúsítvány elkerülése érdekében kérni fogja, ha az eszközök regisztrálása a felhasználók hitelesítéséhez az Azure AD-szabályzat leküldése a tartományhoz csatlakoztatott eszközök a következő URL-cím hozzáadása a helyi intranetzónához az Internet Explorerben:

`https://device.login.microsoftonline.com`


## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

Ellenőrizheti a sikeres csatlakoztatott eszközök a szervezetben használatával a [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) parancsmagot a [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Az ezzel a parancsmaggal megjelenítheti által regisztrált és az Azure AD-csatlakoztatott eszközök. Minden eszköz lekéréséhez használja a **-minden** paramétert, majd szűrheti őket, és használatával a **deviceTrustType** tulajdonság. Tartományhoz csatlakoztatott eszközök a egy értéke lehet **tartományhoz csatlakozó**.



## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a hibrid befejezése az Azure AD join tartományhoz csatlakoztatott Windows-eszközök, lásd:

- [Aktuális Windows-eszközök hibrid Azure AD joinnal hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Hibaelhárítás Windows régebbi verziójú eszközök hibrid Azure AD joinnal](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

* [Az Eszközfelügyelet az Azure Active Directory bemutatása](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
