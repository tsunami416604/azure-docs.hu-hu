---
title: Nem felügyelt könyvtár rendszergazdai átvétele – Azure AD | Microsoft dokumentumok
description: Dns-tartománynév átirányítása nem felügyelt Azure AD-szervezetben (árnyékbérlő).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834188"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Nem felügyelt címtár átirányítása rendszergazdaként az Azure Active Directoryban

Ez a cikk két módon veheti át a DNS-tartománynevet egy nem felügyelt címtárban az Azure Active Directory (Azure AD). Amikor egy önkiszolgáló felhasználó regisztrál egy Azure AD-t használó felhőszolgáltatásra, a rendszer az e-mail-címe alapján hozzáadja egy nem felügyelt Azure AD-címtárhoz. Ha többet szeretne megtudni egy szolgáltatásön való önkiszolgáló vagy "vírusos" regisztrációról, olvassa el [a Mi az Azure Active Directoryra való önkiszolgáló regisztráció?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Döntse el, hogyan szeretné átvenni a nem felügyelt könyvtárat
A rendszergazdai átvétel során az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című cikkben leírt módon bizonyíthatja tulajdonjogát. A következő szakaszok részletesen ismertetik a rendszergazdai feladatokat, amelyek összegzése itt olvasható:

* Ha [„belső” rendszergazdai átvételt](#internal-admin-takeover) végez egy nem felügyelt Azure-címtáron, a rendszer hozzáadja Önt a nem felügyelt címtár globális rendszergazdájaként. Semmilyen felhasználó, tartomány vagy szolgáltatáscsomag nem lesz migrálva az Ön által felügyelt más címtárakba.

* Ha [„külső” rendszergazdai átvételt](#external-admin-takeover) végez egy nem felügyelt Azure-címtáron, hozzáadja a nem felügyelt címtár DNS-tartománynevét az Ön által felügyelt Azure-címtárhoz. A tartománynév hozzáadásakor az Ön által felügyelt Azure-címtárban létrejön a felhasználók és erőforrások közötti kapcsolatok leképezése, így a felhasználók továbbra is megszakítás nélkül érhetik el a szolgáltatásokat. 

## <a name="internal-admin-takeover"></a>Belső rendszergazdai átvétel

Egyes SharePoint ot és OneDrive-ot tartalmazó termékek, például az Office 365, nem támogatják a külső átvételt. Ha ez a forgatókönyv, vagy ha egy rendszergazda, és szeretné átvenni a nem felügyelt vagy "árnyék" bérlő létrehozása a felhasználók, akik önkiszolgáló regisztráció, megteheti ezt egy belső rendszergazdai átvétele.

1. Hozzon létre egy felhasználói környezetet a nem felügyelt bérlőben a Power BI-ra való feliratkozással. A kényelem érdekében például ezek a lépések feltételezik, hogy az elérési utat.

2. Nyissa meg a [Power BI-webhelyet,](https://powerbi.com) és válassza az **Ingyenes indítás**lehetőséget. Adjon meg egy felhasználói fiókot, amely a szervezet tartománynevét használja; például `admin@fourthcoffee.xyz`. Miután megadta az ellenőrző kódot, ellenőrizze az e-mailben a megerősítő kódot.

3. A Power BI visszaigazoló e-mailjében válassza az **Igen, azaz én**lehetőséget.

4. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://portal.office.com/admintakeover) a Power BI felhasználói fiókjával. Megjelenik egy üzenet, amely arra utasítja, hogy **legyen a rendszergazda** a tartománynév, amely már ellenőrizte a nem felügyelt bérlő. válassza az **Igen, azt akarom, hogy az admin**.
  
   ![első képernyőkép a Legyen az admin](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adja hozzá a TXT rekordot annak igazolására, hogy ön a **fourthcoffee.xyz** tartománynév tulajdonosa a tartományregisztrálónál. Ebben a példában ez GoDaddy.com.
  
   ![Txt rekord hozzáadása a tartománynévhez](./media/domains-admin-takeover/become-admin-txt-record.png)

Amikor a DNS TXT rekordok ellenőrzése a tartománynév-regisztráló, kezelheti az Azure AD-bérlő.

Az előző lépések végrehajtásával ön az Office 365 negyedik kávébérlőjének globális rendszergazdája. A tartománynév integrálása a többi Azure-szolgáltatásokkal, eltávolíthatja az Office 365-ből, és hozzáadhatja egy másik felügyelt bérlőhöz az Azure-ban.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>A tartománynév hozzáadása felügyelt bérlőhöz az Azure AD-ben

1. Nyissa meg a [Microsoft 365 Felügyeleti központot](https://admin.microsoft.com).
2. Válassza a **Felhasználók** lapot, és hozzon létre egy új felhasználói fiókot olyan névvel, mint a *\@felhasználói fourthcoffeexyz.onmicrosoft.com,* amely nem használja az egyéni tartománynevet. 
3. Győződjön meg arról, hogy az új felhasználói fiók globális rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz.
4. Nyissa meg a Microsoft 365 Felügyeleti központ **Tartományok lapját,** jelölje ki a tartománynevet, és válassza az **Eltávolítás lehetőséget.** 
  
   ![a tartománynév eltávolítása az Office 365-ből](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha az Office 365-ben olyan felhasználók vagy csoportok hivatkoznak az eltávolított tartománynévre, akkor át kell nevezni őket .onmicrosoft.com tartományra. Ha kényszeríti a tartománynév törlését, a rendszer automatikusan átnevezi az összes felhasználót, ebben a példában *a\@felhasználó fourthcoffeexyz.onmicrosoft.com.*
  
6. Jelentkezzen be az [Azure AD felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
  
7. Válassza **az Egyéni tartománynevek lehetőséget,** majd adja hozzá a tartománynevet. A tartománynév tulajdonjogának ellenőrzéséhez meg kell adnia a DNS TXT-rekordokat. 
  
   ![az Azure AD-hez hozzáadottként ellenőrzött tartomány](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> A Power BI vagy az Azure Rights Management szolgáltatás minden olyan felhasználójának, aki licenccel rendelkezik az Office 365-ös bérlőhöz, mentenie kell az irányítópultjait, ha a tartománynevet eltávolítja. Meg kell bejelentkezni a felhasználó nevét, mint *a\@felhasználó fourthcoffeexyz.onmicrosoft.com* helyett a felhasználó *\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Külső rendszergazdai átvétel

Ha már kezeli a bérlő az Azure-szolgáltatások vagy az Office 365, nem adhat hozzá egyéni tartománynevet, ha már ellenőrizte egy másik Azure AD-bérlő. Azonban a felügyelt bérlő az Azure AD-ben átveheti a nem felügyelt bérlő külső rendszergazdai átvétele. Az általános eljárás az Egyéni tartomány hozzáadása az [Azure AD-hez](../fundamentals/add-custom-domain.md)című cikket követi.

Amikor ellenőrzi a tartománynév tulajdonjogát, az Azure AD eltávolítja a tartománynevet a nem felügyelt bérlőből, és áthelyezi a meglévő bérlőbe. A nem felügyelt könyvtár külső rendszergazdai átvételéhez ugyanaz a DNS TXT-ellenőrzési folyamat szükséges, mint a belső rendszergazdai átvétel. A különbség az, hogy a következő is átköltözött a domain nevet:

- Felhasználók
- Előfizetések
- Licenc-hozzárendelések

### <a name="support-for-external-admin-takeover"></a>Külső rendszergazdai átvétel támogatása
A külső rendszergazdai átvételt a következő online szolgáltatások támogatják:

- Azure Rights Management
- Exchange Online

A támogatott szolgáltatási csomagok a következők:

- PowerApps ingyenes
- PowerFlow Ingyenes
- RMS egyéni felhasználók számára
- Microsoft Stream
- A Dynamics 365 ingyenes próbaverziója

A külső rendszergazdai átvétel nem támogatott olyan szolgáltatások esetében, amelyek sharepointot, OneDrive-ot vagy Skype Vállalati verziót tartalmazó szolgáltatási csomagokkal rendelkeznek; például egy ingyenes Office-előfizetésen keresztül. 

A [ **ForceTakeover** beállítás](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) sal elmozdíthatja a tartománynevet a nem felügyelt bérlőből, és ellenőrizheti a kívánt bérlőn. 

#### <a name="more-information-about-rms-for-individuals"></a>További információ az RMS-ről magánszemélyek számára

Az [RMS egyéni,](/azure/information-protection/rms-for-individuals)ha a nem felügyelt bérlő ugyanabban a régióban, mint a bérlő, amely a saját, az automatikusan létrehozott [Azure Information Protection bérlői kulcs](/azure/information-protection/plan-implement-tenant-key) és az alapértelmezett védelmi [sablonok](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) is átkerülnek a tartománynév.

A kulcs és a sablonok nem kerülnek át, ha a nem felügyelt bérlő egy másik régióban van. Ha például a nem felügyelt bérlő Európában van, és a saját szervezete Észak-Amerikában van.

Bár az RMS az egyének számára úgy tervezték, hogy támogassa az Azure AD-hitelesítést a védett tartalom megnyitásához, ez nem akadályozza meg a felhasználókat a tartalom védelmében. Ha a felhasználók az RMS for individuals előfizetéssel védték a tartalmat, és a kulcs és a sablonok nem kerültek át, akkor az adott tartalom nem érhető el a tartomány átvétele után.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-parancsmagok a ForceTakeover beállításhoz
Ezeket a parancsmagokat a [PowerShell példában](#powershell-example)használják.

Parancsmag | Használat
------- | -------
`connect-msolservice` | Amikor a rendszer kéri, jelentkezzen be a felügyelt bérlőbe.
`get-msoldomain` | Az aktuális bérlőhöz társított tartománynevek megjelenítése.
`new-msoldomain –name <domainname>` | Hozzáadja a tartománynevet a bérlőhöz nem ellenőrzöttként (még nem történt DNS-ellenőrzés).
`get-msoldomain` | A tartománynév most már szerepel a felügyelt bérlőhöz társított tartománynevek listájában, de **nem ellenőrzött**ként szerepel.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | A tartomány új DNS TXT rekordjába (MS=xxxxx) való behelyezési információkat tartalmazza. Előfordulhat, hogy az ellenőrzés nem történik meg azonnal, mert a TXT rekord propagálása némi időt vesz igénybe, ezért várjon néhány percet, mielőtt fontolóra venné a **-ForceTakeover** opciót. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha a tartománynév továbbra sem ellenőrizve, folytathatja a **-ForceTakeover** kapcsolót. Ellenőrzi, hogy a TXT rekord létrejött-e, és elindítja az átvételi folyamatot.<li>A **-ForceTakeover** opciót csak akkor kell hozzáadni a parancsmaghoz, ha külső rendszergazdai átvételt kényszerít, például ha a nem felügyelt bérlő office 365-szolgáltatások blokkolják az átvételt.
`get-msoldomain` | A tartománylista **mostantól ellenőrzött**tartománynévként jeleníti meg.

> [!NOTE]
> A nem felügyelt Azure AD-szervezet törlődik 10 nappal a külső átvételi kényszerítési lehetőség gyakorlása után.

### <a name="powershell-example"></a>PowerShell-példa

1. Csatlakozzon az Azure AD-hez az önkiszolgáló ajánlat megválaszolásához használt hitelesítő adatokkal:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. A tartományok listájának beszereznie:
  
   ```powershell
   Get-MsolDomain
   ```
3. A Get-MsolDomainVerificationDns parancsmag futtatásával hozzon létre egy kihívást:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Példa:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Másolja a parancsból visszaadott értéket (a kihívást). Példa:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. A nyilvános DNS-névtérben hozzon létre egy DNS txt rekordot, amely az előző lépésben másolt értéket tartalmazza. A rekord neve a szülőtartomány neve, ezért ha ezt az erőforrásrekordot a Windows Server DNS-szerepkörével hozza létre, hagyja üresen a Rekord nevet, és illessze be az értéket a Szöveg mezőbe.
6. Futtassa a Confirm-MsolDomain parancsmagát a kihívás ellenőrzéséhez:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Példa:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

A sikeres kihívás hiba nélkül visszaállítja a kérdést.

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
