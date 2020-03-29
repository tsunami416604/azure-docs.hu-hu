---
title: Több tartomány hozása az Azure AD-ben
description: Ez a dokumentum több legfelső szintű tartomány beállítását és konfigurálását ismerteti az O365 és az Azure AD használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b5f19e3e994aa05fa99caf360d0c1be69ec7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049774"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Többtartományos támogatás az Azure AD összevonási szolgáltatásához
Az alábbi dokumentáció útmutatást nyújt több legfelső szintű tartomány és altartomány használatához az Office 365-ös vagy Az Azure AD-tartományok összevonásakor.

## <a name="multiple-top-level-domain-support"></a>Több legfelső szintű tartománytámogatás
Több legfelső szintű tartomány összevont Az Azure AD igényel néhány további konfigurációt, amely nem szükséges, ha egy legfelső szintű tartomány egyesítése.

Ha egy tartomány össze van kapcsolva az Azure AD-vel, több tulajdonság van beállítva az Azure-beli tartományban.  Az egyik fontos a IssuerUri.  Ez a tulajdonság egy URI, amely et az Azure AD a jogkivonathoz társított tartomány azonosítására használ.  Az URI-nak nem kell megoldania semmit, de érvényes URI-nak kell lennie.  Alapértelmezés szerint az Azure AD beállítja az URI-t az összevonási szolgáltatásazonosító értékére a helyszíni AD FS-konfigurációban.

> [!NOTE]
> Az összevonási szolgáltatás azonosítója egy URI, amely egyedileg azonosítja az összevonási szolgáltatást.  Az összevonási szolgáltatás az AD FS egy példánya, amely biztonsági jogkivonat-szolgáltatásként működik.
>
>

A IssuerUri a PowerShell paranccsal `Get-MsolDomainFederationSettings -DomainName <your domain>`tekinthető meg.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Probléma akkor merül fel, ha egynél több legfelső szintű tartományt ad hozzá.  Tegyük fel például, hogy beállította az Azure AD és a helyszíni környezet közötti összevonást.  Ehhez a dokumentumhoz a tartomány, bmcontoso.com használatban van.  Most egy második, legfelső szintű domain, bmfabrikam.com bővült.

![Tartományok](./media/how-to-connect-install-multiple-domains/domains.png)

Amikor megpróbálja a bmfabrikam.com tartományösszevontra konvertálni, hiba történik.  Ennek oka az, hogy az Azure AD rendelkezik egy megkötés, amely nem teszi lehetővé a IssuerUri tulajdonság ugyanazt az értéket több tartományhoz.  

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain paraméter
A megkötés megkerüléséhez hozzá kell adnia egy másik IssuerUri-t, amely a `-SupportMultipleDomain` paraméter használatával elvégezhető.  Ez a paraméter a következő parancsmagokkal használható:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ez a paraméter teszi az Azure AD konfigurálja a IssuerUri úgy, hogy a tartomány neve alapján.  A IssuerUri egyedi lesz az Azure AD-ben a könyvtárak között.  A paraméter használatával a PowerShell parancs sikeresen befejeződött.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/convert.png)

A bmfabrikam.com tartomány beállításait a következők közül választhatja:

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`nem módosítja a többi végpontot, amelyek még mindig úgy vannak beállítva, hogy az adfs.bmcontoso.com összevonási szolgáltatásra mutassanak.

A másik `-SupportMultipleDomain` dolog, ami nem az, hogy biztosítja, hogy az AD FS-rendszer tartalmazza a megfelelő kiállítói érték az Azure AD-hez kiadott jogkivonatok. Ez az érték úgy van beállítva, hogy a felhasználók felhasználói fogadótartományát, és a IssuerUri tartományaként állítja be, azaz https://{upn utótag}/adfs/services/trust.

Így az Azure AD vagy az Office 365 hitelesítése során a user token IssuerUri eleme a tartomány azure AD-ben való megkereséséhez szolgál.  Ha nem található egyezés, a hitelesítés sikertelen lesz.

Ha például egy felhasználó upn-je , bsimon@bmcontoso.comakkor a token IssuerUri eleme , az `http://bmcontoso.com/adfs/services/trust`AD FS-problémák a parancsra lesz állítva. Ez az elem megfelel az Azure AD-konfigurációnak, és a hitelesítés sikeres lesz.

A logikát megvalósító testreszabott jogcímszabály a következő:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> A -SupportMultipleDomain kapcsoló használatához új vagy már meglévő tartományok konvertálása esetén az összevont megbízhatósági kapcsolatot már be kell állítani a támogatásukhoz.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Az AD FS és az Azure AD közötti bizalmi kapcsolat frissítése
Ha nem állította be az összevont megbízhatósági kapcsolatot az AD FS és az Azure AD példánya között, előfordulhat, hogy újra létre kell hoznia ezt a megbízhatóságot.  Ennek az az oka, hogy `-SupportMultipleDomain` ha eredetileg a paraméter nélkül van beállítva, a IssuerUri az alapértelmezett értékkel van beállítva.  Az alábbi képernyőképen láthatja, hogy a `https://adfs.bmcontoso.com/adfs/services/trust`IssuerUri beállítása .

Ha sikeresen hozzáadott egy új tartományt az Azure AD `Convert-MsolDomaintoFederated -DomainName <your domain>`portálon, majd megpróbálja konvertálni a használatával, a következő hibaüzenet jelenik meg.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust1.png)

Ha megpróbálja hozzáadni `-SupportMultipleDomain` a kapcsolót, a következő hibaüzenet jelenik meg:

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust2.png)

Egyszerűen próbál `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` futni az eredeti tartományban is hibát eredményez.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust3.png)

Az alábbi lépésekkel további legfelső szintű tartományt adhat hozzá.  Ha már felvett egy tartományt, és `-SupportMultipleDomain` nem használta a paramétert, kezdje az eredeti tartomány eltávolításának és frissítésének lépéseivel.  Ha még nem adott hozzá legfelső szintű tartományt, elkezdheti a tartomány hozzáadásának lépéseit az Azure AD Connect PowerShell használatával.

Az alábbi lépésekkel távolíthatja el a Microsoft Online megbízhatósági kapcsolatot, és frissítheti az eredeti tartományt.

1. Az AD FS összevonási kiszolgálón nyissa meg **az AD FS-kezelést.**
2. A bal oldalon bontsa ki a **bizalmi kapcsolatokat** és **a függő entitásmegbízhatóságokat**
3. A jobb oldalon törölje a **Microsoft Office 365 Identity Platform** bejegyzést.
   ![A Microsoft Online eltávolítása](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Egy olyan számítógépen, amelyen telepítve van [az Azure Active Directory Module for Windows PowerShell,](https://msdn.microsoft.com/library/azure/jj151815.aspx) a következőt futtatja: `$cred=Get-Credential`.  
5. Adja meg a felhasználónevét és jelszavát egy globális rendszergazda az Azure AD-tartományban, amelyhez összeegyezteti.
6. A PowerShell mezőbe írja be a`Connect-MsolService -Credential $cred`
7. A PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`mezőbe írja be a .  Ez a frissítés az eredeti tartományra tartozik.  Tehát a fenti domainlenne:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Az alábbi lépésekkel adja hozzá az új legfelső szintű tartományt a PowerShell használatával

1. Egy olyan számítógépen, amelyen telepítve van [az Azure Active Directory Module for Windows PowerShell,](https://msdn.microsoft.com/library/azure/jj151815.aspx) a következőt futtatja: `$cred=Get-Credential`.  
2. Adja meg egy globális rendszergazda felhasználónevét és jelszavát ahhoz az Azure AD-tartományhoz, amelyhez össze van egyeztetve.
3. A PowerShell mezőbe írja be a`Connect-MsolService -Credential $cred`
4. A PowerShell mezőbe írja be a`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Az alábbi lépésekkel adja hozzá az új legfelső szintű tartományt az Azure AD Connect használatával.

1. Az Azure AD Connect indítása az asztalról vagy a Start menüből
2. Válassza a "További Azure AD-tartomány hozzáadása" ![lehetőséget További Azure AD-tartomány hozzáadása](./media/how-to-connect-install-multiple-domains/add1.png)
3. Adja meg az Azure AD és az Active Directory hitelesítő adatait
4. Válassza ki a második tartományt, amelyet az összevonáshoz konfigurálni kíván.
   ![További Azure AD-tartomány hozzáadása](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kattintson a Telepítés gombra

### <a name="verify-the-new-top-level-domain"></a>Az új legfelső szintű tartomány ellenőrzése
A PowerShell paranccsal `Get-MsolDomainFederationSettings -DomainName <your domain>`megtekintheti a frissített IssuerUri.  Az alábbi képernyőképen látható, hogy az összevonási beállítások frissültek az eredeti tartományban`http://bmcontoso.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

És az új tartomány IssuerUri-ja úgy van beállítva, hogy`https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Altartományok támogatása
Altartomány hozzáadásakor, mert az Azure AD kezeli a tartományokat, örökli a szülő beállításait.  Tehát a IssuerUri, kell egyeznie a szülők.

Tehát mondjuk, például, hogy van bmcontoso.com, majd adjunk hozzá corp.bmcontoso.com.  A corp.bmcontoso.com felhasználó számára a IssuerUri azonosítónak kell ** http://bmcontoso.com/adfs/services/trustlennie.**  Azonban a fent bevezetett általános szabály az Azure AD, létrehoz egy jogkivonatot egy kibocsátó, mint ** http://corp.bmcontoso.com/adfs/services/trust.** amely nem felel meg a tartomány szükséges értékének, és a hitelesítés sikertelen lesz.

### <a name="how-to-enable-support-for-subdomains"></a>Az altartományok támogatásának engedélyezése
A probléma kerülő megoldásához frissíteni kell a Microsoft Online AD FS függő entitásának megbízhatóságát.  Ehhez egyéni jogcímszabályt kell beállítania, hogy az egyéni kiállítói érték összeállításakor a felhasználó UPN-utótagjából származó altartományokat levegye.

A következő jogcím ezt teszi:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
A reguláris kifejezéskészlet utolsó száma az, hogy hány szülőtartomány van a gyökértartományban. Itt bmcontoso.com van használva, ezért két szülőtartományra van szükség. Ha három szülőtartományt kellene megtartani (azaz corp.bmcontoso.com), akkor ez a szám három lett volna. Végül egy tartomány tágan, a mérkőzés mindig történik, hogy megfeleljen a maximális domain. "{2,3}" két-három domainnek felel meg (azaz bmfabrikam.com és corp.bmcontoso.com).

Az alábbi lépésekkel egyéni jogcímet adhat hozzá az altartományok támogatásához.

1. Az AD FS-kezelés megnyitása
2. Kattintson a jobb gombbal a Microsoft Online RP megbízhatósági kapcsolatra, és válassza a Jogcímszabályok szerkesztése parancsot
3. Válassza ki a harmadik ![jogcímszabályt, és cserélje le a Jogcím szerkesztése parancsot](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Az aktuális jogcím cseréje:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Jogcím cseréje](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kattintson az Ok gombra.  Kattintson az Apply (Alkalmaz) gombra.  Kattintson az Ok gombra.  Zárja be az AD FS felügyeleti konzolt.

## <a name="next-steps"></a>További lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: az [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md), a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
