---
title: "Egy nem felügyelt könyvtár vagy az Azure Active Directoryban árnyékmásolat Bérlői rendszergazda felvásárlási |} Microsoft Docs"
description: "Hogyan vegye át az Azure Active Directoryban egy nem felügyelt könyvtárban (árnyékmásolat bérlő) egy DNS-tartománynevet."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: ee100fa86d78840a3b6a6bbf9453954c054931c2
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Egy nem felügyelt directory átveszi az Azure Active Directoryban rendszergazdaként
Ez a cikk ismerteti a két módon vegye át egy DNS-tartománynév, az Azure Active Directory (Azure AD) egy nem felügyelt könyvtárban. Egy önkiszolgáló felhasználó előfizet egy felhőalapú szolgáltatás által használt Azure AD, ha hozzáadja őket egy nem felügyelt Azure AD-címtár az e-mail tartománya alapján. Önkiszolgáló, vagy a szolgáltatás "ugrásszerű" létrehozni kapcsolatban bővebben lásd: [Mi az az Azure Active Directory önkiszolgáló regisztráció?]()

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Eldöntheti, hogyan egy nem felügyelt directory átvétele
A rendszergazda felvásárlási során igazolhatja tulajdonjogát a [egy egyéni tartománynév hozzáadása az Azure AD](add-custom-domain.md). A következő szakaszok ismertetik a rendszergazdai feladatok részletes, de összegzése:

* Ha egy ["belső" rendszergazda felvásárlási](#internal-admin-takeover) egy nem felügyelt Azure címtár kerülnek a nem felügyelt címtár globális rendszergazdájaként. Nincs felhasználók, a tartományok vagy a service-csomagokról felügyelhető bármely más könyvtárba lesznek áttelepítve.

* Ha egy ["external" admin felvásárlási](#external-admin-takeover) egy nem felügyelt Azure-címtárhoz, a DNS-tartománynév, a nem felügyelt könyvtár hozzáadása az felügyelt Azure-címtár. Amikor a tartomány nevét, a felhasználók, erőforrások hozzárendelése létrejön az felügyelt Azure-címtár, hogy a felhasználók továbbra is hozzáférjen a szolgáltatásokhoz megszakítás nélkül. 

## <a name="internal-admin-takeover"></a>Belső rendszergazda felvásárlási

Termékek, például a SharePoint és a OneDrive, például az Office 365, nem támogatja a külső felvásárlási. Ha ez a forgatókönyv, vagy ha egy rendszergazda, és szeretné, hogy egy nem felügyelt átvétele vagy "árnyékmásolat" bérlői önkiszolgáló regisztrációs használó felhasználók létrehozása, akkor is ugyanezt a műveletet egy belső rendszergazda felvásárlási.

1. A nem felügyelt bérlői iratkozik fel például a Power BI használatával hozzon létre felhasználói környezetet. Példa kényelmi célokat szolgál ezek a lépések feltételezik, hogy elérési útja.

2. Nyissa meg a [Power BI-webhelyen](https://powerbi.com) válassza **szabad Start**. Adjon meg egy felhasználói fiókot, amely a szervezet; tartománynevet használja: például `admin@fourthcoffee.xyz`. Az ellenőrző kód megadása után ellenőrizze az e-maileket, a megerősítési kódot.

3. Válassza ki a visszaigazoló e-mailben a Power BI szolgáltatásból, **Igen, ez én vagyok**.

4. Jelentkezzen be a [Office 365 felügyeleti központban](https://portal.office.com/adminportal/Home) a Power BI felhasználói fiókkal. Kap egy üzenetet, amely arra utasítja, hogy **válnak a felügyeleti** a már a nem felügyelt bérlői ellenőrzött tartomány neve. Válassza ki **Igen, szeretnék-e a rendszergazda**.
  
  ![a rendszergazda Become első képernyőképe](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adja hozzá a TXT-rekord igazolnia, hogy Ön a tulajdonosa a tartománynév **fourthcoffee.xyz** neve tartományregisztrálónál. Az ebben a példában GoDaddy.com is.
  
  ![Adjon hozzá egy txt rekordot a tartománynév](./media/domains-admin-takeover/become-admin-txt-record.png)

Ha a DNS TXT rekord ellenőrzött tartomány nevét a regisztrálójánál, kezelheti az Azure AD-bérlő.

Amikor befejezte az előző lépéseket, áll az Office 365-ben a negyedik kávé bérlő globális rendszergazdája. A tartománynév integrálása a más Azure-szolgáltatásokkal, távolítsa el az Office 365 szolgáltatásból, és adja hozzá az Azure-ban a különböző felügyelt bérlő.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>A tartománynév hozzáadása egy felügyelt bérlői Azure AD-ben 

1. Nyissa meg a [Office 365 felügyeleti központban](https://portal.office.com/adminportal/Home).
2. Válassza ki **felhasználók** lapot, és hozzon létre egy új felhasználói fiók egy nevet, például a  *user@fourthcoffeexyz.onmicrosoft.com*  , amely nem használja az egyéni tartománynév. 
3. Győződjön meg arról, hogy az új felhasználói fiók az Azure AD-bérlő globális rendszergazdai jogosultságokkal rendelkezik.
4. Nyissa meg **tartományok** az Office 365 felügyeleti központban lapot, válassza ki a tartomány nevét, és válasszon **eltávolítása**. 
  
  ![Távolítsa el a tartománynév az Office 365 szolgáltatásból](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Ha a felhasználók és csoportok az Office 365-ben az eltávolított tartománynév van, akkor kell átnevezni az. onmicrosoft.com tartományt. Ha kényszeríti a tartománynév törlése, minden felhasználó automatikusan átnevezi, a példában szereplő  *user@fourthcoffeexyz.onmicrosoft.com* .
  
6. Jelentkezzen be a [az Azure AD felügyeleti központban](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely az az Azure AD-bérlő globális rendszergazdája.
  
7. Válassza ki **egyéni tartománynevek**, majd adja hozzá a tartomány nevét. Írja be a tulajdonjogát, a tartománynév ellenőrzése DNS TXT-rekordot kell. 
  
  ![az Azure AD hozzá tartomány](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> A Power bi-ban vagy az Azure Rights Management szolgáltatást az Office 365-bérlő a licenccel rendelkező felhasználók menteni kell az irányítópultokat, ha a tartomány nevét a rendszer eltávolítja. Azok a felhasználónevet, például be kell jelentkeznie  *user@fourthcoffeexyz.onmicrosoft.com*  helyett  *user@fourthcoffee.xyz* .

## <a name="external-admin-takeover"></a>Külső felügyeleti felvásárlási

Ha már kezeli egy Azure-szolgáltatások vagy az Office 365 bérlői, egy egyéni tartománynevet nem vehető fel, ha már egy másik Azure AD-bérlő a rendszer ellenőrzi. Azonban az Azure AD-ben a felügyelt bérlői végre egy nem felügyelt bérlői keresztül, egy külső rendszergazda felvásárlási. Az általános eljárást követi a cikk [hozzá egyéni tartományt az Azure AD](add-custom-domain.md).

Ellenőrizze a tartománynév tulajdonjogát, az Azure AD a nem felügyelt bérlői eltávolítja a tartomány nevét, és áthelyezi azt a meglévő bérlő. Külső felügyeleti felvásárlási egy nem felügyelt könyvtár DNS TXT érvényesítési leírt eljárást belső rendszergazda felvásárlási igényel. A különbség az, hogy a következő vannak is átkerül a tartomány nevét:

- Felhasználók
- Előfizetések
- A licenc-hozzárendeléseivel
 
A [ **ForceTakeover** beállítás](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) tartomány nevét a külső rendszergazda felvásárlási csak két szolgáltatást, a Power bi-ban és az Azure RMS esetén támogatott.

### <a name="support-for-external-admin-takeover"></a>Külső felügyeleti felvásárlási támogatása
Külső felügyeleti felvásárlási a következő online szolgáltatások által támogatott:

- Power BI
- Az Azure Rights Management szolgáltatásbeli (RMS)
- Exchange Online

A támogatott service-csomagok a következők:

- Ingyenes Power bi-ban
- A Power BI Pro
- Szabad PowerApps
- Szabad PowerFlow
- Az Azure Rights Management szolgáltatás Basic (RMS)
- Az Azure Rights Management szolgáltatás vállalati (RMS)
- Microsoft adatfolyam
- Dynamics 365 ingyenes próbaverzió

Nem támogatott Exernal admin felvásárlási bármely szolgáltatás, amely rendelkezik a SharePoint, a onedrive-on, illetve az üzleti Skype; service-csomagokról például az ingyenes Office-előfizetés vagy az Office alapszintű Termékváltozat.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>A ForceTakeover lehetőséget az Azure AD PowerShell-parancsmagjai
Láthatja, hogy ezek a parancsmagok használt [PowerShell-példa](#powershell-example).


A parancsmag | Használat 
------- | -------
`connect-msolservice` | Amikor a rendszer kéri, jelentkezzen be a felügyelt bérlő.
`get-msoldomain` | A tartománynév, az aktuális bérlőhöz társított jeleníti meg.
`new-msoldomain –name <domainname>` | Hozzáadja a tartomány nevét (nincs DNS-ellenőrzés elvégzése még) tartalomszűrő, a bérlői számára.
`get-msoldomain` | A tartomány neve már szerepel a felügyelt tenanthoz társított tartománynevek listája, de van megadva, **tartalomszűrő**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | A tájékoztatást ad meg szeretne adni a tartomány új DNS TXT-rekord (MS = xxxxx). Ellenőrzési előfordulhat, hogy hiba akkor fordulhat elő azonnal csak bizonyos idő a TXT rekord propagálása, mert így várja meg, annak eldöntéséhez, hogy néhány percet a **- ForceTakeover** lehetőséget. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Ha a tartomány neve még nem ellenőrizhető, és folytassa a **- ForceTakeover** lehetőséget. Ellenőrzi, hogy a TXT-rekord lett létrehozva, ezért a felvásárlási folyamat másolattól.<li>A **- ForceTakeover** beállítás hozzá kell adni a parancsmag csak akkor, ha egy külső rendszergazda felvásárlási, például ha a nem felügyelt bérlői rendelkezik Office 365-szolgáltatásokhoz blokkolja a felvásárlási kényszerítése.
`get-msoldomain` | A lista már helyesen jelenik meg a tartománynevet **ellenőrizve**.

### <a name="powershell-example"></a>PowerShell – példa

1. Csatlakozás az Azure AD az önkiszolgáló elérhető válaszadás során használt hitelesítő adatok használatával:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Tartományok listájának beolvasása:
  
  ````
    Get-MsolDomain
  ````
3. Futtassa a Get-MsolDomainVerificationDns parancsmaggal hozzon létre egy kérdés:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Ez a parancs által visszaadott érték (kérdés) másolja. Példa:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. A nyilvános DNS-névtérben az előző lépésben másolt értéket tartalmazó DNS txt rekordot kell létrehozni. A rekord értéke a szülőtartomány nevét, ezért ez az erőforrásrekord a DNS-szerepkört a Windows Server használatával hozzon létre, ha hagyja meg a rekord neve üres, és csak illessze be az értéket a szövegmezőbe.
6. A kérdés ellenőrzése a megerősítés-MsolDomain parancsmag futtatásához:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Példa:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

Sikeres kihívást visszatér a parancssorba hiba nélkül.

## <a name="next-steps"></a>Következő lépések
* [Egyéni tartománynév hozzáadása az Azure AD](add-custom-domain.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
