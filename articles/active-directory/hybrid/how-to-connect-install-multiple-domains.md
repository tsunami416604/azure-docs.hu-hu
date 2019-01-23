---
title: Az Azure AD Connect több tartományban
description: Ez a dokumentum ismerteti, és az Office 365 és az Azure ad-ben több felső szintű tartomány konfigurálása.
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
ms.topic: article
ms.date: 05/31/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: abc01239a2bf61c39f99fe880bf17d7958a1597c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477926"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Többtartományos támogatás az Azure AD összevonási szolgáltatásához
Az alábbi dokumentáció használata több legfelső szintű tartományt és altartományt, ha az Office 365 vagy Azure AD-tartomány összevonása nyújt útmutatást.

## <a name="multiple-top-level-domain-support"></a>Több felső szintű tartomány támogatása
Többtartományos több, az Azure ad-vel a legfelső szintű tartományok, amelyek esetén nem szükséges egy legfelső szintű tartomány összevonása további konfigurálást igényel.

Ha egy tartomány össze van vonva az Azure ad-vel, számos tulajdonságát az Azure-ban a tartományban vannak beállítva.  Egy fontos egyik IssuerUri lesz.  Ez a tulajdonság egy URI-t az Azure AD által a tartomány, amelyhez társítva van a jogkivonat azonosítására használt.  Az URI-t nem kell feloldani bármely, de érvényes URI-t kell lennie.  Alapértelmezés szerint az Azure AD állítja be az URI-t az összevonási szolgáltatás azonosítója értéke a helyszíni AD FS konfigurációt.

> [!NOTE]
> Az összevonási szolgáltatás azonosítója, amely egyedileg azonosítja az összevonási szolgáltatás URI.  Az összevonási szolgáltatás AD FS a funkciók egy példányát, mint a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól.
>
>

A PowerShell-paranccsal megtekintheti a IssuerUri `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Probléma merül fel, ha egynél több felső szintű tartomány hozzáadásakor.  Például tegyük fel, között az Azure AD összevonási beállítása és a helyszíni környezetben.  Ebben a dokumentumban a tartomány bmcontoso.com használatban van.  Most egy második felső szintű tartomány bmfabrikam.com hozzá lett adva.

![Tartományok](./media/how-to-connect-install-multiple-domains/domains.png)

Ha megkísérli a bmfabrikam.com tartomány, hiba történik.  Az az oka, Azure ad-ben, amely nem engedélyezi a több tartomány számára ugyanazon értékekkel rendelkeznek IssuerUri tulajdonság korlátozást tartalmaz.  

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain paraméter
Ennél a határértéknél megkerüléséhez kell hozzáadnia egy másik IssuerUri, amelyek használatával teheti meg a `-SupportMultipleDomain` paraméter.  Ezt a paramétert használja a következő parancsmagokkal:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ez a paraméter lehetővé teszi a IssuerUri konfigurálása, hogy-alapú annak a tartománynak a nevét, az Azure AD.  A IssuerUri egyedi lesz címtárak között az Azure ad-ben.  A paraméter használata lehetővé teszi, hogy a PowerShell-parancs sikeresen befejeződik.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/convert.png)

A beállítások a bmfabrikam.com tartomány megnézzük a következő látható:

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` nem módosítja a többi végpont, amely még mindig vannak konfigurálva, hogy az összevonási szolgáltatás a adfs.bmcontoso.com mutasson.

Még egy dolog, amely `-SupportMultipleDomain` does, hogy azt biztosítja, hogy az AD FS rendszer tartalmazza-e a megfelelő kibocsátó értékét az Azure ad-ben a kiállított jogkivonatokban. Ez az érték van beállítva, a felhasználók UPN tartomány része, és a IssuerUri, azaz https://{upn utótag-tartományának részeként állítja,} / adfs/services/megbízhatósági kapcsolat.

Így az Azure AD-hitelesítés során, vagy Office 365, a felhasználói jogkivonat IssuerUri elemében használatos keresse meg a tartományt az Azure ad-ben.  Ha nem talál egyezést, a hitelesítés sikertelen lesz.

Például ha egy felhasználó egyszerű Felhasználónevét, bsimon@bmcontoso.com, állítja be a jogkivonatot, az AD FS-problémák, IssuerUri elemében http://bmcontoso.com/adfs/services/trust. Ez az elem egyezni fog az Azure AD-konfigurációjának, és a hitelesítés sikeres lesz.

Az alábbiakban látható a logikát megvalósító egyéni jogcímszabály:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Annak érdekében, hogy a használatával – SupportMultipleDomain kísérlet során hozzáadhat új vagy már létező tartományok átalakítása, az összevont megbízhatósági kapcsolatot kell már beállított támogatja azokat.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Az AD FS és az Azure AD közötti bizalmi kapcsolat frissítése
Ha nem állította be az AD FS és az Azure AD-példány között összevont megbízhatósági kapcsolat, szükség lehet hozza létre újból a bizalmi kapcsolat.  Azért, ha azt eredetileg nélkül szeretné beállítani a `-SupportMultipleDomain` paraméter, a IssuerUri van állítva az alapértelmezett értékkel.  Az alábbi képernyőképen láthatja a IssuerUri értékre van állítva https://adfs.bmcontoso.com/adfs/services/trust.

Ha sikeresen hozzáadott egy új tartományt az Azure AD portálon, és próbálja meg konvertálni a `Convert-MsolDomaintoFederated -DomainName <your domain>`, a következő hibaüzenetet kap.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust1.png)

Ha megpróbálja hozzáadni a `-SupportMultipleDomain` vált, a következő hibaüzenetet kap:

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust2.png)

Egyszerűen a futtatni kívánt `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` az eredeti tartományon is egy hibát eredményez.

![Összevonási hiba](./media/how-to-connect-install-multiple-domains/trust3.png)

Az alábbi lépések használatával vegyen fel egy további legfelső szintű tartományt.  Ha már felvett egy tartományhoz, és nem használja a `-SupportMultipleDomain` paramétert, a lépésekről, eltávolítása és frissítése az eredeti tartomány kezdete.  Ha még nem hozzáadott egy legfelső szintű tartományt, a PowerShell az Azure AD Connect használatával tartomány hozzáadása a lépések megkezdése.

A következő lépések segítségével távolítsa el a Microsoft Online adatvédelmi, és az eredeti tartomány frissítése.

1. Nyissa meg az AD FS összevonási kiszolgálón **AD FS-kezelőben.**
2. Bontsa ki a bal oldali **megbízhatósági kapcsolatok** és **függő entitás Megbízhatóságai**
3. A jobb oldalon törölje a **a Microsoft Office 365 Identity Platform** bejegyzés.
   ![Remove Microsoft Online](./media/how-to-connect-install-multiple-domains/trust4.png)
4. A gépen, amelyen [Azure Active Directory modul Windows Powershellhez készült](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van a következő parancs futtatásával: `$cred=Get-Credential`.  
5. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja a az Azure AD-tartomány.
6. Adja meg a PowerShellben `Connect-MsolService -Credential $cred`
7. Adja meg a PowerShellben `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Ez a frissítés az eredeti tartomány van.  A fenti tartományok lenne használatával:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Kövesse az alábbi lépéseket a PowerShell használatával új felső szintű tartomány hozzáadása

1. A gépen, amelyen [Azure Active Directory modul Windows Powershellhez készült](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van a következő parancs futtatásával: `$cred=Get-Credential`.  
2. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja a az Azure AD-tartomány
3. Adja meg a PowerShellben `Connect-MsolService -Credential $cred`
4. Adja meg a PowerShellben `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Az alábbi lépések segítségével adja hozzá az új legfelső szintű tartományt az Azure AD Connect használatával.

1. Nyissa meg az Azure AD Connect, az asztalon vagy a start menü
2. Válassza a "Egy további Azure AD-tartomány hozzáadása" ![hozzá egy további Azure AD-tartomány](./media/how-to-connect-install-multiple-domains/add1.png)
3. Adja meg az Azure AD és az Active Directory hitelesítő adatok
4. Válassza ki a második tartomány szeretne beállítani az összevonáshoz.
   ![Adjon hozzá egy további Azure AD-tartomány](./media/how-to-connect-install-multiple-domains/add2.png)
5. Kattintson a Telepítés gombra

### <a name="verify-the-new-top-level-domain"></a>Az új felső szintű tartomány ellenőrzése
A PowerShell-parancs használatával `Get-MsolDomainFederationSettings -DomainName <your domain>`tekintheti meg a frissített IssuerUri.  Az alábbi képernyőfelvételen az összevonási beállításainak frissítése az eredeti tartományon http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

És az új tartományon a IssuerUri értékre lett állítva https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Altartományok támogatása
Amikor hozzáad egy altartomány miatt a kezelt lépésig az Azure AD-tartományok, azt beállításait, a szülő öröklik.  Így a IssuerUri, meg kell felelnie a szülők.

Így lehetővé teszi, hogy tegyük fel, például, hogy rendelkezem bmcontoso.com, és hozzáadhatja a corp.bmcontoso.com.  A felhasználók corp.bmcontoso.com IssuerUri kell  **http://bmcontoso.com/adfs/services/trust.**  A fenti az Azure AD-ben megvalósított standard szabályt hoz létre a jogkivonatot kibocsátó, az azonban  **http://corp.bmcontoso.com/adfs/services/trust.** hitelesítés sikertelen lesz, és amelyek nem egyeznek meg a tartomány szükséges értéket.

### <a name="how-to-enable-support-for-subdomains"></a>Altartományok támogatásának engedélyezése
Annak érdekében, hogy ez a probléma megkerüléséhez az AD FS megbízható függő entitás a Microsoft Online frissíteni kell.  Ehhez konfigurálnia kell egyéni jogcímszabályt, hogy, törli a felhasználói UPN-utótagot az altartományok ki létrehozásakor az egyéni kibocsátói értéket.

A következő jogcím ezt a műveletet:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
A reguláris kifejezés készlet utolsó száma, a legfelső szintű tartományban találhatók, hány szülő tartományok van. Itt bmcontoso.com szolgál, így két szülő tartomány szükséges. Ha három szülő tartományok lettek-e megőrizni (pl.: corp.bmcontoso.com), majd a szám lett volna három. Végül egy is fel kell tüntetni, az egyezés mindig a tartomány maximális megfelelő lesz. "{2,3}" két-három tartományokat egyezését (pl.: bmfabrikam.com és corp.bmcontoso.com).

A következő lépések használatával adjon hozzá egy egyéni jogcímszabályok támogatja altartományok.

1. Nyissa meg az AD FS kezelése
2. Kattintson a jobb gombbal a Microsoft Online helyreállítási pont Védettként megbízhatósági kapcsolat, és válassza a jogcímszabályok szerkesztése
3. Válassza ki a harmadik jogcímszabály, és cserélje le ![jogcím szerkesztése](./media/how-to-connect-install-multiple-domains/sub1.png)
4. Cserélje le a jelenlegi jogcím:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Cserélje le a jogcímet](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Kattintson az OK gombra.  Kattintson a alkalmazni.  Kattintson az OK gombra.  Zárja be az AD FS felügyeleti konzolt.

## <a name="next-steps"></a>További lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

További információkért ezekről a szolgáltatásokról, amelyek a telepítéssel engedélyezett szolgáltatásokkal: [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md), [véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md), és [az Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).