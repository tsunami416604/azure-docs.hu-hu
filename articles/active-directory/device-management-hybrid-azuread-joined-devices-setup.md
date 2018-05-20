---
title: Hogyan hibrid konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök |} Microsoft Docs
description: Tudnivalók a hibrid Azure Active Directoryhoz csatlakoztatott eszközök konfigurálásához.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a74a16fa583ac3bc7ea2250f916e855a0bd9d1c1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Hibrid Azure Active Directoryhoz csatlakoztatott eszközök konfigurálása

Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. További részletekért lásd: [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

Ha szeretné a tartományhoz csatlakozó eszközök csatlakoztatása az Azure AD egy a helyszíni Active Directory-környezettel rendelkezik, ez elvégezhető hibrid az Azure AD csatlakoztatott eszközök konfigurálásával. A témakör az kapcsolódó lépést. 


## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené a hibrid az Azure AD csatlakoztatott eszközök konfigurálása a környezetben, tanulmányozza át a támogatott forgatókönyveket és a korlátozásokkal.  

Ha a a [rendszer-előkészítő eszköz (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), ellenőrizze, hogy forrását, amely nem lett még regisztrálva az Azure ad-val a Windows telepítési lemezképeket.

Az összes futó Windows 10 évforduló Update és Windows Server 2016 automatikusan az eszköz újraindítása vagy a felhasználó Azure AD-val regisztrálja a tartományhoz csatlakoztatott eszközök jelentkezzen be az alábbiakban leírt konfigurációs lépéseket befejezése után. **Ha ezt a viselkedést automatikus regisztrálása használata nem ajánlott, vagy amennyiben ellenőrzött bevezetés szükséges**, hajtsa végre a "Lépés 4: vezérlő központi telepítés és a Bevezetés" először szelektív letiltása és engedélyezése automatikus bevezetése előtt az alábbi részben található útmutatást a további konfigurációs lépések után.  

A leírások olvashatóságának, ez a témakör a következő kifejezést használja: 

- **Aktuális Windows-eszközök** -a Windows 10 vagy Windows Server 2016 rendszert futtató, tartományhoz csatlakoztatott eszközökre vonatkozik.
- **Régebbi Windows-eszközök** -e az összes kifejezés **támogatott** tartományhoz csatlakoztatott Windows-eszközök végrehajtott futó Windows 10 és Windows Server 2016.  

### <a name="windows-current-devices"></a>Aktuális Windows-eszközök

- A Windows asztali operációs rendszert futtató eszközök esetén a támogatott verziója a Windows 10 évforduló frissítést (1607) vagy újabb. 
- Aktuális Windows-eszközök regisztrációjának **van** nem összevont környezetekben, például a jelszó kivonatát szinkronizálási konfiguráció támogatott.  


### <a name="windows-down-level-devices"></a>Régebbi Windows-eszközök

- A következő Windows-kezelés régebbi eszközöket támogatja:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- A régebbi Windows-eszközök regisztrálását **van** keresztül zökkenőmentes egyszeri bejelentkezés nem összevont környezetekben támogatott [Azure Active Directory zökkenőmentes egyszeri bejelentkezést](https://aka.ms/hybrid/sso).
- A régebbi Windows-eszközök regisztrálását **nem** eszközök központi profilok használata támogatott. Ha a központi profilok vagy a beállítások, Windows 10 használata biztosítja.



## <a name="prerequisites"></a>Előfeltételek

Hibrid csatlakozott az Azure AD a szervezetnél található eszközökön engedélyezése előtt győződjön meg arról, hogy kell adnia:

- Futtatja egy naprakész az Azure AD connect.

- Az Azure AD connect szinkronizálása megtörtént-e a számítógép-objektumokat kell az Azure AD-tartományhoz az Azure AD hibrid kívánt eszközt. Amennyiben a számítógép-objektumok tartozik adott szervezeti egységekhez (OU), majd a szervezeti egységek be kell állítani a szinkronizáláshoz az Azure AD connect is.

  

Az Azure AD Connect:

- A számítógépfiók a helyszíni Active Directory (AD) és a eszközobjektumot az Azure AD közötti társítás tartja. 
- Lehetővé teszi, hogy más eszköz kapcsolódó szolgáltatások, például a vállalati Windows Hello.

Győződjön meg arról, hogy a következő URL-címekkel érhető el az Azure AD, a számítógép regisztrációjához a szervezeti hálózaton belüli számítógépek:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

- A szervezet STS (összevont tartományt)

Ha nem tette meg, a szervezet STS (az összevont tartományt) szerepelnie kell a felhasználó helyi intranet beállításait.

A szervezet tervezi, hogy zökkenőmentes SSO használata, ha a szervezeten belüli számítógépek elérhetőnek kell lennie kell a következő URL-címeket, és azok is szerepelnie kell a felhasználó helyi intranet zóna:

- https://autologon.microsoftazuread-sso.com

- https://aadg.windows.net.nsatc.net

- Emellett a következő beállítást engedélyezni kell a felhasználó intranet zóna: "Beállítható a frissítések állapotsor parancsfájl használatával."

Ha a szervezet használja a felügyelt (nem összevont) telepítése a helyszíni AD, és nem használja az AD FS összevonásához az Azure ad-vel, majd a Windows 10 csatlakoztatása az Azure AD hibrid támaszkodik a számítógép-objektumokat kell lennie az Azure AD sync'ed AD-ben. Győződjön meg arról, hogy bármely szervezeti egység (OU), amelyek tartalmazzák a számítógép-objektumok, csak az Azure AD-tartományhoz hibrid engedélyezve vannak-e az Azure AD Connect szinkronizálási konfigurációban szinkronizáláshoz.

Ha a szervezet az interneten keresztül egy kimenő proxy hozzáférésre van szüksége, meg kell valósítani webes Proxy automatikus felderítését a lekérdezés (WPA) lehetővé teszik a Windows 10 számítógépek regisztrálása az Azure ad Szolgáltatásba.

## <a name="configuration-steps"></a>Konfigurációs lépések

Hibrid csatlakozott az Azure AD-eszközök Windows eszközplatformok különféle típusú konfigurálhatja. Ez a témakör minden tipikus forgatókönyve a szükséges lépéseket tartalmazza.  

Az alábbi táblázat segítségével áttekintheti a forgatókönyvhöz szükséges lépéseket:  



| Lépések                                      | A Windows jelenlegi és a jelszó Jelszókivonat-szinkronizálás | A Windows jelenlegi és a következő összevonáshoz | Windows-kezelés régebbi |
| :--                                        | :-:                                    | :-:                            | :-:                |
| 1. lépés: A szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| 2. lépés: A jogcímek kiállítási beállítása           |                                        | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| 3. lépés: A Windows 10-eszközök engedélyezése      |                                        |                                | ![Jelölőnégyzet][1]        |
| 4. lépés: Központi telepítési és a bevezetés ellenőrzése     | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| 5. lépés: Ellenőrizze a csatlakoztatott eszközök          | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |



## <a name="step-1-configure-service-connection-point"></a>1. lépés: A szolgáltatáskapcsolódási pont konfigurálása

A szolgáltatás kapcsolódási pontjának (SCP) objektum által az eszközök regisztrálásakor felderítésére szolgál információkat az Azure AD bérlői. A helyszíni Active Directoryban (AD) a szolgáltatáskapcsolódási pont objektum a hibrid csatlakozott az Azure AD-eszközök konfigurációs környezet partíciójára a számítógép erdő léteznie kell. Csak egy konfigurációs névhasználati környezet minden erdőre van. Többerdős Active Directory-konfiguráció esetén a szolgáltatáskapcsolódási pont léteznie kell az összes olyan erdőben, a tartományhoz csatlakoztatott számítógépeket tartalmazó.

Használhatja a [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) parancsmag használatával kérhet le az erdő konfigurációelnevezési környezetében.  

Az Active Directory tartományi nevű erdő *fabrikam.com*, konfigurációelnevezési környezetében van:

`CN=Configuration,DC=fabrikam,DC=com`

Az erdőben az SCP objektumot a tartományhoz csatlakozó eszközök automatikus regisztrációja a következő helyen található:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Attól függően, hogy telepítette az Azure AD Connect az SCP objektum lehet, hogy már meg vannak adva.
Ellenőrizze az objektum létezését, és a következő Windows PowerShell-parancsfájl használatával felderítési értékeinek beolvasásához: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

A **$scp. Kulcsszavak** az alábbiakat mutatja be az Azure AD bérlői kapcsolatos információkat, például:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Ha a szolgáltatáskapcsolódási pont nem létezik, létrehozhatja futtatásával a `Initialize-ADSyncDomainJoinedComputerSync` parancsmag az Azure AD Connect-kiszolgálón. Ez a parancsmag futtatásához szükséges vállalati rendszergazda hitelesítő adatai.  
A parancsmagot:

- Az Active Directory-erdőben, az Azure AD Connect csatlakozik-e a szolgáltatáskapcsolódási pontot hoz létre. 
- Meg kell adja meg a `AdConnectorAccount` paraméter. Ez az a fiók, amely az Active Directory connector fiók az Azure AD connect van konfigurálva. 


Az alábbi parancsfájlt mutat be a parancsmag használatával. Ezt a parancsfájlt a `$aadAdminCred = Get-Credential` meg kell írjon be egy felhasználónevet. Meg kell adnia a felhasználónevet, a felhasználó egyszerű felhasználónév (UPN) formátumban (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

A `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot:

- Használja az Active Directory PowerShell-moduljában és AD DS-eszközei, amely a tartományvezérlőn futó Active Directory webszolgáltatások támaszkodnak. Az Active Directory webszolgáltatások a Windows Server 2008 R2 rendszerű tartományvezérlők és újabb rendszer.
- Csak akkor támogatott a **MSOnline PowerShell modul verziója 1.1.166.0**. Ez a modul letöltéséhez használjon ez [hivatkozás](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Ha az Active Directory tartományi szolgáltatások eszközei nincsenek telepítve, a `Initialize-ADSyncDomainJoinedComputerSync` sikertelen lesz.  Az Active Directory tartományi szolgáltatások eszközei a Kiszolgálókezelő a szerepkör-szolgáltatások - távoli kiszolgálófelügyelet eszközei - felügyeleti eszközök telepíthetők.

A Windows Server 2008 vagy korábbi verzióit futtató tartományvezérlők a szolgáltatáskapcsolódási pont létrehozásához használja az alábbi parancsfájlt.

Többerdős konfiguráció esetén a következő parancsfájlt kell használnia minden olyan erdőben, ahol a számítógépek létezik a szolgáltatáskapcsolódási pont létrehozása:
 
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

- `$verifiedDomain = "contoso.com"` pedig ki kell cserélni az egyik az ellenőrzött tartomány nevét az Azure ad-ben. Meg kell vásárolnia a tartományt, mielőtt is használhatja.

Ellenőrzött tartomány nevét kapcsolatos további tudnivalókért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](active-directory-domains-add-azure-portal.md).  
A vállalat ellenőrzött tartományok listájának lekéréséhez használja a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) parancsmag. 

![Get-AzureADDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

## <a name="step-2-setup-issuance-of-claims"></a>2. lépés: A jogcímek kiállítási beállítása

Az összevont Azure Active Directory beállítása, a eszközök támaszkodnak az Active Directory összevonási szolgáltatások (AD FS) vagy 3. fél a helyi összevonási szolgáltatás az Azure AD felé történő hitelesítésre. Eszközök regisztrálása az Azure Active Directory Eszközregisztrációs szolgáltatás (Azure DRS) szemben olyan hozzáférési jogkivonatot beolvasni hitelesítéséhez.

A Windows jelenlegi eszközök hitelesítés integrált Windows-hitelesítés egy aktív WS-Trust végponthoz (1.3 vagy 2005 verzió) a helyi összevonási szolgáltatás által üzemeltetett használatával.

> [!NOTE]
> AD FS-ben vagy használatakor **adfs/services/megbízhatósági/13/windowstransport** vagy **adfs/services/megbízhatósági/2005/windowstransport** engedélyezve kell lennie. Ha a hitelesítési Webproxyt használ, bizonyosodjon meg arról, hogy ezt a végpontot a proxy használatával van közzétéve. Láthatja, hogy mely végpontokat engedélyezve vannak a az AD FS felügyeleti konzolon keresztül **szolgáltatás > végpontok**.
>
>Ha a helyi összevonási szolgáltatás AD FS nem rendelkezik, az utasítások a szállító győződjön meg arról, hogy a WS-Trust 1.3 vagy 2005 végpontjainak és, hogy ezek a metaadatok Exchange fájlt (MEX) keresztül közzétett támogatják.

A következő jogcímeket befejezéséhez az eszközregisztrációhoz tartozó Azure DRS által kapott jogkivonat léteznie kell. Azure DRS egy eszközobjektumot hoz létre az Azure AD Connect objektumokkal való társítás az újonnan létrehozott eszköz a számítógép fiók a helyi majd használt információk az Azure AD-ben.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartomány nevét, meg kell adnia a következő jogcímet ad ki a számítógépeket:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha a rendszer már kiállító egy ImmutableID jogcímet (pl. másodlagos bejelentkezési Azonosítóval) meg kell adnia egy megfelelő jogcím számítógépek:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban azt talál információkat:
 
- Az értékeket az egyes jogcímek kell rendelkeznie
- Hogyan definíció jelenne meg az AD FS-ben

A definíció segítségével győződjön meg arról, hogy találhatók-e az értékeket, vagy ha szeretné-e létre.

> [!NOTE]
> Ha a helyi összevonási kiszolgáló AD FS nem használja, lépésekkel a gyártója által biztosított jogcímek ki a megfelelő konfigurációs.

### <a name="issue-account-type-claim"></a>A probléma fiók típusa jogcím

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** – Ezt az igényt az értéket kell tartalmaznia, **DJ**, amely azonosítja, hogy az eszközt egy tartományhoz csatlakozó számítógép. Az AD FS-ben adhat meg egy kiadási átalakítási szabálykészlet, amely a következőképpen néz ki:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>A számítógép fiók helyszínen probléma objectGUID

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** -A jogcím tartalmaznia kell a **objectGUID** értéket a helyi fiók. Az AD FS-ben adhat meg egy kiadási átalakítási szabálykészlet, amely a következőképpen néz ki:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>A számítógép fiók helyszínen probléma objectSID

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** -A jogcím tartalmaznia kell a a **objectSid** értéket a helyi fiók. Az AD FS-ben adhat meg egy kiadási átalakítási szabálykészlet, amely a következőképpen néz ki:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Számítógép issuerID kibocsátani, ha több tartománynév ellenőrzése az Azure ad-ben

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** – A jogcím az ellenőrzött tartomány nevét, amelyek kapcsolódnak a helyszíni összevonási szolgáltatással (AD FS vagy 3. fél) bármelyikének egységes erőforrás azonosítója (URI) kell tartalmaznia a jogkivonatot kibocsátó. Az AD FS-ben a kiadás átalakítási szabályai, hasonló meghatározott sorrendben alatt megfelelően fenti megfelelően után is hozzáadhat. Ne feledje, hogy egy szabály kifejezetten ki a szabály a felhasználók számára a szükséges. Az alábbi szabályokkal szemben, a felhasználók és számítógépek hitelesítését azonosító első szabály jelenik meg.

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


A fenti, jogcímek

- `<verified-domain-name>` pedig ki kell cserélni az egyik az ellenőrzött tartomány nevét az Azure ad-ben. Például, Value = "http://contoso.com/adfs/services/trust/"



Ellenőrzött tartomány nevét kapcsolatos további tudnivalókért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](active-directory-domains-add-azure-portal.md).  

A vállalat ellenőrzött tartományok listájának lekéréséhez használja a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) parancsmag. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Számítógép ImmutableID ki, ha egy, a felhasználók létező (pl. másodlagos bejelentkezési azonosító beállítása)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** -A jogcím számítógépek érvényes értéket kell tartalmaznia. Az AD FS-ben is létrehozhat egy kiadási átalakítási szabálykészlet bocsát az alábbiak szerint:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Az AD FS kiállítási átalakítási szabályok létrehozásához segítő parancsfájl

A következő parancsfájl segítségével létrehozását a kiállítási átalakítási szabályok a fent leírt.

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

- Ezt a parancsfájlt a szabályok hozzáfűzi a meglévő szabályok. Ne futtassa a parancsfájlt kétszer mivel szabálykészlet kétszer volna adni. Győződjön meg arról, hogy nem megfelelő szabályok tartoznak ezeket a jogcímeket (megfelelő feltételekkel) a parancsfájl ismételt futtatása előtt.

- Ha több ellenőrzött tartomány nevét (ahogy az Azure AD portálon vagy a Get-MsolDomains parancsmag segítségével), állítsa be a **$multipleVerifiedDomainNames** a parancsfájl **$true**. Győződjön meg arról is, hogy távolítsa el a meglévő issuerid jogcím létrehozott előfordulhat, hogy az Azure AD Connect vagy más eszközök segítségével. Íme egy példa a ehhez a szabályhoz:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Ha már kiadott egy **ImmutableID** vonatkozó felhasználói fiókok, állítsa be a **$immutableIDAlreadyIssuedforUsers** a parancsfájl **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>3. lépés: Engedélyezze a régebbi Windows-eszközök

Ha néhány, a tartományhoz csatlakoztatott eszközök a Windows-kezelés régebbi eszközöket, akkor:

- Házirend beállítása az Azure AD-engedélyezése a felhasználóknak, hogy regisztrálják eszközeiket.
 
- Konfigurálja a helyi összevonási szolgáltatás támogatása jogcímek kiállítására **integrált Windows-hitelesítéssel (IWA)** az eszközregisztrációhoz tartozó.
 
- Az eszköz az Azure AD hitelesítési végpont hozzáadása a Helyi Intranet zóna tanúsítványokra elkerülésére, ha az eszköz hitelesítésére.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Házirend beállítása az Azure AD-eszközök regisztrálása a felhasználók engedélyezése

Régebbi Windows-eszközök regisztrálásához szükséges győződjön meg arról, hogy a felhasználók regisztrálják az eszközeiket az Azure AD beállítása. Az Azure-portálon találja meg a beállítás:

`Azure Active Directory > Users and groups > Device settings`
    
A következő házirend értékre kell állítani **minden**: **felhasználók előfordulhat, hogy regisztrálják az eszközeiket az Azure ad szolgáltatással**

![Eszközök regisztrálása](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>A helyi összevonási szolgáltatás konfigurálása 

A helyi összevonási szolgáltatás támogatnia kell a kiállító a **authenticationmethod** és **wiaormultiauthn** amikor hitelesítési kérelmet okozó az Azure AD függő entitás kap jogcímeket egy resouce_params paraméterhez a kódolt érték látható:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Ha ilyen kérelem érkezik, a helyi összevonási szolgáltatás kell hitelesíteni a felhasználót, integrált Windows-hitelesítés használatával, és sikeres, akkor a következő két jogcímek kiadja:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Az AD FS-ben hozzá kell adnia egy kiadási átalakítási szabálykészlet, amely folyamaton keresztül a hitelesítési módszert.  

**Ez a szabály hozzáadása:**

1. A az AD FS felügyeleti konzolon váltson `AD FS > Trust Relationships > Relying Party Trusts`.
2. Kattintson a jobb gombbal a Microsoft Office 365 Identitásplatformmal függő entitás megbízhatósági objektum, majd válassza ki **Jogcímszabályok szerkesztése**.
3. Az a **kiadás átalakítási szabályai** lapon jelölje be **szabály hozzáadása**.
4. Az a **jogcímszabály** sablon listáról válassza ki **jogcímek küldése egyéni szabály segítségével**.
5. Kattintson a **Tovább** gombra.
6. Az a **Jogcímszabály nevének** mezőbe írja be **hitelesítési módszer Jogcímszabály**.
7. Az a **jogcímszabály** mezőbe írja be a következő szabályt:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Az összevonási kiszolgálón, írja be az alábbi PowerShell-paranccsal cseréje után **\<RPObjectName\>** az Azure AD függő entitás megbízhatósági objektum a függő entitás objektum névvel. Ez az objektum neve általában **Microsoft Office 365 Identitásplatformmal**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Az eszköz az Azure AD hitelesítési végpont hozzáadása a Helyi Intranet zóna

Tanúsítvány elkerülése érdekében felkéri a felhasználók az eszközök regisztrálása az Azure AD-házirend leküldése a tartományhoz csatlakozó eszközök a következő URL-cím hozzáadása a Helyi Intranet zónához, az Internet Explorer hitelesítéshez:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>4. lépés: Központi telepítési és a bevezetés ellenőrzése

Amikor befejezte a szükséges lépéseket, tartományhoz csatlakozó eszközök automatikusan csatlakoznak az Azure AD készen áll:

- Minden tartományhoz csatlakozó eszközök a Windows 10 évforduló Update és Windows Server 2016 rendszert futtató automatikusan regisztrálja az Azure AD-eszközön indítsa újra, vagy felhasználói bejelentkezés. 

- Új eszközök regisztrálása az Azure AD, ha az eszköz újraindul, a tartományhoz csatlakoztatás művelet befejeződése után.

- (Például az Intune-hoz) regisztrált eszközök, melyeket korábban az Azure AD átmenet "*tartományhoz csatlakoztatott, aad-ben regisztrált*"; azonban a folyamat minden eszközön miatt a normál folyamat tartomány és a felhasználói tevékenység befejezése némi időt vesz igénybe.

### <a name="remarks"></a>Megjegyzések

- Csoportházirend-objektum segítségével szabályozhatja a Windows 10 és Windows Server 2016 tartományhoz csatlakoztatott számítógépekre az automatikus regisztráció bevezetésének. **Ha nem szeretné, hogy ezek az eszközök automatikusan regisztrálja az Azure ad-val vagy a regisztráció szabályozni szeretné**, majd a csoportházirend az automatikus regisztrációt, hogy ezek az eszközök először letiltása konfigurációval megkezdése előtt kell megkezdik lépéseket. Miután befejezte az állítja be, amikor készen áll a tesztelése, kizárólag megkezdik a csak a vizsgálati eszközök automatikus regisztráció engedélyezése csoportházirend és majd, ha Ön minden egyéb eszköz válassza.

- Bevezetés a Windows-kezelés régebbi rendszerű számítógépek, központilag telepítheti egy [Windows Installer-csomag](#windows-installer-packages-for-non-windows-10-computers) a kiválasztott számítógépekre.

- Ha Windows 8.1-tartományhoz csatlakoztatott eszközökre küldje le a csoportházirend-objektumot, a csatlakozási kísérlet történik; azt ajánljuk, hogy használja a [Windows Installer-csomag](#windows-installer-packages-for-non-windows-10-computers) a Windows-kezelés régebbi minden eszköz csatlakozni. 

### <a name="create-a-group-policy-object"></a>A csoportházirend-objektum létrehozása 

A szabályozáshoz a jelenlegi Windows-számítógépek, telepítenie kell a **eszközként regisztrálja a tartományhoz csatlakozó számítógépek** csoportházirend-objektumot a regisztrálni kívánt eszközöket. Telepíthet például a házirend kapcsolását egy szervezeti egységhez vagy a biztonsági csoporthoz.

**A házirend beállítása:**

1. Nyissa meg **Kiszolgálókezelő**, majd lépjen `Tools > Group Policy Management`.
2. Nyissa meg a tartomány csomópontot, amely megfelel a tartományhoz, ahol szeretné aktiválni automatikus regisztráció a jelenlegi Windows-számítógépek.
3. Kattintson a jobb gombbal **csoportházirend-objektumok**, majd válassza ki **új**.
4. Írja be a csoportházirend-objektum nevét. Például * hibrid az Azure AD join. 
5. Kattintson az **OK** gombra.
6. Kattintson a jobb gombbal az új csoportházirend-objektumot, majd válassza ki **szerkesztése**.
7. Ugrás a **számítógép konfigurációja** > **házirendek** > **felügyeleti sablonok** > **Windows-összetevők** > **Eszközregisztráció**. 
8. Kattintson a jobb gombbal **eszközként regisztrálja a tartományhoz csatlakozó számítógépek**, majd válassza ki **szerkesztése**.
   
   > [!NOTE]
   > Ez a csoportházirend-sablon át lett nevezve a Csoportházirend kezelése konzol korábbi verzióihoz képest. Egy korábbi verzióját a konzol használatakor Ugrás `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Válassza ki **engedélyezve**, és kattintson a **alkalmaz**. Ki kell választania **letiltott** Ha azt szeretné, hogy a házirendet, az eszközök automatikusan regisztrálja az Azure ad-vel a csoportházirend szabályoz.

8. Kattintson az **OK** gombra.
9. A csoportházirend-objektum csatolása a megfelelő helyre. Például társíthatja azt egy adott szervezeti egység. Is sikerült csatolható egy adott biztonsági számítógépek csoportja, amelyek automatikusan csatlakoznak az Azure ad-val. Az ezzel a házirend-beállítását minden tartományhoz csatlakoztatott Windows 10 és Windows Server 2016 a szervezet számítógépeire, kapcsolja a csoportházirend-objektumot a tartományhoz.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>A Windows 10 számítógépek Windows Installer-csomag

Windows-kezelés régebbi számítógépek tartományhoz csatlakoztatása összevont környezetben, töltse le és telepítse a Windows Installer-csomag (.msi) letöltőközpontján a [Microsoft munkahelyi csatlakoztatás Windows 10 számítógépek](https://www.microsoft.com/en-us/download/details.aspx?id=53554) lap.

A csomagot a szoftverek terjesztési rendszer például System Center Configuration Manager segítségével telepíthet. A csomag támogatja a szabványos beavatkozás nélküli telepítés beállításainak a *csendes* paraméter. A System Center Configuration Manager aktuális ágának további előnyökkel korábbi verzióiról, például a befejezett regisztrációk követését. További információkért lásd: [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

A telepítő ütemezett feladatot hoz létre, amely a felhasználó környezetében fut a rendszeren. A feladat lesz kiváltva, ha a felhasználó bejelentkezik a Windowsba. A feladat csendes csatlakozik az eszköz hitelesítése az integrált Windows-hitelesítés használata után a felhasználói hitelesítő adatokat az Azure AD-val. Az eszközt, az ütemezett feladat megtekintéséhez lépjen **Microsoft** > **munkahelyi csatlakoztatás**, és folytassa a Feladatütemező könyvtárba.

## <a name="step-5-verify-joined-devices"></a>5. lépés: Ellenőrizze a csatlakoztatott eszközök

Ellenőrizheti a sikeres csatlakoztatott eszközök a szervezetben használatával a [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) a parancsmag a [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A parancsmag kimenete a regisztrált vagy az Azure ad-val csatlakoztatott eszközöket jeleníti meg. Minden eszköz használatához a **-minden** paraméter, és majd szűréséhez használja a **deviceTrustType** tulajdonság. Tartományhoz csatlakozó eszközök értéket veheti fel **tartományhoz**.

## <a name="next-steps"></a>További lépések

* [Kezelés az Azure Active Directoryban bemutatása](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
