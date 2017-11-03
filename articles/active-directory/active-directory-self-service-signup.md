---
title: "Mi az az Azure önkiszolgáló regisztráció? | Microsoft Docs"
description: "Az Áttekintés önkiszolgáló regisztrációt Azure, a regisztrációs folyamat kezelése és a DNS-tartománynév átvétele."
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
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 511088156d3546e2e0f3ac40e72bf2b8e4ae2cb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-self-service-signup-for-azure"></a>Mi az az Azure önkiszolgáló regisztráció?
Ez a témakör ismerteti az előfizetési önkiszolgáló folyamat és a DNS-tartománynév átvétele.  

## <a name="why-use-self-service-signup"></a>Miért érdemes használni önkiszolgáló regisztrációt?
* Az ügyfelek eléréséhez cég gyorsabb szolgáltatások.
* E-mail alapú ajánlatok szolgáltatás létrehozása.
* Hozzon létre előfizetési e-mail-alapú forgalom gyorsan engedélyezése a felhasználók számára a könnyen megjegyezhető munkahelyi e-mail aliasok identitások létrehozását.
* Nem felügyelt Azure-könyvtárak később a felügyelt könyvtárak tehető, és használja fel újra az egyéb szolgáltatásokat.

## <a name="terms-and-definitions"></a>Kifejezések és meghatározások
* **Önkiszolgáló regisztrációt**: Ez a mód az a felhasználó előfizet egy felhőalapú szolgáltatás és van egy automatikusan létrehozott számukra az Azure Active Directory (Azure AD) identitás alapján az e-mail tartománya.
* **Nem felügyelt Azure-címtár**: Ez az a könyvtár, ahol létrehozzák identitásukat. Egy nem felügyelt könyvtár értéke nem globális rendszergazda megegyező nevű könyvtárat.
* **E-mailek ellenőrzött felhasználói**: Ez egy felhasználói fiók típusa az Azure AD-ben. A felhasználó identitása önkiszolgáló szolgáltatásokat a regisztráció után automatikusan létrehozza az e-mailek ellenőrzött felhasználói néven ismert. Az e-mailek ellenőrzése felhasználó tagja rendszeres creationmethod címkéjű könyvtár = EmailVerified.

## <a name="user-experience"></a>Felhasználói élmény
Például tegyük fel, a felhasználó, akinek e-mail Dan@BellowsCollege.com érzékeny fájlok e-mailben kap. Azure Rights Management (Azure RMS) által védett fájlokat. De Dan a szervezeten belül, csőrugó felsőoktatási rendelkezik nem regisztrált az Azure RMS, és nem rendelkezik telepített Active Directory RMS. Ebben az esetben Dan regisztrálhat egy ingyenes RMS egyéni felhasználók számára előfizetés ahhoz, hogy a védett fájlok olvasásához.

Ha Dan az első felhasználó BellowsCollege.com való feliratkozáshoz az önkiszolgáló kínál, majd egy nem felügyelt directory hoz létre BellowsCollege.com, az Azure ad-ben az e-mail címmel. Ha a BellowsCollege.com tartomány más felhasználói regisztráljon az ajánlat vagy egy hasonló önkiszolgáló ajánlat, azokat is ugyanabban a könyvtárban nem felügyelt Azure-ban létrehozott-e-mailek ellenőrzött felhasználói fiókot.

## <a name="admin-experience"></a>Rendszergazdai feladatok
A DNS-tartománynév, a nem felügyelt Azure-címtár birtokló rendszergazda átveszi, vagy a könyvtár egyesítése után tulajdonjoga igazolására. A következő szakaszok ismertetik a rendszergazdai feladatok részletes, de összegzése:

* Egy nem felügyelt Azure-címtár átvételekor egyszerűen lesz a nem felügyelt könyvtár globális rendszergazdája. Ez egy belső felvásárlási is nevezik.
* Egy nem felügyelt Azure-címtár egyesít, a DNS-tartománynév, a nem felügyelt könyvtár hozzáadása az felügyelt Azure-címtár és a felhasználók-erőforrások leképezéseket jön létre, felhasználók továbbra is hozzáférjen a szolgáltatásokhoz megszakítás nélkül. Egy külső felvásárlási is nevezik.

## <a name="what-gets-created-in-azure-active-directory"></a>Mi az Azure Active Directoryban jön létre?
#### <a name="directory"></a>Könyvtár
* Azure Active Directory címtárhoz, a tartomány jön létre, egy címtárban tartományonként.
* Az Azure AD-címtárral rendelkezik nem globális rendszergazda segítségét.

#### <a name="users"></a>Felhasználók
* Minden felhasználó, aki regisztrál létrejön egy felhasználói objektum az Azure AD-címtár.
* Minden felhasználói objektum a külső van megjelölve.
* Egyes felhasználók hozzáférése a szolgáltatásnak, hogy regisztrált-e az.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Milyen jogcímek önkiszolgáló az Azure AD címtár saját tartomány?
Akkor igényelhet egy önkiszolgáló az Azure AD tartomány ellenőrzéséhez elvégzésével könyvtárába. Tartomány ellenőrzéséhez igazolja, hogy Ön a tulajdonosa a tartomány DNS-rekordok létrehozásával.

Az Azure AD-címtár DNS átvétel ehhez két módja van:

* belső felvásárlási (Admin deríti fel a nem felügyelt Azure-címtár és szeretne egy felügyelt könyvtárba bekapcsolása)
* külső felvásárlási (Admin próbál egy új tartomány hozzáadása a felügyelt Azure-címtár)

Előfordulhat, hogy az érdekelt a tartomány tulajdonosa, mert véve egy nem felügyelt directory keresztül után a felhasználó önkiszolgáló regisztrációt végrehajtva, vagy ha, előfordulhat, hogy lehet új tartomány hozzáadása meglévő felügyelt directory ellenőrzése. Például egy contoso.com nevű tartomány, és szeretne hozzáadni egy új tartomány contoso.co.uk vagy contoso.uk nevet.

## <a name="what-is-domain-takeover"></a>Mi az a tartomány felvásárlási?
Ez a szakasz bemutatja, hogyan adhat ellenőrizze, hogy a tartomány tulajdonosa

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Mi az tartomány ellenőrzéséhez és miért van ezt fogja használni?
Ahhoz, hogy műveleteket hajtson végre a egy könyvtárat, az Azure AD szükséges érvényesítése a DNS-tartomány tulajdonjogát.  A tartomány ellenőrzése teszi lehetővé a jogcím-a könyvtárban, és vagy lépteti elő az önkiszolgáló directory felügyelt könyvtárra, vagy az önkiszolgáló directory egyesítése a létező felügyelt könyvtárat.

## <a name="examples-of-domain-validation"></a>Példák tartomány ellenőrzéséhez
A DNS-felvásárlási könyvtár tennie két módja van:

* belső felvásárlási (például egy rendszergazda felderíti egy önkiszolgáló, nem felügyelt könyvtárat, és szeretne rendelni a felügyelt könyvtárba, kapcsolja be)
* külső felvásárlási (például egy rendszergazda próbál egy új tartomány hozzáadása egy felügyelt könyvtár)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Belső felvásárlási - előléptetéséhez lennie kezelve önkiszolgáló, nem felügyelt könyvtár
Ha így tesz, belső felvásárlási, a címtár alakítja, egy nem felügyelt könyvtárból kezelve. DNS-tartomány neve ellenőrzést, ahol az MX-rekord vagy létrehozhat egy TXT rekordot a DNS-zónában végrehajtásához szükséges. A művelet:

* Ellenőrzi, hogy Ön a tulajdonosa a tartományhoz
* Lehetővé teszi a felügyelt könyvtárban
* Lehetővé teszi, hogy a címtár globális rendszergazdai

Tegyük fel, a rendszergazda a csőrugó felsőoktatási deríti fel, hogy az iskola a felhasználók regisztráltak-e önkiszolgáló ajánlatokról. A DNS-ben regisztrált tulajdonosának nevével BellowsCollege.com, mert a rendszergazda tulajdonjogát az Azure DNS-név ellenőrzése, és majd átveszi a nem felügyelt könyvtárban. A könyvtár kezelve lesz, és az informatikai rendszergazda hozzá van rendelve a BellowsCollege.com címtár globális rendszergazdai szerepkörrel.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Külső felvásárlási - könyvtár önkiszolgáló egyesítése a létező felügyelt könyvtárat
Egy külső felvásárlási a már van kezelve, és azt szeretné, hogy minden felhasználó és csoportok egy nem felügyelt könyvtárból a felügyelt könyvtárban csatlakozni, nem pedig saját két külön könyvtárak.

Felügyelt könyvtár rendszergazdaként hozzáadta a tartományt, és adott tartomány történik, a vele társított egy nem felügyelt könyvtárat.

Például tegyük fel, a rendszergazda és contoso.com, egy tartomány nevét, amely regisztrálva van a szervezet már van kezelve. Azt tapasztalja, hogy a szervezet felhasználóinak végeztek el az önkiszolgáló bejelentkezési fel egy ajánlatot az e-mailek tartománynév használatával user@contoso.co.uk, amely egy másik, a szervezete tulajdonában lévő tartománynevet. Ezek a felhasználók fiókokat jelenleg rendelkeznek contoso.co.uk egy nem felügyelt könyvtárban.

Nem szeretnénk contoso.co.uk nem felügyelt könyvtára egyesítése a létező felügyelt informatikai könyvtár contoso.com két külön könyvtárakban, kezelését.

Külső felvásárlási DNS érvényesítési folyamatot követi, belső felvásárlási.  A különbözetet: felhasználók és a szolgáltatások rendszer újramegfeleltetése felügyelt informatikai könyvtárba.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Mi az a hajt végre egy külső felvásárlási hatását?
Egy külső felvásárlási, a felhasználók-erőforrások leképezéseket jön létre, így a felhasználók továbbra is hozzáférjen a szolgáltatásokhoz megszakítás nélkül. És a sok alkalmazások, beleértve az RMS egyéni felhasználók számára, felhasználók-erőforrások leképezése jól kezelésére, a felhasználók továbbra is hozzáférhetnek az ezekbe a szolgáltatásokba módosítás nélkül. Ha egy alkalmazás nem kezeli a felhasználó-erőforrások leképezése hatékonyan, külső felvásárlási explicit módon megakadályozhatja, hogy gyenge élmény a felhasználók.

#### <a name="directory-takeover-support-by-service"></a>Directory felvásárlási támogatási szolgáltatás
A következő szolgáltatások jelenleg felvásárlási támogatja:

* RMS

A következő szolgáltatások hamarosan támogatni felvásárlási:

* PowerBI

A következő azonban nem, és további felügyeleti tevékenység felhasználói adatok áttelepítése után egy külső felvásárlási igényel.

* SharePoint/onedrive vállalati verzió

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>A DNS-tartomány neve felvásárlási végrehajtása
Tartomány-ellenőrzés végrehajtása (és egy felvásárlási teendő, ha kívánja) néhány lehetőség közül választhat:

1. Azure felügyeleti portál

   Olyan felvásárlási elindul egy tartomány hozzáadása végrehajtásával.  Ha a könyvtár már létezik a tartományban, lehetősége lesz van egy külső felvásárlási végrehajtásához.

   Jelentkezzen be az Azure-portálon a hitelesítő adatok használatával.  Keresse meg a meglévő címtárhoz, és ezután **tartomány hozzáadása**.
2. Office 365

   A beállítások használhatja a [tartományok kezelése](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) oldal az Office 365 rendszerben a tartományok és a DNS-rekordokat szeretne használni. Lásd: [ellenőrizze a tartományt, az Office 365-ben](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell

   A következő lépések szükségesek a Windows PowerShell használatával ellenőrzés végrehajtása.

   | Lépés | Parancsmag használata |
   | --- | --- |
   | A hitelesítő objektum létrehozása |A GET-Credential parancsmag |
   | Csatlakozás az Azure AD szolgáltatáshoz |Connect-MsolService |
   | tartományok listájának beolvasása |Get-MsolDomain |
   | Feladat létrehozása |Get-MsolDomainVerificationDns |
   | DNS-rekord létrehozása |Ehhez a DNS-kiszolgálón |
   | A kérdés ellenőrzése |Confirm-MsolEmailVerifiedDomain |

Példa:

1. Csatlakozás az Azure AD az önkiszolgáló elérhető válaszadás során használt hitelesítő adatok használatával:

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. Tartományok listájának beolvasása:

    Get-MsolDomain
3. Ezután futtassa a Get-MsolDomainVerificationDns parancsmaggal hozzon létre egy kérdés:

    Get-MsolDomainVerificationDns – tartománynév *your_domain_name* – mód DnsTxtRecord

    Példa:

    Get-MsolDomainVerificationDns – tartománynév contoso.com – mód DnsTxtRecord
4. Ez a parancs által visszaadott érték (kérdés) másolja.

    Példa:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. A nyilvános DNS-névtérben az előző lépésben másolt értéket tartalmazó DNS txt rekordot kell létrehozni.

    A rekord értéke a szülőtartomány nevét, ezért ez az erőforrásrekord a DNS-szerepkört a Windows Server használatával hozzon létre, ha hagyja meg a rekord neve üres, és csak illessze be az értéket a szövegmezőbe
6. A kérdés ellenőrzése a megerősítés-MsolDomain parancsmag futtatásához:

    Erősítse meg MsolEmailVerifiedDomain - tartománynév *your_domain_name*

    Példa:

    Erősítse meg MsolEmailVerifiedDomain - tartománynév contoso.com

Sikeres kihívást visszatér a parancssorba hiba nélkül.

## <a name="how-do-i-control-self-service-settings"></a>Hogyan szabályozza a önkiszolgáló beállítások?
Rendszergazdák két önkiszolgáló vezérlők ma rendelkezik. Szabályozhatja:

* -E a felhasználók úgy csatlakozhatnak a könyvtárat, e-mailben.
* E felhasználók licencelheti magukat az alkalmazások és szolgáltatások.

### <a name="how-can-i-control-these-capabilities"></a>Hogyan szabályozhatja ezeket a képességeket?
Egy rendszergazda konfigurálhatja ezeket az Azure AD Set-MsolCompanySettings parancsmag-paraméterek felületével:

* **AllowEmailVerifiedUsers** meghatározza, hogy a felhasználó létrehozása, vagy egy nem felügyelt directory join. $False értékre állítja, hogy a paramétert, ha nem ellenőrzött e-mail felhasználók csatlakozhatnak a könyvtár.
* **AllowAdHocSubscriptions** meghatározza, hogy a felhasználók számára az önkiszolgáló bejelentkezési hajtsa végre a mentést. $False értékre állítja, hogy a paramétert, ha a felhasználók nem végezheti önkiszolgáló regisztrációt.

### <a name="how-do-the-controls-work-together"></a>Hogyan vezérlők működnek együtt?
A két paraméter segítségével együtt határozza meg az önkiszolgáló bejelentkezési pontosabb szabályozhatják fel. Például az alábbi parancs segítségével a felhasználók kell végrehajtani az önkiszolgáló bejelentkezési, de csak azoknak a felhasználóknak már van fiókja Azure AD-ben (más szóval, felhasználók, akik létrehozni ellenőrzött e-mail fiókkal kell nem végezhető el az önkiszolgáló bejelentkezési fel):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

A következő folyamatábra bemutatja a különböző kombinációit e paraméterek és az eredményül kapott a feltételeket a címtár és az önkiszolgáló bejelentkezési fel.

![][1]

További információt és példákat ezekkel a paraméterekkel, [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="see-also"></a>Lásd még:
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
