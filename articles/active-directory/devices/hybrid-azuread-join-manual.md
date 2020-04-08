---
title: Hibrid Azure Active Directory-csatlakoztatott eszközök manuális konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja manuálisan a hibrid Azure Active Directory-csatlakozású eszközöket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 878960738830dbe2f94b977e98215a681c4a79d2
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802552"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Oktatóanyag: Az Azure Active Directoryhoz csatlakoztatott hibrid eszközök manuális konfigurálása

Az Azure Active Directory (Azure AD) eszközkezelésével biztosíthatja, hogy a felhasználók olyan eszközökről férjenek hozzá az erőforrásokhoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. További információ: [Bevezetés az eszközkezelés az Azure Active Directoryban című témakörben.](overview.md)

> [!TIP]
> Ha az Azure AD Connect használata egy lehetőség az Ön számára, tekintse meg a kapcsolódó oktatóanyagok [felügyelt](hybrid-azuread-join-managed-domains.md) vagy [összevont](hybrid-azuread-join-federated-domains.md) tartományok. Az Azure AD Connect használatával jelentősen egyszerűsítheti a hibrid Azure AD-csatlakozás konfigurációját.

Ha helyszíni Active Directory-környezettel rendelkezik, és csatlakoztatni szeretné a tartományokhoz csatlakoztatott eszközeit az Azure AD-hoz, ezt hibrid Azure AD-csatlakoztatott eszközök konfigurálásával teheti meg. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hibrid Azure AD-csatlakozás manuális konfigurálása
> * Szolgáltatáskapcsolódási pont konfigurálása
> * Jogcímek kiadásának beállítása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * A megvalósítás hibaelhárítása

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy ismeri a következőket:

* [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)
* [A hibrid Azure Active Directory-csatlakozási megvalósítás megtervezése](hybrid-azuread-join-plan.md)
* [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)

Mielőtt elkezdené engedélyezni a hibrid Azure AD-hez csatlakozó eszközöket a szervezetben, győződjön meg arról, hogy:

* Az Azure AD Connect naprakész verzióját futtatja.
* Az Azure AD Connect szinkronizálta azoknak az eszközöknek a számítógép-objektumait, amelyekhibrid Azure AD-t szeretne csatlakoztatni az Azure AD-hez. Ha a számítógép-objektumok adott szervezeti egységekhez (Szervezeti egységekhez) tartoznak, ezeket az ous-okat az Azure AD Connect szinkronizálásához is konfigurálni kell.

Azure AD Connect:

* Megőrzi a társítás a számítógépfiók a helyszíni Active Directory-példány és az eszköz objektum az Azure AD-ben.
* Engedélyezi az eszközökkel kapcsolatos egyéb szolgáltatásokat, például a Windows Hello vállalati verziót.

Győződjön meg arról, hogy a következő URL-címek érhetők el a szervezet hálózatán belüli számítógépekről a számítógépek Azure AD-re való regisztrálásához:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* A szervezet STS-e (összevont tartományok esetén), amelyet a felhasználó helyi intranetbeállításainak tartalmaznia kell

Ha a szervezet zökkenőmentes egyszeri bejelentkezést kíván használni, a következő URL-címnek elérhetőnek kell lennie a szervezeten belüli számítógépekről. Hozzá kell adni a felhasználó helyi intranetzónájának is.

* `https://autologon.microsoftazuread-sso.com`

Emellett a következő beállításokat kell engedélyezni a felhasználó intranetes zónájában: „Az állapotsor szkriptekkel való frissítése.”

Ha a szervezet felügyelt (nem összevont) beállítást használ a helyszíni Active Directoryval, és nem használja az Active Directory összevonási szolgáltatásokat (AD FS) az Azure AD-vel való összevonáshoz, akkor a hibrid Azure AD-csatlakozás a Windows 10-en az Active Directory ban lévő számítógép-objektumokra támaszkodik az Azure AD-vel való szinkronizáláshoz. Győződjön meg arról, hogy minden olyan számítógép-objektumot, amely hibrid Azure AD-hez való csatlakozást igénylő számítógép-objektumokat tartalmaz, engedélyezve van az Azure AD Connect szinkronizálási konfigurációjában való szinkronizáláshoz.

Az 1703-as vagy korábbi verziójú Windows 10-es eszközök esetében, ha a szervezetnek kimenő proxyn keresztül kell hozzáférnie az internethez, a Web Proxy Auto-Discovery (WPAD) alkalmazást kell megvalósítania ahhoz, hogy a Windows 10-es számítógépek regisztrálhassanak az Azure AD-re.

A Windows 10 1803-tól kezdve, még akkor is, ha egy hibrid Azure AD-illesztési kísérlet egy eszköz egy összevont tartományban az AD FS-en keresztül sikertelen, és ha az Azure AD Connect úgy van beállítva, hogy szinkronizálja a számítógép/eszköz objektumok at Azure AD, az eszköz megpróbálja befejezni a hibrid Azure AD-csatlakozás a szinkronizált számítógép/eszköz használatával.

Annak ellenőrzéséhez, hogy az eszköz hozzá tud-e férni a fenti Microsoft-erőforrásokhoz a rendszerfiók alatt, használhatja a [Test Device Registration Connectivity](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) parancsfájlt.

## <a name="verify-configuration-steps"></a>Konfigurációs lépések ellenőrzése

A hibrid Azure AD-csatlakoztatott eszközöket számos típusú Windows-alapú eszközplatformhoz konfigurálhatja. Ez a témakör az összes jellemző konfigurációs forgatókönyv szükséges lépéseit tartalmazza:  

Az alábbi táblázatban áttekintheti a forgatókönyvéhez szükséges lépéseket:  

| Lépések | Jelenlegi Windows és a jelszókivonat szinkronizálása | Jelenlegi Windows és összevonás | Korábbi verziójú Windows |
| :--- | :---: | :---: | :---: |
| A szolgáltatáskapcsolódási pont konfigurálása | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Jogcímek kiadásának beállítása |     | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |
| Nem Windows 10 operációs rendszerű eszközök engedélyezése |       |        | ![Jelölőnégyzet][1] |
| Csatlakoztatott eszközök ellenőrzése | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] | ![Jelölőnégyzet][1] |

## <a name="configure-a-service-connection-point"></a>Szolgáltatáskapcsolódási pont konfigurálása

Az eszközök egy szolgáltatás csatlakozási pont (SCP) objektumot használ a regisztráció során az Azure AD-bérlői adatok felderítéséhez. A helyszíni Active Directory-példányban a hibrid Azure AD-hez csatlakozó eszközök SCP-objektumának a számítógép erdőjének konfigurációs elnevezési környezetpartíciójában kell lennie. Erdőnként egy konfigurációs névhasználati környezet létezik. Többerdős Active Directory-konfigurációesetén a szolgáltatás csatlakozási pontjának minden olyan erdőben léteznie kell, amely tartományhoz csatlakozó számítógépeket tartalmaz.

Használja a [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) parancsmagot az erdő konfigurációs névhasználati környezetének lekéréséhez.  

A *fabrikam.com* Active Directory-tartománynevű erdő konfigurációs névhasználati környezete a következő:

`CN=Configuration,DC=fabrikam,DC=com`

A tartományokhoz csatlakoztatott eszközök automatikus regisztrációjának SCP-objektuma a következő helyen található a saját erdőjében:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Attól függően, hogy hogyan telepítette az Azure AD Connect, az SCP-objektum lehet, hogy már konfigurálva van.
Ellenőrizheti az objektum létezését, és lekérheti a felderítési értékeket a következő Windows PowerShell parancsfájl használatával:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

A **$scp. A kulcsszavak** kimenete az Azure AD-bérlői adatokat jeleníti meg. Például:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Ha a szolgáltatáskapcsolódási pont nem létezik, a létrehozásához futtassa az `Initialize-ADSyncDomainJoinedComputerSync` parancsmagot az Azure AD Connect-kiszolgálóján. A parancsmag futtatásához vállalati rendszergazdai hitelesítő adatok szükségesek.  

A parancsmag:

* Létrehozza a szolgáltatás csatlakozási pontot az Active Directory erdőben, amelyhez az Azure AD Connect csatlakozik.
* Az `AdConnectorAccount` paraméter megadása szükséges hozzá. Ez a fiók az Azure AD Connect Active Directory-összekötő fiókként van konfigurálva.


Az alábbi szkriptben egy példa látható a parancsmag használatára. A szkript `$aadAdminCred = Get-Credential` eleme esetében egy felhasználónév megadása szükséges. A felhasználónevet egyszerű felhasználónév (UPN) formátumban kell megadni (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Az `Initialize-ADSyncDomainJoinedComputerSync` parancsmag:

* Az Active Directory PowerShell modult és az Azure Active Directory tartományi szolgáltatások (Azure AD DS) eszközeit használja. Ezek az eszközök a tartományvezérlőn futó Active Directory webszolgáltatásokra támaszkodnak. Az Active Directory webszolgáltatások Windows Server 2008 R2 vagy újabb rendszerű tartományvezérlőkön támogatott.
* Kizárólag az MSOnline PowerShell modul 1.1.166.0-s verziója támogatja. A modul letöltéséhez használja [ezt a linket](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).
* Ha az AD DS-eszközök `Initialize-ADSyncDomainJoinedComputerSync` nincsenek telepítve, sikertelen lesz. Az AD DS-eszközök a Kiszolgálókezelőn keresztül a **Szolgáltatások** > **távoli kiszolgálófelügyeleti eszközök** > **szerepkörfelügyeleti eszközei területen**telepíthetők.

Windows Server 2008 vagy korábbi verziókat futtató tartományvezérlők esetén a következő parancsfájl segítségével hozza létre a szolgáltatás csatlakozási pontját. Többerdős konfigurációesetén a következő parancsfájl segítségével hozza létre a szolgáltatás csatlakozási pontját minden olyan erdőben, ahol számítógépek léteznek.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

Az előző parancsfájlban `$verifiedDomain = "contoso.com"` egy helyőrző. Cserélje le az Azure AD-ben az ellenőrzött tartománynevek egyikére. Használat előtt a tartománynak kell a tulajdonában lennie.

Az ellenőrzött tartománynevekről további információt az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz című témakörben talál.](../active-directory-domains-add-azure-portal.md)

Az ellenőrzött vállalati tartományok listáját a [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Jogcímek kiadásának beállítása

Összevont Azure AD-konfigurációban az eszközök az AD FS-re vagy egy Microsoft-partner helyszíni összevonási szolgáltatására támaszkodnak az Azure AD hitelesítéséhez. Az eszközök a hitelesítés révén kapnak hozzáférési jogkivonatokat, amelyekkel regisztrálhatnak az Azure Active Directory eszközregisztrációs szolgáltatásába (Azure DRS).

A Windows aktuális eszközei integrált Windows-hitelesítéssel hitelesítik magukat a helyszíni összevonási szolgáltatás által üzemeltetett aktív WS-Trust végponthoz (1.3-as vagy 2005-ös verziók).

AD FS használata esetén engedélyeznie kell a következő WS-Trust végpontokat
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Mind **az adfs/services/trust/2005/windowstransport,** mind **az adfs/services/trust/13/windowstransport** csak intranetes végpontként engedélyezhető, és NEM lehet extranetes végpontként kitenni a webalkalmazás-proxyn keresztül. A WS-Trust Windows végpontok letiltásáról a [WS-Trust Windows végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakörben olvashat bővebben. A > **Szolgáltatásvégpontok**csoportban láthatja, hogy az **Service**AD FS felügyeleti konzolon keresztül milyen végpontok vannak engedélyezve.

> [!NOTE]
>Ha az AD FS nem rendelkezik helyszíni összevonási szolgáltatásként, kövesse a szállító utasításait, és győződjön meg arról, hogy támogatják a WS-Trust 1.3 vagy 2005 végpontokat, és hogy ezek a metaadat-exchange-fájlon (MEX) keresztül kerülnek közzétételre.

Az eszköz regisztrációjának befejezéséhez a következő jogcímeknek kell létezniük az Azure DRS által kapott jogkivonatban. Az Azure DRS egy eszközobjektumot hoz létre az Azure AD-ben ezen információk egy részével. Az Azure AD Connect ezután ezeket az információkat használja az újonnan létrehozott eszközobjektum társításához a helyszíni számítógépfiókkal.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Ha több ellenőrzött tartománynévvel rendelkezik, a következő jogcímeket kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Ha már kiad egy ImmutableID-jogcímet (például alternatív bejelentkezési azonosítót), akkor egy megfelelő jogcímet kell megadnia a számítógépekhez:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

A következő szakaszokban az alábbiakról talál információt:

* Az egyes jogcímekkel rendelkezniük kívánt értékek.
* Hogyan nézne ki egy definíció az AD FS-ben.

A meghatározás segít ellenőrizni, hogy az értékek léteznek-e már, vagy még létre kell őket hozni.

> [!NOTE]
> Ha helyszíni összevonási kiszolgálóként nem az AD FS-t használja, kövesse a szállítója utasításait a jogcímek kiállításához megfelelő konfiguráció létrehozásához.

### <a name="issue-account-type-claim"></a>Fióktípusra vonatkozó jogcím kiállítása

A `http://schemas.microsoft.com/ws/2012/01/accounttype` jogcímnek **tartalmaznia**kell egy DJ értéket, amely az eszközt tartományhoz csatlakozó számítógépként azonosítja. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
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
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectGUID értékének kiállítása

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` jogcímnek tartalmaznia kell a helyszíni számítógépfiók **objectGUID** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
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
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>A helyszíni számítógépfiók objectSID értékének kiállítása

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` jogcímnek tartalmaznia kell a helyszíni számítógépfiók **objectSid** értékét. Az AD FS-ben hozzáadhat egy kiállítási átalakítószabályt, amely a következőképpen néz ki:

   ```
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
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>IssuerID kiadása a számítógéphez, ha több ellenőrzött tartománynév van az Azure AD-ben

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` jogcímnek tartalmaznia kell a jogkivonatot kiállító helyszíni összevonási szolgáltatáshoz (AD FS vagy partner) kapcsolódó ellenőrzött tartománynevek bármelyikének egységes erőforrás-azonosítóját (URI). Az AD FS-ben hozzáadhat kiállítási átalakítási szabályokat, amelyek az előzőek után az alábbihoz hasonlóan néznek ki az adott sorrendben. Vegye figyelembe, hogy a felhasználók számára a szabály kifejezett kiadásához egy szabály szükséges. A következő szabályokban megjelenik egy első szabály, amely azonosítja a felhasználó és a számítógép hitelesítését.

   ```
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
   ```

Az előző jogcímben `<verified-domain-name>` helyőrző. Cserélje le az Azure AD-ben az ellenőrzött tartománynevek egyikére. Használja például `Value = "http://contoso.com/adfs/services/trust/"`a használatát.

Az ellenőrzött tartománynevekről további információt az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz című témakörben talál.](../active-directory-domains-add-azure-portal.md)  

Az ellenőrzött vállalati tartományok listáját a [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) parancsmaggal kaphatja meg.

![Vállalati tartományok listája](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Probléma azonosítóa a számítógépnek, ha van ilyen a felhasználók számára (például egy másik bejelentkezési azonosító van beállítva)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` jogcímnek érvényes értéket kell tartalmaznia a számítógépek számára. Az AD FS-ben a következő módon hozhat létre kiállítási átalakítószabályt:

   ```
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
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Az AD FS kiállítási átalakítószabályainak létrehozását segítő szkript

A következő parancsfájl segít a korábban ismertetett kiállítási átalakítási szabályok létrehozásában.

   ```
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
   ```

#### <a name="remarks"></a>Megjegyzések

* A szkript hozzáfűzi a szabályokat a meglévő szabályokhoz. Ne futtassa a parancsfájlt kétszer, mert a szabálykészlet kétszer kerül hozzáadásra. A szkript újbóli futtatása előtt ellenőrizze, hogy a jogcímekre nem vonatkoznak kapcsolódó szabályok (a kapcsolódó feltételek mellett).
* Ha több ellenőrzött tartománynévvel rendelkezik (ahogy az az Azure AD portálon vagy a **Get-MsolDomain** parancsmagon keresztül látható, állítsa be a **parancsfájlban $multipleVerifiedDomainNames** értékét **$true**. Győződjön meg arról is, hogy eltávolít minden olyan meglévő **problémajogcímet,** amelyet az Azure AD Connect vagy más módon hozhatott létre. Íme egy példa erre a szabályra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Ha a felhasználói fiókokhoz már kiállított egy **ImmutableID** jogcímet, állítsa az **$immutableIDAlreadyIssuedforUsers** értékét a **$true** értékre a szkriptben.

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

* Állítson be egy szabályzatot az Azure AD-ben, amellyel a felhasználók eszközeinek regisztrációját engedélyezheti.
* Konfigurálja a helyszíni összevonási szolgáltatást arra, hogy jogcímek kiállításával támogassa az integrált Windows-hitelesítést (IWA) az eszközök regisztrációjához.
* Adja hozzá az Azure AD-eszköz hitelesítési végpontját a helyi intranetes zónákhoz, hogy elkerülje a tanúsítványkéréseket az eszköz hitelesítésekénte.
* A Windows lefelé simuló eszközeinek vezérlése.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Szabályzat beállítása az Azure AD-ben, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását

A Windows-alapú lefelé simuló eszközök regisztrálásához győződjön meg arról, hogy a beállítás, amely lehetővé teszi a felhasználók számára, hogy regisztráljon eszközöket az Azure AD-ben engedélyezve van. Az Azure Portalon ezt a beállítást az **Azure Active Directory** > **felhasználói és csoportjai** > **eszközbeállítások**csoportban találja.

A következő szabályzatot **minden**: **A felhasználók regisztrálhatják eszközeiket az Azure AD-vel.**

![A Minden gomb, amely lehetővé teszi a felhasználók számára az eszközök regisztrálását](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>A helyszíni összevonási szolgáltatás konfigurálása

A helyszíni összevonási szolgáltatásnak támogatnia kell a **hitelesítési módszer** és a **wiaormultiauthn** jogcímek kiadását, amikor hitelesítési kérelmet kap az Azure AD függő entitásnak, amely resource_params paraméterrel rendelkezik a következő kódolt értékkel:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Ilyen kérés esetén a helyszíni összevonási szolgáltatásnak integrált Windows-hitelesítéssel kell hitelesítenie a felhasználót. Ha a hitelesítés sikeres, az összevonási szolgáltatásnak a következő két jogcímeket kell kiadnia:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

Az AD FS-ben hozzá kell adnia egy kiállításátalakítási szabályt, amely áthalad a hitelesítési módszeren. A szabály hozzáadásához:

1. Az AD FS felügyeleti konzolon nyissa meg **az AD FS** > **megbízhatósági** > kapcsolatok**függő entitásmegbízhatóságait.**
1. Kattintson a jobb gombbal a Microsoft Office 365 Identity Platform megbízható függő entitás elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.
1. A **Kiállítási átalakítószabályok** lapon válassza a **Szabály hozzáadása** lehetőséget.
1. A **Jogcímszabály** sablonlistában válassza a **Jogcímek küldése egyéni szabállyal** lehetőséget.
1. Válassza a **Tovább lehetőséget.**
1. A **Jogcímszabály neve** mezőbe írja be **az Auth metódus jogcímszabályát.**
1. A **Jogcímszabály** mezőbe írja be a következő szabályt:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Az összevonási kiszolgálón adja meg a következő PowerShell-parancsot. Cserélje ** \<le\> az RPObjectName** objektumot az Azure AD függő entitás megbízhatósági objektumának függő entitásnevére. Ezen objektum neve általában **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Az Azure AD-eszköz hitelesítési végpontjának hozzáadása a helyi intranetes zónákhoz

A tanúsítványkérések elkerülése érdekében, amikor a regisztrált eszközök felhasználói hitelesítik magukat az Azure AD-ben, leküldéses házirendet a tartományhoz csatlakozó eszközökre, hogy a következő URL-címet adja hozzá az Internet Explorer helyi intranetzónájához:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információt a [hibrid Azure AD-csatlakozás ellenőrzött érvényesítése Windows-alapú eszközökön című](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices)szakaszban talál.

## <a name="verify-joined-devices"></a>Csatlakoztatott eszközök ellenőrzése

Az [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0) [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) parancsmagjával ellenőrizheti, hogy vannak-e sikeresen összekapcsolt eszközök a szervezetben.

Ezen parancsmag kimenete megjeleníti az Azure AD-be regisztrált és az ahhoz csatlakoztatott eszközöket. Az összes eszköz bekéséhez használja a **-All** paramétert, majd szűrje őket az **deviceTrustType** tulajdonság használatával. A tartományhoz csatlakozó eszközök értéke **tartományhoz csatlakozott**.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakozó Windows-eszközökhöz való hibrid Azure AD-csatlakozással kapcsolatos problémákat tapasztal, olvassa el a következő témakört:

* [Jelenlegi Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
* [Korábbi verziójú Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Active Directory eszközkezelésének alapjai](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
