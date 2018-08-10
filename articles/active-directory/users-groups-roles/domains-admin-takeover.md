---
title: Egy nem felügyelt címtár vagy egy árnyékmásolat-bérlő az Azure Active Directory rendszergazdai átvételt |} A Microsoft Docs
description: Hogyan vegye át az Azure Active Directoryban egy nem felügyelt címtár (árnyékmásolat-bérlő) egy DNS-tartománynevet.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 04/06/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 52ae7da666acaf234920a7f03afe3766f29a1e85
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629123"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Rendszergazdaként az Azure Active Directoryban egy nem felügyelt könyvtár átvétele
Ez a cikk ismerteti egy DNS-tartománynevet az Azure Active Directoryban (Azure AD) egy nem felügyelt könyvtár átvétele kétféle módon. Amikor egy önkiszolgáló felhasználó regisztrál egy Azure AD-t használó felhőszolgáltatásra, a rendszer az e-mail-címe alapján hozzáadja egy nem felügyelt Azure AD-címtárhoz. Önkiszolgáló vagy "vírusos" regisztráció kapcsolatos további információkért lásd: [Mi az Azure Active Directory önkiszolgáló regisztráció?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-self-service-signup)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Annak eldöntése, hogyan szeretné egy nem felügyelt könyvtár átvétele
A rendszergazdai átvétel során az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md) című cikkben leírt módon bizonyíthatja tulajdonjogát. A következő szakaszok részletesen ismertetik a rendszergazdai feladatokat, amelyek összegzése itt olvasható:

* Ha [„belső” rendszergazdai átvételt](#internal-admin-takeover) végez egy nem felügyelt Azure-címtáron, a rendszer hozzáadja Önt a nem felügyelt címtár globális rendszergazdájaként. Semmilyen felhasználó, tartomány vagy szolgáltatáscsomag nem lesz migrálva az Ön által felügyelt más címtárakba.

* Ha [„külső” rendszergazdai átvételt](#external-admin-takeover) végez egy nem felügyelt Azure-címtáron, hozzáadja a nem felügyelt címtár DNS-tartománynevét az Ön által felügyelt Azure-címtárhoz. A tartománynév hozzáadásakor az Ön által felügyelt Azure-címtárban létrejön a felhasználók és erőforrások közötti kapcsolatok leképezése, így a felhasználók továbbra is megszakítás nélkül érhetik el a szolgáltatásokat. 

## <a name="internal-admin-takeover"></a>Belső rendszergazdai átvétellel

Egyes termékek, amelyek tartalmazzák a SharePoint és onedrive vállalati verzió, például az Office 365, nem támogatja a külső átvételt. Ha ez a forgatókönyv, vagy ha a rendszergazda, és a egy nem felügyelt átvenni szeretné, vagy "árnyékmásolat" bérlő létrehozása a felhasználók, akik önkiszolgáló bejelentkezést használt a, ezt megteheti egy belső rendszergazdai átvétellel.

1. Hozzon létre egy felhasználói környezetben keresztül, mint a Power BI regisztráció a nem felügyelt bérlőt. Példa kényelmi célokat szolgál ezek a lépések feltételezik, hogy az elérési út.

2. Nyissa meg a [Power BI-webhelyén](https://powerbi.com) válassza **ingyenes próba megkezdése**. Adjon meg egy felhasználói fiókot, amely a szervezet; tartománynevet használja: Ha például `admin@fourthcoffee.xyz`. Az ellenőrző kód megadása után ellenőrizze az e-mailben a megerősítő kód.

3. A megerősítő e-mailben a Power bi-BÓL, válassza ki a **Igen, én**.

4. Jelentkezzen be a [Office 365 felügyeleti központban](https://portal.office.com/adminportal/Home) a Power BI felhasználói fiókkal. Megjelenik az üzenet, amely arra utasítja, hogy **váljon rendszergazdává** a tartomány neve, amely már megtörtént a nem felügyelt bérlőt. Válassza ki **Igen, szeretnék lenni a rendszergazda**.
  
  ![a rendszergazda legyen az első képernyőképe](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adja hozzá a TXT-rekordot a tartománynév tulajdonjogának igazolásához **fourthcoffee.xyz** , a tartománynév regisztrálójánál. Ebben a példában GoDaddy.com.
  
  ![Egy txt típusú rekordot a tartománynév hozzáadása](./media/domains-admin-takeover/become-admin-txt-record.png)

Ha a DNS txt típusú rekordok, a tartománynév regisztrálójánál az megtörtént, az Azure AD-bérlő is kezelheti.

Amikor végzett a fenti lépéseket, Ön mostantól az Office 365-ben a negyedik kávé bérlő globális rendszergazdája. A tartománynév integrálása más Azure-szolgáltatások, távolítsa el az Office 365-ből, és adja hozzá a különböző felügyelt bérlőre, az Azure-ban.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>A tartománynév hozzáadását felügyelt bérlőre az Azure ad-ben 

1. Nyissa meg a [Office 365 felügyeleti központban](https://portal.office.com/adminportal/Home).
2. Válassza ki **felhasználók** lapra, és hozzon létre egy új felhasználói fiókot vagy hasonló néven * user@fourthcoffeexyz.onmicrosoft.com * , amely nem használja az egyéni tartomány nevét. 
3. Győződjön meg arról, hogy az új felhasználói fiók rendelkezik-e az Azure AD-bérlő globális rendszergazdai jogosultságokkal.
4. Nyissa meg **tartományok** lapra az Office 365 felügyeleti központot, válassza ki a tartomány nevét, és válassza **eltávolítása**. 
  
  ![Távolítsa el a tartománynevet az Office 365-ből](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha a felhasználók és csoportok az Office 365-ben az eltávolított tartománynév rendelkezik, azok kell átnevezni az. onmicrosoft.com tartomány. Ha kényszeríti a tartománynév törlése, minden felhasználó automatikusan átnevezi, ebben a példában a * user@fourthcoffeexyz.onmicrosoft.com *.
  
6. Jelentkezzen be a [Azure AD felügyeleti központ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a az Azure AD-bérlő globális rendszergazdája.
  
7. Válassza ki **egyéni tartománynevek**, majd adja hozzá a tartomány nevét. Írja be az DNS txt típusú rekordot a tartománynév tulajdonjogának ellenőrzése kell. 
  
  ![tartomány hozzáadva az Azure ad-hez](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Bármely Power bi-ban vagy az Azure Rights Management szolgáltatást az Office 365-bérlő a hozzárendelt licenccel rendelkező felhasználók irányítópultokkal kell menteni, ha a tartomány nevét a rendszer eltávolítja. Jelentkezzen be egy felhasználónevet, például a használatához be kell jelentkezniük * user@fourthcoffeexyz.onmicrosoft.com * helyett * user@fourthcoffee.xyz *.

## <a name="external-admin-takeover"></a>Külső rendszergazdai átvétellel

Már az Azure-szolgáltatások vagy az Office 365-bérlő kezelése esetén nem adhat hozzá egyéni tartománynév már ellenőrizve van egy másik Azure AD-bérlő Ha. Azonban az Azure ad-ben a felügyelt bérlőről, képes átvenni egy nem felügyelt bérlőt egy külső rendszergazdai átvétellel. Az általános eljárást követi a cikk [egyéni tartomány hozzáadása az Azure AD](../fundamentals/add-custom-domain.md).

Ha a tartománynév tulajdonjogának ellenőrzéséhez az Azure AD a tartománynév távolít el a nem felügyelt bérlőt, és áthelyezi a meglévő bérlőhöz. Külső rendszergazdai átvétellel egy nem felügyelt címtár DNS TXT érvényesítési leírt eljárást belső rendszergazdai átvétellel van szükség. A különbség az, hogy a következő vannak is átkerül a tartomány nevét:

- Felhasználók
- Előfizetések
- Licenc-hozzárendelések

### <a name="support-for-external-admin-takeover"></a>Külső rendszergazdai átvétellel támogatása
A következő online services által támogatott külső rendszergazdai átvétel:

- Power BI
- Azure Rights Management
- Exchange Online

A támogatott service-csomagok a következők:

- A Power BI ingyenes
- A Power BI Pro
- A PowerApps ingyenes
- Ingyenes PowerFlow
- RMS egyéni felhasználók számára
- Microsoft Stream
- Dynamics 365 ingyenes próbaverzió

Külső rendszergazdai átvétellel bármely szolgáltatás, amely rendelkezik a SharePoint, a onedrive vállalati verzió, illetve a Skype For Business; service-csomagok esetén nem támogatott például a ingyenes Office-előfizetés vagy az Office alapszintű Termékváltozat. Lehetősége van a [ **ForceTakeover** beállítás](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) eltávolítása a nem felügyelt bérlőt a tartomány nevét, és ellenőrzi a kívánt bérlőre. Ez a beállítás ForceTakeover nem felhasználók átvitele, vagy az előfizetéshez való hozzáférés megőrzése. Ehelyett ezt a beállítást csak áthelyezi a tartomány nevét. 

#### <a name="more-information-about-rms-for-individuals"></a>További információk az RMS egyéni felhasználók számára

A [RMS egyéni felhasználók számára](/azure/information-protection/rms-for-individuals), ha a nem felügyelt bérlőt a bérlő ugyanabban a régióban, hogy saját, az automatikusan létrehozott [Azure Information Protection-bérlőkulcs](/azure/information-protection/plan-implement-tenant-key) és [alapértelmezett védelmi sablonok](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) továbbá a tartomány nevét átkerül. 

A kulcs és a sablonok nem kerülnek, ha a nem felügyelt bérlő egy másik régióban van. Például a nem felügyelt bérlő az Európai és a bérlő Észak-amerikai van saját. 

RMS egyéni felhasználók számára védett tartalom megnyitása az Azure AD-hitelesítés támogatására szolgálnak, bár nem rendszergazdák számára is a tartalmak védelmére. Felhasználók tartalomvédelemre volt az RMS egyéni felhasználók számára előfizetéssel, és a kulcs és a sablonok nem lett áthelyezve, tartalom nem érhető el követően a tartomány átvételéről.    

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Az Azure AD PowerShell-parancsmagok a ForceTakeover lehetőséget
Láthatja, hogy ezeket a parancsmagokat a használt [PowerShell-példa](#powershell-example).


A parancsmag | Használat 
------- | -------
`connect-msolservice` | Amikor a rendszer kéri, jelentkezzen be a felügyelt bérlőhöz.
`get-msoldomain` | A tartománynevek az aktuális bérlőhöz társított mutatja.
`new-msoldomain –name <domainname>` | Hozzáadja a bérlő ellenőrzött (nem DNS-ellenőrzés elvégzése még), a tartomány nevét.
`get-msoldomain` | A tartománynév már szerepel a felügyelt bérlőhöz társított tartománynevek listája, de állapottal **ellenőrzött**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Megadja az adatokat a tartományhoz tartozó új DNS txt típusú rekordot üzembe (MS = xxxxx). Ellenőrzési előfordulhat, hogy nem történik azonnal, mivel némi időre a txt típusú rekord propagálása, ezért vár néhány percet, mielőtt a mérlegeli a **- ForceTakeover** lehetőséget. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha továbbra is nem ellenőrzi a tartománynevet, folytathatja a **- ForceTakeover** lehetőséget. Ellenőrzi, hogy a TXT-rekord lett létrehozva, és az átvétel folyamat elindít.<li>A **- ForceTakeover** beállítás hozzá kell adni a parancsmag csak akkor, ha egy külső alá vonhatja rendszergazdai átvétellel, például ha a nem felügyelt bérlőt rendelkezik a Office 365-szolgáltatások blokkolja az átvétel kényszerítése.
`get-msoldomain` | A tartományok listáját jeleníti meg most tartománynevet **ellenőrzött**.

### <a name="powershell-example"></a>A PowerShell-példa

1. Csatlakozás az Azure AD önkiszolgáló előfizetésért válaszolni használt hitelesítő adatok használatával:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Tartományok listájának lekérése:
  
  ````
    Get-MsolDomain
  ````
3. Futtassa a Get-MsolDomainVerificationDns parancsmagot kihívást létrehozásához:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Ez a parancs által visszaadott értéket (a kihívás) másolja. Példa:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. Hozzon létre egy, az előző lépésben kimásolt értéket tartalmazó DNS txt típusú rekordot a nyilvános DNS-névtérben. Ez a bejegyzés a név a szülőtartomány neve, így a rekord a DNS-szerepkört a Windows Server használatával hoz létre, ha hagyja meg a rekord neve üres, és csak illessze be az értéket a szövegmezőbe.
6. Futtassa a a kérdés ellenőrzése Confirm-MsolDomain-parancsmagot:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Példa:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Sikeres kihívást visszatér a parancssorba hiba nélkül.

## <a name="next-steps"></a>További lépések
* [Egy egyéni tartománynév hozzáadása az Azure ad-ben](../fundamentals/add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
