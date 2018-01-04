---
title: "Az Azure AD Connect több tartományban"
description: "Ez a dokumentum ismerteti, és az Office 365 és az Azure AD több felső szintű tartomány beállításával."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: db4cfe91b8d27b5336763eff7c6f22f0f345caf2
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Többtartományos támogatás az Azure AD összevonási szolgáltatásához
Az alábbi dokumentáció nyújt útmutatást több felső szintű tartományok és altartományok használata, ha az Office 365 vagy az Azure AD-tartomány összevonását.

## <a name="multiple-top-level-domain-support"></a>Több felső szintű tartomány támogatása
Összevonását több, az Azure ad-vel a legfelső szintű tartományoknak nincs szükség, amikor egy legfelső szintű tartomány összevonását néhány további konfigurálást igényel.

Amikor egy tartományt az Azure AD össze van vonva, több tulajdonság a tartományban az Azure-ban beállítva.  Egy fontos egyik IssuerUri lesz.  Ez az URI, amely az Azure ad azonosítására szolgál a tartományhoz, amely a token társítva van.  Az URI nem kell tartoznia semmi, de kell lennie egy érvényes URI.  Alapértelmezés szerint az Azure AD beállítja az összevonási szolgáltatás azonosítóját értékének a helyszíni AD FS konfigurációs.

> [!NOTE]
> Az összevonási szolgáltatás azonosítóját, amely egyedileg azonosítja az összevonási szolgáltatás URI.  Az összevonási szolgáltatás AD FS funkcionáló példányának, mint a biztonsági jogkivonatokkal kapcsolatos szolgáltatástól.
>
>

A PowerShell-parancs használatával megtekintheti a IssuerUri `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

A probléma merül fel, ha azt szeretné, egynél több felső szintű tartomány hozzáadása.  Például tegyük fel, a telepítő összevonási az Azure AD között van, és a helyszíni környezetben.  A dokumentum használok bmcontoso.com.  Most már hozzáadtam egy második felső szintű tartomány bmfabrikam.com.

![Tartományok](./media/active-directory-multiple-domains/domains.png)

Próbálja meg konvertálni a bmfabrikam.com tartomány átalakításakor, ha azt hibaüzenetet kap.  Ennek oka az, Azure AD is rendelkezik, amely nem teszi lehetővé a IssuerUri tulajdonság ugyanazt az értéket egynél több tartomány korlátozás.  

![Összevonási hiba](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain paraméter
A megoldás, azt kell adnia egy másik IssuerUri, amelyek segítségével teheti a `-SupportMultipleDomain` paraméter.  Ezt a paramétert a következő parancsmagokat használja:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Ez a paraméter lehetővé teszi az Azure AD a IssuerUri konfigurálja, hogy annak a tartománynak a nevét alapul.  Ez lesz egyedi könyvtárak között az Azure ad-ben.  A paraméter használata lehetővé teszi, hogy a PowerShell-parancs végrehajtása sikeres legyen.

![Összevonási hiba](./media/active-directory-multiple-domains/convert.png)

A beállításokat az új bmfabrikam.com tartomány megnézzük látható a következő:

![Összevonási hiba](./media/active-directory-multiple-domains/settings.png)

Vegye figyelembe, hogy `-SupportMultipleDomain` nem változtatja meg a többi végpont, amely az összevonási szolgáltatás adfs.bmcontoso.com mutasson konfigurálása még folyamatban van.

Egy másik művelet, amely `-SupportMultipleDomain` does, biztosítja, hogy az AD FS rendszer tartalmazza a megfelelő kibocsátó érték az Azure AD kiállított jogkivonatokat. A felhasználók egyszerű Felhasználónevük tartomány része, és ezt a tartományt a IssuerUri, azaz https://{upn utótag beállítását tesz} / adfs/services/megbízhatósági.

Így az Azure AD-hitelesítés során vagy az Office 365, a felhasználói jogkivonat IssuerUri elemének segítségével keresse meg a tartományt az Azure ad-ben.  Ha nem található egyezés a hitelesítés sikertelen lesz.

Például, ha a felhasználói UPN van bsimon@bmcontoso.com, http://bmcontoso.com/adfs/services/trust úgy lesz beállítva, a jogkivonatot AD FS problémák IssuerUri elemében. Ez fog egyezni az Azure Active Directory beállítása, és a hitelesítés sikeres lesz.

A következő értéke a testreszabott jogcímszabály, amely megvalósítja ezt a programot:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> A - SupportMultipleDomain kapcsoló használata, amikor új hozzáadása vagy konvertálása már hozzáadott tartomány, van szüksége a telepítő az összevont megbízhatósági kapcsolathoz azokat eredetileg támogatásához.  
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Az AD FS és az Azure AD közötti megbízhatósági kapcsolat frissítése
Ha nem állított be az összevont megbízhatósági kapcsolathoz AD FS és az Azure AD példányával között, szükség lehet újból létrehozni a bizalmi kapcsolat.  Ennek az az oka eredetileg beállítása nélkül a `-SupportMultipleDomain` paraméter, a IssuerUri értéke az alapértelmezett értékkel.  Az alábbi képernyőképen látható a IssuerUri https://adfs.bmcontoso.com/adfs/services/trust értékre van állítva.

Így mostantól, ha azt egy új tartomány sikeresen hozzáadta az Azure AD portálon, majd próbálja meg konvertálni használatával `Convert-MsolDomaintoFederated -DomainName <your domain>`, azt a következő hibaüzenet.

![Összevonási hiba](./media/active-directory-multiple-domains/trust1.png)

Ha megpróbálja felvenni a `-SupportMultipleDomain` azt hibaüzenet jelenik meg a következő kapcsolóhoz:

![Összevonási hiba](./media/active-directory-multiple-domains/trust2.png)

Egyszerűen a futtatni kívánt `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` az eredeti tartományon is jelent hibát.

![Összevonási hiba](./media/active-directory-multiple-domains/trust3.png)

Az alábbi lépések segítségével további felső szintű tartomány hozzáadása.  Ha már hozzáadott egy tartományhoz, és nem használja a `-SupportMultipleDomain` eltávolítása és frissítése az eredeti tartomány lépései paraméter kezdődik.  Ha nem adja hozzá a legfelső szintű tartomány még Kezdésként használhatja az PowerShell Azure AD Connect használatával tartomány lépései.

Az alábbi lépések segítségével a Microsoft Online bizalmi kapcsolatot, és az eredeti tartomány frissítése.

1. Az AD FS összevonási kiszolgálón nyissa meg **AD FS kezelő.**
2. Bontsa ki a bal oldali **megbízhatósági kapcsolatok** és **függő entitás Megbízhatóságai**
3. A jobb oldalon törölje a **Microsoft Office 365 Identitásplatformmal** bejegyzés.
   ![Távolítsa el a Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. A gépen, amelyen van [Active Directory modul Windows Powershellhez készült Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van-e futtassa a következő: `$cred=Get-Credential`.  
5. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja az Azure AD-tartomány
6. Adja meg a PowerShellben`Connect-MsolService -Credential $cred`
7. Adja meg a PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Ez az eredeti tartomány esetén.  A fenti tartományok lenne használatával:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Az alábbi lépések segítségével a PowerShell használatával új legfelső szintű tartomány hozzáadása

1. A gépen, amelyen van [Active Directory modul Windows Powershellhez készült Azure](https://msdn.microsoft.com/library/azure/jj151815.aspx) telepítve van-e futtassa a következő: `$cred=Get-Credential`.  
2. Adja meg a felhasználónevet és jelszót egy globális rendszergazda összevonja az Azure AD-tartomány
3. Adja meg a PowerShellben`Connect-MsolService -Credential $cred`
4. Adja meg a PowerShellben`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

A következő lépésekkel adja hozzá az új legfelső szintű tartomány, az Azure AD Connect használatával.

1. Indítsa el az Azure AD Connect az asztalon vagy start menüben
2. "Egy további Azure AD-tartomány hozzáadása" Válasszon ![adnia egy további Azure AD-tartomány](./media/active-directory-multiple-domains/add1.png)
3. Adja meg az Azure AD és az Active Directory hitelesítő adatait.
4. Válassza ki a második tartomány szeretne beállítani az összevonáshoz.
   ![Felvesz egy további Azure AD-tartomány](./media/active-directory-multiple-domains/add2.png)
5. Kattintson a telepítés

### <a name="verify-the-new-top-level-domain"></a>Ellenőrizze az új legfelső szintű tartomány
A PowerShell-paranccsal `Get-MsolDomainFederationSettings -DomainName <your domain>`tekintheti meg a frissített IssuerUri.  Az alábbi képernyőfelvételen látható az összevonási beállítások lettek frissítve az az eredeti tartomány http://bmcontoso.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

És az új tartományon IssuerUri https://bmfabrikam.com/adfs/services/trust értékre lett állítva

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-sub-domains"></a>Altartományok támogatása
Altartomány, a kezelt módon az Azure AD-tartományok miatt hozzáadásakor azt öröklik a szülő beállításait.  Ez azt jelenti, hogy a IssuerUri a szülők egyezniük kell.

Így lehetővé teszi, hogy tegyük fel például, hogy rendelkezem bmcontoso.com, és adja meg az corp.bmcontoso.com.  Ez azt jelenti, hogy egy felhasználó a corp.bmcontoso.com IssuerUri kell lennie **http://bmcontoso.com/adfs/services/trust.**  Azonban a standard szabály az Azure AD fent megvalósított hoz létre a jogkivonatot kibocsátó, az **http://corp.bmcontoso.com/adfs/services/trust.** amely nem azonos a tartomány szükséges érték, és a hitelesítés sikertelen lesz.

### <a name="how-to-enable-support-for-sub-domains"></a>Altartományok támogatásának engedélyezése
Ahhoz, hogy elkerüléséhez az AD FS függőentitás-megbízhatóságot Microsoft Online frissíteni kell.  Ehhez konfigurálnia kell egyéni jogcímszabályt, hogy azt törli a felhasználói UPN-utótagot a bármely altartományok ki az egyéni kibocsátó érték kiszámításakor.

A következő jogcím fog tegye a következőket:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
A reguláris kifejezés utolsó számát a hány szülő tartományt, a legfelső szintű tartomány van beállítva. Itt az i bmcontoso.com rendelkezik, ezért két szülő tartomány szükség. Ha három szülő tartományok tartandó volt-e (pl.: corp.bmcontoso.com), majd a szám kellett volna lennie három. Végül egy is fel kell tüntetni, az egyeztetés mindig lesz a tartományok maximálisan megengedett kereséséhez. "a(z) {2,3}" fog egyezni a két-három tartományok (pl.: bmfabrikam.com és corp.bmcontoso.com).

A következő lépésekkel adja hozzá az altartományok támogatásához egyéni jogcímleírásokat.

1. Nyissa meg az AD FS-Szolgáltatáskezelés
2. Kattintson jobb gombbal a Microsoft Online függő Entitás megbízhatóságát, majd válassza ki a jogcímszabályok szerkesztése
3. Válassza ki a harmadik jogcímszabály, és cserélje le ![jogcímszabályok szerkesztése](./media/active-directory-multiple-domains/sub1.png)
4. Cserélje le a jelenlegi jogcímek:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Cserélje ki a jogcímet](./media/active-directory-multiple-domains/sub2.png)

5. Kattintson az OK gombra.  Az Alkalmaz gombra.  Kattintson az OK gombra.  Zárja be az AD FS felügyeleti konzolt.
