---
title: Az Azure AD Connect - AD FS kezelő és a Testreszabás |} Microsoft Docs
description: AD FS felügyelet az Azure AD Connect és felhasználói AD FS bejelentkezési élmény és az Azure AD Connect PowerShell testreszabása.
keywords: Az AD FS-, ADFS, az AD FS felügyeleti AAD-csatlakozás, csatlakozás, bejelentkezés, az AD FS testreszabás javítani bizalmi kapcsolat, O365, összevonási, függő entitás
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 276e53784b30c2196ad7455cf9fd801a103fdc30
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590854"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Kezelése és testreszabása az Active Directory összevonási szolgáltatások az Azure AD Connect használatával
Ez a cikk ismerteti a kezelése és testreszabása az Active Directory összevonási szolgáltatások (AD FS) Azure Active Directory (Azure AD) Connect használatával. Egyéb gyakori AD FS feladatok, amelyek hasznosak lehetnek az AD FS-farm teljes konfigurációját is tartalmaz.

| Témakör | Ez vonatkozik |
|:--- |:--- |
| **Az AD FS kezelése** | |
| [Javítsa ki a bizalmi kapcsolat](#repairthetrust) |Javítsa ki az összevonási megbízhatósági kapcsolat, és az Office 365 módjáról. |
| [Az Azure AD használatával másodlagos bejelentkezési Azonosítóval összevonni ](#alternateid) | Másodlagos bejelentkezési azonosítóval összevonás konfigurálása  |
| [Az AD FS-kiszolgáló hozzáadása](#addadfsserver) |Hogyan bontsa ki az AD FS-farm további AD FS-kiszolgálóval. |
| [AD FS webalkalmazás-Proxy kiszolgáló hozzáadása](#addwapserver) |Bontsa ki a további webalkalmazás-proxykiszolgálóként (WAP) kiszolgáló AD FS farmot módjáról. |
| [Összevont tartomány hozzáadása](#addfeddomain) |Hogyan adhat egy összevont tartományt. |
| [Az SSL-tanúsítvány frissítése](active-directory-aadconnectfed-ssl-update.md)| Hogyan lehet frissíteni az SSL-tanúsítványt az AD FS-farm. |
| **Az AD FS testreszabása** | |
| [Adjon hozzá egy egyéni vállalati emblémát vagy ábra](#customlogo) |Az AD FS bejelentkezési oldalát a vállalati embléma és illusztráció testreszabásának módját. |
| [Adjon meg egy bejelentkezési leírást](#addsignindescription) |Hogyan bejelentkezésilap-leírást. |
| [AD FS jogcím szabályok módosítása](#modclaims) |Hogyan lehet módosítani az AD FS összevonási több, különböző esetekre jogcímek. |

## <a name="manage-ad-fs"></a>Az AD FS kezelése
Különböző AD FS-sel kapcsolatos feladat az Azure AD Connect varázsló segítségével elvégezhető az Azure AD Connect minimális felhasználói beavatkozás. Miután befejezte az Azure AD Connect telepítése varázslóval, a varázsló futtatása újra további feladatok elvégzéséhez.

## <a name="repairthetrust"></a>Javítsa ki a bizalmi kapcsolat 
Az Azure AD Connect használatával ellenőrizze az AD FS és az Azure AD megbízik, és tegye meg a megfelelő lépéseket a megbízhatóság javításához, aktuális állapotát. Kövesse az alábbi lépéseket az Azure AD kijavításához és az AD FS megbízható.

1. Válassza ki **javítási aad-ben és az AD FS megbízható** további feladatok közül.
   ![Javítsa az aad-ben és az AD FS megbízható](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Az a **az Azure AD Connect** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adataival, és kattintson a **következő**.
   ![Csatlakozás az Azure AD-hez](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Az a **távelérési hitelesítő adatainak** lapon, a tartományi rendszergazda adja meg a hitelesítő adatait.

   ![Távoli hozzáférési hitelesítő adatok](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Miután rákattintott **következő**, az Azure AD Connect tanúsítványállapot keres, és az esetleges problémákat jeleníti meg.

    ![A tanúsítványok állapota](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    A **beállíthatja az** lap a megbízhatóság javításához végrehajtott műveletek listáját jeleníti meg.

    ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Kattintson a **telepítése** a megbízhatóság javításához.

> [!NOTE]
> Az Azure AD Connect is csak javítása vagy a törvény önaláírt tanúsítványokat. Az Azure AD Connect harmadik féltől származó tanúsítványok nem lehet kijavítani.

## <a name="alternateid"></a>Az Azure AD használatával AlternateID összevonni 
Javasoljuk, hogy a helyszíni felhasználói egyszerű Name(UPN) és a felhő egyszerű felhasználónév mindig azonos. Ha a helyszíni egyszerű Felhasználónévvel használ egy nem átirányítható tartomány (például) Contoso.local), vagy nem módosítható miatt helyi alkalmazásfüggőségek, azt javasoljuk másodlagos bejelentkezési azonosító beállítása Másodlagos bejelentkezési Azonosítóval is lehetővé teszi a bejelentkezés során tapasztal élmény konfigurálását ahol felhasználók bejelentkezhetnek csak az egyszerű Felhasználónevük, például a mail attribútum. A választás az egyszerű felhasználónév az Azure AD Connect az alapértelmezett érték a userPrincipalName attribútum az Active Directoryban. Ha az attribútum válasszon az egyszerű felhasználónév, és az AD FS segítségével összevonja, majd az Azure AD Connect AD FS beállítják másodlagos bejelentkezési azonosítóval. Egy másik attribútum kiválasztása az egyszerű felhasználónév példát alább találja:

![Alternatív ID attribútum kiválasztása](media/active-directory-aadconnect-federation-management/attributeselection.png)

Másodlagos bejelentkezési azonosító konfigurálása az AD FS két fő lépésből áll:
1. **Állítsa be az jobb kiállítási jogcímek**: az Azure AD függő entitáshoz kiállítási jogcímszabályok megbízható módosultak, hogy a kijelölt UserPrincipalName attribútum használata a másik azonosító a felhasználó.
2. **Engedélyezze a másodlagos bejelentkezési Azonosítóval a az AD FS konfigurációt**: az AD FS konfigurációt frissül, hogy az Active Directory összevonási szolgáltatások is kereshet a felhasználók a megfelelő erdőben, a másik azonosítójával. Ez a konfiguráció az AD FS Windows Server 2012 R2 (az KB2919355) vagy újabb rendszeren támogatott. Ha az AD FS-kiszolgáló 2012 R2, az Azure AD Connect ellenőrzi, hogy a szükséges KB. Ha a rendszer nem ismeri fel a KB, egy figyelmeztetés jelenik meg konfiguráció befejezése után alább látható módon:

    ![A hiányzó KB 2012R2 a figyelmeztetés](media/active-directory-aadconnect-federation-management/kbwarning.png)

    A konfiguráció esetén a hiányzó KB kijavításához, telepítse a szükséges [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) , és javítsa a bizalmi kapcsolat használatával [javítása aad-ben és az AD FS-megbízhatóság](#repairthetrust).

> [!NOTE]
> További információk a alternateID és a kézi konfigurálásának lépései, [másodlagos bejelentkezési azonosító beállítása](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Az AD FS-kiszolgáló hozzáadása 

> [!NOTE]
> Az AD FS-kiszolgáló hozzáadása az Azure AD Connect a PFX-tanúsítványt igényel. Ezért a művelet végrehajtása, csak akkor, ha az AD FS-farm konfigurálta az Azure AD Connect használatával.

1. Válassza ki **további összevonási kiszolgáló üzembe helyezése**, és kattintson a **következő**.

   ![További összevonási kiszolgáló](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Az a **az Azure AD Connect** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adataival, és kattintson a **következő**.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Adja meg a tartomány rendszergazdai hitelesítő adatokat.

   ![Tartományi rendszergazda hitelesítő adatai](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Az Azure AD Connect kéri a jelszót az új AD FS-farm konfigurálása az Azure AD Connect közben megadott PFX-fájl. Kattintson a **jelszó megadása** kell adnia a jelszót a PFX-fájlból.

   ![Tanúsítványjelszó](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![SSL-tanúsítvány megadása](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Az a **AD FS-kiszolgálók** lapján adja meg a kiszolgáló neve vagy IP-cím, fel kell venni az AD FS-farm.

   ![AD FS-kiszolgálók](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Kattintson a **következő**, és hajtsa végre a végleges **konfigurálása** lap. Miután az Azure AD Connect befejezte a kiszolgálók hozzáadását az AD FS-farm, nyílik lehetőség a kapcsolat ellenőrzése.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![A telepítés befejeződött](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>AD FS WAP-kiszolgáló hozzáadása 

> [!NOTE]
> A WAP-kiszolgáló, az Azure AD Connect megköveteli a PFX-tanúsítvány. Ezért csak hajthatja végre ezt a műveletet, ha az AD FS-farm konfigurálta az Azure AD Connect használatával.

1. Válassza ki **webalkalmazás-Proxy telepítése** rendelkezésre álló feladatok közül.

   ![Webalkalmazás-Proxy telepítéséhez](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Adja meg az Azure globális rendszergazdai hitelesítő adatokat.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Az a **adja meg az SSL-tanúsítvány** lapján adja meg a jelszót az AD FS-farm konfigurálása az Azure AD Connect során megadott PFX-fájl.
   ![Tanúsítvány jelszava](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![SSL-tanúsítvány megadása](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Adja hozzá a kiszolgálót, lehet hozzáadni a WAP-kiszolgálóként. A WAP-kiszolgáló nem lehet, hogy a tartományhoz csatlakoztatni, mert a varázsló megkérdezi, a hozzáadni kívánt kiszolgáló rendszergazdai hitelesítő adatokat.

   ![A felügyeleti kiszolgáló hitelesítő adatai](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Az a **Proxy megbízhatósági hitelesítő adatok** lapján adja meg a rendszergazdai hitelesítő adatokat konfigurálja a proxyt megbízik, és hozzáférés az AD FS-farm elsődleges kiszolgálóját.

   ![Proxy megbízhatósági hitelesítő adatai](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Az a **beállíthatja az** lap, a varázsló végrehajtott műveletek listáját tartalmazza.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Kattintson a **telepítése** a konfiguráció befejezéséhez. A konfiguráció befejezése után a varázsló lehetővé teszi a a kiszolgálók ellenőrzésére is. Kattintson a **ellenőrizze** kapcsolat ellenőrzése.

   ![A telepítés befejeződött](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Összevont tartomány hozzáadása 

Nem lehet összevont Azure AD-val az Azure AD Connect használatával tartomány hozzáadása. Az Azure AD Connect a tartományt összevonásra hozzáadja, és módosítja a jogcímszabályok a kibocsátó megfelelően megfelelően, és az Azure AD összevont több tartomány esetén.

1. Egy összevont tartományt hozzáadásához válassza ki a feladatot **adnia egy további Azure AD-tartomány**.

   ![További Azure AD-tartomány](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. A varázsló következő oldalán az Azure AD globális rendszergazdai hitelesítő adatok megadása.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Az a **távelérési hitelesítő adatainak** lapján adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Távoli hozzáférési hitelesítő adatok](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. A következő oldalon a varázsló a lehet összevonást végezni a helyszíni címtár az Azure AD-tartományok listáját tartalmazza. Válassza ki a tartományt a listából.

   ![Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Után úgy dönt, hogy a tartomány, a varázsló nyújt további a varázsló által elvégzendő műveleteket és a hatása a konfiguráció megfelelő információt. Bizonyos esetekben egy tartományhoz, amely még nincs ellenőrizve az Azure ad-ben, ha a varázsló nyújt segítséget, ellenőrizze a tartományt. Lásd: [az egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md) további részleteket.

5. Kattintson a **Tovább** gombra. A **beállíthatja az** lap, amely az Azure AD Connect hajtja végre műveletek listáját jeleníti meg. Kattintson a **telepítése** a konfiguráció befejezéséhez.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> A hozzáadott összevont tartomány felhasználóinak szinkronizálása előtt fogja tudni belépni az Azure AD kell.

## <a name="ad-fs-customization"></a>AD FS Testreszabás
A következő szakaszok részletesen bemutatják a gyakori feladatokat, amelyek akkor lehet végrehajtani, ha a az AD FS bejelentkezési oldal testreszabása.

## <a name="customlogo"></a>Adjon hozzá egy egyéni vállalati emblémát vagy ábra 
A megjelenő vállalatembléma módosításához a **bejelentkezési** lapon, használja a következő Windows PowerShell-parancsmagot és szintaxist.

> [!NOTE]
> Az embléma ajánlott dimenziói 260 x 35 pixel, legfeljebb 10 KB-os fájlméretben 96 dpi.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> A *TargetName* paraméter megadása kötelező. Az alapértelmezett témájának az AD FS alapértelmezett neve.

## <a name="addsignindescription"></a>Adjon meg egy bejelentkezési leírást 
A bejelentkezésilap-leírást a **bejelentkezési oldal**, használja a következő Windows PowerShell-parancsmagot és szintaxist.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>AD FS jogcím szabályok módosítása 
Az AD FS támogatja a gazdag jogcím nyelv, amely segítségével egyéni jogcímszabályok létrehozása. További információkért lásd: [a Jogcímszabály nyelvének szerepe](https://technet.microsoft.com/library/dd807118.aspx).

A következő szakaszok ismertetik, hogyan írhat egyéni szabályok bizonyos forgatókönyvek esetén is az Azure AD és az AD FS összevonási.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Nem módosítható egy értéket, hogy az attribútum a jelen feltételes azonosítója
Az Azure AD Connect lehetővé teszi egy adatforrás kapcsolatainak használt objektumok szinkronizált az Azure AD-attribútum megadását. Ha az egyéni attribútum értéke nem üres, érdemes egy nem módosítható Azonosítót jogcímet.

Például kiválaszthatja **ms-ds-consistencyguid** forráshorgony és probléma attribútumaként **ImmutableID** , **ms-ds-consistencyguid** abban az esetben, ha az attribútum értéke rajta. Ha nincs érték az attribútum ellen, **objectGuid** , az nem módosítható azonosítót. Egyéni jogcímszabályok a következő szakaszban leírt módon készletét, hogyan hozhat létre.

**1. szabály: Lekérdezés attribútumok**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Ebben a szabályban értékének lekérdezésére **ms-ds-consistencyguid** és **objectGuid** a felhasználó az Active Directoryból. Módosítsa a tároló neve az AD FS üzembe helyezése a megfelelő névre. Is módosítása a jogcímek írja be a megfelelő jogcím-e az összevonási típus meghatározottak szerint **objectGuid** és **ms-ds-consistencyguid**.

Továbbá használatával **hozzáadása** és nem **probléma**, ne telepítsen az entitás kimenő problémát, és az értékeket is használhat köztes értékként. A jogcím egy újabb szabályt állít ki, melyik értéket nem módosítható azonosítóként létrehozása után

**2. szabály: Ellenőrizze, hogy létezik-e az ms-ds-consistencyguid a felhasználó**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ez a szabály meghatározása nevű ideiglenes jelzőt **idflag** értékre van állítva, amely **useguid** esetén nem **ms-ds-consistencyguid** feltöltve a felhasználó számára. Ez mögötti logika a tényt, hogy az AD FS nem engedélyezi üres jogcímeket. Ezért amikor jogcímeket adhatnak hozzá http://contoso.com/ws/2016/02/identity/claims/objectguid és http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid 1. szabály, hogy végül egy **msdsconsistencyguid** csak jogcím, ha a rendszer a felhasználó tölti fel az értéket. Ha azt nem üres, az AD FS látja, hogy egy üres érték fog szerepelni, elutasítja azokat azonnal. Minden objektumot kell **objectGuid**, így az igény mindig van 1. szabály végrehajtása után.

**3. szabály: Ki az ms-ds-consistencyguid ID nem módosítható, ha telepítve**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Ez az egy implicit **Exist** ellenőrzése. Ha a jogcím értéke, hogyan adhat ki, amely nem módosítható azonosítóként Az előző példában a **nameidentifier** jogcímek. Állítson be a megfelelő jogcím típusa a környezetben nem módosítható a(z) kell.

**4. szabály: Ki objectGuid ID nem módosítható, ha az ms-ds-consistencyGuid nincs jelen**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Ebben a szabályban, akkor még egyszerűen ellenőrzése az ideiglenes jelző **idflag**. Eldöntheti, hogy a jogcím értéke alapján kiadása.

> [!NOTE]
> Ezek a szabályok sorrendjének fontos.

### <a name="sso-with-a-subdomain-upn"></a>Egyszerű felhasználónév altartomány egyszeri bejelentkezés
Összevont Azure AD Connect használatával, a több tartomány is hozzáadhat [hozzá egy új összevont tartományt](active-directory-aadconnect-federation-management.md#addfeddomain). Módosítania kell a felhasználó egyszerű felhasználónév (UPN) jogcím, hogy a kibocsátó azonosító felel meg a legfelső szintű tartomány és nem a altartomány, mert az összevont gyökértartomány is magában foglalja a gyermek.

Alapértelmezés szerint a jogcímszabály kibocsátó azonosító értéke:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Alapértelmezett kibocsátó azonosító jogcím](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

Az alapértelmezett szabály egyszerűen vesz igénybe az UPN-utótagot, és használja a kibocsátó azonosító jogcímek. Például John sub.contoso.com felhasználóként, és contoso.com össze van vonva az Azure ad-val. John megadja john@sub.contoso.com során az Azure AD bejelentkezés felhasználóneveként. Az alapértelmezett kibocsátó azonosító jogcímszabály, ha az AD FS kezelő a következő módon:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**A jogcím értéke:**  http://sub.contoso.com/adfs/services/trust/

A kibocsátó jogcím értéke csak a legfelső szintű tartomány van, módosítsa megfelelően a következő jogcímszabály:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>További lépések
További információ [felhasználói bejelentkezés lehetőségei](active-directory-aadconnect-user-signin.md).
