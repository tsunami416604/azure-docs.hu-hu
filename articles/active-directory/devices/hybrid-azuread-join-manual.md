---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja manuálisan a hibrid Azure Active Directoryhoz csatlakoztatott eszközökön.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 851a340c4c5624fe45d065e002b732517a657303
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174265"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Hibrid Azure Active Directoryhoz csatlakoztatott eszközök kézi konfigurálása 

Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információkért lásd: [bemutatása az Eszközfelügyelet az Azure Active Directory](overview.md).


> [!TIP]
> Ha az Azure AD Connect használatával, lehetőség, tekintse meg a kapcsolódó oktatóanyagok [felügyelt](hybrid-azuread-join-managed-domains.md) vagy [összevont](hybrid-azuread-join-federated-domains.md) tartományok. Az Azure AD Connect használatával jelentősen egyszerűsítheti a hibrid Azure AD-csatlakozás konfigurálása.

Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Manuálisan konfigurálnia a hibrid Azure AD-csatlakozás
> * A szolgáltatáskapcsolódási pont konfigurálása
> * Állítsa be a kiállítási jogcímek
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * A megvalósítás hibaelhárítása
 




## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy ismeri a:
    
-  [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)    
-  [A hibrid Azure Active Directory join megvalósítás megtervezése](hybrid-azuread-join-plan.md)
-  [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)


Hibrid Azure AD-csatlakoztatott eszközök a szervezetben engedélyezése előtt ellenőrizze, hogy:

- Az Azure AD Connect egy naprakész verzióját futtatja.
- Az Azure AD Connect szinkronizálja a számítógép-objektumokat szeretné a hibrid Azure AD-hez az Azure AD-eszközök. Ha a számítógép-objektumok adott szervezeti egységben (OU) tartozik, ezeket a szervezeti egységek szinkronizáláshoz az Azure AD Connectben is konfigurálni kell.

  

Azure AD Connect:

- A számítógépfiók a helyszíni Active Directory-példányában és az eszköz objektum az Azure AD közötti társítás megőrzi. 
- Lehetővé teszi, hogy más eszközzel kapcsolatos funkciók – például a Windows Hello for Business.

Győződjön meg arról, hogy a következő URL-címek elérhetők számítógépek regisztráció a vállalati hálózaton belüli számítógépekről az Azure ad-hez:

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- A szervezet STS (az összevont tartományok), amelynek szerepelnie kell a felhasználó helyi intranet beállításait

Ha a szervezet tervei közvetlen egyszeri bejelentkezés használatára, a következő URL-címet kell lennie a szervezeten belüli számítógépekről érhető el. Azt is hozzá kell adni a felhasználó helyi intranet zónához.

- https://autologon.microsoftazuread-sso.com

Ezenkívül a következő beállítást engedélyezni kell a felhasználó intranet zóna: "Állapot parancsfájllal történő frissítésének engedélyezése."

A szervezet használja felügyelt (nem összevont) beállítása a helyszíni Active Directory, és nem használja az Active Directory összevonási szolgáltatások (AD FS) és az Azure AD összevonása, akkor a hibrid Azure AD join a Windows 10-es aktív számítógép-objektumai támaszkodik. Szinkronizálja az Azure AD-könyvtárat. Győződjön meg arról, hogy bármely szervezeti egységek, amelyek tartalmazzák a számítógép, objektumok, amelyek hibrid Azure AD-hez szükséges engedélyezve vannak az Azure AD Connect szinkronizálási konfigurációjában a szinkronizálási szolgáltatás.

1703-as vagy korábbi verziójú Windows 10 rendszerű eszközökhöz Ha a szervezet megköveteli a hozzáférést az interneten keresztül egy kimenő proxy meg kell valósítani Proxy automatikus felderítési WPAD (Web) lehetővé teszik a Windows 10-es számítógépek regisztrálása az Azure AD. 

Kezdve a Windows 10 1803, még akkor is, ha egy eszközt az AD FS-n keresztül összevont tartományt egy hibrid Azure AD join kísérlete sikertelen lesz, és ha az Azure AD Connect van konfigurálva a számítógép/eszköz objektumok az Azure AD szinkronizálása, az eszköz megpróbálja befejeződik, a hibrid Azure AD-csatlakozás a szinkronizált számítógép/eszköz hálózatokban.

## <a name="verify-configuration-steps"></a>Ellenőrizze a konfigurációs lépések

A hibrid Azure AD-csatlakoztatott eszközöket számos típusú Windows-alapú eszközplatformhoz konfigurálhatja. Ez a témakör az összes jellemző konfigurációs forgatókönyv szükséges lépéseit tartalmazza:  

Az alábbi táblázatban áttekintheti a forgatókönyvéhez szükséges lépéseket:  



| Lépések                                      | Jelenlegi Windows és a jelszókivonat szinkronizálása | Jelenlegi Windows és összevonás | Korábbi verziójú Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| A szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| Állítsa be a kiállítási jogcímek           |                                        | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |
| Nem Windows 10 operációs rendszerű eszközök engedélyezése      |                                        |                                | ![Jelölőnégyzet][1]        |
| Csatlakoztatott eszközök ellenőrzése          | ![Jelölőnégyzet][1]                            | ![Jelölőnégyzet][1]                    | ![Jelölőnégyzet][1]        |



## <a name="configure-a-service-connection-point"></a>A szolgáltatáskapcsolódási pont konfigurálása

Az eszközök a regisztráció során a szolgáltatás kapcsolódási pontjának (SCP) objektum segítségével felderíteni az információkat az Azure AD-bérlő. A helyszíni Active Directory-példányában a hibrid Azure AD-hez csatlakoztatott eszközök a szolgáltatáskapcsolódási pont objektum konfigurációs környezet partíciójára a számítógép erdő léteznie kell. Erdőnként egy konfigurációs névhasználati környezet létezik. Többerdős Active Directory-konfiguráció esetén a szolgáltatáskapcsolódási pont minden tartományhoz csatlakoztatott számítógépeket tartalmazó erdőivel léteznie kell.

Használja a [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) parancsmagot az erdő konfigurációs névhasználati környezetének lekéréséhez.  

A *fabrikam.com* Active Directory-tartománynevű erdő konfigurációs névhasználati környezete a következő:

`CN=Configuration,DC=fabrikam,DC=com`

A tartományokhoz csatlakoztatott eszközök automatikus regisztrációjának SCP-objektuma a következő helyen található a saját erdőjében:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Attól függően, hogy az Azure AD Connect telepített az SCP-objektum előfordulhat, hogy már be van állítva.
Ellenőrizze az objektum létezik-e, és a felderítési értékeket beolvasni a következő Windows PowerShell-parancsfájl használatával: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

A **$scp. A kulcsszavak** kimenet az Azure AD bérlő információit jeleníti meg. Például:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Ha a szolgáltatáskapcsolódási pont nem létezik, a létrehozásához futtassa az `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálóján. Vállalati rendszergazda hitelesítő adatai szükségesek a parancsmag futtatásához.  

A parancsmag:

- A szolgáltatáskapcsolódási pont az Active Directory-erdőben, amely csatlakozik az Azure AD Connect létrehoz. 
- Az `AdConnectorAccount` paraméter megadása szükséges hozzá. Ezt a fiókot az Active Directory-összekötő fiók az Azure AD Connectben van konfigurálva. 


Az alábbi szkriptben egy példa látható a parancsmag használatára. A szkript `$aadAdminCred = Get-Credential` eleme esetében egy felhasználónév megadása szükséges. A felhasználónevet egyszerű felhasználónév (UPN) formátumban kell megadni (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Az `Initialize-ADSyncDomainJoinedComputerSync` parancsmag:

- Az Active Directory PowerShell-modul és az Azure Active Directory Domain Services (Azure AD DS) eszközöket használ. Ezek az eszközök a tartományvezérlőn futó Active Directory webszolgáltatások támaszkodnak. Az Active Directory webszolgáltatások Windows Server 2008 R2 vagy újabb rendszerű tartományvezérlőkön támogatott.
- Az MSOnline PowerShell modul 1.1.166.0-s csak támogatja. Ez a modul letöltéséhez használjon [ezt a hivatkozást](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Ha az Azure Active Directory tartományi szolgáltatások eszközei nincsenek telepítve, `Initialize-ADSyncDomainJoinedComputerSync` sikertelen lesz. Az Azure AD DS eszközök keresztül Kiszolgálókezelő alatt telepítheti **funkciók** > **távoli kiszolgálófelügyelet eszközei** > **szerepkör-felügyeleti eszközök** .

A Windows Server 2008 vagy korábbi verzióit futtató tartományvezérlők a következő szkript használatával a szolgáltatáskapcsolódási pont létrehozása. Többerdős konfiguráció esetén a következő szkript használatával a szolgáltatáskapcsolódási pont létrehozása minden olyan erdőben, amennyiben a számítógép létezik.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your Active Directory configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

Az előző szkriptben `$verifiedDomain = "contoso.com"` helyőrző. Cserélje le valamelyik az ellenőrzött tartomány nevét az Azure ad-ben. A tartománynak a tulajdonosa, mielőtt használhatná azt kell.

Ellenőrzött tartomány nevét kapcsolatos további információkért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md). 

Az ellenőrzött vállalati tartományok listáját a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) parancsmaggal kaphatja meg. 

![Vállalati tartomány listája](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Állítsa be a kiállítási jogcímek

Az összevont Azure AD konfigurálása, eszközök az AD FS vagy egy helyszíni összevonási szolgáltatás az Azure AD-hitelesítést a Microsoft-partner alapulnak. Az eszközök a hitelesítés révén kapnak hozzáférési jogkivonatokat, amelyekkel regisztrálhatnak az Azure Active Directory eszközregisztrációs szolgáltatásába (Azure DRS).

Aktuális Windows-eszközök hitelesítése az integrált Windows-hitelesítés segítségével a helyi összevonási szolgáltatás által üzemeltetett egy aktív végpontot WS-Trust (1.3-as vagy 2005-ös verzió).

> [!NOTE]
> Vagy ha a az AD FS-ben **adfs/services/megbízhatósági/13/windowstransport** vagy **adfs/services/megbízhatósági/2005/windowstransport** engedélyezve kell lennie. Ha a webproxy-hitelesítés használata esetén is győződjön meg arról, hogy ezt a végpontot a proxyn keresztül van közzétéve. Láthatja, hogy milyen a végpontok engedélyezve vannak a az AD FS felügyeleti konzolon keresztül **szolgáltatás** > **végpontok**.
>
>Ha a helyi összevonási szolgáltatás AD FS nincs telepítve, kövesse az utasításokat a gyártójától, hogy a WS-Trust 1.3-as vagy 2005 végpontok és, hogy ezeket a metaadatokat az Exchange-fájl (MEX) keresztül közzétett támogatják.

Az eszköz a regisztráció befejezéséhez a következő jogcímeket a jogkivonatot, amely megkapja a Azure DRS léteznie kell. Az Azure DRS egy eszközobjektumot hoz létre az információk az Azure AD-ben. Az Azure AD Connect ezt az információt használja az újonnan létrehozott eszköz objektum társítása a számítógép fiók a helyszínen.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartománynévvel rendelkezik, a következő jogcímeket kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már bocsát ki egy ImmutableID jogcím (például a másodlagos bejelentkezési azonosító), meg kell adnia egy megfelelő jogcím számítógépek esetén:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban az alábbiakról talál információt:
 
- Az értékeket, amelyek az egyes jogcímek kell rendelkeznie.
- Milyen definici módon jelenik meg az AD FS-ben.

A meghatározás segít ellenőrizni, hogy az értékek léteznek-e már, vagy még létre kell őket hozni.

> [!NOTE]
> Ha helyszíni összevonási kiszolgálóként nem az AD FS-t használja, kövesse a szállítója utasításait a jogcímek kiállításához megfelelő konfiguráció létrehozásához.

### <a name="issue-account-type-claim"></a>Fióktípusra vonatkozó jogcím kiállítása

A `http://schemas.microsoft.com/ws/2012/01/accounttype` jogcím tartalmaznia kell a egy értéke **DJ**, amely azonosítja, hogy az eszköz egy tartományhoz csatlakozó számítógép. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` jogcímet kell tartalmaznia a **objectGUID** értéket a helyi számítógépfiók. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` jogcímet kell tartalmaznia a **objectSid** értéket a helyi számítógépfiók. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>A számítógép issuerID ki, ha több ellenőrzött tartomány nevét az Azure ad-ben

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` jogcímet kell tartalmaznia a egységes erőforrás-azonosító (URI) bármely, a helyi összevonási szolgáltatás (AD FS vagy partner) csatlakozó ellenőrzött tartomány nevét a jogkivonat kiadása. Az AD FS-ben jogcímkiadás-átalakítási szabályok, amelyek adott sorrendben, a következő rétegében előző azokat után is hozzáadhat. Vegye figyelembe, hogy a szabály a felhasználók explicit módon ki egy szabályt szükség. A következő szabályokat, az első szabály, amely azonosítja a felhasználói és számítógép-hitelesítés egészül ki.

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


A fenti jogcímek `<verified-domain-name>` helyőrző. Cserélje le valamelyik az ellenőrzött tartomány nevét az Azure ad-ben. Például `Value = "http://contoso.com/adfs/services/trust/"`.



Ellenőrzött tartomány nevét kapcsolatos további információkért lásd: [egy egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Az ellenőrzött vállalati tartományok listáját a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) parancsmaggal kaphatja meg. 

![Vállalati tartomány listája](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>ImmutableID kibocsátani a számítógéphez, ha a felhasználók létezik (például egy alternatív bejelentkezési azonosító beállítása)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` jogcím számítógépek érvényes értéket kell tartalmaznia. Az AD FS-ben a következő módon hozhat létre kiállítási átalakítószabályt:

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

A következő parancsfájl segítségével létrehozni a kiállítási átalakítási szabályok a fent leírtaknak.

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

#### <a name="remarks"></a>Megjegyzések 

- A szkript hozzáfűzi a szabályokat a meglévő szabályokhoz. Ne futtassa a parancsfájlt kétszer, mert a szabálykészletet kétszer kell hozzáadni. A szkript újbóli futtatása előtt ellenőrizze, hogy a jogcímekre nem vonatkoznak kapcsolódó szabályok (a kapcsolódó feltételek mellett).

- Ha több ellenőrzött tartomány nevét (az Azure AD portálon vagy keresztül látható módon a **Get-MsolDomain** parancsmaggal), az értékét állítsa be **$multipleVerifiedDomainNames** a parancsfájl **$true** . Győződjön meg arról, hogy távolítsa el a minden meglévő is **issuerid** jogcím, előfordulhat, hogy létrehozott Azure AD Connect vagy más módon. Íme egy példa, ehhez a szabályhoz:

      c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Ha a felhasználói fiókokhoz már kiállított egy **ImmutableID** jogcímet, állítsa az **$immutableIDAlreadyIssuedforUsers** értékét a **$true** értékre a szkriptben.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

- Állítson be egy szabályzatot az Azure AD-ben, amellyel a felhasználók eszközeinek regisztrációját engedélyezheti. 
- Az integrált Windows-hitelesítés (IWA) támogatásához az eszközregisztrációhoz tartozó jogcímeket kiadni a helyi összevonási szolgáltatás konfigurálása. 
- Az Azure AD-eszközök hitelesítési végpont hozzáadása a helyi intranet zónák elkerülése érdekében tanúsítványokra, ha az eszköz hitelesítéséhez.
- Windows alacsonyabb szintű eszközeinek vezérléséhez. 


### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Szabályzat beállítása az Azure AD-felhasználók regisztrálhatják eszközeiket

Windows régebbi verziójú eszközök regisztrálása, győződjön meg arról, hogy a felhasználók regisztrálhatják az eszközeiket az Azure ad-ben a beállítás engedélyezve van. Az Azure Portalon keresse meg a beállítás **Azure Active Directory** > **felhasználók és csoportok** > **eszközbeállítások**.
    
A következő szabályzatot kell beállítani **összes**: **A felhasználók regisztrálhatják eszközeiket az Azure ad-vel**.

![Az összes gombra, amely lehetővé teszi a felhasználók, eszközök regisztrálása](./media/hybrid-azuread-join-manual/23.png)


### <a name="configure-the-on-premises-federation-service"></a>A helyi összevonási szolgáltatás konfigurálása 

A helyi összevonási szolgáltatás támogatnia kell a kiállító a **authenticationmethod** és **wiaormultiauthn** , amikor egy hitelesítési kérést a tároló az Azure AD függő entitás kap jogcímeket egy a következő kódolt érték resource_params paramétert:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Ha ilyen kérést, a helyi összevonási szolgáltatás integrált Windows-hitelesítés használatával hitelesíteni a felhasználót kell. Ha a hitelesítés sikeres, akkor az összevonási szolgáltatás a következő két jogcímeket kell kiadni:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

Az AD FS-ben hozzá kell adnia egy kiállítási átalakítási szabályt, amely áthalad a hitelesítési módszert. Ez a szabály hozzáadása:

1. Az AD FS felügyeleti konzolon lépjen a **az AD FS** > **megbízhatósági kapcsolatok** > **függő entitás Megbízhatóságai**.
2. Kattintson a jobb gombbal a Microsoft Office 365 Identity Platform megbízható függő entitás elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.
3. A **Kiállítási átalakítószabályok** lapon válassza a **Szabály hozzáadása** lehetőséget.
4. A **Jogcímszabály** sablonlistában válassza a **Jogcímek küldése egyéni szabállyal** lehetőséget.
5. Kattintson a **Tovább** gombra.
6. Az a **Jogcímszabály neve** mezőbe írja be **hitelesítési módszer Jogcímszabály**.
7. Az a **jogcímszabály** mezőbe írja be a következő szabálynak:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Az összevonási kiszolgálón adja meg a következő PowerShell-parancsot. Cserélje le **\<RPObjectName\>** az Azure AD függő entitás megbízhatósági objektum a függő entitás objektum nevére. Ezen objektum neve általában **Microsoft Office 365 Identity Platform**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Az Azure AD-eszközök hitelesítési végpont hozzáadása a helyi intranet zónák

Regisztrált eszközök felhasználóinak hitelesítésére az Azure AD-tanúsítványokra elkerüléséhez küldje le a házirend a tartományhoz csatlakoztatott eszközök a következő URL-cím hozzáadása a helyi intranetzónához az Internet Explorerben:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése 

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információkért lásd: [szabályozhatja az eszközt, a hibrid Azure AD join](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

Ön sikeresen csatlakoztatott eszközök esetében a szervezet használatával ellenőrizheti a [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) parancsmagot a [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ezen parancsmag kimenete megjeleníti az Azure AD-be regisztrált és az ahhoz csatlakoztatott eszközöket. Minden eszköz lekéréséhez használja a **– minden** paramétert, és szűrése használatával a **deviceTrustType** tulajdonság. Tartományhoz csatlakoztatott eszközök a egy értéke lehet **tartományhoz csatlakozó**.



## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha olyan tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD joinnal befejezése a problémákat tapasztal, tekintse meg:

- [Jelenlegi Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Korábbi verziójú Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory eszközkezelésének alapjai](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
