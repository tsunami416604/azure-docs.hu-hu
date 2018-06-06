---
title: Az Azure AD Connect több tartományban
description: Ez a dokumentum ismerteti, és az Office 365 és az Azure AD több felső szintű tartomány beállításával.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2f596f64041b3d429b99db482cd635ab441bf468
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801507"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Többtartományos támogatás az Azure AD összevonási szolgáltatásához
Az alábbi dokumentáció nyújt útmutatást több legfelső szintű tartományok és altartományok használata, ha az Office 365 vagy az Azure AD-tartomány összevonását.

## <a name="multiple-top-level-domain-support"></a>Több felső szintű tartomány támogatása
Összevonását több, az Azure ad-vel a legfelső szintű tartományoknak nincs szükség, amikor egy legfelső szintű tartomány összevonását néhány további konfigurálást igényel.

Amikor egy tartományt az Azure AD össze van vonva, több tulajdonság a tartományban az Azure-ban beállítva.  Egy fontos egyik IssuerUri lesz.  Ez a tulajdonság URI, amely az Azure ad azonosítására szolgál a tartományhoz, amely a token társítva van.  Az URI nem kell tartoznia semmi, de kell lennie egy érvényes URI.  Alapértelmezés szerint az Azure AD állítja be az URI azonosító az összevonási szolgáltatás azonosítóját értékének a helyszíni AD FS konfigurációs.

> [!NOTE]
> Az összevonási szolgáltatás azonosítóját, amely egyedileg azonosítja az összevonási szolgáltatás URI.  Az összevonási szolgáltatás AD FS funkcionáló példányának, mint a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól.
>
>

A PowerShell-parancs használatával megtekintheti a IssuerUri `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

A probléma merül fel, ha egynél több felső szintű tartomány hozzáadása.  Például tegyük fel, az Azure AD között összevonási beállítása és a helyszíni környezetben.  A dokumentum, a tartomány bmcontoso.com használatban van.  Most egy második felső szintű tartomány bmfabrikam.com hozzá lett adva.

![Tartományok](./media/active-directory-multiple-domains/domains.png)

Ha megkísérli a bmfabrikam.com tartomány átalakításakor, hiba történik.  A hiba oka, Azure AD is rendelkezik, amely nem teszi lehetővé a IssuerUri tulajdonság ugyanazt az értéket egynél több tartomány korlátozás.  

![Összevonási hiba](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain paraméter
Ennél a határértéknél megoldása, akkor hozzon létre egy másik IssuerUri, amelyek segítségével végezhető a `-SupportMultipleDomain` paraméter.  Ezt a paramétert a következő parancsmagokat használja:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ez a paraméter lehetővé teszi az Azure AD a IssuerUri konfigurálja, hogy annak a tartománynak a nevét alapul.  A IssuerUri lesz egyedi könyvtárak között az Azure ad-ben.  A paraméter használata lehetővé teszi, hogy a PowerShell-parancs végrehajtása sikeres legyen.

![Összevonási hiba](./media/active-directory-multiple-domains/convert.png)

A beállítások a bmfabrikam.com tartomány megnézzük látható a következő:

![Összevonási hiba](./media/active-directory-multiple-domains/settings.png)

`-SupportMultipleDomain` nem módosítja a többi végpont, amely az összevonási szolgáltatás adfs.bmcontoso.com mutasson konfigurálása még folyamatban van.

Egy másik művelet, amely `-SupportMultipleDomain` does, biztosítja, hogy az AD FS rendszer tartalmazza a megfelelő kibocsátó érték az Azure AD kiállított jogkivonatokat. Ezen érték beállítása esetén a felhasználók egyszerű Felhasználónevük tartomány része, és azt a tartományt a IssuerUri, azaz https://{upn utótag beállítását} / adfs/services/megbízhatósági.

Így az Azure AD-hitelesítés során vagy az Office 365, a felhasználói jogkivonat IssuerUri elemének segítségével keresse meg a tartományt az Azure ad-ben.  Ha nem található egyezés, a hitelesítés sikertelen lesz.

Például, ha a felhasználói UPN van bsimon@bmcontoso.com, állítja be a tokent, az AD FS problémák IssuerUri elemének http://bmcontoso.com/adfs/services/trust. Ez az elem fog egyezni az Azure Active Directory beállítása, és a hitelesítés sikeres lesz.

A következő értéke a testreszabott jogcímszabály, amely megvalósítja ezt a programot:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Ahhoz, hogy a - SupportMultipleDomain kapcsolót megkísérelte használatával hozzáadhat új vagy már létező tartományok átalakítása, az összevont megbízhatósági kapcsolathoz kell korábban már beállított támogatásához.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Az AD FS és az Azure AD közötti megbízhatósági kapcsolat frissítése
Ha nem állított be az AD FS és az Azure AD példányával között összevont megbízhatósági kapcsolathoz esetleg újra létre kell hoznia a bizalmi kapcsolat.  A hiba oka, ha eredetileg nélkül beállított a `-SupportMultipleDomain` paraméter, a IssuerUri értéke az alapértelmezett értékkel.  A az alábbi képernyőképen látható, hogy a IssuerUri értéke https://adfs.bmcontoso.com/adfs/services/trust.

Ha sikeresen hozzáadott egy új tartományt az Azure AD portálon, és próbálja meg konvertálni használatával `Convert-MsolDomaintoFederated -DomainName <your domain>`, hibaüzenetet kap.

![Összevonási hiba](./media/active-directory-multiple-domains/trust1.png)

Ha megpróbálja felvenni a `-SupportMultipleDomain` kapcsoló, a következő hibaüzenet fog megjelenni:

![Összevonási hiba](./media/active-directory-multiple-domains/trust2.png)

Egyszerűen a futtatni kívánt `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` az eredeti tartományon is jelent hibát.

![Összevonási hiba](./media/active-directory-multiple-domains/trust3.png)

Az alábbi lépések segítségével további felső szintű tartomány hozzáadása.  Ha már hozzáadott egy tartományhoz, és nem használja a `-SupportMultipleDomain` paraméter, eltávolítása és frissítése az eredeti tartomány lépései kezdődik.  Ha még nem vett egy legfelső szintű tartományt, megkezdheti a lépések az PowerShell Azure AD Connect használatával tartomány hozzáadása.

Az alábbi lépések segítségével a Microsoft Online bizalmi kapcsolatot, és az eredeti tartomány frissítése.

1. Az AD FS összevonási kiszolgálón nyissa meg **AD FS kezelő.**
2. Bontsa ki a bal oldali **megbízhatósági kapcsolatok** és **függő entitás Megbízhatóságai**
3. A jobb oldalon törölje a **Microsoft Office 365 Identitásplatformmal** bejegyzés.
   ![Távolítsa el a Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. A gépen, amelyen van [Active Directory modul Windows Powershellhez készült Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van-e futtassa a következő: `$cred=Get-Credential`.  
5. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja az Azure AD-tartományt.
6. A PowerShell-ablakba írja be a `Connect-MsolService -Credential $cred`
7. A PowerShell-ablakba írja be a `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  A frissítés az eredeti tartomány van.  A fenti tartományok lenne használatával:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Az alábbi lépések segítségével a PowerShell használatával új legfelső szintű tartomány hozzáadása

1. A gépen, amelyen van [Active Directory modul Windows Powershellhez készült Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van-e futtassa a következő: `$cred=Get-Credential`.  
2. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja az Azure AD-tartomány
3. A PowerShell-ablakba írja be a `Connect-MsolService -Credential $cred`
4. A PowerShell-ablakba írja be a `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

A következő lépésekkel adja hozzá az új legfelső szintű tartomány, az Azure AD Connect használatával.

1. Indítsa el az Azure AD Connect az asztalon vagy start menüben
2. "Egy további Azure AD-tartomány hozzáadása" Válasszon ![adnia egy további Azure AD-tartomány](./media/active-directory-multiple-domains/add1.png)
3. Adja meg az Azure AD és az Active Directory hitelesítő adatait.
4. Válassza ki a második tartomány szeretne beállítani az összevonáshoz.
   ![Felvesz egy további Azure AD-tartomány](./media/active-directory-multiple-domains/add2.png)
5. Kattintson a Telepítés gombra

### <a name="verify-the-new-top-level-domain"></a>Ellenőrizze az új legfelső szintű tartomány
A PowerShell-paranccsal `Get-MsolDomainFederationSettings -DomainName <your domain>`tekintheti meg a frissített IssuerUri.  Az alábbi képernyőfelvételen látható az összevonási beállításai frissültek eredeti tartományban http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

És az új tartományon IssuerUri értékre lett állítva https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Altartományok támogatása
Hozzáadásakor altartomány, a kezelt módon az Azure AD-tartományok miatt azt öröklik a szülő beállításait.  Igen a IssuerUri egyezniük kell a szülők.

Így lehetővé tegyük fel például, például, hogy rendelkezem bmcontoso.com, és adja meg az corp.bmcontoso.com.  Egy felhasználó a corp.bmcontoso.com IssuerUri kell  **http://bmcontoso.com/adfs/services/trust.**  Azonban a standard szabály az Azure AD fent megvalósított hoz létre a jogkivonatot kibocsátó, az  **http://corp.bmcontoso.com/adfs/services/trust.** amely nem azonos a tartomány szükséges érték, és a hitelesítés sikertelen lesz.

### <a name="how-to-enable-support-for-subdomains"></a>Altartományok támogatásának engedélyezése
Ahhoz, hogy ez a probléma az AD FS megbízható függő entitás a Microsoft Online frissíteni kell.  Ehhez konfigurálnia kell egyéni jogcímszabályt, hogy azt szalagok ki a felhasználói UPN-utótagot az altartományok, ha az egyéni kibocsátó érték kiszámításakor.

A következő jogcím fog tegye a következőket:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Az utolsó a reguláris kifejezés set értéke hány szülő tartományok nem a legfelső szintű tartományban vannak. Itt bmcontoso.com szolgál, így két szülő tartomány szükséges. Ha három szülő tartományok tartandó volt-e (pl.: corp.bmcontoso.com), majd a szám kellett volna lennie három. Végül egy is fel kell tüntetni, az egyeztetés mindig lesz a tartományok maximálisan megengedett kereséséhez. "{2,3}" fog egyezni a két-három tartományok (pl.: bmfabrikam.com és corp.bmcontoso.com).

A következő lépésekkel adja hozzá altartományokat támogatásához egyéni jogcímleírásokat.

1. Nyissa meg az AD FS-Szolgáltatáskezelés
2. Kattintson a jobb gombbal a Microsoft Online függő Entitás megbízhatóságát, és válassza ki a jogcímszabályok szerkesztése
3. Válassza ki a harmadik jogcímszabály, és cserélje le ![jogcímszabályok szerkesztése](./media/active-directory-multiple-domains/sub1.png)
4. Cserélje le a jelenlegi jogcímek:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Cserélje ki a jogcímet](./media/active-directory-multiple-domains/sub2.png)

5. Kattintson az OK gombra.  Az Alkalmaz gombra.  Kattintson az OK gombra.  Zárja be az AD FS felügyeleti konzolt.

## <a name="next-steps"></a>További lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](active-directory-aadconnect-whats-next.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: az [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md), a [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](active-directory-aadconnectsync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).