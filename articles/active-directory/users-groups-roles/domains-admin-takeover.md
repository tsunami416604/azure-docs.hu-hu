---
title: Nem felügyelt könyvtár rendszergazdai átvétele – Azure AD | Microsoft Docs
description: DNS-tartománynév átvétele nem felügyelt Azure AD-szervezetben (árnyékmásolat-bérlő).
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
ms.openlocfilehash: 7a0697e151c50b9722fef908eeb2c7498503b8c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027373"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Nem felügyelt címtár átvétele rendszergazdaként Azure Active Directory

Ez a cikk két módszert ismertet a DNS-tartománynév átvételére egy nem felügyelt címtárban Azure Active Directory (Azure AD). Amikor egy önkiszolgáló felhasználó regisztrál egy Azure AD-t használó felhőszolgáltatásra, a rendszer az e-mail-címe alapján hozzáadja egy nem felügyelt Azure AD-címtárhoz. További információ az önkiszolgáló vagy a "vírusos" szolgáltatással való regisztrációról: [Mi az a Azure Active Directory önkiszolgáló regisztrációja?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Döntse el, hogyan szeretné átvenni a nem felügyelt címtárat
A rendszergazdai átvétel során az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című cikkben leírt módon bizonyíthatja tulajdonjogát. A következő szakaszok részletesen ismertetik a rendszergazdai feladatokat, amelyek összegzése itt olvasható:

* Ha [„belső” rendszergazdai átvételt](#internal-admin-takeover) végez egy nem felügyelt Azure-címtáron, a rendszer hozzáadja Önt a nem felügyelt címtár globális rendszergazdájaként. Semmilyen felhasználó, tartomány vagy szolgáltatáscsomag nem lesz migrálva az Ön által felügyelt más címtárakba.

* Ha [„külső” rendszergazdai átvételt](#external-admin-takeover) végez egy nem felügyelt Azure-címtáron, hozzáadja a nem felügyelt címtár DNS-tartománynevét az Ön által felügyelt Azure-címtárhoz. A tartománynév hozzáadásakor az Ön által felügyelt Azure-címtárban létrejön a felhasználók és erőforrások közötti kapcsolatok leképezése, így a felhasználók továbbra is megszakítás nélkül érhetik el a szolgáltatásokat. 

## <a name="internal-admin-takeover"></a>Belső rendszergazdai átvétel

A SharePoint-és OneDrive, például az Office 365-et tartalmazó termékek nem támogatják a külső átvételt. Ha ez a forgatókönyv, vagy ha Ön rendszergazda, és nem felügyelt vagy "Shadow" bérlőt szeretne létrehozni az önkiszolgáló regisztrációt használó felhasználók számára, ezt egy belső rendszergazdai átvételsel teheti meg.

1. Hozzon létre egy felhasználói környezetet a nem felügyelt bérlőn a Power BI regisztrálásával. Például a következő lépések elvégzésével feltételezhető, hogy az elérési út.

2. Nyissa meg az [Power bi webhelyet](https://powerbi.com) , és válassza az **ingyenes indítás**lehetőséget. Adjon meg egy olyan felhasználói fiókot, amely a szervezet tartománynevét használja; például `admin@fourthcoffee.xyz`. Az ellenőrző kód megadása után tekintse meg az e-mailt a megerősítő kódhoz.

3. A Power BI megerősítő e-mailben válassza az **Igen, ez nekem**lehetőséget.

4. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://portal.office.com/admintakeover) a Power bi felhasználói fiókkal. Egy üzenet jelenik meg, amely arra utasítja Önt, hogy a nem felügyelt bérlőben már ellenőrzött tartománynév **rendszergazdája legyen** . válassza **az igen, szeretnék a rendszergazdát**.
  
   ![a rendszergazdai jogosultságok első képernyőképe](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adja hozzá a TXT-rekordot, hogy igazolja, hogy a tartománynév-regisztrálónál a **fourthcoffee. xyz** nevű tartománynév van. Ebben a példában ez a GoDaddy.com.
  
   ![TXT-rekord hozzáadása a tartománynévhez](./media/domains-admin-takeover/become-admin-txt-record.png)

A DNS TXT-rekordok a tartománynév-regisztrálónál való ellenőrzésekor kezelheti az Azure AD-bérlőt.

Az előző lépések elvégzése után Ön most már az Office 365 negyedik kávé bérlője globális rendszergazdája. Ha a tartománynevet a többi Azure-szolgáltatással szeretné integrálni, távolítsa el az Office 365-ből, és vegye fel egy másik felügyelt bérlőbe az Azure-ban.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Tartománynév hozzáadása felügyelt bérlőhöz az Azure AD-ben

1. Nyissa meg a [Microsoft 365 felügyeleti központot](https://admin.microsoft.com).
2. Válassza a **felhasználók** fület, és hozzon létre egy olyan nevű új felhasználói fiókot, amelynek a neve például a *User\@fourthcoffeexyz.onmicrosoft.com* , amely nem az egyéni tartománynevet használja. 
3. Győződjön meg arról, hogy az új felhasználói fiók globális rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz.
4. Nyissa meg a **tartományok** fület a Microsoft 365 felügyeleti központban, válassza ki a tartománynevet, és válassza az **Eltávolítás**lehetőséget. 
  
   ![a tartománynév eltávolítása az Office 365-ből](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha az Office 365-ben olyan felhasználók vagy csoportok találhatók, amelyek az eltávolított tartománynévre hivatkoznak, akkor azokat a. onmicrosoft.com tartományba kell átnevezni. Ha kényszeríti a tartománynév törlését, az összes felhasználó automatikusan átnevezve lesz, ebben a példában a *felhasználó\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Jelentkezzen be az [Azure ad felügyeleti központba](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely az Azure ad-bérlő globális rendszergazdája.
  
7. Válassza az **Egyéni tartománynevek**lehetőséget, majd adja hozzá a tartománynevet. A tartománynév tulajdonjogának ellenőrzéséhez meg kell adnia a DNS TXT-rekordokat. 
  
   ![Az Azure AD-hez hozzáadott tartomány ellenőrizve](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> A Power BI vagy az Azure Rights Management szolgáltatás azon felhasználói, akik rendelkeznek az Office 365-bérlőhöz hozzárendelt licenccel, menteniük kell az irányítópultokat, ha a tartománynév el lett távolítva. Felhasználói névvel (például *felhasználói\@fourthcoffeexyz.onmicrosoft.com* ) kell bejelentkezniük, ahelyett, hogy a *felhasználó\@a fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Külső rendszergazdai átvétel

Ha már felügyel egy bérlőt az Azure-szolgáltatásokkal vagy az Office 365-mel, nem adhat hozzá egyéni tartománynevet, ha már ellenőrizve van egy másik Azure AD-bérlőben. Az Azure AD-beli felügyelt bérlője azonban átveheti a nem felügyelt bérlőt külső rendszergazdai átvételre. Az általános eljárás az [egyéni tartomány hozzáadása az Azure ad-hoz](../fundamentals/add-custom-domain.md)című cikket követi.

A tartománynév tulajdonjogának ellenőrzésekor az Azure AD eltávolítja a tartománynevet a nem felügyelt bérlőről, és áthelyezi a meglévő bérlőre. A nem felügyelt címtár külső rendszergazdai átvételéhez ugyanazt a DNS TXT-ellenőrzési folyamatot kell megadnia, mint a belső rendszergazdai átvétel. A különbség az, hogy az alábbiak is át lesznek helyezve a tartománynévvel:

- Felhasználók
- Előfizetések
- Licenc-hozzárendelések

### <a name="support-for-external-admin-takeover"></a>Külső rendszergazdai átvétel támogatása
A külső rendszergazdai átvételt a következő online szolgáltatások támogatja:

- Azure Rights Management
- Exchange Online

A támogatott szolgáltatási csomagok a következők:

- Ingyenes PowerApps
- Ingyenes PowerFlow
- RMS egyéni felhasználók számára
- Microsoft Stream
- Dynamics 365 ingyenes próbaverzió

A külső rendszergazdai átvétel nem támogatott bármely olyan szolgáltatás esetében, amely tartalmazza a SharePoint, a OneDrive vagy a Skype vállalati verziójának szolgáltatási csomagját. például egy Office ingyenes előfizetésen keresztül. 

Igény szerint a [ **ForceTakeover** beállítással](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) is eltávolíthatja a tartománynevet a nem felügyelt bérlőről, és ellenőrizheti a kívánt bérlőn. 

#### <a name="more-information-about-rms-for-individuals"></a>További információ az RMS egyéni felhasználók számára szolgáltatásról

Az [RMS egyéni felhasználók](/azure/information-protection/rms-for-individuals)számára esetében, ha a nem felügyelt bérlő ugyanabban a régióban található, mint a bérlő, az automatikusan létrehozott [Azure Information Protection bérlői kulcsot](/azure/information-protection/plan-implement-tenant-key) és az [alapértelmezett védelmi sablonokat](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) is áthelyezi a tartománynévvel.

A kulcs és a sablonok nem lesznek áthelyezve, amikor a nem felügyelt bérlő egy másik régióban található. Ha például a nem felügyelt bérlő Európában található, és a saját szervezete Észak-Amerika van.

Bár az RMS egyéni felhasználók számára készült, hogy támogassa az Azure AD-hitelesítést a védett tartalom megnyitásához, nem akadályozza meg, hogy a felhasználók is megóvják a tartalmat. Ha a felhasználók védik a tartalmat az RMS egyéni felhasználók számára előfizetéssel, és a kulcs és a sablonok nem lettek áthelyezve, akkor ez a tartalom nem érhető el a tartomány átvétele után.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-parancsmagok a ForceTakeover beállításhoz
Ezeket a parancsmagokat a [PowerShell-példában](#powershell-example)is láthatja.

parancsmag | Használat
------- | -------
`connect-msolservice` | Ha a rendszer kéri, jelentkezzen be a felügyelt bérlőbe.
`get-msoldomain` | Megjeleníti az aktuális bérlőhöz társított tartományneveket.
`new-msoldomain –name <domainname>` | Nem ellenőrzöttként adja hozzá a tartománynevet a bérlőhöz (a DNS-ellenőrzés még nem lett elvégezve).
`get-msoldomain` | A tartománynév már szerepel a felügyelt bérlőhöz társított tartománynevek listáján, de nem **ellenőrzöttként**van felsorolva.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Megadja a tartományhoz tartozó új DNS TXT-rekordba (MS = XXXXX) való üzembe helyezéshez szükséges adatokat. Előfordulhat, hogy az ellenőrzés nem történik meg azonnal, mert eltart egy ideig a TXT-rekord propagálásához, ezért várjon néhány percet, mielőtt fontolóra venné a **-ForceTakeover** kapcsolót. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha a tartománynevet még nem ellenőrzi, folytathatja a **-ForceTakeover** kapcsolót. Ellenőrzi, hogy létrejött-e a TXT-rekord, és elindította-e az átvételi folyamatot.<li>A **-ForceTakeover** beállítást csak akkor adja hozzá a parancsmaghoz, ha a külső rendszergazdai átvételt kényszeríti, például ha a nem felügyelt bérlő rendelkezik Office 365-szolgáltatásokkal, amelyek blokkolják az átvételt.
`get-msoldomain` | A tartománynevek mostantól **ellenőrzöttként**jelenítik meg a tartománynevet.

> [!NOTE]
> A nem felügyelt Azure AD-szervezet 10 nappal a külső átvételi kényszerítési lehetőség gyakorlása után törlődik.

### <a name="powershell-example"></a>PowerShell-példa

1. Kapcsolódjon az Azure AD-hez az önkiszolgáló ajánlatra való válaszadáshoz használt hitelesítő adatok használatával:
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Tartományok listájának lekérése:
  
   ```powershell
    Get-MsolDomain
   ```
3. A Get-MsolDomainVerificationDns parancsmag futtatásával hozzon létre egy kihívást:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Másolja ki a parancs által visszaadott értéket (a kihívást). Például:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. A nyilvános DNS-névtérben hozzon létre egy olyan DNS TXT-rekordot, amely az előző lépésben másolt értéket tartalmazza. A rekord neve a szülőtartomány neve, így ha létrehozza ezt az erőforrásrekordot a Windows Server DNS-szerepkörének használatával, hagyja üresen a rekord nevét, és illessze be az értéket a szövegmezőbe.
6. Futtassa a Confirm-MsolDomain parancsmagot a probléma ellenőrzéséhez:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Például:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

A sikeres kihívás egy hiba nélkül visszaadja a kérést.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartománynév hozzáadása az Azure AD-hez](../fundamentals/add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
